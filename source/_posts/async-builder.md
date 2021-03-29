---
title: Async Builder Pattern
date: 2019-10-03 15:02:17
tags:
- design patterns
- builder pattern
- async
---

In my [previous blog post](https://www.selcuksasoglu.com/2018/12/18/builder-pattern-with-mandatory-calls/), I discussed an approach to solve a weak point of [builder pattern](https://en.wikipedia.org/wiki/Builder_pattern). In this blog post, I want to continue with a related discussion. The challenge that will be discussed over this blog post is "How can we make builder pattern async?" This is an issue we faced when we decided to migrate our current project to async to increase our throughput. We came to a decision moment, we would either delete the builder classes we had (there were many) or find a way to utilize them in async world.

## Challenge

Let's go over the problem briefly. I will reuse the example from [the previous blog post](https://www.selcuksasoglu.com/2018/12/18/builder-pattern-with-mandatory-calls/) and try to create a `Contact` again. You can [see or try this code on repl.it](https://repl.it/@selcuks/builder-pattern-example)

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

In a typical builder pattern implementation you would have something like this:

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

  // Rest is skipped for brevity.

  public Contact Build()
  {
    return this.contact;
  }
}
```

Of course in an implementation like this, you would not need an async operation, so let's spice it up a little bit.

Imagine that you would like to construct your contacts in such a way that name and surname should be queried from Linkedin profile, phone and address from Google Contacts. Let's inject 2 new dependencies to the builder class `ILinkedinRepository` and `IGoogleContactsRepository`. For brevity I will skip implementation of these classes, but let's assume that they will both have an option to read data through username.

``` csharp
public class ContactBuilder : IContactBuilder
{
  private readonly Contact contact;
  private readonly string username;

  public ContactBuilder(
    ILinkedinRepository linkedinRepository,
    IGoogleContactsRepository googleContactsRepository,
    string username)
  {
    this.linkedinRepository = linkedinRepository;
    this.googleContactsRepository = googleContactsRepository;
    this.contact = new Contact();
    this.username = username;
  }

  public IContactBuilder WithName()
  {
    // Network operation that blocks the running thread:
    var name = this.linkedinRepository.GetName(username);
    contact.Name = name;
    return this;
  }

  // Rest is skipped for brevity.

  public Contact Build()
  {
    return this.contact;
  }
}
```

Please note that the concrete implementation always does a network operation and returns `this`.

## Converting the Builder to Async

In async implementation of the same class returning `this` is not a possible option because we will need to return either a `Task` for void functions or `Task<T>` when we want to return a value. Even if we return `Task<IContactBuilder>` we will break the simplicity of builder pattern. Why? Because our builder and consumer would look like this:

Our concrete builder:

``` csharp
public class ContactBuilder : IContactBuilder
{
  private readonly ILinkedinRepository linkedinRepository;
  private readonly IGoogleContactsRepository googleContactsRepository;
  private readonly Contact contact;
  private readonly string username;

  public ContactBuilder(
    ILinkedinRepository linkedinRepository,
    IGoogleContactsRepository googleContactsRepository,
    string username)
  {
    this.linkedinRepository = linkedinRepository;
    this.googleContactsRepository = googleContactsRepository;
    this.contact = new Contact();
    this.username = username;
  }

  public async Task<IContactBuilder> WithName()
  {
    // Network operation that blocks the running thread:
    var name = this.linkedinRepository.GetName(username);
    contact.Name = fullName;
    return this;
  }

  // Rest is skipped for brevity.

  public async Task<Contact> Build()
  {
    return this.contact;
  }
}
```

Our consumer class:

``` csharp
  public class BuilderConsumerClass
{
  public BuilderConsumerClass(IContactBuilder builder)
  {
    // Declarations here...

    var withNameBuilder = await builder.WithName();
    var contact = await withNameBuilder.Build();
  }
}
```

You can see where I am trying to go with this. While trying to make the builder pattern async, we are actually sacrificing the advantages of using it.

## Proposed Solution(s)

During my initial research for a solution, [this StackOverflow question](https://stackoverflow.com/questions/25302178/using-async-tasks-with-the-builder-pattern) was the only thing that I could find. I started digging up with this starting point to see my options. In the next section you can see the viable options I could come up with.

### 1- Builder with Parallel Executing Tasks

In the most simple solution, we can use a `Queue` to start and keep track of new threads per methods within the builder class. Each method action will be a new `Task` and we can still return `this` from builder methods. With this approach running thread will never be blocked and only `Build()` method will be async.

[Try here.](https://repl.it/@selcuks/builder-parallel-running-actions)

```csharp
public class ContactBuilder : IContactBuilder
{
  private readonly ILinkedinRepository linkedinRepository;
  private readonly IGoogleContactsRepository googleContactsRepository;
  private readonly Contact contact;
  private readonly string username;
  private Queue<Task> builderQueue;

  public ContactBuilder(
    ILinkedinRepository linkedinRepository,
    IGoogleContactsRepository googleContactsRepository,
    string username)
  {
    this.linkedinRepository = linkedinRepository;
    this.googleContactsRepository = googleContactsRepository;
    this.contact = new Contact();
    this.username = username;
    this.builderQueue = new Queue<Task>();
  }

  public IContactBuilder WithName()
  {
    builderQueue.Enqueue(Task.Run(() => 
    {
      var name = this.linkedinRepository.GetName(this.username);
      contact.Name = name;
    }));

    return this;
  }

  // Other methods are skipped for brevity

