## Factors to Consider When Choosing a Mode

In this section, we will go into the details of key factors that should guide your decision when choosing between SSR and Interactive modes:

1. Core Rendering and Interaction Features
2. State and Navigation
3. JavaScript and External Interop
4. User Experience Features
5. Advanced Developer Features
6. Scalability and Performance Considerations
7. Security Considerations
8. Deployment and Hosting
9. Development and Debugging
10. Transitioning Between Modes
11. Advanced Developer Features
12. Additional Considerations

---

### 1. Core Rendering and Interaction Features
As the foundation of rendering, these features set the stage for understanding the technical capabilities and limitations of each mode.

| Factor                  | SSR (Static Server Render) | Interactive Server | Interactive Client  |
|--------------------------|----------------------------|--------------------|---------------------|
| **Initial Load Performance** | Faster initial load: Prerendered HTML is sent immediately. | Moderate: Initial load includes some interactivity setup. | Slower: Requires loading and initializing client-side resources. |
| **User Interaction** | Limited: Static content only until browser-side rendering begins. | Partial: Server-driven interactivity with potential latency. | Full: Immediate client-side interactivity once client-side resources loaded. |
| **Dynamic Content Updates** | Not supported: Requires full page refresh. | Supported via server roundtrips. | Fully supported: Real-time updates without page refresh. |
| **Event Handling** | Not supported on initial render. | Handled on server with network latency. | Handled immediately on client. |
| **OnAfterRenderAsync** | Not triggered: Only invoked after interactive rendering. | Triggered on server with potential network latency to client | Fully supported: Immediate execution in browser without latency. |


---

### 2. State and Navigation

State management and navigation are fundamental aspects of building interactive web applications, closely following rendering.

| Factor                  | SSR (Static Server Render) | Interactive Server | Interactive Client  |
|--------------------------|----------------------------|--------------------|---------------------|
| **State Management** | Limited: Relies on server-side solutions like session storage. | Supported: State is managed on the server, often using SignalR for real-time updates. | Fully supported: Rich client-side state management options using frameworks like Redux or MobX. |
| **State Persistence** | Not supported: State must be reloaded on each request. | Supported: State persists during the session on the server. | Fully supported: Client-side state persistence across interactions, even offline. |
| **Navigation Manager** | Limited: Can only navigate via server-side redirection. | Supported: Allows client-side navigation with SPA-like functionality using SignalR. | Fully supported: Supports client-side navigation with full SPA functionality. |

**Note:**

1. **State Management:**
   - SSR relies on server-side mechanisms such as sessions, which can be limiting for dynamic applications.
   - Interactive Server manages state on the server, providing real-time updates but requiring a constant connection.
   - Interactive Client offers robust client-side state management with various libraries and tools.

2. **State Persistence:**
   - SSR requires reloading state with each request due to its stateless nature.
   - Interactive Server maintains state during user sessions, which can enhance user experience.
   - Interactive Client can persist state across sessions and even support offline scenarios.

3. **Navigation Manager:**
   - SSR typically handles navigation through full page reloads.
   - Interactive Server can provide smoother navigation experiences similar to Single Page Applications (SPAs) using technologies like SignalR.
   - Interactive Client fully supports SPA navigation, offering seamless transitions without full page reloads.

---

### 3. JavaScript and External Interop
Interop capabilities are critical for integrating with external libraries and adding advanced client-side functionality.

| Factor                  | SSR (Static Server Render) | Interactive |
|------------------------------|--------------------------------|-----------------|
| **JavaScript without Return Value** | Partially supported: Enqueue calls for later execution on the client.     | Fully supported: Immediate execution of JavaScript functions. |
| **JavaScript with Return Value** | Not supported: Cannot execute JavaScript requiring return values.         | Fully supported: Allows calling JavaScript functions and retrieving return values. |
| **C# Callback from JavaScript** | Not supported: Cannot invoke C# methods from JavaScript in this mode.      | Fully supported: JavaScript can call C# methods directly with parameters. |

---

### 4. User Experience Features
These directly impact how the application feels to users, making them a natural follow-up to interactivity and navigation.

| Factor                  | SSR (Static Server Render) | Interactive |
|------------------------------|--------------------------------|-----------------|
| **Form Validation**          | Server-side only: Requires round trips for validation logic.         | Fully supported: Real-time client-side validation supported. |
| **SEO and Metadata Rendering** | Fully supported: Content is fully server-rendered and crawlable.   | Partially supported: Requires additional effort for crawlers to process dynamic content. |
| **Offline Support**          | Not supported: Fully server-dependent.                               | Supported: Possible with Blazor WebAssembly.                |

---

### 5. Advanced Developer Features
Ensuring the application is usable for everyone is a logical progression after covering core user experience aspects.

