# Blazor Component Testing with bUnit
## Overview

Testing Blazor components doesn’t have to be complicated. With **bUnit**, you can validate component logic, rendering, and behavior without dealing with a full browser setup. This guide walks through the essentials of using bUnit to test components efficiently and effectively.  

We'll cover how to set up **bUnit**, why **xUnit** is a solid choice for running tests, and how **FluentAssertions** can make assertions more readable. You’ll also learn how to mock dependencies, handle JavaScript interop, and manage asynchronous operations—all without leaving the comfort of a simulated testing environment.  

By the end, you’ll have a clear understanding of how to write reliable tests, follow best practices, and know when to reach for tools like Selenium for more comprehensive end-to-end testing.  


# Why We Chose These Tools

## xUnit

When it comes to testing Blazor components, xUnit is our go-to framework. It's modern, straightforward, and packed with useful features that make testing a breeze. Unlike older frameworks that can feel clunky, xUnit is designed with simplicity and flexibility in mind.

So why xUnit? It keeps things clean and organized, supports async tests right out of the box, and runs tests in parallel to speed things up. Plus, it works seamlessly with FluentAssertions, making your test assertions clearer and more readable.

Other options like NUnit and MSTest? Sure, they’ve been around for a while, but they come with their own quirks. NUnit can struggle with managing test instances efficiently, and MSTest just doesn’t offer the same level of flexibility and ease of use. In the end, xUnit is simply a better fit for modern Blazor component testing.

For a more detailed breakdown of why we chose xUnit over other options, see comprehensive [xUnit vs. nUnit comparison](x-nUnit-Comparison.md).


## bUnit

Blazor components need a testing tool that speaks their language, and that's where bUnit shines. It creates a simulated browser-like environment, letting us test how our components behave without actually firing up a real browser.

With bUnit, we can check component logic, rendering, and behavior quickly and efficiently. It’s great for running tests in isolation without external dependencies slowing things down. Plus, mocking JavaScript interop calls with `IJSRuntime` is a piece of cake.

But bUnit isn’t perfect. It doesn’t fully support CSS, so visual styling tests might not always behave as expected. Also, since it’s not a real browser, JavaScript execution is limited, which can sometimes be a challenge.

> **Note:** [Learn more about bUnit limitations in detailed description.](bUnit-limitations.md)  

How does bUnit compare to other tools? Well, if you need fast, focused unit tests, bUnit is your best bet. If you’re looking to test the entire application flow, tools like Selenium, Playwright, or Cypress might be better suited—but they come with more overhead and slower execution.

In short, bUnit is perfect for testing individual components in isolation, while Selenium and similar tools are better for full end-to-end testing when you need a complete browser experience.

# Core bUnit Concepts and Setup

## Setting Up the Test Context

Before writing tests, the first step is to initialize a `TestContext`. Think of it as your testing hub—it sets up everything you need to render, find, and interact with Blazor components. Here's how to get started:

```csharp
using Bunit;
using Xunit;

public class MyComponentTest
{
    [Fact]
    public void MyTest()
    {
       using var ctx = new TestContext();
        // Test will be written here
    }
}
```
`TestContext` provides access to various services required to test components without needing any external dependencies.


## Component Rendering Approaches

Once you've got your `TestContext` set up, you can render components in different ways depending on what you need to test.

- **Basic Component Rendering:**  `RenderComponent<T>()`: This is a straightforward way to render a component of type `T` . For instance:

    ```csharp
    [Fact]
    public void MyComponent_Render()
    {
        // Arrange
        using var ctx = new TestContext();

        // Act
        var cut = ctx.RenderComponent<MyComponent>();

        // Assert
        Assert.NotNull(cut);
    }
    ```
    This method returns `IRenderedComponent<T>`, which lets you interact with the component in your tests.

- **Rendering with Parameters:**  `RenderComponent(ComponentParameter[] parameters)`: This is handy if you need to pass parameters to the component during rendering. Consider you have `MyComponent` which accepts a parameter `Title`:

    ```csharp
    [Fact]
    public void MyComponent_RenderWithTitle()
    {
        // Arrange
        using var ctx = new TestContext();

        // Act
        var cut = ctx.RenderComponent<MyComponent>(
            ComponentParameter.CreateParameter(nameof(MyComponent.Title), "Hello bUnit!"));

        // Assert
        Assert.NotNull(cut);
        Assert.Equal("Hello bUnit!", cut.Instance.Title);
    }
    ```
    `ComponentParameter` lets you supply properties in a type-safe manner.

