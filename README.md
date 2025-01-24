# Blazor for You

## Overview

In this article, I provide a comprehensive summary of my work and resources around Blazor.
This collection is designed to offer insights for both managers and developers interested in Blazor, 
covering foundational concepts as well as practical applications through code examples and open-source projects.

### Articles

1. **Part 1: Blazor for Managers ([EN](ForManagers/readme.md), [DE version](ForManagers/readme-de.md))**: This article helps non-technical stakeholders understand Blazor's benefits, including reduced development time, leveraging .NET skills, and delivering seamless user experiences.

2. **Part 2: Blazor for Developers ([EN](ForDevelopers/readme.md), [DE version](ForDevelopers/readme-de.md))** Focused on the technical aspects, this article dives into Blazor from a developer's perspective.
   It covers best practices, architecture considerations, and explores how Blazor enables full-stack C# development on the web, simplifying the development workflow and enhancing productivity.

3. **Blazor Component Testing with bUnit**  ([EN](bUnit/readme.md),[DE version](bUnit/readme-de.md)): This article introduces bUnit and xUnit for testing Blazor components, covering its purpose, benefits, and limitations when writing unit tests in Blazor.

# How to use
- [Logging](https://github.com/AlexNek/FolderObserver/blob/master/Docs/Logging.md)
  - [Graylog](https://github.com/AlexNek/Serilog.Sinks.GraylogGelf/blob/master/Documentation/graylog.md)
  - [Serilog](https://github.com/AlexNek/Serilog.Sinks.GraylogGelf/blob/master/Documentation/serilog.md)
- [Keycloak](https://github.com/AlexNek/BlazorWasmOidc/blob/master/Docu/keycloak.md)
- [Auth0](https://github.com/AlexNek/BlazorWasmOidc/blob/master/Docu/auth0.md)
- [Azure AD](https://github.com/AlexNek/BlazorWasmOidc/blob/master/Docu/azuread.md)
- [Unit tests](https://github.com/AlexNek/FolderObserver/blob/master/Docs/UnitTests.md)

### In the Works
- [Blazor:Implement Custom Authentication](Authentication.md)
- [Blazor localization steps](Localization.md)
- [GraphQL for Blazor](graphql.md)

### Examples

Here are some practical examples demonstrating Blazor's capabilities:

#### Graphics and Interactivity
- **[BabylonBlazor](https://github.com/AlexNek/BabylonBlazor)**  
  Integrates Babylon.js with Blazor, showcasing 3D graphics in Blazor applications. Ideal for developers adding WebGL-powered 3D visualizations.

- **[Blazor.LogicalGame](https://github.com/AlexNek/Blazor.LogicalGame)**  
  A Blazor-based logical game that demonstrates Blazor’s potential in interactive client-side web games.

- **[Blazor.BullsAndCows](https://github.com/AlexNek/Blazor-BullsAndCows)**
 This project speeds up Blazor WASM's initial load by showing text immediately while the rest loads in the background. We use server-side prerendering for faster first loads and a client-side component to prevent early clicks. Demonstrated with a Bulls and Cows game 

#### Authentication and Security
- **[Blazor WebAssembly Standalone Authentication](https://github.com/AlexNek/BlazorWasmOidc)**  
  A standalone Blazor WebAssembly project implementing OIDC authentication, useful for understanding secure user authentication workflows in Blazor applications.

#### UI Components and Libraries
- **[Blazor.QrCode](https://github.com/AlexNek/Blazor.QrCode)**  
  A library for QR code generation directly within Blazor, highlighting Blazor's flexibility for server- and client-side QR code creation.
  - **[Blazor.SsrButton](https://github.com/AlexNek/Blazor.SsrButton)**  
  A button component for Blazor applications. It supports both server-side rendering (SSR) and client-side rendering, making it ideal for use in various Blazor project types.

- **[BlazorSortableList](https://github.com/AlexNek/BlazorSortableList)**  
  A sortable list component using SortableJS, showing how Blazor can leverage JavaScript libraries to create dynamic UI elements.
- **[TodoApp MAUI](https://github.com/AlexNek/TodoAppMaui)**  
  This app shows how to make one codebase work on many different devices - Windows, Android, iOS, and even in a web browser.


#### Framework Transition Examples
- **[Angular to Blazor Examples](https://github.com/AlexNek/Angular2BlazorExamples)**  
  Provides migration examples from Angular to Blazor, valuable for developers shifting frameworks or integrating Angular with Blazor.

This summary provides an accessible way to explore my Blazor work, helping you dive deeper into articles and code examples that best fit your needs.