| Factor                  | SSR (Static Server Render) | Interactive |
|------------------------------|--------------------------------|-----------------|
| **HttpContext Visibility**   | Available during server-side execution.                                  | Not directly accessible after transitioning to the client.   |
| **RenderFragment**           | Supported: RenderFragment can be used but limited to static rendering.    | Fully supported for app and pages render mode: Enables dynamic, reusable rendering logic.  Not supported for component render mode declation|
| **Error Handling**           | Server-side errors can be caught and handled during rendering. Fallback content or error pages can be served | Client-side runtime errors must be managed in the UI, typically using error boundaries or try-catch blocks. Server errors during API calls need separate handling        |


---

### 6. Scalability and Performance Considerations

Performance and scalability are important for both user experience and system design. They connect user-focused aspects with technical and operational concerns.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive WebAssembly |
|---|---|---|---|
| **Server Load** | **Higher**: Every user interaction involves a server request. | **Moderate**: Server handles UI updates and state management, but less frequent than SSR | **Lower**: Client-side rendering reduces server load. |
| **Bandwidth Usage** | **Minimal**: Sends only the necessary HTML and assets for the initial load. | **Moderate to High**: Continuous SignalR connection for real-time updates | **Higher**: Requires downloading the .NET runtime and app bundle. |
| **Concurrent Users** | Limited by server capacity, as each user session requires resources. | Scales well with additional servers, but still relies on server resources. | Scales well, as client-side processing reduces server load. |

**Note:** While Interactive Server offers better scalability than SSR, it still relies on server resources for UI updates and state management. Interactive WebAssembly, on the other hand, can scale better by offloading processing to the client-side.

---
### 7. Security Considerations

Security is vital for safeguarding applications, data, and users, making it essential to address before deployment.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive Client |
|---|---|---|
| **Sensitive Data Handling** | **Safer**: Data is processed and stored on the server, reducing exposure. | **Safer:** Sensitive data is primarily handled on the server, minimizing client exposure. | **Riskier:** More data and logic are exposed to the client-side, increasing potential vulnerabilities |
| **Authentication** | Fully supported with server-side sessions or cookies, ensuring secure management | Fully supported with server-side sessions or token-based authentication, providing robust security | Fully supported, often using token-based authentication, but requires careful handling to prevent token exposure |
| **Cross-Site Scripting (XSS)** | **Lower risk:** No client-side execution during the initial load, reducing XSS vectors | **Lower risk:** Server-side rendering minimizes direct client exposure to XSS | **Higher risk:** Requires careful sanitization of client-side inputs and output encoding to prevent XSS attacks |

**Note:**
- SSR and Interactive Server process data on the server. This helps keep sensitive information safe and prevents it from being seen by clients.
- Interactive Client applications must implement robust security practices to mitigate risks associated with client-side data exposure and manipulation.
- Authentication mechanisms are generally robust across all models but require careful implementation to ensure tokens or session identifiers are not exposed.

---

### 8. Deployment and Hosting
With security in mind, deployment and hosting become the next logical step, covering how to operate the application effectively.

| Factor                  | SSR (Static Server Render) | Interactive Server | Interactive Client  |
|--------------------------|----------------------------|--------------------|---------------------|
| **Hosting Requirements**     | Requires a capable server to handle SSR requests (e.g., ASP.NET Core) | Requires a server to manage real-time interactions (e.g., SignalR) | Can run entirely on static hosting platforms |
| **Edge or CDN Support**      | Limited: Requires origin server for dynamic rendering. Modern CDNs can cache some dynamic content but are still reliant on server processing | Limited: Real-time updates depend on server connections; modern CDNs can help accelerate delivery | Fully supported: Static resources can be cached globally, while dynamic content is processed client-side |
| [**Progressive Enhancement**](prog-enh.md)  | Fully supported: Works even in environments [without JavaScript](prog-eng-js) | Partially supported: Requires JavaScript for real-time interactions. | Requires JavaScript for functionality beyond initial load. |

**Note:**

- **Hosting Requirements:** SSR and Interactive Server both require server resources, but Interactive Client (WebAssembly) can be hosted on static platforms, reducing infrastructure needs.
- **Edge or CDN Support:** Interactive Client benefits from global caching of static assets, while SSR and Interactive Server have limitations due to their reliance on server-side processing, although modern CDNs can optimize dynamic content delivery.
- **Progressive Enhancement:** SSR supports environments without JavaScript, whereas both Interactive Server and Client rely on JavaScript for full functionality.

---

### 9. Development and Debugging

Once hosting is addressed, focus shifts to how developers work with these modes during the development cycle.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive Client |
|---|---|---|---|
| **Development Complexity** | Lower: Simple, traditional server-side rendering model. | Moderate: Involves both server-side logic and real-time browser interactions, but often leverages existing server-side frameworks. | Higher: Involves both client-side and server-side logic, often requiring more complex tooling. |
| **Debugging Tools** | Standard server-side debugging tools. | Combination of server-side and browser debugging tools. | Primarily browser-based debugging tools. |
| **Hot Reload Support** | Fully supported in server-side development. | Supported, but changes must be synced to the client. | Supported, but changes must be synced to the client. |

