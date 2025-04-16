# Introduction to Leptos Framework

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Official Leptos Documentation](https://leptos.dev/) - The main documentation site (free)
- [Leptos GitHub Repository](https://github.com/leptos-rs/leptos) - Source code and examples (free)
- [Leptos Book](https://book.leptos.dev/) - Comprehensive guide to Leptos (free)
- [Leptos Examples](https://github.com/leptos-rs/leptos/tree/main/examples) - Official example projects (free)
- [Leptos Discord](https://discord.gg/YdRAhS7eQB) - Community support and discussions (free)
- [Leptos Router Documentation](https://docs.rs/leptos_router/latest/leptos_router/) - Client-side routing (free)
- [Leptos Meta Documentation](https://docs.rs/leptos_meta/latest/leptos_meta/) - Document head management (free)
- [Axum Integration](https://github.com/leptos-rs/start-axum) - Leptos with Axum server template (free)

---

Leptos is a full-stack, reactive web framework for building web applications in Rust. It allows you to write your entire web application—both frontend and backend—in Rust, leveraging WebAssembly (WASM) for the client-side code.

## Why Leptos?

Leptos offers several advantages for web development:

- **Full-stack Rust**: Write both client and server code in Rust
- **Reactive by design**: Fine-grained reactivity system similar to SolidJS
- **Server-side rendering (SSR)**: Improve initial load performance and SEO
- **Hydration**: Seamlessly transition from server-rendered to interactive client-side app
- **Small bundle sizes**: Efficient compilation to WebAssembly
- **Type safety**: Leverage Rust's strong type system across your entire application
- **Component-based**: Build UIs from reusable components

## Getting Started with Leptos

### Prerequisites

Before starting with Leptos, ensure you have:

1. Rust installed (via rustup)
2. Trunk (for client-side rendering)
3. wasm32-unknown-unknown target added:
   ```bash
   rustup target add wasm32-unknown-unknown
   ```

### Installation

Install Trunk for client-side rendering:

```bash
cargo install trunk
```

### Creating Your First Leptos Project

Let's create a simple client-side rendered (CSR) Leptos application:

1. Create a new project:
   ```bash
   cargo new --bin leptos-demo
   cd leptos-demo
   ```

2. Add Leptos dependencies to `Cargo.toml`:
   ```toml
   [dependencies]
   leptos = { version = "0.7", features = ["csr"] }
   ```

3. Create an `index.html` file in the project root:
   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Leptos Demo</title>
     </head>
     <body>
       <div id="app"></div>
     </body>
   </html>
   ```

4. Update `src/main.rs` with a simple Leptos app:
   ```rust
   use leptos::*;

   #[component]
   fn App() -> impl IntoView {
       let (count, set_count) = create_signal(0);

       view! {
           <div>
               <h1>"Welcome to Leptos!"</h1>
               <button 
                   on:click=move |_| set_count.update(|n| *n += 1)
               >
                   "Click me: " {count}
               </button>
           </div>
       }
   }

   fn main() {
       mount_to_body(App);
   }
   ```
   *This simple app creates a reactive counter button that updates its display when clicked, demonstrating Leptos' signal-based reactivity system.*

5. Run the application with Trunk:
   ```bash
   trunk serve --open
   ```

This will compile your Rust code to WebAssembly, bundle it with your HTML, and serve it locally, automatically opening it in your browser.

## Core Concepts in Leptos

### Components

Components are the building blocks of Leptos applications. They encapsulate UI and behavior:

```rust
#[component]
fn Counter(
    #[prop(default = 0)] initial_value: i32,
    #[prop(optional)] label: Option<String>,
) -> impl IntoView {
    let (count, set_count) = create_signal(initial_value);
    
    view! {
        <div>
            <h3>{label.unwrap_or_else(|| "Counter".to_string())}</h3>
            <button on:click=move |_| set_count.update(|n| *n -= 1)>"-"</button>
            <span>{count}</span>
            <button on:click=move |_| set_count.update(|n| *n += 1)>"+"</button>
        </div>
    }
}
```
*This component defines a reusable counter with customizable initial value and label, demonstrating component props and local state management.*

Usage:
```rust
view! {
    <Counter initial_value=5 label="My Counter" />
}
```

### Reactivity

Leptos uses a fine-grained reactivity system based on signals:

```rust
// Create a signal with an initial value
let (count, set_count) = create_signal(0);

// Read the signal value
let current = count();

// Set a new value
set_count(42);

// Update based on previous value
set_count.update(|n| *n += 1);

// Derived computations
let doubled = create_memo(move |_| count() * 2);
```
*This code demonstrates Leptos' reactive primitives: signals for state that changes over time, and memos for derived values that automatically update when their dependencies change.*

### Effects

Effects run when their dependencies change:

```rust
let (name, set_name) = create_signal("World".to_string());

create_effect(move |_| {
    // This will run whenever name changes
    log!("Hello, {}!", name());
});

// Trigger the effect
set_name("Leptos".to_string());
```
*This example shows how to use effects to perform side effects (like logging) whenever reactive state changes, without manually tracking dependencies.*

### Event Handling

Handle DOM events with the `on:` directive:

```rust
let (input_value, set_input_value) = create_signal("".to_string());

view! {
    <input 
        type="text"
        on:input=move |ev| {
            let value = event_target_value(&ev);
            set_input_value(value);
        }
        prop:value=input_value
    />
    <p>"You typed: " {input_value}</p>
}
```
*This code demonstrates two-way binding between an input field and a signal, updating the signal when the user types and displaying the current value.*

### Conditional Rendering

Use `Show` for conditional rendering:

```rust
let (show_details, set_show_details) = create_signal(false);

view! {
    <button on:click=move |_| set_show_details.update(|v| *v = !*v)>
        {move || if show_details() { "Hide" } else { "Show" } } " Details"
    </button>
    
    <Show
        when=move || show_details()
        fallback=|| view! { <p>"Click to show details"</p> }
    >
        <p>"These are the details you requested."</p>
    </Show>
}
```
*This example shows conditional rendering with the Show component, toggling content visibility based on a signal's value and providing a fallback when the condition is false.*

### Lists and Iteration

Use `For` to render lists:

```rust
let (items, set_items) = create_signal(vec!["Apple", "Banana", "Cherry"]);

view! {
    <button on:click=move |_| set_items.update(|v| v.push("New Item"))>
        "Add Item"
    </button>
    
    <ul>
        <For
            each=move || items()
            key=|item| item.to_string()
            children=move |item| {
                view! { <li>{item}</li> }
            }
        />
    </ul>
}
```
*This code demonstrates efficient list rendering with the For component, which handles updates to the list without re-rendering unchanged items, using keys for stable identity.*

## WebSockets in Leptos 0.7 and 0.8

Leptos 0.7 and 0.8 provide support for WebSockets through integration with various backend frameworks. Here's how to implement WebSockets in a Leptos application:

### Server-Side Setup (with Axum)

First, add the necessary dependencies to your `Cargo.toml`:

```toml
[dependencies]
leptos = { version = "0.8", features = ["ssr"] }
leptos_axum = { version = "0.8" }
axum = { version = "0.7", features = ["ws"] }
tokio = { version = "1", features = ["full"] }
futures = "0.3"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

Create a WebSocket handler in your server code:

```rust
use axum::{
    extract::ws::{WebSocket, WebSocketUpgrade},
    response::IntoResponse,
    routing::get,
    Router,
};
use futures::{sink::SinkExt, stream::StreamExt};
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
struct ChatMessage {
    user: String,
    message: String,
}

async fn websocket_handler(ws: WebSocketUpgrade) -> impl IntoResponse {
    ws.on_upgrade(handle_socket)
}

async fn handle_socket(mut socket: WebSocket) {
    // Split the socket into sender and receiver
    let (mut sender, mut receiver) = socket.split();
    
    // Process messages
    while let Some(msg) = receiver.next().await {
        if let Ok(msg) = msg {
            if let axum::extract::ws::Message::Text(text) = msg {
                // Parse the message
                if let Ok(chat_msg) = serde_json::from_str::<ChatMessage>(&text) {
                    // Echo the message back with a prefix
                    let response = serde_json::to_string(
                        &ChatMessage {
                            user: "Server".to_string(),
                            message: format!("Received: {}", chat_msg.message),
                        }
                    ).unwrap();
                    
                    if sender.send(axum::extract::ws::Message::Text(response)).await.is_err() {
                        break;
                    }
                }
            }
        } else {
            // Client disconnected or error occurred
            break;
        }
    }
}

// Add the WebSocket route to your Axum router
let app = Router::new()
    .route("/ws", get(websocket_handler))
    // ... other routes
```
*This server-side code sets up a WebSocket endpoint with Axum, handling message serialization/deserialization and implementing a simple echo server pattern.*

### Client-Side Implementation

On the client side, you can use the browser's WebSocket API through Leptos:

```rust
use leptos::*;
use serde::{Deserialize, Serialize};
use wasm_bindgen::{prelude::*, JsCast};
use web_sys::{ErrorEvent, MessageEvent, WebSocket};

#[derive(Serialize, Deserialize, Clone)]
struct ChatMessage {
    user: String,
    message: String,
}

#[component]
fn ChatApp() -> impl IntoView {
    let (messages, set_messages) = create_signal(Vec::<ChatMessage>::new());
    let (input_text, set_input_text) = create_signal(String::new());
    let (ws, set_ws) = create_signal::<Option<WebSocket>>(None);
    
    // Connect to WebSocket on component mount
    create_effect(move |_| {
        let ws_connection = WebSocket::new("ws://localhost:3000/ws").unwrap();
        
        // Set up event handlers
        let on_message = Closure::wrap(Box::new(move |e: MessageEvent| {
            if let Ok(txt) = e.data().dyn_into::<js_sys::JsString>() {
                let txt = String::from(txt);
                if let Ok(chat_msg) = serde_json::from_str::<ChatMessage>(&txt) {
                    set_messages.update(|msgs| msgs.push(chat_msg));
                }
            }
        }) as Box<dyn FnMut(MessageEvent)>);
        
        ws_connection.set_onmessage(Some(on_message.as_ref().unchecked_ref()));
        on_message.forget(); // Prevent closure from being dropped
        
        // Store the WebSocket connection
        set_ws(Some(ws_connection));
    });
    
    // Function to send a message
    let send_message = move |_| {
        if let Some(ws_conn) = ws() {
            if ws_conn.ready_state() == 1 { // OPEN
                let msg = ChatMessage {
                    user: "Me".to_string(),
                    message: input_text(),
                };
                
                if let Ok(json) = serde_json::to_string(&msg) {
                    let _ = ws_conn.send_with_str(&json);
                    set_input_text(String::new());
                }
            }
        }
    };
    
    view! {
        <div class="chat-container">
            <div class="messages">
                <For
                    each=move || messages()
                    key=|msg| format!("{}-{}", msg.user, msg.message)
                    children=move |msg| {
                        view! {
                            <div class="message">
                                <strong>{msg.user}: </strong>
                                <span>{msg.message}</span>
                            </div>
                        }
                    }
                />
            </div>
            <div class="input-area">
                <input 
                    type="text"
                    prop:value=input_text
                    on:input=move |ev| set_input_text(event_target_value(&ev))
                    on:keypress=move |ev| {
                        if ev.key() == "Enter" {
                            send_message(());
                        }
                    }
                />
                <button on:click=send_message>"Send"</button>
            </div>
        </div>
    }
}
```
*This client-side component implements a chat interface using WebSockets, handling connection establishment, message sending/receiving, and displaying messages in a reactive list.*

## Building a Full-Stack Leptos Application

For full-stack applications, Leptos supports server-side rendering (SSR) and hydration.

### Project Setup

1. Create a new project:
   ```bash
   cargo new --bin leptos-fullstack
   cd leptos-fullstack
   ```

2. Add dependencies to `Cargo.toml`:
   ```toml
   [dependencies]
   leptos = { version = "0.8", features = ["ssr"] }
   leptos_meta = { version = "0.8", features = ["ssr"] }
   leptos_router = { version = "0.8", features = ["ssr"] }
   leptos_axum = { version = "0.8" }
   axum = "0.7"
   tokio = { version = "1", features = ["full"] }
   tower = "0.4"
   tower-http = { version = "0.5", features = ["fs"] }
   ```

3. Create a basic file structure:
   ```
   src/
   ├── app.rs       # Main application component
   ├── error_template.rs  # Error handling
   ├── main.rs      # Server entry point
   ├── routes/      # Application routes
   │   ├── mod.rs
   │   ├── home.rs
   │   └── about.rs
   └── components/  # Reusable components
       ├── mod.rs
       └── counter.rs
   ```

### Server Setup (main.rs)

```rust
use axum::{
    routing::get,
    Router,
};
use leptos::*;
use leptos_axum::{generate_route_list, LeptosRoutes};
use tower_http::services::ServeDir;

mod app;
mod
(Content truncated due to size limit. Use line ranges to read in chunks)