  public async Task<Contact> Build()
  {
    Task currentTask = null;
    Console.WriteLine(">>> Building contact...");

    while (builderQueue.TryDequeue(out currentTask))
    {
      await currentTask.ConfigureAwait(false);
    }

    Console.WriteLine(">>> Done...");
    return this.contact;
  }
}
```

Please note that with every builder method a new `Task` is being created to handle the operation and running thread just continues execution. Each created `Task` is running immediately. In the build method, we are awaiting the created tasks.

#### Pros

- All tasks run in parallel
- Main thread is not blocked
- Faster

#### Cons

- Execution order of tasks cannot be known before hand
- If one task fails, other scheduled tasks will still continue to work

### 2- Builder with ordered `Func<Task>`

Another variation of this solution can also be necessary. Sometimes when you are building an object, you need to make sure that fields are being assigned in order. Then this variation can be used to do it.

[Try here.](https://repl.it/@selcuks/builder-ordered-func-of-task)

```csharp
public class ContactBuilder : IContactBuilder
{
  private readonly ILinkedinRepository linkedinRepository;
  private readonly IGoogleContactsRepository googleContactsRepository;
  private readonly Contact contact;
  private readonly string username;
  private Queue<Func<Task>> builderQueue;

  public ContactBuilder(
    ILinkedinRepository linkedinRepository,
    IGoogleContactsRepository googleContactsRepository,
    string username)
  {
    this.linkedinRepository = linkedinRepository;
    this.googleContactsRepository = googleContactsRepository;
    this.contact = new Contact();
    this.username = username;
    this.builderQueue = new Queue<Func<Task>>();
  }

  public IContactBuilder WithName()
  {
    builderQueue.Enqueue(() => Task.Run(() => 
    {
      var name = this.linkedinRepository.GetName(this.username);
      contact.Name = name;
    }));

    return this;
  }

  // Other methods are skipped for brevity

  public async Task<Contact> Build()
  {
    Func<Task> startTaskExecution = null;
    Console.WriteLine(">>> Building contact...");
    while (builderQueue.TryDequeue(out startTaskExecution))
    {
      var currentTask = startTaskExecution.Invoke();
      await currentTask.ConfigureAwait(false);
    }
    Console.WriteLine(">>> Done...");
    return this.contact;
  }
}
```

Please note that the builder queue is now of type `Func<Task>`. So when a new task is being created, it is not immediately running. We still need to invoke this `Func` to start operation. With this approach, we make sure that builder methods are run in the order they were created.

#### Pros

- Order of execution is under control
- Main thread is not blocked
- If one task fails, remaining tasks won't execute.

#### Cons

- Slower. Tasks run in a waterfall fashion.

### 3- Builder with cancellable Tasks

[Try here.](https://repl.it/@selcuks/builder-with-cancellable-tasks)

```csharp
public class ContactBuilder : IContactBuilder
{
  private readonly ILinkedinRepository linkedinRepository;
  private readonly IGoogleContactsRepository googleContactsRepository;
  private readonly Contact contact;
  private readonly string username;
  private CancellationToken cancellationToken;
  private CancellationTokenSource cancellationTokenSource;
  private Queue<Task> builderQueue;

  public ContactBuilder(
    ILinkedinRepository linkedinRepository,
    IGoogleContactsRepository googleContactsRepository,
    string username)
  {
    this.linkedinRepository = linkedinRepository;
    this.googleContactsRepository = googleContactsRepository;
    this.contact = new Contact();
    this.username = username;
    this.cancellationTokenSource = new CancellationTokenSource();
    this.cancellationToken = cancellationTokenSource.Token;
    this.builderQueue = new Queue<Task>();
  }

  public IContactBuilder WithName()
  {
    builderQueue.Enqueue(Task.Run(() =>
    {
      var name = this.linkedinRepository.GetName(this.username);

      if (cancellationToken.IsCancellationRequested)
      {
        cancellationToken.ThrowIfCancellationRequested();
      }

      contact.Name = name;
    }));

    return this;
  }

  // Other methods are skipped for brevity

  public async Task<Contact> Build()
  {
    Task currentTask = null;
    Console.WriteLine(">>> Building contact...");

    while (builderQueue.TryDequeue(out currentTask))
    {
      await currentTask.ConfigureAwait(false);
    }

    Console.WriteLine(">>> Done...");
    return this.contact;
  }
}
```

Please note the builder queue is here again of type `Task`. For each method in the builder class a new `Task` is created and it is immediately running. But we also added cancellation checks on the `CancellationToken` so if any of the operations has a failure, we can stop all other tasks.

#### Pros

- Best of the both worlds from previous examples
- Main thread is not blocked
- Fast, threads run in parallel
- Threads can be cancelled. If one thread gets an exception, other threads can be stopped through cancellation

#### Cons

- More complex. You have to think carefully for cancellation checks and insert where necessary
- Execution order of tasks cannot be known before hand

## Conclusion

As discussed in the previous sections, keeping builder pattern in an async codebase is difficult. If you are a developer (like me) who thinks builder pattern has a lot of advantages and would like to keep it in your async codebase, then you can adapt one of the solutions discussed here. In all of the solutions, exception handling needs careful decisions because you will be handling a lot of threads and correct decisions need to be made in case one of these threads throws an exception.

Don't forget that you can also combine one of these solutions with Chain Builders which was mentioned in [my previous blog post.](https://www.selcuksasoglu.com/2018/12/18/builder-pattern-with-mandatory-calls/)

Please let me know what you think in the comments below. I hope you find this post helpful. Example code related to this topic is available in [blog-code-examples](https://github.com/ssasoglu/blog-code-examples) or in the repl.it links that I have provided in previous sections.

Happy coding!