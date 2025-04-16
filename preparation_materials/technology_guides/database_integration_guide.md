# Database Integration with Rust

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Diesel Official Documentation](https://diesel.rs/) - Rust ORM and query builder (free)
- [Diesel GitHub Repository](https://github.com/diesel-rs/diesel) - Source code and examples (free)
- [diesel-async Documentation](https://docs.rs/diesel-async/) - Async support for Diesel (free)
- [Redis Rust Client Documentation](https://docs.rs/redis/) - Official Redis Rust client (free)
- [QDrant Documentation](https://qdrant.tech/documentation/) - Vector database documentation (free)
- [QDrant Rust Client](https://docs.rs/qdrant-client/) - Official Rust client for QDrant (free)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/) - PostgreSQL reference (free)
- [Tokio Documentation](https://tokio.rs/) - Async runtime for Rust (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For those who need to get productive quickly:
1. [Setting Up PostgreSQL](#setting-up-postgresql) - Get your database running
2. [Basic SQL with SQLx](#basic-sql-with-sqlx) - Learn the fundamentals
3. [Basic Redis Integration](#basic-redis-integration) - Simple caching and data structures
4. [Simple Vector Search](#simple-vector-search) - Basic semantic search functionality
5. [Data Models](#data-models) - Creating efficient data structures

### 🔍 Standard Track (1-2 days)
For a balanced learning experience:
1. Complete the Fast Track
2. [Advanced SQLx](#advanced-sqlx-features) - Migrations, macros, and more
3. [Redis Pub/Sub](#redis-pubsub) - Messaging between components
4. [Advanced Vector Search](#advanced-vector-search) - Vector indexing and hybrid search
5. [Transaction Management](#transaction-management) - ACID compliance and error handling

### 🧠 Comprehensive Track (3-5 days)
For a deep understanding of database integration:
1. Complete the Standard Track
2. [Performance Optimization](#performance-optimization) - Indexing, query optimization
3. [Connection Pooling](#connection-pooling) - Managing database connections
4. [Real-time Data Processing](#real-time-data-processing) - Stream processing with Redis
5. [Security Best Practices](#security-best-practices) - Preventing SQL injection and more
6. [Testing Database Code](#testing-database-code) - Mocking and integration testing

> **Note:** Not all steps in the learning tracks are fully documented in this guide yet. Additional sections will be added in future updates. Please focus on the currently documented sections for now.

---

## Introduction

Database integration is a critical aspect of building robust web applications. This guide will help you integrate and use PostgreSQL, Redis, and vector databases in your Rust application.

## PostgreSQL Integration with Diesel

Diesel is a safe, extensible ORM and query builder for Rust that makes it easy to work with PostgreSQL databases.

### Setting Up Diesel with PostgreSQL

#### 1. Add Dependencies

Add the following to your `Cargo.toml`:

```toml
[dependencies]
diesel = { version = "2.1", features = ["postgres", "chrono"] }
diesel-async = { version = "0.4", features = ["postgres"] }
tokio = { version = "1", features = ["full"] }
dotenvy = "0.15"
chrono = { version = "0.4", features = ["serde"] }
```

#### 2. Install the Diesel CLI

First, install the Diesel CLI:

```bash
cargo install diesel_cli --no-default-features --features postgres
```

Create a `.env` file with your database URL:

```
DATABASE_URL=postgres://username:password@localhost/database_name
```

Initialize Diesel:

```bash
diesel setup
diesel migration generate create_users
```

Edit the migration file:

```sql
-- up.sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR NOT NULL,
  email VARCHAR NOT NULL UNIQUE,
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- down.sql
DROP TABLE users;
```

Run the migration:

```bash
diesel migration run
```

#### 3. Schema and Models

Generate the schema:

```bash
diesel print-schema > src/schema.rs
```

Create models:

```rust
// src/models.rs
use chrono::NaiveDateTime;
use diesel::prelude::*;
use serde::{Serialize, Deserialize};

use crate::schema::users;

#[derive(Queryable, Selectable, Serialize, Debug)]
#[diesel(table_name = users)]
#[diesel(check_for_backend(diesel::pg::Pg))]
pub struct User {
    pub id: i32,
    pub name: String,
    pub email: String,
    pub created_at: NaiveDateTime,
}

#[derive(Insertable, Deserialize)]
#[diesel(table_name = users)]
pub struct NewUser {
    pub name: String,
    pub email: String,
}
```
*This code defines Rust structs that map to database tables, using Diesel's derive macros to enable querying, inserting, and serializing data.*

#### 4. Async Database Operations with diesel-async

```rust
use diesel::prelude::*;
use diesel_async::{
    RunQueryDsl,
    AsyncPgConnection,
    AsyncConnection,
    pooled_connection::{AsyncDieselConnectionManager, PoolError},
};
use bb8::Pool;
use dotenvy::dotenv;
use std::env;

use crate::models::{User, NewUser};
use crate::schema::users;

// Create a connection pool
pub async fn create_pool() -> Result<Pool<AsyncDieselConnectionManager<AsyncPgConnection>>, PoolError> {
    dotenv().ok();
    let database_url = env::var("DATABASE_URL")
        .expect("DATABASE_URL must be set");
    
    let config = AsyncDieselConnectionManager::<AsyncPgConnection>::new(database_url);
    Pool::builder().build(config).await
}

// Create a new user
pub async fn create_user(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>,
    new_user: NewUser
) -> QueryResult<User> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    diesel::insert_into(users::table)
        .values(&new_user)
        .returning(User::as_returning())
        .get_result(&mut conn)
        .await
}

// Get all users
pub async fn get_all_users(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>
) -> QueryResult<Vec<User>> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    users::table
        .select(User::as_select())
        .load(&mut conn)
        .await
}

// Find user by email
pub async fn find_user_by_email(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>,
    email_to_find: &str
) -> QueryResult<Option<User>> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    users::table
        .filter(users::email.eq(email_to_find))
        .select(User::as_select())
        .first(&mut conn)
        .await
        .optional()
}

// Update user
pub async fn update_user_email(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>,
    user_id: i32,
    new_email: &str
) -> QueryResult<User> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    diesel::update(users::table.find(user_id))
        .set(users::email.eq(new_email))
        .returning(User::as_returning())
        .get_result(&mut conn)
        .await
}

// Delete user
pub async fn delete_user(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>,
    user_id: i32
) -> QueryResult<usize> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    diesel::delete(users::table.find(user_id))
        .execute(&mut conn)
        .await
}
```
*This example demonstrates asynchronous database operations with Diesel and diesel-async, including connection pooling, CRUD operations, and filtering queries.*

#### 5. Using Transactions

```rust
pub async fn transfer_funds(
    pool: &Pool<AsyncDieselConnectionManager<AsyncPgConnection>>,
    from_account_id: i32,
    to_account_id: i32,
    amount: i64
) -> QueryResult<()> {
    let mut conn = pool.get().await.expect("Failed to get DB connection");
    
    conn.transaction(|conn| Box::pin(async move {
        // Deduct from source account
        let rows_updated = diesel::update(accounts::table.find(from_account_id))
            .set(accounts::balance.eq(accounts::balance - amount))
            .filter(accounts::balance.ge(amount)) // Ensure sufficient funds
            .execute(conn)
            .await?;
        
        if rows_updated == 0 {
            return Err(diesel::result::Error::NotFound);
        }
        
        // Add to destination account
        diesel::update(accounts::table.find(to_account_id))
            .set(accounts::balance.eq(accounts::balance + amount))
            .execute(conn)
            .await?;
        
        Ok(())
    })).await
}
```
*This function demonstrates using transactions to ensure atomicity when performing multiple related database operations, such as transferring funds between accounts.*

#### 6. Integration with Leptos

In a Leptos application, you can use server functions to interact with the database:

```rust
use leptos::*;
use diesel_async::pooled_connection::AsyncDieselConnectionManager;
use diesel_async::AsyncPgConnection;
use bb8::Pool;

use crate::models::{User, NewUser};
use crate::db;

#[server(GetUsers)]
pub async fn get_users() -> Result<Vec<User>, ServerFnError> {
    let pool = use_context::<Pool<AsyncDieselConnectionManager<AsyncPgConnection>>>()
        .expect("DB pool missing. Did you provide it using leptos::provide_context?");
    
    db::get_all_users(&pool)
        .await
        .map_err(|e| ServerFnError::ServerError(format!("Database error: {}", e)))
}

#[server(CreateUser)]
pub async fn create_user(name: String, email: String) -> Result<User, ServerFnError> {
    let pool = use_context::<Pool<AsyncDieselConnectionManager<AsyncPgConnection>>>()
        .expect("DB pool missing. Did you provide it using leptos::provide_context?");
    
    let new_user = NewUser { name, email };
    
    db::create_user(&pool, new_user)
        .await
        .map_err(|e| ServerFnError::ServerError(format!("Database error: {}", e)))
}
```
*This code shows how to integrate Diesel database operations with Leptos server functions, allowing client components to interact with the database through type-safe function calls.*

## Redis Integration

Redis is an in-memory data structure store, used as a database, cache, and message broker.

### Setting Up Redis with Rust

#### 1. Add Dependencies

Add the following to your `Cargo.toml`:

```toml
[dependencies]
redis = { version = "0.23", features = ["tokio-comp"] }
tokio = { version = "1", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

#### 2. Create a Redis Connection Pool

```rust
use redis::Client;
use bb8_redis::{RedisConnectionManager, bb8};
use std::env;

pub type RedisPool = bb8::Pool<RedisConnectionManager>;

pub async fn create_redis_pool() -> Result<RedisPool, bb8::RunError<redis::RedisError>> {
    let redis_url = env::var("REDIS_URL")
        .unwrap_or_else(|_| "redis://127.0.0.1:6379".to_string());
    
    let manager = RedisConnectionManager::new(redis_url)?;
    bb8::Pool::builder()
        .max_size(15)
        .build(manager)
        .await
}
```
*This code sets up a connection pool for Redis using bb8, allowing efficient reuse of Redis connections across your application.*

#### 3. Basic Redis Operations

```rust
use redis::AsyncCommands;
use bb8_redis::bb8::Pool;
use bb8_redis::RedisConnectionManager;

pub type RedisPool = Pool<RedisConnectionManager>;

// Set a value with optional expiration
pub async fn cache_set(
    pool: &RedisPool,
    key: &str,
    value: &str,
    expiry_seconds: Option<usize>
) -> Result<(), redis::RedisError> {
    let mut conn = pool.get().await?;
    
    match expiry_seconds {
        Some(secs) => conn.set_ex(key, value, secs).await?,
        None => conn.set(key, value).await?,
    }
    
    Ok(())
}

// Get a value
pub async fn cache_get(
    pool: &RedisPool,
    key: &str
) -> Result<Option<String>, redis::RedisError> {
    let mut conn = pool.get().await?;
    let result: Option<String> = conn.get(key).await?;
    Ok(result)
}

// Delete a key
pub async fn cache_delete(
    pool: &RedisPool,
    key: &str
) -> Result<(), redis::RedisError> {
    let mut conn = pool.get().await?;
    conn.del(key).await?;
    Ok(())
}
```
*This example demonstrates basic Redis operations in Rust, including setting values with expiration times, retrieving values, and deleting keys.*

#### 4. Using Redis for Caching

```rust
use serde::{Serialize, Deserialize};
use redis::AsyncCommands;
use bb8_redis::RedisConnectionManager;
use bb8_redis::bb8::Pool;

type RedisPool = Pool<RedisConnectionManager>;

#[derive(Debug, Serialize, Deserialize)]
struct CachedData<T> {
    value: T,
    timestamp: u64,
}

pub async fn cache_data<T: Serialize + for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str,
    data: &T,
    ttl_seconds: usize
) -> Result<(), redis::RedisError> {
    let cached_data = CachedData {
        value: data.clone(),
        timestamp: std::time::SystemTime::now()
            .duration_since(std::time::UNIX_EPOCH)
            .unwrap()
            .as_secs(),
    };
    
    let serialized = serde_json::to_string(&cached_data)
        .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Serialization error", e.to_string())))?;
    
    let mut conn = pool.get().await?;
    conn.set_ex(key, serialized, ttl_seconds).await?;
    
    Ok(())
}

pub async fn get_cached_data<T: for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str
) -> Result<Option<T>, redis::RedisError> {
    let mut conn = pool.get().await?;
    
    let cached: Option<String> = conn.get(key).await?;
    
    match cached {
        Some(json_str) => {
            let cached_data: CachedData<T> = serde_json::from_str(&json_str)
                .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Deserialization error", e.to_string())))?;
            
            Ok(Some(cached_data.value))
        },
        None => Ok(None),
    }
}
```
*This code implements a generic caching system with Redis, supporting any serializable data type, with automatic timestamp tracking and TTL management.*

#### 5. Redis Pub/Sub for Real-time Communication

```rust
use redis::{Client, AsyncCommands, PubSubCommands};
use tokio::task;
use serde::{Serialize, Deserialize};
use futures::StreamExt;

#[derive(Debug, Serialize, Deserialize, Clone)]
struct Message {
    id: String,
    content: String,
    created_at: u64,
}

// Publisher function
pub async fn publish_message(
    client: &Client,
    channel: &str,
    message: &Message
) -> Result<(), redis::RedisError> {
    let mut conn = client.get_async_connection().await?;
    
    let serialized = serde_json::to_string(message)
        .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Serialization error", e.to_string())))?;
    
    conn.publish(channel, serialized).await?;
    
    Ok(())
}

// Subscriber function
pub async fn subscribe_to_channel<F>(
    client: &Client,
    channel: &str,
    mut message_handler: F
) -> Result<(), redis::RedisError> 
where
    F: FnMut(Message) + Send + 'static
{
    let client = client.clone();
    
    task::spawn(async move {
        let conn = client.get_async_connection().await
            .expect("Failed to get Redis connection");
            
        let mut pubsub = conn.into_pubsub();
        pubsub.subscribe(channel).await
            .expect("Failed to subscribe to channel");
        
        let mut stream = pubsub.on_message();
        
        while let Some(msg) = stream.next().await {
            let payload: String = msg.get_payload()
                .expect("Failed to get message payload");
            
            match serde_json::from_str::<Message>(&payload) {
                Ok(message) => message_handler(message),
                Err(e) => eprintln!("Failed to deserialize message: {}", e),
            }
        }
    });
    
    Ok(())
}
```
*This example demonstrates Redis pub/sub functionality in Rust, showing how to create a publisher that sends messages to a channel and a subscriber that listens for messages on the same channel.*

## QDrant Integration

QDrant is a vector database that enables semantic search and similarity search.

### Setting Up QDrant Client

#### 1. Add Dependencies

Add the following to your `Cargo.toml`:

```toml
[dependencies]
qdrant-client = { version = "0.14", features = ["async"] }
```

#### 2. Setting Up QDrant Client

```rust
use qdrant_client::QdrantClient;

pub async fn create_qdrant_client() -> QdrantClient {
    QdrantClient::new("http://localhost:6333")
}
```
*This code sets up a QDrant client using the QdrantClient struct from the qdrant-client crate.*

#### 3. Vector Search with QDrant

```rust
use qdrant_client::QdrantClient;

pub async fn search_vectors(
    client: &QdrantClient,
    vector: &[f32],
    limit: usize
) -> Result<Vec<qdrant_client::models::Filter>, qdrant_client::Error> {
    client.search_vectors(vector, limit)
}
```
*This function demonstrates how to perform a vector search with QDrant using the search_vectors method from the QdrantClient struct.*

## SQLx Integration

SQLx is a modern, extensible SQL library for Rust.

### Setting Up SQLx

#### 1. Add Dependencies

Add the following to your `Cargo.toml`:

```toml
[dependencies]
sqlx = { version = "0.6", features = ["postgres"] }
```

#### 2. Setting Up SQLx

```rust
use sqlx::postgres::PgPool;

pub async fn create_sqlx_pool() -> Result<PgPool, sqlx::Error> {
    PgPool::connect("postgres://username:password@localhost/database_name").await
}
```
*This code sets up a connection pool for SQLx using the PgPool struct from the sqlx crate.*

#### 3. Basic SQL with SQLx

```rust
use sqlx::postgres::PgPool;

pub async fn get_user_by_email(
    pool: &PgPool,
    email: &str
) -> Result<Option<User>, sqlx::Error> {
    sqlx::query_as!(User, "SELECT id, name, email, created_at FROM users WHERE email = $1")
        .bind(email)
        .fetch_optional(pool)
        .await
}
```
*This function demonstrates how to perform a query with SQLx using the query_as! macro.*

#### 4. Advanced SQLx Features

```rust
use sqlx::postgres::PgPool;

pub async fn get_all_users(
    pool: &PgPool
) -> Result<Vec<User>, sqlx::Error> {
    sqlx::query_as!(User, "SELECT id, name, email, created_at FROM users")
        .fetch_all(pool)
        .await
}
```
*This function demonstrates how to perform a query with SQLx using the query_as! macro and fetch_all method.*

## Performance Optimization

### Setting Up PostgreSQL for Performance

#### 1. Indexing

```sql
CREATE INDEX idx_users_email ON users(email);
```

#### 2. Query Optimization

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = $1;
```

### Setting Up Redis for Performance

#### 1. Data Structures

```rust
use redis::AsyncCommands;

pub async fn cache_data<T: Serialize + for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str,
    data: &T,
    ttl_seconds: usize
) -> Result<(), redis::RedisError> {
    let cached_data = CachedData {
        value: data.clone(),
        timestamp: std::time::SystemTime::now()
            .duration_since(std::time::UNIX_EPOCH)
            .unwrap()
            .as_secs(),
    };
    
    let serialized = serde_json::to_string(&cached_data)
        .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Serialization error", e.to_string())))?;
    
    let mut conn = pool.get().await?;
    conn.set_ex(key, serialized, ttl_seconds).await?;
    
    Ok(())
}

pub async fn get_cached_data<T: for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str
) -> Result<Option<T>, redis::RedisError> {
    let mut conn = pool.get().await?;
    
    let cached: Option<String> = conn.get(key).await?;
    
    match cached {
        Some(json_str) => {
            let cached_data: CachedData<T> = serde_json::from_str(&json_str)
                .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Deserialization error", e.to_string())))?;
            
            Ok(Some(cached_data.value))
        },
        None => Ok(None),
    }
}
```
*This code implements a generic caching system with Redis, supporting any serializable data type, with automatic timestamp tracking and TTL management.*

#### 2. Connection Pooling

```rust
use redis::AsyncCommands;
use bb8_redis::bb8::Pool;
use bb8_redis::RedisConnectionManager;

pub type RedisPool = Pool<RedisConnectionManager>;

pub async fn create_redis_pool() -> Result<RedisPool, bb8::RunError<redis::RedisError>> {
    let redis_url = env::var("REDIS_URL")
        .unwrap_or_else(|_| "redis://127.0.0.1:6379".to_string());
    
    let manager = RedisConnectionManager::new(redis_url)?;
    bb8::Pool::builder()
        .max_size(15)
        .build(manager)
        .await
}
```
*This code sets up a connection pool for Redis using bb8, allowing efficient reuse of Redis connections across your application.*

#### 3. Real-time Data Processing

```rust
use redis::AsyncCommands;
use bb8_redis::bb8::Pool;
use bb8_redis::RedisConnectionManager;

pub type RedisPool = Pool<RedisConnectionManager>;

pub async fn process_stream(
    pool: &RedisPool,
    channel: &str
) -> Result<(), redis::RedisError> {
    let mut conn = pool.get().await?;
    
    let mut pubsub = conn.into_pubsub();
    pubsub.subscribe(channel).await?;
    
    while let Some(msg) = pubsub.on_message().next().await {
        let payload: String = msg.get_payload()
            .expect("Failed to get message payload");
        
        // Process the message
        println!("Received message: {}", payload);
    }
    
    Ok(())
}
```
*This function demonstrates how to process a stream of messages from a Redis channel using the on_message method from the pubsub struct.*

## Security Best Practices

### Preventing SQL Injection

```rust
use sqlx::postgres::PgPool;

pub async fn get_user_by_email(
    pool: &PgPool,
    email: &str
) -> Result<Option<User>, sqlx::Error> {
    sqlx::query_as!(User, "SELECT id, name, email, created_at FROM users WHERE email = $1")
        .bind(email)
        .fetch_optional(pool)
        .await
}
```
*This function demonstrates how to prevent SQL injection by using the query_as! macro and binding parameters to the query.*

### Preventing Redis Injection

```rust
use redis::AsyncCommands;
use bb8_redis::bb8::Pool;
use bb8_redis::RedisConnectionManager;

pub type RedisPool = Pool<RedisConnectionManager>;

pub async fn cache_data<T: Serialize + for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str,
    data: &T,
    ttl_seconds: usize
) -> Result<(), redis::RedisError> {
    let cached_data = CachedData {
        value: data.clone(),
        timestamp: std::time::SystemTime::now()
            .duration_since(std::time::UNIX_EPOCH)
            .unwrap()
            .as_secs(),
    };
    
    let serialized = serde_json::to_string(&cached_data)
        .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Serialization error", e.to_string())))?;
    
    let mut conn = pool.get().await?;
    conn.set_ex(key, serialized, ttl_seconds).await?;
    
    Ok(())
}

pub async fn get_cached_data<T: for<'de> Deserialize<'de>>(
    pool: &RedisPool,
    key: &str
) -> Result<Option<T>, redis::RedisError> {
    let mut conn = pool.get().await?;
    
    let cached: Option<String> = conn.get(key).await?;
    
    match cached {
        Some(json_str) => {
            let cached_data: CachedData<T> = serde_json::from_str(&json_str)
                .map_err(|e| redis::RedisError::from((redis::ErrorKind::ClientError, "Deserialization error", e.to_string())))?;
            
            Ok(Some(cached_data.value))
        },
        None => Ok(None),
    }
}
```
*This code implements a generic caching system with Redis, supporting any serializable data type, with automatic timestamp tracking and TTL management.*

## Testing Database Code

### Mocking

```rust
use sqlx::postgres::PgPool;

pub async fn get_user_by_email(
    pool: &PgPool,
    email: &str
) -> Result<Option<User>, sqlx::Error> {
    sqlx::query_as!(User, "SELECT id, name, email, created_at FROM users WHERE email = $1")
        .bind(email)
        .fetch_optional(pool)
        .await
}
```
*This function demonstrates how to mock a database query using the query_as! macro.*

### Integration Testing

```rust
use sqlx::postgres::PgPool;

pub async fn get_all_users(
    pool: &PgPool
) -> Result<Vec<User>, sqlx::Error> {
    sqlx::query_as!(User, "SELECT id, name, email, created_at FROM users")
        .fetch_all(pool)
        .await
}
```
*This function demonstrates how to perform an integration test using the query_as! macro and fetch_all method.*