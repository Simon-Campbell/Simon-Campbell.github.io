---
title: Unit Testing Gotcha with Entity Framework
author: Simon Campbell
layout: post
categories:
  - Development
tags:
  - 'c#'
  - entity framework
  - linq
  - programming
  - sql
  - testing
  - unit testing
  - webapi
---
I have been working with the [Entity Framework][1] at work. It has been used to connect with a database for a RESTful [ASP.Net Web API][2] service.

Testing the Web API has been interesting to learn about. Originally I decided to test this Web API using [Fiddler][3] to do HTTP POSTs to selected URLs and then analyzing the results. It soon became apparent that this method was slow and error prone&#8211; making me opt for unit-testing.

I spent a good portion of the day writing the tests, fixing bugs as I found them. Fortunately, the API scope of the API was relatively small and writing most of the tests did not take too long. That is, until I encountered a weird side effect of the C# Entity Framework.

One of the API method tests that made a POST required the use of a database ObjectContextobject to check if the request actually updated the database entities that it should have. The test however was failing, I believe this was due a ObjectContext being created in the unit-test method and another being created in the method being tested. The two differentObjectContexts were out of sync even though they were attached to the same datastore.

The structure of the code that caused the problem was similar to this piece of skeleton code:

{% highlight csharp %}
class TestClass
{
    ObjectContext db;

    [TestInitialize]
    public void Setup()
    {
       db = new ObjectContextEntities();
    }

    [TestCleanup]
    public void Cleanup()
    {
        db.Dispose();
    }

    [TestMethod]
    public void TestPost()
    {
        // Add a new Entity to the Entities table,
        // with a TimeOut of null.
        Entity e = new Entity();
        e.TimeIn = DateTime.Now;
        e.TimeOut = null;
        db.AddToEntities(e);

        // Call a method which updates TimeOut for all entities to
        // DateTime.Now
        new PostingController().Post();

        // TimeOut for each row in Entities should have been changed
        // from NULL to not now
        var q = from q in db.Entities select e;

        foreach (var r in q)
            Assert.IsTrue(r.TimeOut != null);
    }
}

class PostingController() // Class under test
{
    public void Post()
    {
        var db = new ObjectContextEntities();
        var q = from e in db.Entity select e;

        foreach (var r in q)
            q.TimeOut = DateTime.Now;

        db.SaveChanges();
        db.Dispose();
    }
}
{% endhighlight %} 

It appears that the Entity Framework tries to be &#8220;smart&#8221; and efficient which means that the update is not synchronized between the ObjectContexts. In order to work around this I was required to call the Refresh() method on the ObjectContext after the call to Post(), like so:

{% highlight csharp %}
db.Refresh(RefreshMode.StoreWins, db.Entities);
{% endhighlight %} 

I am not sure if this is the recommended way to work around this but it works and the test will pass as you expect. I will look into this more when I get back to the code at work. I will post any new findings or corrections.

 [1]: https://www.google.co.nz/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&ved=0CDUQFjAA&url=http%3A%2F%2Fmsdn.microsoft.com%2Fen-us%2Fdata%2Fef.aspx&ei=8CnMUP7nMa2jiAeC-oHYDA&usg=AFQjCNGnRYM3tghOlk7YE3qY8RyjXpulaQ&sig2=KnR4_5pyp6HCgG6AyjsqNg&bvm=bv.1355325884,d.aGc
 [2]: http://www.asp.net/web-api
 [3]: http://www.fiddler2.com/fiddler2/