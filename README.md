# Tauri Plugin graphql

A plugin for Tauri that enables type-safe IPC through GraphQL.

## Install

### Rust

```toml
[dependencies]
tauri-plugin-graphql = "2.0.0"
```

### JavaScript

The only client-side adapter currently is `tauri-plugin-graphql-urql`, a custom exchange for [`urql`]. 
If you need adapters for other GraphQL clients, open a PR!

| Package                       | Version (click for changelogs) |
|-------------------------------|--------------------------------|
| [`tauri-plugin-graphql-urql`] | [![urql adapter version][urql-adapter-version-badge]][urql-adapter-changelog]

## Usage

You need to register the plugin giving it a [`async_graphql::Schema`]. This schema will be used to fulfill requests.

```rust
use async_graphql::{Schema, Object, EmptySubscription, EmptyMutation, Result as GraphQLResult, SimpleObject};

#[derive(SimpleObject, Debug, Clone)]
struct ListItem {
    id: i32,
    text: String
}

impl ListItem {
    pub fn new(text: String) -> Self {
        Self {
            id: rand::random::<i32>(),
            text
        }
    }
}

struct Query;

#[Object]
impl Query {
    async fn list(&self) -> GraphQLResult<Vec<ListItem>> {
        let item = vec![
            ListItem::new("foo".to_string()),
            ListItem::new("bar".to_string())
        ];

        Ok(item)
    }
}

fn main() {
    let schema = Schema::new(
        Query,
        EmptyMutation,
        EmptySubscription,
    );

    tauri::Builder::default()
        .plugin(tauri_plugin_graphql::init(schema))
        .run(tauri::generate_context!())
        .expect("failed to run app");
}
```