-   **Rendering with Markup:** Sometimes you'll need to render components within some additional markup. For this, bUnit provides the `Render` method. It lets you pass arbitrary Razor markup.

    ```csharp
    private RenderFragment ConvertToFragment(string html) => builder =>
    {
        builder.AddMarkupContent(0, html);
    };
    
    [Fact]
    public void MyComponent_RenderInMarkup()
    {
    // Arrange
    using var ctx = new TestContext();

    // Act
    var htmlString = """
        <p>
            <MyComponent Title="Hello" />
        </p>
    """;

    var cut = ctx.Render(ConvertToFragment(htmlString));

    // Assert
    var component = cut.FindComponent<MyComponent>();
    Assert.Equal("Hello", component.Instance.Title);
    }
    ```

## Verifying Lifecycle Events

Blazor components go through a lifecycle. bUnit lets you verify lifecycle events are firing correctly using hooks like `OnInitialized`, `OnParametersSet`, and others. You can do this by setting up a fake component. Then you need to inherit your components from it.

```csharp
public class LifecycleComponent : ComponentBase
{
    public bool OnInitializedCalled { get; set; }
    public bool OnParametersSetCalled { get; set; }
    public int ParametersSetCount { get; set; }

    protected override void OnInitialized()
    {
        OnInitializedCalled = true;
    }

    protected override void OnParametersSet()
    {
         ParametersSetCount++;
        OnParametersSetCalled = true;
    }
    [Parameter]
    public int Value {get;set;}
}
```
```csharp
[Fact]
public void LifecycleComponent_LifecycleEvents()
{
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<LifecycleComponent>();

    Assert.True(cut.Instance.OnInitializedCalled);
    Assert.True(cut.Instance.OnParametersSetCalled);
    Assert.Equal(1, cut.Instance.ParametersSetCount);

    cut.SetParametersAndRender(ComponentParameter.CreateParameter(nameof(LifecycleComponent.Value), 2));
    
    Assert.Equal(2, cut.Instance.ParametersSetCount);

}
```

## Managing Dependencies

Often your components depend on external services. bUnit lets you register these services with the `Services` property of your `TestContext`. This is like dependency injection.
```csharp
public interface IMyService {
    string GetData();
}
public class MyService : IMyService {
    public string GetData() {
        return "Hello from the Service!";
    }
}
public class MyComponentWithService: ComponentBase{
    [Inject]
    public IMyService MyService { get; set; }

    public string ServiceData { get; set; }

    protected override void OnInitialized()
    {
        ServiceData = MyService.GetData();
    }
}
```
```csharp
[Fact]
public void MyComponentWithService_Render_ServiceDataIsSet() {
    using var ctx = new TestContext();
    ctx.Services.AddSingleton<IMyService>(new MyService());
    var cut = ctx.RenderComponent<MyComponentWithService>();

    Assert.Equal("Hello from the Service!", cut.Instance.ServiceData);
}
```

That sounds like a great idea! Extracting the bUnit limitations section into a separate file helps keep the main article concise and makes it easier to update or expand the limitations in the future. It also improves readability for your audience by providing a focused, high-level overview while offering detailed insights for those who need them.  


# Advanced Testing Scenarios using bUnit + FluentAssertions

