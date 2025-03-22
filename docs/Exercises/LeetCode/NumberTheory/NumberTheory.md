# <center>数论</center>

### 轮子 1 判断一个数是否是质数

```java
public static boolean isPrime(int x){
    if(x<2) return false;
    for(int i=2;i*i<=x;i++){
        if(x%i==0) return false;
    }
    return true;
}
```


### 轮子 2 欧拉筛法求质数

```java
public static List<Integer> getPrimes(int n){
    boolean[] isPrime = new boolean[n+1];
    List<Integer> primes = new ArrayList<>();

    // initial 
    for(int i=2;i<=n;i++){
        isPrime[i] = true;
    }

    for(int i=2;i<=n;i++){
        if(isPrime[i])
            primes.add(i);
    
        for(int p:primes){
            // 超出范围了;
            if(i*p>n) break;
            // 不是质数;
            isPrime[i*p] = false;
            // 不能被整除;
            if(i%p==0) break;
        }
    }

    return primes;
}
```


### 轮子 3 gcd 最大公约数

```java
public static int gcd(int a,int b){
    if(b == 0)
        return a;
    return gcd(b,a%b);
}
```


### 轮子 4 拓展欧几里得算法

用于寻找一组特解，求解 $ax+by=gcd(a,b)$

```java
public static int[] extendedGcd(int a,int b){
    if(b == 0)
        return new int[]{a,1,0};
    
    // 递归求解;
    int[] result = extendedGcd(b,a%b);
    // 交换;
    int gcd = result[0];
    int x = result[2];
    int y = result[1] - a/b*result[2];
    return new int[]{gcd,x,y};
}
```
