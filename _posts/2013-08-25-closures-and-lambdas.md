---
title: Closures and Lambdas
author: Simon Campbell
layout: post
categories:
  - Development
  - Honours Project
tags:
  - .net
  - android
  - 'c#'
  - closures
  - java
  - lambdas
  - noise
  - programming
---
Over the past year I have been programming heavily in C#. This experience involved working on a few reasonably large projects and has let me get a good feel for lambda expressions and closures.

For those who do not know, a lambda expression is an anonymous function. Closures are functions where <span style="line-height: 1.714285714; font-size: 1rem;">variables not defined in the function parameter-list but accessible in the environment where the function is defined. Lambda expressions in C# obviously define lambdas, but can also define closures. In saying this, it is important to note that <strong>closures are not lambdas.</strong></span>

Prior to using lambda expressions in C# I had spent most of my time programming in Java and Pawn. Neither of these languages support lambda expressions and now when I write code in these languages it hurts. Trying to recreate the simplicity that lambdas provide is impossible and in Java there is a lot of noise in defining reusable functions. Reusable functions in Java are usually defined as Functors. The difference is made apparent in my Honours Project where it is necessary write code like this:

<pre class="brush: java; title: ; notranslate" title="">public class VoiceMethodFactory {
    // VoiceMethodMap just works like any other map, I extract a 'VoiceMethod' based
    // on a string key (based on users speech-to-text) and execute it.
    private Map&lt;String, VoiceMethod&gt; voiceMethods = new VoiceMethodMap();

    private VoiceMethod sayUserLocation = new VoiceMethod() {
        @Override
        public void invoke(BlindAssistant assistant, Map&lt;String, String&gt; arguments) {
            assistant.sayCurrentLocation();
        }
    };

    private VoiceMethod stopNavigating = new VoiceMethod() {
        @Override
        public void invoke(BlindAssistant assistant, Map&lt;String, String&gt; arguments) {
            assistant.navigateTo(null);
        }
    };

    private VoiceMethodFactory() {
        voiceMethods.put("where am I", sayUserLocation);
        voiceMethods.put("stop navigating", stopNavigating);
    }

    public static VoiceMethodFactory createStandardFactory() {
        return new VoiceMethodFactory();
    }
}

/**
 * Defines function that can be used to invoke voice methods (defined in another file)
 */
public interface VoiceMethod {
    void invoke(BlindAssistant assistant, Map&lt;String, String&gt; arguments);
}
</pre>

This method of doing things required that I define an additional interface and also define classes which implement it and provide the behaviour necessary. These additional classes contain a lot of boilerplate code and (usually) one out of five lines defines the difference. If this setup was defined in a language which supported lambdas, such as C# then I would be writing something like this:

<pre class="brush: csharp; title: ; notranslate" title="">public class VoiceMethodFactory {
    // Map changes to a Dictionary in C#
    private Dictionary&lt;String, Action&lt;BlindAssistant, Dictionary&lt;String, String&gt;&gt; voiceMethods = new VoiceMethodMap();

    private VoiceMethodFactory() {
        voiceMethods.put("where am I", (b, a) =&gt; a.sayCurrentLocation());
        voiceMethods.put("stop navigating", (b, a) =&gt; a.navigateTo(null));
    }

    public static VoiceMethodFactory createStandardFactory() {
        return new VoiceMethodFactory();
    }
}
</pre>

This is a lot simpler. In terms of line count (without comments) the C# example is 40% smaller than the Java example. I imagine the difference is much greater in my current code base which defines several more of these `VoiceMethod`s. Using lambdas is much more concise and to the point and I could now even argue that my original reasons for the `VoiceMethodFactory` are now void. The reason I created the factory was to move this anonymous class definition noise away from another part of my system.

Java 8 is going to [support lambdas and closures as part of Project Lambda][1]. It is unfortunate however that Android developers may not get to use lambda expressions in their Java code for a little while yet. This is because Android does not use the official Java implementation. Instead Android uses Apache Harmony, an open-source implementation of Java, due to [licensing issues][2] between Sun and Google.

 [1]: http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html "Java 8 - Lambda Expressions"
 [2]: http://stackoverflow.com/questions/16921337/why-apache-harmony-or-how-to-use-java-8-on-android "Why Apache Harmony on Android"