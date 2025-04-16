# Optional Modules

This document contains additional learning modules that may be useful. Each module is designed to be self-contained and focused on a specific technology or concept.

## Database Integration Modules

### PostgreSQL with Diesel

#### Learning Resources
- [Diesel Getting Started Guide](https://diesel.rs/guides/getting-started)
- [Diesel Query Builder Documentation](https://docs.diesel.rs/diesel/query_builder/index.html)
- [diesel-async Documentation](https://docs.rs/diesel-async/)

#### Key Concepts
1. Setting up database schema and migrations
2. Defining models and table associations
3. Writing efficient queries
4. Handling transactions
5. Implementing async database operations

#### Sample Implementation
```rust
// Define schema
table! {
    documents (id) {
        id -> Integer,
        title -> Text,
        content -> Text,
        created_at -> Timestamp,
    }
}

// Define model
#[derive(Queryable, Selectable, Identifiable)]
#[diesel(table_name = documents)]
struct Document {
    id: i32,
    title: String,
    content: String,
    created_at: NaiveDateTime,
}

// Database operations
async fn find_document(conn: &mut AsyncPgConnection, id: i32) -> Result<Document, Error> {
    documents::table
        .filter(documents::id.eq(id))
        .select(Document::as_select())
        .first(conn)
        .await
}
```

### Redis Integration

#### Learning Resources
- [Redis Rust Client Documentation](https://docs.rs/redis/)
- [Redis Commands Reference](https://redis.io/commands/)
- [Redis Pub/Sub Tutorial](https://redis.io/topics/pubsub)

#### Key Concepts
1. Basic key-value operations
2. Using Redis data structures (lists, sets, hashes)
3. Implementing caching strategies
4. Setting up pub/sub messaging
5. Managing connection pools

#### Sample Implementation
```rust
// Cache implementation
async fn get_or_set_cache<T, F>(
    redis: &mut Connection,
    key: &str,
    ttl_seconds: u64,
    db_fetch: F,
) -> Result<T, Error>
where
    T: DeserializeOwned + Serialize,
    F: Future<Output = Result<T, Error>>,
{
    // Try to get from cache
    let cached: Option<String> = redis.get(key).await?;
    
    if let Some(cached_data) = cached {
        // Return cached data if available
        return Ok(serde_json::from_str(&cached_data)?);
    }
    
    // Fetch from database
    let data = db_fetch.await?;
    
    // Cache the result
    let serialized = serde_json::to_string(&data)?;
    redis.set_ex(key, serialized, ttl_seconds).await?;
    
    Ok(data)
}
```

### QDrant Vector Database

#### Learning Resources
- [QDrant Documentation](https://qdrant.tech/documentation/)
- [QDrant Rust Client](https://docs.rs/qdrant-client/)
- [Vector Similarity Search Concepts](https://qdrant.tech/articles/vector-similarity-search/)

#### Key Concepts
1. Understanding vector embeddings
2. Creating and managing collections
3. Performing similarity searches
4. Filtering search results
5. Optimizing vector operations

#### Sample Implementation
```rust
// QDrant client setup
async fn setup_qdrant() -> Result<QdrantClient, Error> {
    let config = QdrantClientConfig::from_url("http://localhost:6333");
    let client = QdrantClient::new(Some(config))?;
    
    // Create collection if not exists
    let create_collection = CreateCollection {
        collection_name: "documents".to_string(),
        vectors_config: Some(VectorsConfig {
            size: 384,
            distance: Distance::Cosine,
        }),
        ..Default::default()
    };
    
    client.create_collection(&create_collection).await?;
    
    Ok(client)
}

// Vector search
async fn search_similar_documents(
    client: &QdrantClient,
    query_vector: Vec<f32>,
    limit: u64,
) -> Result<Vec<ScoredPoint>, Error> {
    let search_result = client
        .search_points(&SearchPoints {
            collection_name: "documents".to_string(),
            vector: query_vector,
            limit,
            with_payload: Some(true.into()),
            ..Default::default()
        })
        .await?;
    
    Ok(search_result.result)
}
```

## Web Development Modules

### Leptos WebSockets

#### Learning Resources
- [Leptos Documentation](https://leptos.dev/)
- [WebSockets in Leptos](https://book.leptos.dev/server/websockets.html)
- [Tokio WebSockets](https://docs.rs/tokio-tungstenite/)

#### Key Concepts
1. Setting up WebSocket connections
2. Handling real-time data updates
3. Managing connection state
4. Implementing pub/sub patterns
5. Error handling and reconnection

#### Sample Implementation
```rust
// Server-side WebSocket handler
#[server(WebSocketHandler)]
pub async fn handle_websocket(
    cx: Scope,
    room_id: String,
) -> Result<(), ServerFnError> {
    let app_state = use_context::<AppState>(cx)?;
    let addr = app_state.addr.clone();
    
    let req = expect_context::<leptos_actix::RequestParts>(cx);
    let (response, session) = actix_ws::handle(&req.request)?;
    
    // Spawn WebSocket handler
    actix_rt::spawn(async move {
        let mut ws = WsSession {
            id: Uuid::new_v4().to_string(),
            room_id,
            addr,
            session,
        };
        
        ws.run().await;
    });
    
    Ok(())
}

// Client-side WebSocket connection
#[component]
fn WebSocketClient(cx: Scope, room_id: String) -> impl IntoView {
    let (messages, set_messages) = create_signal(cx, Vec::<String>::new());
    let (ws, set_ws) = create_signal(cx, None::<WebSocket>);
    
    create_effect(cx, move |_| {
        let ws_url = format!("ws://{}/api/ws/{}", window().location().host().unwrap(), room_id);
        let new_ws = WebSocket::new(&ws_url).unwrap();
        
        new_ws.set_onmessage(Some(&Closure::wrap(Box::new(move |e: MessageEvent| {
            if let Ok(txt) = e.data().dyn_into::<js_sys::JsString>() {
                let msg = String::from(txt);
                set_messages.update(|msgs| msgs.push(msg));
            }
        }) as Box<dyn FnMut(MessageEvent)>).into_js_value()));
        
        set_ws.set(Some(new_ws));
    });
    
    view! { cx,
        <div>
            <h3>"WebSocket Messages:"</h3>
            <ul>
                <For
                    each=messages
                    key=|msg| msg.clone()
                    view=move |cx, msg| view! { cx, <li>{msg}</li> }
                />
            </ul>
        </div>
    }
}
```

### Docker Integration

#### Learning Resources
- [Docker Documentation](https://docs.docker.com/)
- [Rust Docker Guide](https://hub.docker.com/_/rust)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

#### Key Concepts
1. Creating Dockerfiles for Rust applications
2. Multi-stage builds for smaller images
3. Setting up Docker Compose for multi-container applications
4. Managing environment variables
5. Optimizing build times and image sizes

#### Sample Implementation
```dockerfile
# Multi-stage build for a Rust application
FROM rust:1.70 as builder

WORKDIR /usr/src/app
COPY . .

# Build dependencies first (for better caching)
RUN cargo build --release

# Final stage
FROM debian:bullseye-slim

RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates && rm -rf /var/lib/apt/lists/*

COPY --from=builder /usr/src/app/target/release/my-app /usr/local/bin/my-app

EXPOSE 8080
CMD ["my-app"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/mydb
    depends_on:
      - db
      - redis
      - qdrant
  
  db:
    image: postgres:14
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7
    volumes:
      - redis_data:/data
  
  qdrant:
    image: qdrant/qdrant:latest
    volumes:
      - qdrant_data:/qdrant/storage
    ports:
      - "6333:6333"

volumes:
  postgres_data:
  redis_data:
  qdrant_data:
```

## Testing and Documentation Modules

### Comprehensive Documentation

#### Learning Resources
- [Rust Documentation Guidelines](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)
- [Documentation Best Practices](https://rust-lang.github.io/api-guidelines/documentation.html)
- [README Driven Development](https://tom.preston-werner.com/2010/08/23/readme-driven-development.html)

#### Key Concepts
1. Writing clear and comprehensive README files
2. Documenting code with rustdoc comments
3. Creating API documentation
4. Explaining architecture and design decisions
5. Documenting the development process and challenges

#### Sample Implementation
```rust
/// A document in the semantic search engine.
///
/// This struct represents a document that can be indexed and searched.
/// It contains the document's content and metadata.
///
/// # Examples
///
/// ```
/// let doc = Document::new("Sample Document", "This is the content of the document.");
/// assert_eq!(doc.title, "Sample Document");
/// ```
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Document {
    /// Unique identifier for the document
    pub id: Uuid,
    
    /// Document title
    pub title: String,
    
    /// Document content
    pub content: String,
    
    /// When the document was created
    pub created_at: DateTime<Utc>,
}

impl Document {
    /// Creates a new document with the given title and content.
    ///
    /// # Arguments
    ///
    /// * `title` - The document title
    /// * `content` - The document content
    ///
    /// # Returns
    ///
    /// A new `Document` instance with a generated UUID and the current timestamp.
    pub fn new(title: impl Into<String>, content: impl Into<String>) -> Self {
        Self {
            id: Uuid::new_v4(),
            title: title.into(),
            content: content.into(),
            created_at: Utc::now(),
        }
    }
}
```

### Testing

#### Learning Resources
- [Rust Testing Documentation](https://doc.rust-lang.org/book/ch11-00-testing.html)
- [Test Organization](https://doc.rust-lang.org/book/ch11-03-test-organization.html)
- [Mocking in Rust](https://docs.rs/mockall/latest/mockall/)

#### Key Concepts
1. Writing unit tests
2. Integration testing
3. Test organization and structure
4. Mocking dependencies
5. Test-driven development

#### Sample Implementation
```rust
#[cfg(test)]
mod tests {
    use super::*;
    use mockall::predicate::*;
    
    #[test]
    fn test_document_creation() {
        let doc = Document::new("Test", "Content");
        assert_eq!(doc.title, "Test");
        assert_eq!(doc.content, "Content");
    }
    
    #[tokio::test]
    async fn test_document_search() {
        // Set up mock database
        let mut mock_db = MockDatabase::new();
        mock_db.expect_search()
            .with(eq("test query"))
            .times(1)
            .returning(|_| Ok(vec![
                Document::new("Test Doc 1", "Content 1"),
                Document::new("Test Doc 2", "Content 2"),
            ]));
        
        // Create search service with mock
        let service = SearchService::new(mock_db);
        
        // Perform search
        let results = service.search("test query").await.unwrap();
        
        // Verify results
        assert_eq!(results.len(), 2);
        assert_eq!(results[0].title, "Test Doc 1");
        assert_eq!(results[1].title, "Test Doc 2");
    }
}
```

## AI Integration Modules

### Effective Prompt Engineering

#### Learning Resources
- [Cursor AI Documentation](https://cursor.sh/docs)
- [Effective Prompting Guide](https://www.anthropic.com/news/claude-instant-prompt-engineering)
- [Prompt Engineering Best Practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

#### Key Concepts
1. Writing clear and specific prompts
2. Breaking down complex tasks
3. Providing context and examples
4. Iterative refinement of prompts
5. Handling AI limitations

#### Sample Implementation
```
// Example of a well-structured prompt for Cursor AI

I need to implement a function in Rust that:
1. Takes a string of text as input
2. Splits it into sentences
3. Calculates the vector embedding for each sentence using a mock function
4. Returns a vector of (sentence, embedding) pairs

Here's the signature and mock embedding function I already have:

```rust
fn generate_mock_embedding(text: &str) -> Vec<f32> {
    let hash = text.bytes().fold(0u64, |acc, b| acc.wrapping_add(b as u64));
    let mut rng = rand::rngs::StdRng::seed_from_u64(hash);
    (0..384).map(|_| rng.gen_range(-1.0..1.0)).collect()
}

// Function to implement:
fn process_text(text: &str) -> Vec<(String, Vec<f32>)> {
    // TODO: Implement this function
}
```

Please implement this function with:
- Proper error handling
- Efficient memory usage
- Clear comments explaining the approach
```

### AI-Assisted Debugging

#### Learning Resources
- [Debugging with Cursor AI](https://cursor.sh/blog/debugging-with-ai)
- [Rust Debugging Guide](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [Common Rust Errors](https://doc.rust-lang.org/error-index.html)

#### Key Concepts
1. Using AI to understand compiler errors
2. Debugging runtime issues
3. Analyzing and fixing performance problems
4. Refactoring problematic code
5. Learning from debugging sessions

#### Sample Implementation
```
// Example of using AI for debugging

I'm getting this compiler error:

error[E0507]: cannot move out of `document.content` which is behind a shared reference
  --> src/main.rs:15:13
   |
15 |     process(document.content);
   |             ^^^^^^^^^^^^^^^^ move occurs because `document.content` has type `String`, which does not implement the `Copy` trait

Here's my code:

```rust
struct Document {
    id: String,
    content: String,
}

fn process(content: String) {
    // Process the content
    println!("Processing: {}", content);
}

fn main() {
    let document = Document {
        id: "123".to_string(),
        content: "Hello, world!".to_string(),
    };
    
    process(document.content);
    
    // Need to use document again here
    println!("Document ID: {}", document.id);
}
```

How can I fix this error while still being able to use the document after calling process()?
```

## Performance Optimization Modules

### Error Handling

#### Learning Resources
- [Rust Error Handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [thiserror Crate](https://docs.rs/thiserror/)
- [anyhow Crate](https://docs.rs/anyhow/)

#### Key Concepts
1. Using Result and Option types
2. Creating custom error types
3. Error propagation with the ? operator
4. Handling different error scenarios
5. User-friendly error messages

#### Sample Implementation
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum SearchError {
    #[error("Database error: {0}")]
    DatabaseError(#[from] diesel::result::Error),
    
    #[error("Vector database error: {0}")]
    VectorDbError(String),
    
    #[error("Document not found: {0}")]
    NotFound(String),
    
    #[error("Invalid input: {0}")]
    InvalidInput(String),
}

pub type Result<T> = std::result::Result<T, SearchError>;

async fn search_documents(query: &str) -> Result<Vec<Document>> {
    if query.is_empty() {
        return Err(SearchError::InvalidInput("Query cannot be empty".to_string()));
    }
    
    let conn = establish_connection().await
        .map_err(|e| SearchError::DatabaseError(e))?;
    
    let documents = documents::table
        .filter(documents::content.ilike(format!("%{}%", query)))
        .load::<Document>(&conn)
        .await?;
    
    if documents.is_empty() {
        return Err(SearchError::NotFound(format!("No documents matching '{}'", query)));
    }
    
    Ok(documents)
}
```
