---
title: "Selecting The Correct Constructor When Testing With AutoFixture"
date: 2022-03-09 17:22:46
tags:
- autofixture
- automated-testing
- testing
- unit-tests
---

The new version of AutoFixture has attribute `[Greedy]` to select the constructor with that has the most arguments while creating an instance of the related type. There are also other possibilities like `[Modest]`. See them for `XUnit` from [the repository.](https://github.com/AutoFixture/AutoFixture/tree/master/Src/AutoFixture.xUnit2)

```csharp
public class Example
{
  private readonly IFoo _foo;
  private readonly IBar _bar;

  public Example()
  {}

  public Example(IFoo foo, IBar bar)
  {
    _foo = foo;
    _bar = bar;
  }
}

public class ExampleTest
{
  [Theory]
  [AutoMoqData] // Using AutoFixture.AutoMoqCustomization
  public void MyTest([Greedy] Example sut)
  {
    // sut will have IFoo and IBar auto injected at this point
  }
}
```
