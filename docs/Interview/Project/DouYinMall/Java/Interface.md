# <center>接口文档</center>

## 订单服务

### 订单DTO

> 首先，什么是 DTO(Data Transfer Object)？它是一个数据传输对象，是一个纯粹的数据对象，不包含任何业务逻辑。在实际开发中，我们经常会使用到它，用于不同层之间的数据传输。

下面是设计的订单相关的 DTO 数据类型

```java
@Data
public class PlaceOrderDto {
    private Long userId;
    private String userCurrency;
    private Address address;
    private String email;
    private List<OrderItem> orderItems;

    @Data
    public static class Address {
        private String streetAddress;
        private String city;
        private String state;
        private String country;
        private Integer zipCode;
    }
    @Data
    public static class OrderItem{
        private CartItem item;
        private Float cost;

        @Data
        public static class CartItem{
            private Long productId;
            private Integer quantity;
        }
    }
}

```

首先来分析一下，这个订单对象的组成。

- 用户的个人信息 : 
  - 用户ID : `userId` 用于甄别每个用户
  - 用户货币 : `userCurrency` 
  - 收获地址 : 这个是一个 `inner class` 
    - 街道地址 : `streetAddress`
    - 城市 : `city`
    - 省份 : `state`
    - 国家 : `country`
    - 邮政编码 : `zipCode`
  - 订单项 : `OrderItem`
    - 购物车中选中的商品 : `CartItem` 
    - 本件商品的总花费 : `cost`

所以我们观察到，其实就是一个简单的发起订单请求的时候需要的。我们会在购物车结算的时候，发起订单请求。会进行一个对于发起订单的一个调用。那么就会用到我们现在这个DTO。



### 订单分析


#### 订单的生命周期
我们在数据库中有对订单进行状态单独的`record`

- `status` : 订单的状态,下面我们设置成6种生命状态的周期:
    - `PENDING` : 订单已经创建，等待支付 0
    - `PAID` : 订单已经支付 1 
    - `SHIPPED` : 订单已经发货 2 
    - `DELIVERED` : 订单已经送达 3
    - `COMPLETED` : 订单已经完成 4
    - `CANCELLED` : 订单已经取消 5

为此，我在 `Service/constant` 包中添加了 `OrderStateConstant` 类用于描述订单的状态.