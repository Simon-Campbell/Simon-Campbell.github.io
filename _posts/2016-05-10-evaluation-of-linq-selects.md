---
title: 'Evaluation of LINQ Selects'
author: Simon Campbell
layout: post
categories:
  - Development
tags:
  - .net
  - 'c#'
  - programming
---
Recently I was writing a Web API that needed to transform some data. Whilst transforming some data from a data store, I opted to write my code structured like the following:

{% highlight csharp %}
// Example 1
var list = new List<int>() {0, 1, 2, 3 };

var doublePlusThree = list
    .Select(i => i * 2)
    .Select(i => i + 3);
{% endhighlight %} 

What I have done here is broken what could have been a more complex LINQ expression into several smaller ones chained together. Alternatively, and perhaps more commonly this code would have been written as follows:


{% highlight csharp %}
// Example 2
var list = new List<int>() { 0, 1, 2, 3 };
var doublePlusThree = list.Select(i => (i * 2) * 3);
{% endhighlight %} 

In the examples provided the second example may arguably produce easier to read code. This is because as the intermediate operations are quite simple and therefore easier to comprehend. However, there are scenarios when the intermediate operations are more complex and thus are less comprehensible together. In these scenarios you may wish to consider decomposing your expression into smaller intermediate operations.

During a code review a colleague pulled up the structure of the first example in a code review. He thought that the first example would result in two loop iterations. My understanding was that it *should not* result in two loop iterations, and I proceeded to talk about how the expressions were [lazily evaluated and execution was deferred](https://msdn.microsoft.com/en-nz/library/mt693152.aspx). That is, they are not evaluated until required. 

During our discussion I realised that I had never actually tested this behaviour, it was something that was pieced together based on my usage of LINQ and its documentation. I was basing my argument on the idea of what I think the code *should* do, not neccesarily what I had observed. In light of this I proposed that I do a little more research into finding concrete evidence. Unfortunately I was unable to find any evidence explicitly outlining what would happen[^1], so I decided to write up [some rough tests](https://gist.github.com/Simon-Campbell/4edd8644ff552a6e52059fa615c5234a) to make some assertions about the behaviour.

What resulted from those tests were concrete evidence of what was expected. That is: if you iterate through an enumerable created by LINQ statements, then the expressions are being evaluated against an item as you move through the enumerable. For example, two chained `Select` statements produce the same number of functional operations as two functional operations within one `Select` statement.[^2]

When you chain together LINQ statements you are actually creating **new** enumerables with augmented enumerators. An enumerator is a structure for moving through a sequence of items (enumerating). Each chained LINQ statement produces a new enumerable with a new enumerator based off of the dependent enumerables enumerator. The operations defined at each stage is essentially pipelined between enumerators *as you are moving through the sequence of items.* 

In summary, the following can be concluded: when you write a `Select` or `Where` statement you are actually declaring what should happen *when the collection is enumerated.* LINQ is smart enough to build enumerators that execute those expressions as you need them to be evaluated. What this means is that you can enumerate only part of the sequence and not absorb the cost, or you can push the structures onto other processors to divide work. It also may provide you with a nicer way of expressing the ideas within your code.

[^1]: I found evidence after I had written this blog post and recalled the appropriate jargon (deferred execution, lazy evaluation)
[^2]: There may be an overhead for composing operations. If this is important to your application then you will need to benchmark.