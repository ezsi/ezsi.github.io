---
layout: post
title:  "Function decorators using Java8 lambdas"
date:   2014-06-08 11:23:25
categories: java lambda
redirect_from: "/"
---

I've been recently watching some lectures of [Design of Computer Programs](https://www.udacity.com/course/cs212) on [Udacity](http://www.udacity.com) taught by [Peter Norvig](http://en.wikipedia.org/wiki/Peter_Norvig) which was about function decorators in Python. It kept me thinking whether I could do this in Java with lambda expressions so I was playing around a bit ... 

I started with a simple Fibonacci implementation to work with. I used the new [Function](http://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) interface to implement the Fibonacci function and I added a little test code to calculate the 45th element of the series. 

{% highlight java %}
private static Function<Integer, Long> fib = n -> {
    if (n == 0 || n == 1) return 1L;
    return Tools.fib.apply(n - 1) + Tools.fib.apply(n - 2);
};

public static void main(String[] args) {
    Function<Integer, Long> tfib = fib;
    long res = tfib.apply(45);
    System.out.println("res = " + res);
}

{% endhighlight %} 


So far so good. I extended the solution with a *timer* function which takes an another function as an argument, measures the approximate execution time and prints it out to the console.

{% highlight java %}
private static <T, K> Function<T, K> timer(Function<T, K> f) {
    Function<T, K> fun = (args) -> {
        long time = System.currentTimeMillis();
        K ret = f.apply(args);
        time = System.currentTimeMillis() - time;
        System.out.println("time = " + time);
        return ret;
    };
    return fun;
}
{% endhighlight %} 

I slightly modified the main function to *decorate* the Fibonacci function. So running the program now prints out the time it took to run. It took running 16 seconds on my machine. 

{% highlight java %}
public static void main(String[] args) {
    Function<Integer, Long> tfib = timer(fib);
    long res = tfib.apply(45);
    System.out.println("res = " + res);
}
{% endhighlight %} 


I added a *cache* function which also takes a function argument and stores the result of the function call in a cache which is a map where the key is the function argument and the value is the result of the function call. If the given argument can already be found in the cache then it will return that precomputed value if not then it will call the function and store the result so any subsequent requests will be served by the cache. Then I decorated the Fibonacci function with the cache function. The client code in the main function remained the same since I only changed the definition of the *fib* function. Now when I run the program it takes no time to execute since the recursion can rely on the values in the cache. 

{% highlight java %}
private static <T, K> Function<T, K> cache(Function<T, K> f) {
    Map<T, K> cache = new HashMap<>();
    Function<T, K> fun = (args) -> {
        K ret = cache.get(args);
        if( ret == null ) {
            ret = f.apply(args);
            cache.put(args, ret);
        }
        return ret;
    };
    return fun;
}

private static Function<Integer, Long> fib = cache(n -> {
    if (n == 0 || n == 1) return 1L;
    return Tools.fib.apply(n - 1) + Tools.fib.apply(n - 2);
});
{% endhighlight %} 


As a final step I added two static boolean field to allow me to turn on and off the timer and cache functions and I put a test for them in the *timer* and *cache* functions. 

{% highlight java %}
public static final boolean CACHE_ON = true;
public static final boolean TIMER_ON = true;

if( !TIMER_ON ) return f;
if( !CACHE_ON ) return f;

{% endhighlight %} 

So the final code would be something like this:

{% gist ezsi/1306b85ad55a9f7c15f8 %}
