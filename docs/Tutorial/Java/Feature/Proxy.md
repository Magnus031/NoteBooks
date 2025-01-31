# <center>代理</center>
> 代理是Java中很常见的一个设计模式，它是一种结构型设计模式。代理模式为其他对象提供一种代理以控制对这个对象的访问。代理模式定义了一个代理对象，代理对象可以在客户端和目标对象之间起到中介的作用。代理模式最常见的应用场景是：远程代理、虚拟代理、保护代理、缓存代理、智能引用等。

## RPC中的代理
> 我们先介绍一下RPC框架中的出现的代理

代理，顾名思义就是 代替 和 服务。我们在实现RPC框架的时候，使用到了代理模式。也就是为远程的调用接口提供了一个代理对象，这个代理对象可以在客户端和服务端之间起到中介的作用。这样的好处就是，我们可以在客户端调用远程服务的时候，不需要关心底层的网络通信细节，只需要调用代理对象的方法即可。

### 代理的分类
1. 静态代理 : 顾名思义就是在编译时期就已经确定了代理对象 
2. 动态代理 : 顾名思义就是在运行时期才确定代理对象，也就是通过我们传入的类型，来动态的生成代理对象。

下面是一个有关动态代理的例子:
```java
public class ServiceProxyFactory {
    public static <T> T getProxy(Class<T> serviceClass){
        // By distinguishing the configuration,we can know whether to use the mock proxy or the real proxy;
        if(Holder.getRpcConfig().getIsMock())
            return getMockProxy(serviceClass);

        return (T) Proxy.newProxyInstance(
                serviceClass.getClassLoader(),
                new Class[]{serviceClass},
                new ServiceProxy());
    }


    public static <T> T getMockProxy(Class<T> serviceClass){
        return (T) Proxy.newProxyInstance(
                serviceClass.getClassLoader(),
                new Class[]{serviceClass},
                new MockServiceProxy());
    }
}
```
我们利用工厂`Factory`这个设计模式来进行动态代理有关的操作。

#### `getProxy` 方法
> 首先，我们通过传入一个服务的接口类型，从而来返回代理对象。

在这里，我们假设我们利用Java的反射机制传入的是一个 `UserService.class` 的接口服务类型，然后我们通过 `Proxy.newProxyInstance` 来生成一个代理对象。这个代理对象实现了 `UserService` 这个接口，同时我们还传入了一个 `ServiceProxy` 的对象，这个对象是代理对象的逻辑实现。

仔细看上面的代码，我们调用这个方法，传回来的是一个动态代理的实例。`Proxy.newProxyInstance`.这个返回的代理对象具有以下的三个特点:

1. 我们具有一个类加载器，用于专门加载这个代理对象中传入类的字节码。
2. 我们传入的接口类型的数组，用于指定这个代理对象实现了哪些接口。
3. 我们传入的 `InvocationHandler` 对象，用于实现代理对象的逻辑。

> 其实很好理解，就是简单的几个问题，它是谁(第二点)， 它需要做什么(第三点)， 它需要怎么实现(第一点)。

#### `new ServiceProxy` 方法
接下来仔细来看我们传入的实现`InvocationHandler`的对象 `ServiceProxy`。这个对象实现了 `InvocationHandler` 这个接口，这个接口是Java提供的一个接口，用于实现动态代理的逻辑。

下面是我RPC框架中的一个简单的例子，仅供参考

```java
@Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        /**
         * 总结一下
         * 首先，我们知道在invoke的总体逻辑就是通过一个动态代理来帮助我们调用远程的服务。
         * 我们所需要做的就是构造一个请求，然后通过在注册中心中找到服务的方法，并且调用它，然后返回服务方法运行的结果。这个就是我们的总体逻辑。
         *
         * 细分一下就是:
         * Step1:
         *  首先，我们需要一个序列化器来帮助我们传输RpcRequest和RpcResponse。
         *      这个时候我们就需要从SerializerFactory工厂中来获取实例，因为我们假设在通过配置文件指定了一个序列化器。那么就需要从序列化工厂中找到对应的序列化器。
         * Step2:
         *  构造 RpcRequest 请求。这个是我们需要利用builder来创建一个RpcRequest对象。
         * Step3:
         *  发送请求。这个时候我们需要从注册中心来获取服务提供者的地址，然后发送请求。
         *    Step3.1 :
         *      首先，我们需要序列化RpcRequest请求。
         *    Step3.2 :
         *      然后，我们先从当前的RpcConfig的配置文件中找到需要的注册中心的配置。
         *    Step3.3 :
         *      然后我们就可以在注册中心工厂中寻找我们的注册中心实例。如果没有，就直接的利用默认的注册中心。
         *    Step3.4 :
         *      在注册中心中寻找我们需要的服务中的所有节点。
         *    Step3.5 :
         *      这里，我们选择服务中的第一个节点来进行请求。
         * Step 4:
         *  我们找到了可以提供服务的节点之后，我们就可以发送请求了。往找到的服务节点中发送Request请求。
         *
         * Step5 :
         * 返回结果。
         *
         */


        // 指定序列化器
        Serializer serializer = SerializerFactory.getInstance(Holder.getRpcConfig().getSerializer());

        // 构造请求
        String serviceName = method.getDeclaringClass().getName();
        RpcRequest rpcRequest = RpcRequest.builder()
                .serviceName(method.getDeclaringClass().getName())
                .methodName(method.getName())
                .parameterTypes(method.getParameterTypes())
                .args(args)
                .build();


        try {
            // 序列化
            byte[] bodyBytes = serializer.serialize(rpcRequest);
            // 从注册中心来获取服务提供者请求地址
            RpcConfig rpcConfig = Holder.getRpcConfig();
            RegisterTry registerTry = RegisterFactory.getInstance(rpcConfig.getRegisterConfig().getRegisterType());

            ServiceMetaInfo serviceMetaInfo = new ServiceMetaInfo();
            serviceMetaInfo.setServiceName(serviceName);
            serviceMetaInfo.setServiceVersion(RpcConstant.DEFUALT_SERVICE_VERSION);
            System.out.println(serviceMetaInfo);
            // 找到所有提供该服务的节点;
            List<ServiceMetaInfo> list = registerTry.serviceDiscovery(serviceMetaInfo.getServiceKey());
            // 没有节点提供这样的服务；
            if(CollUtil.isEmpty(list))
                throw new RuntimeException("暂无服务地址");
            // 我们优先选取第一个服务;
            ServiceMetaInfo selectedServiceMetaInfo = list.get(0);
            // 在找到可以提供服务的节点之后，我们进行发送请求;
            try(HttpResponse httpResponse = HttpRequest.post(selectedServiceMetaInfo.getServiceAddress()).body(bodyBytes).execute()){
                byte[] results = httpResponse.bodyBytes();
                RpcResponse rpcResponse = serializer.deserialize(results, RpcResponse.class);
                return rpcResponse.getData();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }
```


#### `invoke` 方法

传入的参数有三个,这个是函数的签名 `public Object invoke(Object proxy,Method method,Object[] args) throw Throwable` 

- `Object Proxy` : 这个代理本身
- `Method method` : 这个是我们需要调用的方法
- `Object[] args` : 这个是我们调用方法的参数 