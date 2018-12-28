---
title: Build Pattern With Mandatory Calls
date: 2018-12-18 15:02:17
tags:
- design patterns
- builder pattern
- mandatory
- step builder
- smart builder
- chain builder
---

## Builder Pattern

Builder pattern is one of the creational design patterns which is used in almost all code bases. In this post, I would like to share a different approach to this popular pattern which eliminates error prone usages with high readability.

First things first, let's take a look at the [intent of builder pattern](https://en.wikipedia.org/wiki/Builder_pattern)

> The intent of the Builder design pattern is to separate the construction of a complex object from its representation.

And this intention is to [solve problems like:](https://en.wikipedia.org/wiki/Builder_pattern)

> * How can a class (the same construction process) create different representations of a complex object?
> * How can a class that includes creating a complex object be simplified?

In short, we can use builder pattern when we want to create complex objects with ease. The properties of the complex object can also vary depending on usage of the builder class.

## How do we use it?

In our current project, we are also using builder pattern. In many cases, we have to create the same object type in a flexible way with some optional values. Builder classes can achieve these constructions with ease and the final code is highly readable.

## Challenge

To fulfill a recent requirement we needed to build an object with a number of mandatory and optional field values together. Optional fields were not a problem, but the mandatory fields had to be initialized when the resulting object is constructed. This is one of the [weak points of builder pattern.](https://en.wikipedia.org/wiki/Builder_pattern)

> Disadvantages of the Builder pattern include:
> * Data members of class aren't guaranteed to be initialized.

But in our case, anytime the `Build()` method is called from the builder, we should get back an object which has all the mandatory fields initialized. Handling mandatory fields and keeping the code readable can be a difficult task.

General approach to dictate mandatory fields is to require them in the constructor of the builder class. On the other hand, in the case of too many mandatory fields, readability of the code decreases significantly and it becomes more difficult track the mandatory parameters. Achieving this functionality and keeping the builder interface developer-friendly is kind of challenging. This post discusses over an approach to solve this challenge. To elaborate further on the topic, here are the options that are generally used when a mix of mandatory and optional fields are used through builder pattern:

* The recommended way with builder pattern is to add all your mandatory parameters to the constructor of the class and keep optional parameters in separate methods. This option is viable if you do not have too many mandatory parameters. Otherwise it can decrease readability of the code.
* Use a combination of factory class that would default mandatory parameters per use case. This option is viable when you don't have more than 2-3 build cases for the resulting object or some parameters can be defaulted.
* Use several builders and combine the results in the end in object constructor. Each builder will create a part of the final result and they will be combined with a final builder to rule them all. This option can decrease code readability.
* Use builders with mandatory method calls and chain them (chain builders or step builders).

Decision was to use builders with mandatory method calls which is the main idea to write this blog post and it was fun to brainstorm on it. Basically the idea is to help the developer step by step while making sure that mandatory fields are initialized. I have never seen a similar implementation before we actually created it for our project. But before writing this blog post, I did some search on existing blogs and saw that there were some Java implementations for the same concept. They were called chain builders and step builders. You can see them from the references section.

## Show Me The Code

If you kept reading so far, it is great. This section is where the fun begins. I will try to give an example using an entity we use almost everyday. We will create a `Contact` just like the ones on your phone. Let's consider these requirements:

In order to create a contact we will have mandatory fields:

* Name
* Surname
* Phone number

and a few optional fields:

* E-mail address
* Address
* Notes

and when we call the `Build()` anytime, resulting `Contact` object should have the mandatory fields in place.

Here is our `Contact` object:

``` csharp
public class Contact
{
  // Mandatory properties
  public string Name { get; set; }
  public string Surname { get; set; }
  public string PhoneNumber { get; set; }
  // Optional properties
  public string Email { get; set; }
  public string Address { get; set; }
  public string Notes { get; set; }
}
```

### Naive Implementation

Please consider this most basic builder implementation in order to understand why it is better to enforce mandatory fields. Note that for simplicity purposes, I skipped any input validation for now.

``` csharp
public interface IContactBuilder
{
  IContactBuilder WithName(string name);
  IContactBuilder WithSurname(string surname);
  IContactBuilder WithPhone(string phoneNumber);
  IContactBuilder WithEmail(string email);
  IContactBuilder WithAddress(string address);
  IContactBuilder WithNotes(string notes);
  Contact Build();
}

public class ContactBuilder : IContactBuilder
{
  private readonly Contact contact;
  public ContactBuilder()
  {
    contact = new Contact();
  }
  public IContactBuilder WithName(string name)
  {
    contact.Name = name;
    return this;
  }
  public IContactBuilder WithSurname(string surname)
  {
    contact.Surname = surname;
    return this;
  }
  public IContactBuilder WithPhone(string phoneNumber)
  {
    contact.PhoneNumber = phoneNumber;
    return this;
  }
  public IContactBuilder WithEmail(string email)
  {
    contact.Email = email;
    return this;
  }
  public IContactBuilder WithAddress(string address)
  {
    contact.Address = address;
    return this;
  }
  public IContactBuilder WithNotes(string notes)
  {
    contact.Notes = notes;
    return this;
  }
  public Contact Build()
  {
    return this.contact;
  }
}
```

And on another class we consume this builder class to create a new instance of `Contact`.

``` csharp
public class BuilderConsumerClass
{
  public BuilderConsumerClass(IContactBuilder builder)
  {
    var contact = builder
          .Build();
  }
}
```

This is what intellisense shows about our builder class with its current state:

![intellisense-naive-implementation](/images/posts/2018/intellisense-naive.png)

So developers using this builder class can call any method in any order right now. The following call is a possible usage by a developer who does not know the requirements that we knew while implementing the builder class.

``` csharp
var builder = new ContactBuilder();

var contact = builder
                .WithEmail("none@company.com")
                .WithNotes("My notes")
                .Build();
```

It is obvious that resulting `Contact` object violates our requirements. Mandatory fields are not entered yet.

### How Can We Eliminate Error Prone Usages?

Well, normally for an example like this, requiring mandatory fields in the builder constructor is a very good approach. But for the sake of discussion let's imagine that we have too many mandatory fields and using constructor initialization would decrease our code readability.

We would like to enforce our fellow developers to enter the mandatory fields and only after that they can choose to add optional fields or build the contact object. How can we achieve that? Let's make `Name` field mandatory. `Name` field is handled by `WithName()` method in the builder class. In order to make `WithName()` call mandatory, we will extract the method to a new interface and create a chain to `IContactBuilder`.

``` csharp
public interface IContactWithMandatoryNameBuilder
{
  IContactBuilder WithName(string name);
}

public interface IContactBuilder
{
  IContactBuilder WithSurname(string surname);
  IContactBuilder WithPhone(string phoneNumber);
  IContactBuilder WithEmail(string email);
  IContactBuilder WithAddress(string address);
  IContactBuilder WithNotes(string notes);
  Contact Build();
}
```

`IContactWithMandatoryNameBuilder` will now contain the `WithName()` method of `IContactBuilder`. Concrete implementation `ContactBuilder` will now implement both of these interfaces. Here is how it will look after the refactoring.

``` csharp
 public class ContactBuilder : IContactBuilder, IContactWithMandatoryNameBuilder
  {
    private readonly Contact contact;

    public ContactBuilder()
    {
      contact = new Contact();
    }

    public IContactBuilder WithName(string name)
    {
      contact.Name = name;
      return this;
    }

    // ...
    // Rest is the same
```

From now on we will inject `IContactWithMandatoryNameBuilder` to our consumer class

``` csharp
public class BuilderConsumerClass
{
  public BuilderConsumerClass(IContactWithMandatoryNameBuilder builder )
  {
    var contact = builder
          .WithName("Alex")
          .Build();
  }
}
```

The new interface will show only `WithName()` method in the intellisense, nothing else:

![Intellisense before mandatory call](/images/posts/2018/intellisense-mandatory-name-builder.png)

Once the `WithName()` call has been made, our fellow developers will be able to access the `Build()` method. Now we guaranteed that `Name` field will be filled anytime `Build()` method is called.

![Intellisense after mandatory call](/images/posts/2018/intellisense-after-mandatory-name-builder.png)

### Add More Mandatory Calls

Now that we have `Name` field as mandatory on the `Contact` object, it is simple to add more mandatory method calls. We will add more links to the chain and optional parameters will stay in the final builder interface. Let's quickly make the remaining two fields `Surname` and `PhoneNumber` also mandatory.

``` csharp
public interface IContactWithMandatoryNameBuilder
{
  IContactWithMandatorySurnameBuilder WithName(string name);
}

public interface IContactWithMandatorySurnameBuilder
{
  IContactWithMandatoryPhoneBuilder WithSurname(string surname);
}

public interface IContactWithMandatoryPhoneBuilder
{
  IContactBuilder WithPhone(string phoneNumber);
}

public interface IContactBuilder
{
  IContactBuilder WithEmail(string email);
  IContactBuilder WithAddress(string address);
  IContactBuilder WithNotes(string notes);
  Contact Build();
}
```

Please note how the interfaces refer to each other just like links in a chain. In order to access `Build()` method our fellow developers will need to call `WithName()`, `WithSurname()`, `WithPhone()` methods. Another advantage is that the code is now self-describing. It is obvious that these calls have to be made (which means the fields that handled by these methods need to be filled in). Let's take a look to final usage:

![Intellisense after mandatory surname call](/images/posts/2018/intellisense-after-mandatory-surname-builder.png)

![Intellisense after mandatory phone call](/images/posts/2018/intellisense-after-mandatory-phone-builder.png)

As you can see, at this point all the mandatory fields have been handled and it is the only way to access the `Build()` method. Now the developer can choose whether to add optional fields or not.

![Intellisense final call](/images/posts/2018/intellisense-final.png)

### Remarks

If you decide to use this approach, please consider the following points:

* Parameters that are related to each other can be grouped in the interface methods so maybe it is possible to shorten the chain.
* If you can only see a few types of `Contact` being created in the codebase, it might be better to implement factory methods/classes in combination with the naive builder class. With that approach you can hide builder class under the hood and only allow calls to factory methods to get back the `Contact` you need.

## Summary

Now that we know how it is done, mandatory meme will follow.

![Chain them all](/images/posts/2018/chain.jpg)

Creational design patterns are frequently used in almost any code base. Having minor tweaks on them, as described in this post, can be handy when you spot a requirement similar to the example. As my final words in this post, I would like to share the pros and cons of using this approach.

### Pros

* Easy to implement
* Easy to understand
* Eliminates error prone usages
* Code is directly reflecting requirements (Self-describing)
* Code readability is high

### Cons

* Unit testing on the link chain will require more effort
* Double check would be necessary on IoC container registrations

Please let me know what you think in the comments below. I hope you find this post helpful. An example project related to this topic is available in [blog-code-examples.](https://github.com/nerdomancer/blog-code-examples) Special thanks to my team members who helped me brainstorming and implementation on this topic.

Happy coding!

## References

* Builder Pattern
  * [GOF](https://www.dofactory.com/net/builder-design-pattern)
  * [Sourcemaking](https://sourcemaking.com/design_patterns/builder)
* [Smart Builders](https://www.javacodegeeks.com/2013/05/building-smart-builders.html)
* [Step Builder](http://rdafbn.blogspot.com/2012/10/step-builder-pattern-definition-and.html)