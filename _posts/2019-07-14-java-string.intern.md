---
layout: post
title:  "Java String.intern() and String Pooling"
date:  20190714 17:09:11 +0800
categories: default 
tags:
 - java 
---

`String` is immutable in java, JVM can optimize the memory usage if only one copy of each literal string gets stored in a `pool`.

## what is String.intern()?
```
java 1.8:
    /**
     * Returns a canonical representation for the string object.
     * <p>
     * A pool of strings, initially empty, is maintained privately by the
     * class {@code String}.
     * <p>
     * When the intern method is invoked, if the pool already contains a
     * string equal to this {@code String} object as determined by
     * the {@link #equals(Object)} method, then the string from the pool is
     * returned. Otherwise, this {@code String} object is added to the
     * pool and a reference to this {@code String} object is returned.
     * <p>
     * It follows that for any two strings {@code s} and {@code t},
     * {@code s.intern() == t.intern()} is {@code true}
     * if and only if {@code s.equals(t)} is {@code true}.
     * <p>
     * All literal strings and string-valued constant expressions are
     * interned. String literals are defined in section 3.10.5 of the
     * <cite>The Java&trade; Language Specification</cite>.
     *
     * @return  a string that has the same contents as this string, but is
     *          guaranteed to be from a pool of unique strings.
     */
    public native String intern();
```

Try out:
```java
String s2 = new String("another-random-string"); // "another-random-string" is part of the pool during jvm initializing.
System.out.println(s2 == s2.intern()); // false. s2.inter() returns reference to the existing reference from the string pool.

String s1 = new String(new StringBuilder("this").append("-is-a-random-string")); // this-is-a-random-string will be totally new to jvm during runtime.
System.out.println(s1 == s1.intern()); // true. s1.intern() adds s1 to the string pool.
```

with `-XX:+PrintStringTableStatistics`:
```
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :       883 =     21192 bytes, avg  24.000
Number of literals      :       883 =     57864 bytes, avg  65.531
Total footprint         :           =    559160 bytes
Average bucket size     :     0.015
Variance of bucket size :     0.015
Std. dev. of bucket size:     0.121
Maximum bucket size     :         2
```

## Conclusion
 - All literal strings and string-valued constants are referring to a string inside the pool
 - when an intern() method is invoked, a reference from the pool will be returned. if the string is not existing in the pool, the string will be added


a deeper post: http://java-performance.info/string-intern-in-java-6-7-8/