Now that you're familiar with the core concepts, let's explore more advanced testing techniques using `FluentAssertions` to make assertions clearer and easier to understand.
> **Note**: Starting from version 8, [FluentAssertion](https://xceed.com/products/unit-testing/fluent-assertions) is no longer free of charge for everyone. Use version 7.x instead.

## Testing Component State Changes

Blazor components often have state that updates when users interact with them. `FluentAssertions` provides an intuitive way to verify these state changes.

```csharp
using FluentAssertions;
public class CounterComponent : ComponentBase
{
    public int CurrentCount { get; private set; }

    public void IncrementCount()
    {
        CurrentCount++;
    }
}
```
```csharp
[Fact]
public void CounterComponent_IncrementCount()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<CounterComponent>();

    // Act
    cut.Instance.IncrementCount();

    // Assert
    cut.Instance.CurrentCount.Should().Be(1);
}
```

The `Should()` method from `FluentAssertions` makes tests easier to read and provides a natural way to write assertions.

## Verifying Event Handlers and User Interactions

Blazor components often respond to user interactions through event handlers. With bUnit, you can simulate those interactions and check their effects.

```csharp
public class ButtonComponent : ComponentBase
{
    [Parameter]
    public EventCallback OnButtonClicked { get; set; }

    public async Task HandleClick()
    {
        await OnButtonClicked.InvokeAsync();
    }
}

[Fact]
public async Task ButtonComponent_ClickEvent()
{
    // Arrange
    using var ctx = new TestContext();
    bool eventTriggered = false;
    var cut = ctx.RenderComponent<ButtonComponent>(
        ComponentParameter.CreateParameter(
            nameof(ButtonComponent.OnButtonClicked),
            EventCallback.Factory.Create(this, () => eventTriggered = true)));

    // Act
    await cut.Instance.HandleClick();

    // Assert
    eventTriggered.Should().BeTrue();
}
```

## Mocking Dependencies and External Services

Blazor components frequently depend on external services, and bUnit, combined with Moq, allows you to create reliable test environments by simulating those dependencies.

```csharp
public interface IDataService {
    Task<string> GetDataAsync();
}
public class DataComponent : ComponentBase
{
    [Inject]
    public IDataService DataService { get; set; }

    public string Data { get; set; }

    protected override async Task OnInitializedAsync()
    {
        Data = await DataService.GetDataAsync();
    }
}
```

```csharp
using Moq;
...
[Fact]
public async Task DataComponent_DataIsLoadedFromService()
{
    // Arrange
    using var ctx = new TestContext();
    var mockService = new Mock<IDataService>();
    mockService.Setup(s => s.GetDataAsync()).ReturnsAsync("Mocked Data");
    ctx.Services.AddSingleton<IDataService>(mockService.Object);
    var cut = ctx.RenderComponent<DataComponent>();

    // Assert
    cut.Instance.Data.Should().Be("Mocked Data");
}
```

## Testing Component Parameters and Cascading Values

Blazor components often receive parameters and cascading values from parent components. bUnit makes it easy to pass and verify these values.

```csharp
public class ParameterComponent : ComponentBase
{
    [Parameter]
    public string Message { get; set; }
}
```
```csharp
[Fact]
public void ParameterComponent_MessageIsSet()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<ParameterComponent>(
        ComponentParameter.CreateParameter(nameof(ParameterComponent.Message), "Hello Parameters"));

    // Assert
    cut.Instance.Message.Should().Be("Hello Parameters");
}
```

For cascading values:

```csharp
@* ParentComponent.razor *@
@using Microsoft.AspNetCore.Components

<CascadingValue Value="@SomeValue">
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public int SomeValue { get; set; } = 123;
}
```
```csharp
// CascadingValueComponent.razor.cs
using Microsoft.AspNetCore.Components;

public class CascadingValueComponent : ComponentBase
{
    [CascadingParameter]
    public int SomeValue { get; set; }
}
```

```csharp
// Test
using Bunit;
using FluentAssertions;
using Xunit;

public class CascadingValueComponentTests
{
    [Fact]
    public void ParentComponent_CascadingValue_ValueIsCascaded()
    {
        // Arrange
        using var ctx = new TestContext();
        var cut = ctx.RenderComponent<ParentComponent>(parameters =>
        {
            parameters.Add(p => p.ChildContent, childComponent =>
            {
                childComponent.AddChildContent<CascadingValueComponent>();
            });
        });
        //Act
        var component = cut.FindComponent<CascadingValueComponent>();
        // Assert
        component.Instance.SomeValue.Should().Be(123);
    }
}
```

## Handling JavaScript Interop

If your Blazor components interact with JavaScript, you can mock `IJSRuntime` calls using bUnit.

```csharp
using Microsoft.JSInterop;
public class JsComponent : ComponentBase
{
    [Inject]
    public IJSRuntime Js { get; set; }

    public string Result { get; set; }

    public async Task GetJsValue()
    {
        Result = await Js.InvokeAsync<string>("someJsFunction");
    }
}
```

```csharp
[Fact]
public async Task JsComponent_InvokeJs()
{
    // Arrange
    using var ctx = new TestContext();
    var mockJsRuntime = new Mock<IJSRuntime>();
    mockJsRuntime.Setup(x => x.InvokeAsync<string>("someJsFunction", It.IsAny<object[]>()))
      .ReturnsAsync("Hello JS");
    ctx.Services.AddSingleton<IJSRuntime>(mockJsRuntime.Object);
    var cut = ctx.RenderComponent<JsComponent>();

    // Act
    await cut.Instance.GetJsValue();

    // Assert
    cut.Instance.Result.Should().Be("Hello JS");
}
```

By mocking `IJSRuntime`, you can isolate your component logic without requiring actual JavaScript execution.
More bUnit examples can be found [here](https://github.com/AlexNek/TodoAppMaui)

# Best Practices and Patterns  

Now that we've covered the fundamentals, let's dive into some best practices to help keep your test code well-structured, reliable, and efficient.  

## Test Organization and Structure  

A well-structured test suite is just as crucial as clean application code. Follow these guidelines to maintain clarity and efficiency:  

- **One test class per component:** Keep tests for each component in a dedicated test file to ensure related tests are grouped logically and easy to find.  
- **Descriptive naming:** Adopt a consistent naming convention such as `[Scenario]_[ExpectedOutcome]` (e.g., `IncrementCount_IncrementsByOne`). If tests span multiple components or need extra context, including the component name like `[Component]_[Scenario]_[ExpectedOutcome]` can enhance clarity, especially in test reports and logs.  
- **Follow the Arrange-Act-Assert (AAA) pattern:** Structure your tests using the AAA approach to improve readability and logical separation of concerns.  
- **Utilize helper methods:** Extract common setup logic into helper functions to reduce code duplication and make tests easier to maintain as the system evolves.  
- **Centralize component creation:** Avoid creating the component under test inline within each test. Instead, use a dedicated factory or helper function to handle instantiation. This approach makes it easier to accommodate changes in component dependencies and configurations across all tests.  

## Common Mistakes and How to Avoid Them  

To make sure your tests stay strong and easy to maintain, watch out for these common mistakes:  

- **Testing too much:** Focus on testing what the component should do, not how the framework works or how things are built inside.  
- **Fragile tests:** Don't check the internal details of the component. Instead, test what the component does by using its public methods and properties.  
- **Forgetting special cases:** Make sure to test unusual situations, errors, and limits to avoid unexpected problems later.  

By avoiding these mistakes, your tests will be more reliable and easier to update in the future.
## Performance Considerations

bUnit tests are typically fast, but it's important to optimize for efficiency:

- **Avoid excessive rendering:** Render only the components and elements required for each test to reduce overhead.
- **Lazy loading:** Consider loading components on demand for complex tests to avoid unnecessary processing.
- **Minimize setup time:** Keep test initialization concise and avoid unnecessary dependencies.

## Integration with CI/CD Pipelines

Automating your tests as part of the development pipeline ensures continuous validation of your components:

- **Test runner integration:** Use popular test runners such as `dotnet test` to execute your xUnit tests.
- **CI/CD compatibility:** Most CI/CD tools (e.g., GitHub Actions, Azure DevOps, Jenkins) can detect and run test projects automatically.
- **Pipeline step:** Add a dedicated step in your CI/CD pipeline to execute the tests, ensuring they run consistently with every build.

```yaml
# Example GitHub Actions workflow
name: .NET CI

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup .NET
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: 6.x
      - name: Run tests
        run: dotnet test
```

# Wrapping Up: Bringing It All Together

We've explored how to test Blazor components using bUnit, along with helpful tools like `FluentAssertions`. Testing is not a one-time task but an ongoing effort that grows with your application.  

Now, you know how to structure tests, mock dependencies, and handle different scenarios, from basic component rendering to complex interactions.  

## Key Takeaways  

- **Stay Organized:** Use clear naming, follow the AAA pattern, and keep tests focused.  
- **Test Smart:** Focus on behavior rather than implementation details.  
- **Automate:** Add your tests to CI/CD pipelines to catch issues early.  
- **Keep Improving:** Regularly review and refine your testing approach.  

By applying these principles, you'll ensure your Blazor apps remain reliable and maintainable.  

## What's Next?  

Now it’s time to put your knowledge into action. Keep writing tests, catch issues early, and build better Blazor applications.  

Happy testing!  
