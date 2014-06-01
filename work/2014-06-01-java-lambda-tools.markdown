---
layout: post
title:  "Java lambda tools"
date:   2014-06-01 17:06:25
categories: java lambda
---




<!--
{% highlight java %}
  private static <T, K> Function<T, K> timer(Function<T, K> f) {
        if( !TIMER_ON ) {
            return f;
        }
 
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
-->