---

### 10. Transitioning Between Modes
After understanding development and hosting, this section explains how to handle transitions and hybrid scenarios.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive Client | Interactive Auto |
|---------|----------------------------|--------------------|--------------------|-------------------|
| **State Preservation** | **Manual**: Requires passing state between requests. | **Automatic**: State persists on the server during the session. | **Automatic**: State persists in the client's browser. | **Adaptive**: Preserves state based on the chosen render mode. |
| **Dynamic Mode Switching** | **Supported**: Can transition to either Interactive mode. Switching to Interactive Client typically requires a full reload. | **Supported**: Can transition from SSR with minimal disruption due to persistent connection. | **Supported**: Can transition from SSR, but typically requires a full reload and additional loading of WebAssembly runtime. | **Dynamic**: Chooses render mode based on current context, can switch between Server and Client modes. |
| **Resource Reuse** | Server resources are reused across requests, but not shared with client. | Server resources are reused across requests and SignalR connections. | Client resources persist locally, server resources accessed via API calls. | Adapts resource usage based on chosen render mode. |

**Note:**

1. **State Preservation**:
   - For **Interactive Server**, state is maintained on the server during the session.
   - For **Interactive Client**, state is maintained in the browser.
   - For **Interactive Auto**, state preservation adapts based on the chosen render mode for each component.

2. **Dynamic Mode Switching**:
   - Switching between modes is supported, with specific considerations for each transition.
   - **Interactive Auto** mode dynamically selects the render mode (Server or Client) based on the current context and existing interactivity on the page.
   - Auto mode decisions are made per component/page load, not fixed for the entire session.

3. **Resource Reuse**:
   - In SSR and Interactive Server, server-side resources can be reused across requests but are not shared with the client.
   - In Interactive Client, resources are managed locally in the browser, with server resources accessed via API calls.
   - Interactive Auto adapts its resource usage based on the chosen render mode for each component.

---

### 11. Advanced Developer Features
These features are optional but important for advanced use cases, making them suitable for later in the order.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive Client |
|------------------------------|--------------------------------|-----------------|
| **[ARIA and Semantic HTML](aria.md)** | Fully supported: Markup is static and follows standards easily. | Fully supported: Server generates accessible markup, but requires careful handling of dynamic updates. | Fully supported: Requires careful implementation to ensure dynamically generated content is accessible. |
| **Keyboard Navigation** | Supported for basic interactions: Native browser features like `Tab` navigation and form submission are available. <br>**Limitations:** Complex interactions (e.g., custom key bindings or real-time updates) are not supported and require Interactive mode | Supported, but requires proper event handling on the server for custom interactions. | Supported, but requires proper event handling in client-side code for custom interactions. |
| **Screen Reader Compatibility** | Fully supported: Prerendered content works seamlessly with screen readers. | Supported, but real-time updates require careful implementation to ensure screen readers announce changes. | Supported, but requires careful implementation to ensure dynamically updated content is properly announced by screen readers. |

**Note:**

1. **ARIA and Semantic HTML**: All modes can support this, but interactive modes require more attention to ensure dynamically updated content remains accessible.

2. **Keyboard Navigation**: While basic navigation is supported in all modes, custom interactions in interactive modes require specific implementation to ensure keyboard accessibility.

3. **Screen Reader Compatibility**: SSR has an advantage in initial content accessibility, but both interactive modes can achieve good compatibility with proper implementation of dynamic content updates.

---

### 12. Additional Considerations
This section wraps up with miscellaneous factors that don't fit neatly into other categories but are still important to consider.

| Factor | SSR (Static Server Render) | Interactive Server | Interactive Client |
|------------------------------|--------------------------------|-----------------|
| **Offline Mode** | Limited: Can work with service workers for basic offline content, but dynamic features require server connection. | Limited: Requires constant connection to the server for full functionality. | Supported: Can enable full offline functionality with proper implementation. |
| **Progressive Web App (PWA)** | Partially supported: Can implement some PWA features, but limited by server dependency. | Partially supported: Can implement some PWA features, but limited by server dependency. | Fully supported: Works seamlessly with PWA features, including offline functionality. |
| **Caching Strategies** | Works with traditional server-side caching mechanisms and CDNs. | Combines server-side caching with SignalR connection management for optimized performance. | Leverages client-side caching and service workers for improved performance and offline capabilities. |

**Note:**

1. **Offline Mode**: 
   - SSR and Interactive Server have limitations due to server dependency.
   - Interactive Client (WebAssembly) offers the best support for offline functionality.

2. **Progressive Web App (PWA)**:
   - All modes can implement some PWA features, but Interactive Client offers the most comprehensive support.

3. **Caching Strategies**:
   - Each mode has different caching approaches, with Interactive Client offering the most flexible client-side caching options.
