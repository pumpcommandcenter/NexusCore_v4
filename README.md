# NexusCore_v4
Overhaul and restructuring.
nexuscore/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── backend/
│   ├── src/
│   │   ├── main.rs
│   │   ├── routes/
│   │   │   ├── mod.rs
│   │   │   ├── helius.rs
│   │   │   ├── pq.rs
│   │   │   └── monitoring.rs
│   │   └── services/
│   │       └── redis_consumer.rs
│   ├── Cargo.toml
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── App.tsx
│   │   ├── main.tsx
│   │   ├── components/
│   │   │   └── NexusCoreCommandCenter.tsx
│   │   ├── pages/
│   │   │   ├── Overview.tsx
│   │   │   ├── Assets.tsx
│   │   │   ├── Transactions.tsx
│   │   │   ├── Security.tsx
│   │   │   └── Monitoring.tsx
│   │   ├── layouts/
│   │   │   └── DashboardLayout.tsx
│   │   ├── hooks/
│   │   │   ├── useWebSocket.ts
│   │   │   ├── useHelius.ts
│   │   │   └── usePostQuantum.ts
│   │   └── lib/
│   │       └── api.ts
│   ├── public/
│   ├── nginx.conf
│   ├── package.json
│   ├── vite.config.ts
│   ├── tailwind.config.js
│   └── Dockerfile
├── k8s/
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-service.yaml
│   ├── ingress.yaml
│   └── secret.yaml
├── grafana/
│   └── provisioning/
│       ├── datasources/
│       │   └── prometheus.yml
│       └── dashboards/
│           ├── provider.yaml
│           └── nexuscore-dashboard.json
├── prometheus/
│   └── alerts.yml
├── docker-compose.yml
├── docker-compose.prod.yml
├── fly.toml
├── .env.example
├── .gitignore
└── README.md
# Dependencies
node_modules/
target/

# Build outputs
dist/
build/
*.exe
*.dll

# Environment
.env
.env.local
.env.*.local

# IDE
.idea/
.vscode/
*.swp

# Docker
docker-compose.override.yml

# Misc
.DS_Store
*.log
const API_BASE = import.meta.env.VITE_API_URL || 'http://localhost:8080';

export const api = {
  get: async <T>(endpoint: string): Promise<T> => {
    const res = await fetch(`${API_BASE}${endpoint}`);
    if (!res.ok) throw new Error(`API Error: ${res.status}`);
    return res.json();
  },

  post: async <T>(endpoint: string, body: any): Promise<T> => {
    const res = await fetch(`${API_BASE}${endpoint}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body),
    });
    if (!res.ok) throw new Error(`API Error: ${res.status}`);
    return res.json();
  },
};
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    host: true,
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
  },
});
const [error, setError] = useState<string | null>(null);

// Inside useEffect for fetching
try {
  // existing fetch logic
} catch (err: any) {
  setError(err.message);
}
MIT License

Copyright (c) 2026 NexusCore Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
# Contributing to NexusCore

Thank you for your interest in contributing to **NexusCore** — a post-quantum secured Solana command center.

We welcome contributions of all kinds: bug reports, feature requests, documentation improvements, and code contributions.

## How to Contribute

### 1. Reporting Issues

- Use the GitHub Issues tab
- Include as much detail as possible (steps to reproduce, environment, logs, screenshots)
- Use the appropriate issue template if available

### 2. Suggesting Features

We love new ideas! Please open an issue with the label `enhancement` and describe:
- The problem you're trying to solve
- Your proposed solution
- Any relevant context or alternatives

### 3. Code Contributions

#### Getting Started

1. Fork the repository
2. Clone your fork:
   ```bash
   git clone [https://github.com/yourusername/nexuscore.git](https://github.com/yourusername/nexuscore.git)
   cd nexuscore
   git checkout -b feature/your-feature-name
   # Backend
cd backend
cargo build

# Frontend
cd frontend
npm install
npm run dev
cargo install --locked cargo-leptos
cargo leptos new --tailwind nexus-core-rust  # or clone a starter
cd nexus-core-rust
[dependencies]
leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_meta = "0.7"
leptos_router = "0.7"
axum = { version = "0.8", features = ["ws"] }
tokio = { version = "1", features = ["full"] }
tower = "0.5"
serde = { version = "1", features = ["derive"] }
# For chat / realtime
leptos_ws = "0.1"  # or implement manual WS
use leptos::*;
use leptos_router::*;
use leptos_meta::*;

#[component]
pub fn App() -> impl IntoView {
    provide_meta_context();
    let (active_tab, set_active_tab) = create_signal("dashboard".to_string());

    view! {
        <div class="min-h-screen bg-zinc-950 text-white font-sans">
            {/* Header */}
            <header class="fixed top-0 left-0 right-0 bg-black/90 backdrop-blur z-50 border-b border-zinc-800">
                <div class="max-w-7xl mx-auto px-6 py-4 flex items-center justify-between">
                    <div class="flex items-center gap-3">
                        <h1 class="text-3xl font-bold tracking-tighter text-gold">"NEXUSCORE"</h1>
                    </div>

                    {/* Top Right Alert Icon - FIXED & VISIBLE */}
                    <button 
                        class="alert-icon p-3 rounded-full hover:bg-zinc-800 transition-all text-2xl relative"
                        on:click=move |_| { /* open notifications */ }
                    >
                        "🛎️"
                        <span class="absolute top-1 right-1 w-4 h-4 bg-red-500 rounded-full text-[10px] flex items-center justify-center">3</span>
                    </button>
                </div>
            </header>

            <div class="pt-20 flex">
                {/* Quick Navigation Tabs */}
                <nav class="quick-nav w-72 bg-black border-r border-zinc-800 p-6 flex-shrink-0 h-screen overflow-y-auto">
                    <QuickNavTabs active_tab set_active_tab />
                </nav>

                {/* Main Content */}
                <main class="flex-1 p-8 max-w-7xl mx-auto">
                    <Routes>
                        <Route path="" view=move || view! { <Dashboard active_tab /> } />
                        <Route path="live" view=LiveSection />
                        {/* Add more routes */}
                    </Routes>
                </main>
            </div>
        </div>
    }
}
#[component]
fn QuickNavTabs(active_tab: RwSignal<String>, set_active_tab: WriteSignal<String>) -> impl IntoView {
    let tabs = vec![
        ("dashboard", "🏠", "Dashboard"),
        ("live", "📺", "Live"),
        ("workflows", "⚡", "Workflows"),
        ("integrations", "🔗", "Integrations"),
        ("debug", "🐞", "Debug"),
    ];

    view! {
        <div class="space-y-2">
            {tabs.into_iter().map(move |(id, icon, label)| {
                let is_active = move || active_tab.get() == id;
                view! {
                    <button
                        class=move || format!(
                            "w-full flex flex-col items-center py-6 px-4 rounded-2xl transition-all {}",
                            if is_active() { 
                                "bg-black border-2 border-gold shadow-lg shadow-gold/20" 
                            } else { 
                                "bg-zinc-900 hover:bg-zinc-800" 
                            }
                        )
                        on:click=move |_| set_active_tab.set(id.to_string())
                    >
                        <span class="text-4xl mb-3 text-gold">{icon}</span>
                        <span class="text-sm font-medium tracking-widest text-white uppercase">{label}</span>
                    </button>
                }
            }).collect_view()}
        </div>
    }
}
#[component]
pub fn LiveSection() -> impl IntoView {
    let (messages, set_messages) = create_signal(vec![]);
    let (new_msg, set_new_msg) = create_signal(String::new());

    // WebSocket setup (simplified - use leptos_ws or Axum WS handler)
    // In real app: connect to /ws endpoint

    view! {
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Live Video */}
            <div class="lg:col-span-2 bg-black rounded-3xl overflow-hidden relative aspect-video border border-zinc-800">
                <video 
                    class="w-full h-full object-contain"
                    autoplay=true 
                    controls=true
                    src="YOUR_HLS_STREAM_URL_OR_WEBRTC"  // e.g., HLS from media server
                ></video>
                <div class="absolute top-6 left-6 bg-red-600 px-4 py-1 rounded-full text-sm font-bold flex items-center gap-2">
                    <span class="animate-pulse">●</span> LIVE
                </div>
            </div>

            {/* Real Chat Bar */}
            <div class="bg-zinc-900 rounded-3xl flex flex-col h-[600px] border border-zinc-800">
                <div class="p-6 border-b border-zinc-800 font-semibold text-lg">"Live Chat Stream"</div>
                
                <div class="flex-1 p-6 overflow-y-auto space-y-4" id="chat-messages">
                    {move || messages.get().into_iter().map(|msg: String| {
                        view! { <div class="bg-zinc-800 p-4 rounded-2xl">{msg}</div> }
                    }).collect_view()}
                </div>

                <div class="p-6 border-t border-zinc-800 flex gap-3">
                    <input 
                        type="text"
                        class="flex-1 bg-zinc-800 border border-zinc-700 rounded-2xl px-6 py-4 focus:outline-none focus:border-gold text-white"
                        placeholder="Type message..."
                        prop:value=new_msg
                        on:input=move |ev| set_new_msg.set(event_target_value(&ev))
                        on:keydown=move |ev| {
                            if ev.key() == "Enter" {
                                // Send via WS + update UI
                                set_messages.update(|msgs| msgs.push(new_msg.get()));
                                set_new_msg.set("".to_string());
                            }
                        }
                    />
                    <button 
                        class="bg-gold hover:bg-yellow-400 text-black font-semibold px-10 rounded-2xl transition"
                        on:click=move |_| {
                            // WS send logic
                            set_messages.update(|msgs| msgs.push(new_msg.get()));
                            set_new_msg.set("".to_string());
                        }
                    >
                        "SEND"
                    </button>
                </div>
            </div>
        </div>
    }
}
[dependencies]
leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_meta = "0.7"
leptos_router = "0.7"
axum = { version = "0.8", features = ["ws"] }
tokio = { version = "1", features = ["full"] }
tower = "0.5"
tower-http = { version = "0.6", features = ["fs", "trace"] }
serde = { version = "1", features = ["derive"] }
futures = "0.3"
# For HLS (optional advanced transcoding)
# hlskit = "0.1"  # or use external ffmpeg process
use std::sync::Arc;
use tokio::sync::{broadcast, Mutex};

#[derive(Clone)]
pub struct AppState {
    pub tx: broadcast::Sender<String>,  // Chat messages
}

impl AppState {
    pub fn new() -> Arc<Self> {
        let (tx, _) = broadcast::channel(100);
        Arc::new(Self { tx })
    }
}
use axum::{
    extract::{State, WebSocketUpgrade},
    response::IntoResponse,
    routing::get,
    Router,
};
use axum::extract::ws::{Message, WebSocket};
use futures::{SinkExt, StreamExt};
use std::sync::Arc;
use crate::app_state::AppState;

pub fn ws_routes(state: Arc<AppState>) -> Router {
    Router::new()
        .route("/ws/chat", get(ws_handler))
        .with_state(state)
}

async fn ws_handler(
    ws: WebSocketUpgrade,
    State(state): State<Arc<AppState>>,
) -> impl IntoResponse {
    ws.on_upgrade(move |socket| handle_socket(socket, state))
}

async fn handle_socket(mut socket: WebSocket, state: Arc<AppState>) {
    let mut rx = state.tx.subscribe();

    // Task 1: Forward broadcast messages to client
    let mut send_task = tokio::spawn(async move {
        while let Ok(msg) = rx.recv().await {
            if socket.send(Message::Text(msg)).await.is_err() {
                break;
            }
        }
    });

    // Task 2: Receive messages from client and broadcast
    let mut recv_task = tokio::spawn(async move {
        while let Some(Ok(Message::Text(text))) = socket.recv().await {
            let _ = state.tx.send(text);  // Broadcast to all
        }
    });

    // Wait for either task to finish
    tokio::select! {
        _ = &mut send_task => recv_task.abort(),
        _ = &mut recv_task => send_task.abort(),
    }
}
use axum::{
    extract::State,
    routing::get,
    Router,
    response::{IntoResponse, Response},
    http::StatusCode,
};
use tower_http::services::ServeDir;
use std::path::Path;
use std::sync::Arc;
use crate::app_state::AppState;

// Simple static HLS serve (segments generated by ffmpeg)
pub fn hls_routes() -> Router {
    Router::new()
        .nest_service("/hls", ServeDir::new("public/hls"))
        // Or custom handler for dynamic
        .route("/hls/playlist.m3u8", get(hls_playlist))
}

async fn hls_playlist() -> Result<Response, StatusCode> {
    // In production, generate dynamically or serve pre-made
    let playlist = r#"#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:6
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:6.0,
segment0.ts
#EXTINF:6.0,
segment1.ts
"#;
    Ok((axum::http::header::CONTENT_TYPE, "application/vnd.apple.mpegurl"), playlist).into_response()
}
ffmpeg -i YOUR_INPUT_SOURCE \
  -c:v libx264 -preset veryfast -tune zerolatency \
  -c:a aac -f hls -hls_time 6 -hls_list_size 10 \
  -hls_segment_filename "public/hls/segment%03d.ts" \
  public/hls/playlist.m3u8
  #[cfg(feature = "ssr")]
#[tokio::main]
async fn main() {
    let app_state = AppState::new();

    let app = Router::new()
        .merge(ws_routes(app_state.clone()))
        .merge(hls_routes())
        // Leptos routes (your existing setup)
        .leptos_routes(/* ... */)
        .fallback(leptos_axum::file_and_error_handler(shell))
        .with_state(/* leptos options */);

    let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
    println!("🚀 Server running on http://localhost:3000");
    axum::serve(listener, app).await.unwrap();
}
use leptos::*;
use wasm_bindgen::prelude::*;

#[component]
pub fn LiveSection() -> impl IntoView {
    let (messages, set_messages) = create_signal(vec![]);
    let (new_msg, set_new_msg) = create_signal(String::new());
    let ws = create_rw_signal(None::<web_sys::WebSocket>);

    // Connect WebSocket on mount
    create_effect(move |_| {
        if let Ok(socket) = web_sys::WebSocket::new("ws://localhost:3000/ws/chat") {
            let cloned_set = set_messages;
            let closure = Closure::wrap(Box::new(move |e: web_sys::MessageEvent| {
                if let Some(text) = e.data().as_string() {
                    cloned_set.update(|msgs| msgs.push(text));
                }
            }) as Box<dyn FnMut(_)>);
            socket.set_onmessage(Some(closure.as_ref().unchecked_ref()));
            closure.forget();
            ws.set(Some(socket));
        }
    });

    view! {
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Live Video - HLS */}
            <div class="lg:col-span-2 bg-black rounded-3xl overflow-hidden relative aspect-video">
                <video 
                    class="w-full h-full"
                    autoplay=true 
                    controls=true
                    src="/hls/playlist.m3u8"
                ></video>
                <div class="absolute top-6 left-6 bg-red-600 px-4 py-1 rounded-full text-sm font-bold flex items-center gap-2">
                    <span class="animate-pulse">●</span> LIVE
                </div>
            </div>

            {/* Chat */}
            <div class="bg-zinc-900 rounded-3xl flex flex-col h-[600px]">
                <div class="p-6 border-b">"Live Chat"</div>
                <div class="flex-1 p-6 overflow-y-auto space-y-3">
                    {move || messages.get().into_iter().map(|m| view! { <div class="bg-zinc-800 p-3 rounded-2xl">{m}</div> }).collect_view()}
                </div>
                <div class="p-6 border-t flex gap-3">
                    <input 
                        class="flex-1 bg-zinc-800 rounded-2xl px-6 py-4 text-white"
                        placeholder="Type a message..."
                        prop:value=new_msg
                        on:input=move |ev| set_new_msg.set(event_target_value(&ev))
                        on:keydown=move |ev| {
                            if ev.key() == "Enter" && !new_msg.get().is_empty() {
                                if let Some(socket) = ws.get() {
                                    let _ = socket.send_with_str(&new_msg.get());
                                }
                                set_new_msg.set("".to_string());
                            }
                        }
                    />
                    <button 
                        class="bg-gold text-black px-8 rounded-2xl font-semibold"
                        on:click=move |_| {
                            if let Some(socket) = ws.get() {
                                let _ = socket.send_with_str(&new_msg.get());
                            }
                            set_new_msg.set("".to_string());
                        }
                    >
                        "SEND"
                    </button>
                </div>
            </div>
        </div>
    }
}
[dependencies]
leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_meta = "0.7"
leptos_router = "0.7"
leptos_ws = "0.1"  # or latest compatible version for signal sync

axum = { version = "0.8", features = ["ws"] }
tokio = { version = "1", features = ["full"] }
tower-http = { version = "0.6", features = ["fs", "trace"] }

redis = { version = "0.29", features = ["tokio-comp", "streams"] }  # For Pub/Sub
serde = { version = "1", features = ["derive"] }
futures = "0.3"
webrtc = "0.13"  # webrtc-rs for low-latency
# Optional: webrtc-rs related peers, etc.
use std::sync::Arc;
use redis::AsyncCommands;
use tokio::sync::Mutex;

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<redis::Client>,
    pub chat_channel: String,  // e.g., "nexus:chat:live"
}

impl AppState {
    pub async fn new(redis_url: &str) -> Arc<Self> {
        let client = redis::Client::open(redis_url).unwrap();
        Arc::new(Self {
            redis_client: Arc::new(client),
            chat_channel: "nexus:chat:live".to_string(),
        })
    }
}
use axum::{
    extract::{State, WebSocketUpgrade},
    response::IntoResponse,
    routing::get,
    Router,
};
use axum::extract::ws::{Message, WebSocket};
use futures::{SinkExt, StreamExt};
use std::sync::Arc;
use crate::app_state::AppState;

pub fn ws_routes(state: Arc<AppState>) -> Router {
    Router::new()
        .route("/ws/chat", get(ws_handler))
        .with_state(state)
}

async fn ws_handler(
    ws: WebSocketUpgrade,
    State(state): State<Arc<AppState>>,
) -> impl IntoResponse {
    ws.on_upgrade(move |socket| handle_socket(socket, state))
}

async fn handle_socket(mut socket: WebSocket, state: Arc<AppState>) {
    let mut conn = state.redis_client.get_async_connection().await.unwrap();
    let mut pubsub = conn.as_pubsub();
    let _ = pubsub.subscribe(&state.chat_channel).await;

    // Send task: Redis -> Client
    let mut send_task = tokio::spawn(async move {
        let mut pubsub_stream = pubsub.into_on_message();
        while let Some(msg) = pubsub_stream.next().await {
            if let Ok(payload) = msg.get_payload::<String>() {
                let _ = socket.send(Message::Text(payload)).await;
            }
        }
    });

    // Receive task: Client -> Redis Publish
    let mut recv_task = tokio::spawn(async move {
        while let Some(Ok(Message::Text(text))) = socket.recv().await {
            let mut conn = state.redis_client.get_async_connection().await.unwrap();
            let _: () = conn.publish(&state.chat_channel, text).await.unwrap();
        }
    });

    tokio::select! {
        _ = &mut send_task => recv_task.abort(),
        _ = &mut recv_task => send_task.abort(),
    }
}
use leptos::*;
use leptos_ws::{use_websocket, WebSocketReadyState};

#[component]
pub fn LiveSection() -> impl IntoView {
    let (messages, set_messages) = create_signal::<Vec<String>>(vec![]);
    let (new_msg, set_new_msg) = create_signal(String::new());

    // leptos_ws setup
    let (ws, ws_tx) = use_websocket("/ws/chat");  // Adjust path if needed

    // Listen for messages
    create_effect(move |_| {
        if ws.ready_state.get() == WebSocketReadyState::Open {
            // Subscribe to incoming messages via leptos_ws hooks
        }
    });

    // On message handler (adapt per leptos_ws API - typically provides a signal)
    // Example pattern:
    let incoming = leptos_ws::use_websocket_message(); // pseudo; check docs

    view! {
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Video - see WebRTC below */}
            {/* Chat */}
            <div class="bg-zinc-900 rounded-3xl flex flex-col h-[600px]">
                <div class="flex-1 p-6 overflow-y-auto space-y-3">
                    {move || messages.get().into_iter().map(|m| view! {<div>{m}</div>}).collect_view()}
                </div>
                <div class="p-6 border-t flex gap-3">
                    <input 
                        prop:value=new_msg
                        on:input=move |ev| set_new_msg.set(event_target_value(&ev))
                        class="flex-1 bg-zinc-800 rounded-2xl px-6 py-4"
                    />
                    <button 
                        class="bg-gold text-black px-8 rounded-2xl"
                        on:click=move |_| {
                            if let Some(tx) = &ws_tx {
                                let _ = tx.send(new_msg.get());
                            }
                            set_new_msg.set("".to_string());
                        }
                    >
                        "SEND"
                    </button>
                </div>
            </div>
        </div>
    }
}
use webrtc::peer_connection::{RTCPeerConnection, RTCPeerConnectionConfig};
use webrtc::api::APIBuilder;
// etc.

async fn webrtc_offer_handler(/* ... */) -> /* SDP response */ {
    // Create peer connection, add tracks from media source (e.g., ffmpeg pipe or GStreamer)
    // Exchange SDP offer/answer
}
// In Leptos component - use JS interop or wasm-bindgen for webrtc
// Example:
let peer_connection = /* create RTCPeerConnection */;
let video = document.get_element_by_id("live-video").unwrap();
// Add transceiver, set remote description from server, etc.
#[tokio::main]
async fn main() {
    let app_state = AppState::new("redis://127.0.0.1:6379").await;

    let app = Router::new()
        .merge(ws_routes(app_state.clone()))
        // Add WebRTC routes
        .fallback(/* leptos handler */);

    // ...
}
[dependencies]
leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_router = "0.7"
leptos_ws = "0.1"  # or leptos-ws-pro for production

axum = { version = "0.
use std::sync::Arc;
use redis::Client;
use sfu::SFU;  // From webrtc-rs/sfu

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<Client>,
    pub sfu: Arc<SFU>,           // Shared SFU instance
    pub chat_channel: String,
}

impl AppState {
    pub async fn new(redis_url: &str) -> Arc<Self> {
        let redis_client = Arc::new(Client::open(redis_url).unwrap());
        let sfu = Arc::new(SFU::new(/* config */));  // Initialize with ICE servers, etc.

        Arc::new(Self {
            redis_client,
            sfu,
            chat_channel: "nexus:live".to_string(),
        })
    }
}
use axum::{
    extract::{State, Json, Path},
    http::StatusCode,
    response::IntoResponse,
    Router,
    routing::{post, get},
};
use serde::{Deserialize, Serialize};
use webrtc::peer_connection::RTCPeerConnection;
use sfu::{Publisher, Subscriber};  // SFU abstractions

#[derive(Deserialize)]
pub struct WhipOffer {
    pub sdp: String,
    pub auth_token: String,  // Blockchain-signed JWT or wallet sig
}

#[derive(Serialize)]
pub struct WhipAnswer { pub sdp: String }

async fn whip_ingest(
    State(state): State<Arc<AppState>>,
    Json(offer): Json<WhipOffer>,
) -> Result<Json<WhipAnswer>, StatusCode> {
    // Verify blockchain auth token (e.g., verify signature)
    if !verify_blockchain_auth(&offer.auth_token) {
        return Err(StatusCode::UNAUTHORIZED);
    }

    let pc = state.sfu.create_publisher().await;  // SFU Publisher
    // Set remote description from offer.sdp, gather ICE, etc.
    let answer = pc.create_answer(/* ... */).await;

    Ok(Json(WhipAnswer { sdp: answer }))
}

async fn whep_egress(
    State(state): State<Arc<AppState>>,
    Path(stream_id): Path<String>,
) -> Result<Json<WhipAnswer>, StatusCode> {
    let subscriber = state.sfu.create_subscriber(&stream_id).await;
    let offer = /* generate offer */;
    Ok(Json(WhipAnswer { sdp: offer }))
}

// Blockchain auth helper (example - integrate real crypto lib like k256)
fn verify_blockchain_auth(token: &str) -> bool {
    // Verify JWT or ECDSA signature from wallet
    true  // Placeholder
}

pub fn webrtc_routes(state: Arc<AppState>) -> Router {
    Router::new()
        .route("/whip", post(whip_ingest))
        .route("/whep/:stream_id", get(whep_egress))
        .with_state(state)
}
#[tokio::main]
async fn main() {
    let state = AppState::new("redis://127.0.0.1:6379").await;
    let app = Router::new()
        .merge(ws_routes(state.clone()))      // Chat (below)
        .merge(webrtc_routes(state.clone())) // WHIP/WHEP
        .leptos_routes_with_context(/* ... */);
    // ...
}
use leptos::*;
use leptos_ws::{provide_websocket, use_channel_signal, ChannelSignal};
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Clone)]
pub struct ChatMessage { pub user: String, pub text: String, pub timestamp: u64 }

#[component]
pub fn LiveSection() -> impl IntoView {
    provide_websocket();  // Leptos context

    let (messages, set_messages) = create_signal::<Vec<ChatMessage>>(vec![]);
    let (new_msg, set_new_msg) = create_signal(String::new());

    // leptos_ws Channel Signal (server-synced)
    let chat_signal: ChannelSignal<ChatMessage> = use_channel_signal("live_chat");

    // Reactively update local messages from server signal
    create_effect(move |_| {
        if let Some(msg) = chat_signal.get() {
            set_messages.update(|msgs| msgs.push(msg));
        }
    });

    view! {
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Live Video - WHEP WebRTC */}
            <div class="lg:col-span-2 bg-black rounded-3xl overflow-hidden relative aspect-video">
                <video id="live-video" class="w-full h-full" autoplay controls></video>
                <div class="absolute top-6 left-6 bg-red-600 px-4 py-1 rounded-full flex items-center gap-2">
                    <span class="animate-pulse">●</span> LIVE
                </div>
            </div>

            {/* Chat */}
            <div class="bg-zinc-900 rounded-3xl flex flex-col h-[600px]">
                <div class="p-6 border-b font-semibold">"Live Chat"</div>
                <div class="flex-1 p-6 overflow-y-auto space-y-3">
                    {move || messages.get().into_iter().map(|m| view! {
                        <div class="bg-zinc-800 p-4 rounded-2xl">
                            <strong>{m.user}</strong>: {m.text}
                        </div>
                    }).collect_view()}
                </div>

                <div class="p-6 border-t flex gap-3">
                    <input 
                        class="flex-1 bg-zinc-800 rounded-2xl px-6 py-4 text-white"
                        placeholder="Type message..."
                        prop:value=new_msg
                        on:input=move |ev| set_new_msg.set(event_target_value(&ev))
                    />
                    <button 
                        class="bg-gold text-black px-8 rounded-2xl font-semibold"
                        on:click=move |_| {
                            let msg = ChatMessage {
                                user: "You".to_string(),
                                text: new_msg.get(),
                                timestamp: js_sys::Date::now() as u64,
                            };
                            // Send via leptos_ws channel
                            let _ = chat_signal.send(msg.clone());
                            set_new_msg.set("".to_string());
                        }
                    >
                        "SEND"
                    </button>
                </div>
            </div>
        </div>
    }
}
[dependencies]
mediasoup = "0.22"  # Official Rust crate
# or mediasoup-sys if needed for lower level

leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_ws = "0.1"

axum = { version = "0.8", features = ["ws", "json"] }
tokio = { version = "1", features = ["full"] }

redis = { version = "0.29", features = ["tokio-comp"] }
serde = { version = "1", features = ["derive"] }
uuid = { version = "1", features = ["v4"] }

# Blockchain
solana-sdk = "2.0"          # For Solana Ed25519
ethers = { version = "2", features = ["rustls"] }  # Ethereum/BNB/ARB (EVM)
bs58 = "0.5"
ed25519-dalek = "2"
k256 = "0.13"               # For EVM recovery
use std::sync::Arc;
use mediasoup::prelude::*;
use redis::Client;

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<Client>,
    pub worker: Arc<Worker>,
    pub router: Arc<Router>,
    pub chat_channel: String,
    pub luminex_core_ca: String,  // "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump"
}

impl AppState {
    pub async fn new(redis_url: &str) -> Arc<Self> {
        let redis_client = Arc::new(Client::open(redis_url).unwrap());

        // Mediasoup Worker + Router
        let worker_manager = WorkerManager::new();
        let worker = Arc::new(worker_manager.create_worker(WorkerSettings::default()).await.unwrap());
        let router = Arc::new(worker.create_router(RouterOptions::default()).await.unwrap());

        Arc::new(Self {
            redis_client,
            worker,
            router,
            chat_channel: "nexus:live".to_string(),
            luminex_core_ca: "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump".to_string(),
        })
    }
}
use axum::http::StatusCode;
use serde::{Deserialize, Serialize};

#[derive(Deserialize)]
pub struct WalletAuth {
    pub chain: String,      // "solana" | "evm" (eth/bnb/arb)
    pub address: String,
    pub signature: String,
    pub message: String,    // Nonce/challenge + LuminexCore metadata
    pub token_metadata: Option<String>,  // e.g., LuminexCore holdings proof
}

pub async fn verify_wallet_auth(auth: &WalletAuth, state: &AppState) -> Result<(), StatusCode> {
    match auth.chain.as_str() {
        "solana" => verify_solana(&auth.address, &auth.signature, &auth.message),
        "evm" => verify_evm(&auth.address, &auth.signature, &auth.message),
        _ => Err(StatusCode::BAD_REQUEST),
    }?;

    // LuminexCore-specific extension (e.g., token-gated)
    if auth.address.to_uppercase().contains(&state.luminex_core_ca[0..8].to_uppercase()) 
       || auth.token_metadata.as_deref() == Some("LuminexCore") {
        // Grant elevated access / metadata
    }

    Ok(())
}

fn verify_solana(address: &str, sig_b58: &str, message: &str) -> Result<(), StatusCode> {
    use solana_sdk::signature::{Signature, Verifier};
    use ed25519_dalek::{VerifyingKey, Signature as DalekSig};
    use bs58;

    let pubkey_bytes = bs58::decode(address).into_vec().map_err(|_| StatusCode::BAD_REQUEST)?;
    let sig_bytes = bs58::decode(sig_b58).into_vec().map_err(|_| StatusCode::BAD_REQUEST)?;

    let pubkey = VerifyingKey::from_bytes(&pubkey_bytes[0..32].try_into().unwrap()).unwrap();
    let signature = DalekSig::from_bytes(&sig_bytes[0..64].try_into().unwrap());

    pubkey.verify(message.as_bytes(), &signature)
        .map_err(|_| StatusCode::UNAUTHORIZED)
}

fn verify_evm(address: &str, sig_hex: &str, message: &str) -> Result<(), StatusCode> {
    use ethers::types::Signature;
    use ethers::utils::keccak256;

    let sig: Signature = sig_hex.parse().map_err(|_| StatusCode::BAD_REQUEST)?;
    let msg_hash = keccak256(format!("\x19Ethereum Signed Message:\n{}{}", message.len(), message).as_bytes());
    let recovered = sig.recover(msg_hash).map_err(|_| StatusCode::UNAUTHORIZED)?;

    if format!("{:?}", recovered) == address.to_lowercase() {
        Ok(())
    } else {
        Err(StatusCode::UNAUTHORIZED)
    }
}
use axum::{extract::{State, Json, Path}, routing::{post, get}, Router, http::StatusCode};
use mediasoup::prelude::*;
use crate::{AppState, auth::WalletAuth};

async fn whip_ingest(
    State(state): State<Arc<AppState>>,
    Json(payload): Json<(WalletAuth, String)>,  // (auth, sdp_offer)
) -> Result<Json<String>, StatusCode> {  // Return SDP answer
    let (auth, offer_sdp) = payload;
    verify_wallet_auth(&auth, &state).await?;

    // Create Mediasoup Transport + Producer
    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default()).await.unwrap();
    // Set remote description, produce tracks...

    let answer = /* generate answer SDP from Mediasoup */;
    Ok(Json(answer))
}

async fn whep_egress(
    State(state): State<Arc<AppState>>,
    Path(stream_id): Path<String>,
    // Auth header or query for viewer
) -> Result<Json<String>, StatusCode> {
    // Similar: create consumer transport
    Ok(Json("WHEP SDP answer".to_string()))
}

pub fn webrtc_routes(state: Arc<AppState>) -> Router {
    Router::new()
        .route("/whip", post(whip_ingest))
        .route("/whep/:stream_id", get(whep_egress))
        .with_state(state)
}
use leptos::*;
use leptos_ws::use_channel_signal;

#[component]
pub fn LiveSection() -> impl IntoView {
    let (messages, set_messages) = create_signal(vec![]);
    let (wallet, set_wallet) = create_signal(None::<String>);

    // Wallet connect + sign challenge (frontend JS interop for Phantom/MetaMask etc.)

    view! {
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* Video - Mediasoup WebRTC */}
            <div class="lg:col-span-2 bg-black rounded-3xl overflow-hidden relative aspect-video">
                <video id="live-video" class="w-full h-full" autoplay controls></video>
                <div class="absolute top-6 left-6 bg-red-600 px-4 py-1 rounded-full">"LIVE • LuminexCore Feed"</div>
            </div>

            {/* Chat + Trade Desk Terminal (wallet metadata) */}
            <div class="bg-zinc-900 rounded-3xl flex flex-col h-[600px]">
                // ... chat as before
                <div class="p-4 text-xs text-gold">
                    "Authenticated: " {move || wallet.get().unwrap_or_default()} " • LuminexCore Holder"
                </div>
            </div>
        </div>
    }
}
[dependencies]
mediasoup = "0.22"
solana-sdk = "2.0"
solana-client = "2.0"
spl-token-2022 = "4.0"  # For Token Extensions
spl-associated-token-account = "4.0"

leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_ws = "0.1"

axum = { version = "0.8", features = ["ws", "json"] }
tokio = { version = "1", features = ["full"] }
redis = { version = "0.29", features = ["tokio-comp"] }
serde = { version = "1", features = ["derive"] }
ethers = { version = "2", features = ["rustls"] }
uuid = { version = "1", features = ["v4"] }
use std::sync::Arc;
use mediasoup::prelude::*;
use solana_client::rpc_client::RpcClient;
use redis::Client;

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<Client>,
    pub worker: Arc<Worker>,
    pub router: Arc<Router>,
    pub rpc_client: Arc<RpcClient>,  // Solana RPC
    pub luminex_core_mint: String,   // "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump"
    pub chat_channel: String,
}

impl AppState {
    pub async fn new(redis_url: &str, rpc_url: &str) -> Arc<Self> {
        let redis_client = Arc::new(Client::open(redis_url).unwrap());
        let rpc_client = Arc::new(RpcClient::new(rpc_url.to_string()));

        let worker_manager = WorkerManager::new();
        let worker = Arc::new(worker_manager.create_worker(WorkerSettings::default()).await.unwrap());
        let router = Arc::new(worker.create_router(RouterOptions::default()).await.unwrap());

        Arc::new(Self {
            redis_client,
            worker,
            router,
            rpc_client,
            luminex_core_mint: "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump".to_string(),
            chat_channel: "nexus:live".to_string(),
        })
    }
}
use solana_sdk::{pubkey::Pubkey, commitment_config::CommitmentConfig};
use spl_token_2022::extension::{BaseStateWithExtensions, StateWithExtensions};
use crate::AppState;

pub async fn get_sol_balance(state: &AppState, address: &str) -> Result<u64, String> {
    let pubkey = Pubkey::try_from(address).map_err(|e| e.to_string())?;
    Ok(state.rpc_client.get_balance_with_commitment(&pubkey, CommitmentConfig::confirmed()).await?.value)
}

pub async fn get_luminex_balance(state: &AppState, owner: &str) -> Result<u64, String> {
    let owner_pubkey = Pubkey::try_from(owner).map_err(|e| e.to_string())?;
    let mint = Pubkey::try_from(&state.luminex_core_mint[..]).unwrap();
    
    // Associated Token Account
    let ata = spl_associated_token_account::get_associated_token_address_with_program_id(
        &owner_pubkey, &mint, &spl_token_2022::id()
    );
    
    match state.rpc_client.get_token_account_balance(&ata).await {
        Ok(balance) => Ok(balance.amount.parse().unwrap_or(0)),
        Err(_) => Ok(0),  // No account = 0
    }
}

// Token-2022 Extensions example (minting/launchpad)
pub async fn mint_with_extensions(/* params */) {
    // Use spl-token-2022 instructions: MetadataPointer, TransferFee, MintCloseAuthority, etc.
    // Example: Enable permanent delegate, interest-bearing, etc.
}
use mediasoup::prelude::*;
use axum::Json;
use crate::AppState;

#[derive(serde::Deserialize)]
pub struct WebRtcOffer { pub sdp: String, pub auth: WalletAuth /* from previous */ }

pub async fn create_producer(
    state: Arc<AppState>,
    offer: WebRtcOffer,
) -> Result<String, String> {  // Returns answer SDP
    // Auth check first (wallet + LuminexCore holder)
    // ...

    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default()).await.unwrap();
    transport.set_remote_description(&offer.sdp).await.unwrap();

    let producer_options = ProducerOptions::new(
        MediaKind::Video,
        RtpParameters { /* codecs, etc. */ }
    );
    let producer = transport.produce(producer_options).await.unwrap();

    let answer = transport.get_local_description().await.unwrap();
    Ok(answer)
}

// Consumer for viewers (SFU)
pub async fn create_consumer(
    state: Arc<AppState>,
    stream_id: String,  // Producer ID
    rtp_capabilities: RtpCapabilities,
) -> Result<String, String> {
    let transport = state.router.create_webrtc_transport(/* ... */).await.unwrap();
    let consumer = state.router.create_consumer(ConsumerOptions {
        producer_id: ProducerId::from(stream_id),
        rtp_capabilities,
    }).await.unwrap();

    // Start consumer, get SDP answer
    Ok(/* SDP */)
}
.route("/mediasoup/producer", post(producer_handler))
.route("/mediasoup/consumer/:id", post(consumer_handler))
use leptos::*;
use leptos_ws::use_channel_signal;
// ... previous chat/video

#[component]
pub fn TradeDesk() -> impl IntoView {
    let (sol_balance, set_sol) = create_signal(0u64);
    let (luminex_balance, set_luminex) = create_signal(0u64);
    let wallet_address = create_rw_signal(String::new());

    // On wallet connect + sign
    let check_balances = move || {
        // Call server function or API endpoint with wallet
        spawn_local(async move {
            // leptos server fn or fetch to /api/balances?wallet=...
            // Update signals
        });
    };

    view! {
        <div class="bg-zinc-900 p-6 rounded-3xl">
            <h3 class="text-gold text-xl">"Trade Desk • LuminexCore Terminal"</h3>
            <div>"SOL: " {move || sol_balance.get()}</div>
            <div>"LuminexCore (CA: EyCMR...pump): " {move || luminex_balance.get()}</div>
            
            // Multi-chain portfolio (EVM similar with ethers.rs or client)
            // Token-2022 Launchpad UI: Mint form with extensions toggles
            <button on:click=check_balances>"Refresh Balances"</button>
            
            // Minting panel (Token-2022)
            <div>"Launchpad: Enable Metadata, Transfer Fees, etc."</div>
        </div>
    }
}
[dependencies]
mediasoup = "0.22"
solana-sdk = "2.0"
solana-client = "2.0"
spl-token-2022 = "4.0"
spl-associated-token-account = "4.0"
spl-token-metadata = "0.4"  # For Token-2022 metadata

leptos = { version = "0.7", features = ["nightly"] }
leptos_axum = "0.7"
leptos_ws = "0.1"

axum = { version = "0.8", features = ["ws", "json"] }
tokio = { version = "1", features = ["full"] }
redis = { version = "0.29", features = ["tokio-comp"] }
serde = { version = "1", features = ["derive"] }
ethers = { version = "2", features = ["rustls"] }  # EVM (ETH/BNB/ARB)
uuid = { version = "1", features = ["v4"] }
use std::sync::Arc;
use mediasoup::prelude::*;
use solana_client::rpc_client::RpcClient;
use redis::Client;

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<Client>,
    pub worker: Arc<Worker>,
    pub router: Arc<Router>,
    pub rpc_client: Arc<RpcClient>,  // Helius RPC
    pub luminex_core_mint: String,
    pub chat_channel: String,
}

impl AppState {
    pub async fn new(redis_url: &str, helius_rpc_url: &str) -> Arc<Self> {  // e.g., "https://mainnet.helius-rpc.com/?api-key=..."
        let redis_client = Arc::new(Client::open(redis_url).unwrap());
        let rpc_client = Arc::new(RpcClient::new(helius_rpc_url.to_string()));

        let worker_manager = WorkerManager::new();
        let worker = Arc::new(worker_manager.create_worker(WorkerSettings::default()).await.unwrap());
        let router = Arc::new(worker.create_router(RouterOptions::default()).await.unwrap());

        Arc::new(Self {
            redis_client,
            worker,
            router,
            rpc_client,
            luminex_core_mint: "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump".to_string(),
            chat_channel: "nexus:live".to_string(),
        })
    }
}
use solana_sdk::{pubkey::Pubkey, commitment_config::CommitmentConfig};
use spl_token_2022::{extension::{BaseStateWithExtensions, StateWithExtensions}, state::Mint};
use crate::AppState;

pub async fn get_token_data_helius(state: &AppState, mint: &str) -> Result<String, String> {
    // Helius DAS API or getAccountInfo + parsing for Token-2022 extensions
    let pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    let account = state.rpc_client.get_account_with_commitment(&pubkey, CommitmentConfig::confirmed()).await
        .map_err(|e| e.to_string())?;
    
    // Parse Token-2022 extensions (MetadataPointer, TransferHook, etc.)
    let mint_data: StateWithExtensions<Mint> = StateWithExtensions::unpack(&account.value.data)
        .map_err(|e| e.to_string())?;
    
    // Example: Check Transfer Hook
    if let Some(hook) = mint_data.get_extension::<spl_token_2022::extension::transfer_hook::TransferHook>() {
        // hook.program_id for custom logic
    }
    
    Ok("Token metadata + extensions parsed".to_string())  // Extend with full JSON
}

pub async fn get_multi_chain_balances(state: &AppState, wallet: &str) -> Result<serde_json::Value, String> {
    // Solana
    let sol_balance = /* previous get_sol_balance */;
    let luminex_balance = /* previous get_luminex_balance */;
    
    // EVM (ETH/BNB/ARB) via ethers or separate RPC calls
    // Aggregate into portfolio JSON for Trade Desk
    Ok(serde_json::json!({
        "solana": { "SOL": sol_balance, "LuminexCore": luminex_balance },
        "evm": { /* ETH, BNB, ARB balances */ }
    }))
}

// Transfer Hook example (for custom on-transfer logic)
pub fn setup_transfer_hook(/* mint, hook_program */) {
    // Build instruction with spl-token-2022 for TransferHook extension
}
use mediasoup::prelude::*;
use axum::{extract::State, Json};
use serde::{Deserialize, Serialize};
use std::sync::Arc;
use crate::{AppState, auth::WalletAuth};  // From previous

#[derive(Deserialize)]
pub struct ProducerRequest {
    pub auth: WalletAuth,
    pub sdp_offer: String,
    pub rtp_parameters: RtpParameters,  // Video/Audio params
}

#[derive(Serialize)]
pub struct SdpAnswer { pub sdp: String }

pub async fn create_producer_handler(
    State(state): State<Arc<AppState>>,
    Json(req): Json<ProducerRequest>,
) -> Result<Json<SdpAnswer>, axum::http::StatusCode> {
    // 1. Wallet + LuminexCore auth (Solana Token-2022 balance check)
    // verify_wallet_auth + get_luminex_balance > threshold for streaming rights

    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default())
        .await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    transport.set_remote_description(&req.sdp_offer).await
        .map_err(|_| axum::http::StatusCode::BAD_REQUEST)?;

    let producer_options = ProducerOptions::new(
        MediaKind::Video,
        req.rtp_parameters,
    );

    let _producer = transport.produce(producer_options).await
        .map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    let local_desc = transport.get_local_description().await
        .map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    Ok(Json(SdpAnswer { sdp: local_desc }))
}

// Consumer (Viewer)
#[derive(Deserialize)]
pub struct ConsumerRequest {
    pub producer_id: String,
    pub rtp_capabilities: RtpCapabilities,
}

pub async fn create_consumer_handler(
    State(state): State<Arc<AppState>>,
    Json(req): Json<ConsumerRequest>,
) -> Result<Json<SdpAnswer>, axum::http::StatusCode> {
    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default())
        .await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    let consumer = state.router.create_consumer(ConsumerOptions {
        producer_id: ProducerId::from(req.producer_id),
        rtp_capabilities: req.rtp_capabilities,
    }).await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    // Start consumer, etc.
    let local_desc = transport.get_local_description().await
        .map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    Ok(Json(SdpAnswer { sdp: local_desc }))
}
.route("/mediasoup/producer", post(create_producer_handler))
.route("/mediasoup/consumer", post(create_consumer_handler))
// src/app_state.rs
use std::sync::Arc;
use mediasoup::prelude::*;
use solana_client::rpc_client::RpcClient;
use redis::Client;
use ethers::prelude::*;  // For EVM

#[derive(Clone)]
pub struct AppState {
    pub redis_client: Arc<Client>,
    pub worker: Arc<Worker>,
    pub router: Arc<Router>,
    pub rpc_client: Arc<RpcClient>,           // Helius RPC
    pub evm_providers: Arc<EvmProviders>,     // Multi EVM (ETH/BNB/ARB)
    pub luminex_core_mint: String,
    pub chat_channel: String,
}

#[derive(Clone)]
pub struct EvmProviders {
    pub eth: Provider<Http>,
    pub bnb: Provider<Http>,
    pub arb: Provider<Http>,
}

impl AppState {
    pub async fn new(redis_url: &str, helius_rpc: &str) -> Arc<Self> {
        let redis_client = Arc::new(Client::open(redis_url).unwrap());
        let rpc_client = Arc::new(RpcClient::new(helius_rpc.to_string()));

        // EVM Providers (use your own RPC URLs or Infura/Alchemy)
        let evm_providers = Arc::new(EvmProviders {
            eth: Provider::<Http>::try_from("https://eth.llamarpc.com").unwrap(),
            bnb: Provider::<Http>::try_from("https://bsc-dataseed.binance.org").unwrap(),
            arb: Provider::<Http>::try_from("https://arb1.arbitrum.io/rpc").unwrap(),
        });

        // Mediasoup setup (as before)
        let worker_manager = WorkerManager::new();
        let worker = Arc::new(worker_manager.create_worker(WorkerSettings::default()).await.unwrap());
        let router = Arc::new(worker.create_router(RouterOptions::default()).await.unwrap());

        Arc::new(Self {
            redis_client,
            worker,
            router,
            rpc_client,
            evm_providers,
            luminex_core_mint: "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump".to_string(),
            chat_channel: "nexus:live".to_string(),
        })
    }
}
use solana_sdk::{pubkey::Pubkey, commitment_config::CommitmentConfig};
use spl_token_2022::extension::{BaseStateWithExtensions, StateWithExtensions, transfer_hook::TransferHook};
use crate::AppState;

// Detect / Process Transfer Hook on Token-2022 mints
pub async fn process_transfer_hook_event(state: &AppState, mint_address: &str, tx_signature: &str) -> Result<(), String> {
    let mint_pubkey = Pubkey::try_from(mint_address).map_err(|e| e.to_string())?;
    let account = state.rpc_client.get_account_with_commitment(&mint_pubkey, CommitmentConfig::confirmed())
        .await.map_err(|e| e.to_string())?;

    let mint_data: StateWithExtensions<spl_token_2022::state::Mint> = 
        StateWithExtensions::unpack(&account.value.data).map_err(|e| e.to_string())?;

    if let Some(hook) = mint_data.get_extension::<TransferHook>() {
        // Custom logic: e.g., royalty enforcement, tax, LuminexCore holder check
        println!("Transfer Hook detected for mint {} -> Program: {:?}", mint_address, hook.program_id);

        // Example: Trigger on-chain CPI or off-chain notification via Redis
        let _: () = state.redis_client.get_async_connection().await.unwrap()
            .publish(&state.chat_channel, format!("Hook event on {}: {}", mint_address, tx_signature))
            .await.unwrap();
    }

    Ok(())
}

// Helius-enhanced balance + recent transfers (great for Trade Desk)
pub async fn get_helius_token_data(state: &AppState, wallet: &str, mint: Option<&str>) -> Result<serde_json::Value, String> {
    // Use Helius DAS or getProgramAccounts + parsing for Token-2022
    // For real-time: Set up webhook for TOKEN_TRANSFER / TRANSFER_HOOK events
    let wallet_pubkey = Pubkey::try_from(wallet).map_err(|e| e.to_string())?;
    let balance = state.rpc_client.get_token_account_balance(&wallet_pubkey).await
        .map_err(|e| e.to_string())?;

    Ok(serde_json::json!({
        "balance": balance.amount,
        "mint": mint,
        "extensions": "TransferHook + Metadata + Fees"  // Parsed
    }))
}
use ethers::prelude::*;
use crate::AppState;

#[derive(serde::Serialize)]
pub struct EvmPortfolio {
    pub eth_balance: String,
    pub bnb_balance: String,
    pub arb_balance: String,
    // Add token balances via contract calls
}

pub async fn get_evm_balances(state: &AppState, address: &str) -> Result<EvmPortfolio, String> {
    let addr: Address = address.parse().map_err(|e| e.to_string())?;

    let eth_balance = state.evm_providers.eth.get_balance(addr, None).await
        .map_err(|e| e.to_string())?;
    let bnb_balance = state.evm_providers.bnb.get_balance(addr, None).await
        .map_err(|e| e.to_string())?;
    let arb_balance = state.evm_providers.arb.get_balance(addr, None).await
        .map_err(|e| e.to_string())?;

    Ok(EvmPortfolio {
        eth_balance: format_ether(eth_balance),
        bnb_balance: format_ether(bnb_balance),
        arb_balance: format_ether(arb_balance),
    })
}

// Example ERC20 balance (extend for any token)
async fn get_erc20_balance(provider: &Provider<Http>, token_addr: Address, wallet: Address) -> U256 {
    // Use Contract ABI or simple balanceOf call
    // ...
    U256::zero()
}
// In TradeDesk component
let fetch_portfolio = move || {
    spawn_local(async move {
        // leptos server fn calling get_multi_chain_balances (Solana + EVM)
        // Update signals for SOL, LuminexCore, ETH/BNB/ARB
    });
};
// src/mediasoup_handler.rs
use mediasoup::prelude::*;
use axum::{extract::State, Json};
use serde::{Deserialize, Serialize};
use std::sync::Arc;
use crate::AppState;

#[derive(Deserialize)]
pub struct ProducerRequest {
    pub auth: WalletAuth,  // Includes Solana/EVM signature
    pub sdp_offer: String,
    pub rtp_parameters: RtpParameters,
}

#[derive(Serialize)]
pub struct SdpAnswer { pub sdp: String, pub producer_id: String }

pub async fn create_producer_handler(
    State(state): State<Arc<AppState>>,
    Json(req): Json<ProducerRequest>,
) -> Result<Json<SdpAnswer>, axum::http::StatusCode> {
    // Wallet auth + LuminexCore balance check (Solana Token-2022)
    // ...

    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default())
        .await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    transport.set_remote_description(&req.sdp_offer).await
        .map_err(|_| axum::http::StatusCode::BAD_REQUEST)?;

    let producer = transport.produce(ProducerOptions::new(
        MediaKind::Video,
        req.rtp_parameters,
    )).await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    let local_desc = transport.get_local_description().await
        .map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    Ok(Json(SdpAnswer { 
        sdp: local_desc, 
        producer_id: producer.id().to_string() 
    }))
}

// Consumer (Viewers)
#[derive(Deserialize)]
pub struct ConsumerRequest {
    pub producer_id: String,
    pub rtp_capabilities: RtpCapabilities,
}

pub async fn create_consumer_handler(
    State(state): State<Arc<AppState>>,
    Json(req): Json<ConsumerRequest>,
) -> Result<Json<SdpAnswer>, axum::http::StatusCode> {
    let transport = state.router.create_webrtc_transport(WebRtcTransportOptions::default())
        .await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    let consumer = state.router.create_consumer(ConsumerOptions {
        producer_id: ProducerId::from(req.producer_id),
        rtp_capabilities: req.rtp_capabilities,
    }).await.map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    let local_desc = transport.get_local_description().await
        .map_err(|_| axum::http::StatusCode::INTERNAL_SERVER_ERROR)?;

    Ok(Json(SdpAnswer { sdp: local_desc, producer_id: "".to_string() }))
}
[dependencies]
# ... existing
axum = { version = "0.8", features = ["ws", "json", "headers"] }
serde_json = "1.0"
spl-token-2022 = "4.0"
ethers = { version = "2", features = ["rustls"] }
# For webhook signature verification (optional but recommended)
hmac = "0.12"
sha2 = "0.10"
use axum::{
    extract::{State, Json},
    http::StatusCode,
    response::IntoResponse,
    Router, routing::post,
};
use serde::{Deserialize, Serialize};
use std::sync::Arc;
use crate::AppState;

#[derive(Deserialize, Debug)]
pub struct HeliusWebhookPayload {
    pub webhook_id: String,
    pub transaction: Option<serde_json::Value>, // Parsed tx
    pub transaction_type: String, // e.g., "TRANSFER", "TOKEN_MINT"
    pub token_transfers: Option<Vec<TokenTransfer>>,
    // Add more fields as needed from Helius schema
}

#[derive(Deserialize, Debug)]
pub struct TokenTransfer {
    pub mint: String,
    pub from_user_account: String,
    pub to_user_account: String,
    pub amount: u64,
    // Token-2022 specific
}

pub async fn helius_webhook_handler(
    State(state): State<Arc<AppState>>,
    Json(payload): Json<HeliusWebhookPayload>,
) -> impl IntoResponse {
    // Optional: Verify Helius signature via headers (auth_header set in webhook creation)

    println!("Helius Webhook: {:?}", payload.transaction_type);

    // Process Transfer Hook related events
    if payload.transaction_type == "TRANSFER" || payload.transaction_type.contains("TOKEN") {
        if let Some(transfers) = payload.token_transfers {
            for transfer in transfers {
                if transfer.mint == state.luminex_core_mint {
                    // Trigger Token-2022 hook logic / notifications
                    let _ = crate::solana::process_transfer_hook_event(&state, &transfer.mint, "webhook-tx").await;
                    
                    // Broadcast via Redis / leptos_ws chat
                    let _: () = state.redis_client.get_async_connection().await.unwrap()
                        .publish(&state.chat_channel, format!("LuminexCore Transfer: {} → {}", transfer.from_user_account, transfer.to_user_account))
                        .await.unwrap();
                }
            }
        }
    }

    // Handle TOKEN_MINT, etc. for Launchpad
    StatusCode::OK
}

pub fn helius_webhook_routes(state: Arc<AppState>) -> Router {
    Router::new()
        .route("/webhook/helius", post(helius_webhook_handler))
        .with_state(state)
}
use ethers::prelude::*;
use serde::Serialize;
use crate::AppState;

#[derive(Serialize, Clone)]
pub struct Erc20Balance {
    pub token_symbol: String,
    pub balance: String,
    pub address: String,
}

pub async fn get_erc20_balance(
    provider: &Provider<Http>,
    token_address: &str,
    wallet_address: &str,
) -> Result<Erc20Balance, String> {
    let token_addr: Address = token_address.parse().map_err(|e| e.to_string())?;
    let wallet: Address = wallet_address.parse().map_err(|e| e.to_string())?;

    // Simple balanceOf call (extend with full ABI for symbol/decimals)
    let balance = provider.get_balance(wallet, None).await
        .map_err(|e| e.to_string())?; // For native, or use contract for ERC20

    // For true ERC20 (recommended):
    // Use abigen! or Contract for balanceOf

    Ok(Erc20Balance {
        token_symbol: "EXAMPLE".to_string(), // Fetch via symbol()
        balance: format_ether(balance),
        address: token_address.to_string(),
    })
}

pub async fn get_all_evm_balances(state: &AppState, wallet: &str) -> Result<serde_json::Value, String> {
    let eth = get_erc20_balance(&state.evm_providers.eth, "NATIVE", wallet).await?;
    let bnb = get_erc20_balance(&state.evm_providers.bnb, "NATIVE", wallet).await?;
    let arb = get_erc20_balance(&state.evm_providers.arb, "NATIVE", wallet).await?;

    Ok(serde_json::json!({
        "eth": eth,
        "bnb": bnb,
        "arb": arb,
        // Add specific ERC20s
    }))
}
use spl_token_2022::{
    extension::{BaseStateWithExtensions, StateWithExtensions, transfer_hook::TransferHook, metadata_pointer::MetadataPointer, mint_close_authority::MintCloseAuthority},
    state::Mint,
};
use solana_sdk::{pubkey::Pubkey, instruction::Instruction};

// Full extension support for Launchpad / Trade Desk
pub async fn initialize_token_with_extensions(
    state: &AppState,
    // params: name, symbol, metadata_uri, enable_transfer_hook, etc.
) -> Result<Vec<Instruction>, String> {
    // Example: Enable multiple extensions
    let mut instructions = vec![];

    // Metadata extension
    // Transfer Hook (Helius-monitored)
    // Permanent Delegate, Interest Bearing, etc.

    // Build via spl-token-2022 instruction builders
    Ok(instructions)
}

pub async fn process_transfer_hook_event(state: &AppState, mint: &str, tx_sig: &str) -> Result<(), String> {
    let mint_pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    let account = state.rpc_client.get_account_with_commitment(&mint_pubkey, CommitmentConfig::confirmed())
        .await.map_err(|e| e.to_string())?;

    let mint_data: StateWithExtensions<Mint> = StateWithExtensions::unpack(&account.value.data)
        .map_err(|e| e.to_string())?;

    if let Some(hook) = mint_data.get_extension::<TransferHook>() {
        // Custom logic: royalty, tax, compliance, LuminexCore holder verification
        println!("✅ Transfer Hook triggered for {} (Program: {:?})", mint, hook.program_id);
        // e.g., call custom hook program or notify UI
    }

    if let Some(metadata) = mint_data.get_extension::<MetadataPointer>() {
        // Handle on-chain metadata
    }

    Ok(())
}
let state = AppState::new(redis_url, helius_rpc).await;
let app = Router::new()
    .merge(helius_webhook_routes(state.clone()))
    .merge(webrtc_routes(state.clone()))  // Mediasoup
    // ... leptos routes
    .with_state(state);
    use ethers::{
    prelude::*,
    contract::abigen,
    types::{Address, U256},
};
use serde::Serialize;
use std::sync::Arc;
use crate::AppState;

// Generate type-safe ERC20 bindings using abigen!
abigen!(
    ERC20,
    r#"[
        {
            "constant": true,
            "inputs": [{"name": "owner","type": "address"}],
            "name": "balanceOf",
            "outputs": [{"name": "","type": "uint256"}],
            "payable": false,
            "stateMutability": "view",
            "type": "function"
        },
        {
            "constant": true,
            "inputs": [],
            "name": "decimals",
            "outputs": [{"name": "","type": "uint8"}],
            "payable": false,
            "stateMutability": "view",
            "type": "function"
        },
        {
            "constant": true,
            "inputs": [],
            "name": "symbol",
            "outputs": [{"name": "","type": "string"}],
            "payable": false,
            "stateMutability": "view",
            "type": "function"
        },
        {
            "constant": true,
            "inputs": [],
            "name": "name",
            "outputs": [{"name": "","type": "string"}],
            "payable": false,
            "stateMutability": "view",
            "type": "function"
        }
    ]"#
);

#[derive(Serialize, Clone, Debug)]
pub struct Erc20TokenBalance {
    pub symbol: String,
    pub name: String,
    pub balance: String,        // Formatted with decimals
    pub raw_balance: U256,
    pub token_address: String,
    pub decimals: u8,
}

pub async fn get_erc20_balance(
    provider: &Provider<Http>,
    token_address: &str,
    wallet_address: &str,
) -> Result<Erc20TokenBalance, String> {
    let token_addr: Address = token_address.parse().map_err(|e| e.to_string())?;
    let wallet: Address = wallet_address.parse().map_err(|e| e.to_string())?;

    let contract = ERC20::new(token_addr, Arc::new(provider.clone()));

    let raw_balance = contract.balance_of(wallet).call().await
        .map_err(|e| e.to_string())?;

    let decimals = contract.decimals().call().await
        .map_err(|e| e.to_string())?;

    let symbol = contract.symbol().call().await
        .map_err(|e| e.to_string())?;

    let name = contract.name().call().await
        .map_err(|e| e.to_string())?;

    let formatted = ethers::utils::format_units(raw_balance, decimals as u32)
        .unwrap_or_else(|_| raw_balance.to_string());

    Ok(Erc20TokenBalance {
        symbol,
        name,
        balance: formatted,
        raw_balance,
        token_address: token_address.to_string(),
        decimals,
    })
}

// Native token balance (ETH/BNB/ARB)
pub async fn get_native_balance(
    provider: &Provider<Http>,
    wallet_address: &str,
) -> Result<String, String> {
    let wallet: Address = wallet_address.parse().map_err(|e| e.to_string())?;
    let balance = provider.get_balance(wallet, None).await
        .map_err(|e| e.to_string())?;
    Ok(ethers::utils::format_ether(balance))
}
pub async fn get_full_evm_portfolio(state: &AppState, wallet: &str) -> Result<serde_json::Value, String> {
    let eth_native = get_native_balance(&state.evm_providers.eth, wallet).await?;
    let bnb_native = get_native_balance(&state.evm_providers.bnb, wallet).await?;
    let arb_native = get_native_balance(&state.evm_providers.arb, wallet).await?;

    // Example popular ERC20s (add more as needed)
    let usdc_eth = get_erc20_balance(&state.evm_providers.eth, "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48", wallet).await
        .unwrap_or_default(); // fallback

    Ok(serde_json::json!({
        "native": {
            "ETH": eth_native,
            "BNB": bnb_native,
            "ARB": arb_native,
        },
        "tokens": {
            "USDC_ETH": usdc_eth,
            // Add LuminexCore EVM version or other tokens here
        }
    }))
}
#[server]
pub async fn get_portfolio(wallet: String) -> Result<serde_json::Value, ServerFnError> {
    let state = /* extract from context or use_state */;
    let solana_data = /* call solana balance + Token-2022 */;
    let evm_data = get_full_evm_portfolio(&state, &wallet).await
        .map_err(|e| ServerFnError::ServerError(e))?;
    
    Ok(serde_json::json!({ "solana": solana_data, "evm": evm_data }))
}
use ethers::{
    prelude::*,
    contract::abigen,
    types::{Address, U256},
};
use serde::Serialize;
use std::sync::Arc;
use std::collections::HashMap;
use tokio::sync::Mutex;
use crate::AppState;

abigen!(
    ERC20,
    r#"[
        {"constant":true,"inputs":[{"name":"owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},
        {"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},
        // In src/evm.rs
pub async fn get_full_portfolio(
    state: &AppState,
    wallet: &str,
    cache: BalanceCache,
) -> serde_json::Value {
    let (sol_data, evm_data) = tokio::join!(
        crate::solana::get_multi_chain_balances(state, wallet),  // Previous Solana + Token-2022
        async {
            let eth = get_native_balance_safe(&state.evm_providers.eth, wallet).await;
            let bnb = get_native_balance_safe(&state.evm_providers.bnb, wallet).await;
            let arb = get_native_balance_safe(&state.evm_providers.arb, wallet).await;

            // Batch common tokens
            let tokens = vec![
                ("USDC", "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48", "eth"),
                // Add more ERC20s / LuminexCore EVM if exists
            ];

            let mut token_results = HashMap::new();
            for (symbol, addr, chain) in tokens {
                let balance = if chain == "eth" {
                    get_erc20_balance_safe(&state.evm_providers.eth, addr, wallet, cache.clone()).await
                } else { Erc20TokenBalance::default() };
                token_results.insert(symbol, balance);
            }

            serde_json::json!({
                "native": { "ETH": eth, "BNB": bnb, "ARB": arb },
                "tokens": token_results
            })
        }
    );

    serde_json::json!({
        "solana": sol_data.unwrap_or_default(),
        "evm": evm_data,
        "luminex_core_mint": state.luminex_core_mint
    })
}
use leptos::*;
use leptos_ws::use_channel_signal;

#[component]
pub fn TradeDesk(wallet: RwSignal<String>) -> impl IntoView {
    let (portfolio, set_portfolio) = create_signal(serde_json::json!({}));
    let (loading, set_loading) = create_signal(false);
    let cache = create_rw_signal::<BalanceCache>(Arc::new(Mutex::new(HashMap::new())));

    let fetch_portfolio = move || {
        set_loading.set(true);
        let wallet_val = wallet.get();
        if wallet_val.is_empty() { return; }

        spawn_local(async move {
            // Call server function or direct API
            if let Ok(data) = get_full_portfolio_server(wallet_val).await {  // leptos server fn
                set_portfolio.set(data);
            }
            set_loading.set(false);
        });
    };

    view! {
        <div class="bg-zinc-900 rounded-3xl p-8 border border-gold/30">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-3xl font-bold text-gold">"TRADE DESK TERMINAL"</h2>
                <button 
                    on:click=move |_| fetch_portfolio()
                    class="bg-gold hover:bg-yellow-300 text-black px-6 py-3 rounded-2xl font-semibold transition"
                >
                    {move || if loading.get() { "Refreshing..." } else { "Refresh Portfolio" }}
                </button>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                {/* Solana + Token-2022 */}
                <div>
                    <h3 class="text-xl text-white mb-4">"Solana • Token-2022"</h3>
                    <div class="space-y-4 text-white">
                        <div class="bg-zinc-800 p-5 rounded-2xl">
                            "SOL Balance: " {move || portfolio.get()["solana"]["SOL"].as_str().unwrap_or("0")}
                        </div>
                        <div class="bg-zinc-800 p-5 rounded-2xl">
                            "LuminexCore (Token-2022): " {move || portfolio.get()["solana"]["LuminexCore"].as_str().unwrap_or("0")}
                        </div>
                        <div class="text-xs text-zinc-400">"Extensions: Transfer Hook • Metadata • Mint Authority"</div>
                    </div>
                </div>

                {/* EVM Portfolio */}
                <div>
                    <h3 class="text-xl text-white mb-4">"EVM Chains (ETH • BNB • ARB)"</h3>
                    <div class="space-y-4">
                        {move || {
                            let evm = portfolio.get()["evm"].clone();
                            view! {
                                <div class="bg-zinc-800 p-5 rounded-2xl space-y-3">
                                    <div>"ETH: " {evm["native"]["ETH"].as_str().unwrap_or("0")}</div>
                                    <div>"BNB: " {evm["native"]["BNB"].as_str().unwrap_or("0")}</div>
                                    <div>"ARB: " {evm["native"]["ARB"].as_str().unwrap_or("0")}</div>
                                </div>
                            }
                        }}
                    </div>
                </div>
            </div>

            {/* Live Wallet Status */}
            <div class="mt-8 p-4 bg-black/50 rounded-2xl text-sm text-gold border border-gold/20">
                "Connected Wallet: " {move || wallet.get()} " • LuminexCore Holder"
            </div>
        </div>
    }
}
# Clean previous build artifacts
cargo clean

# Rebuild with Leptos
cargo leptos watch
use reqwest::Client;
use serde_json::Value;
use crate::AppState;

pub async fn get_helius_asset_metadata(
    state: &AppState,
    mint: &str,
) -> Result<Value, String> {
    let client = Client::new();
    let url = format!("{}/?api-key=YOUR_HELIUS_API_KEY", 
        state.rpc_client.get_url().replace("/rpc", ""));  // Adjust base URL

    let payload = serde_json::json!({
        "jsonrpc": "2.0",
        "id": "1",
        "method": "getAsset",
        "params": { "id": mint }
    });

    let resp = client.post(url)
        .json(&payload)
        .send()
        .await.map_err(|e| e.to_string())?;

    let data: Value = resp.json().await.map_err(|e| e.to_string())?;
    
    // Error handling for missing fields
    if let Some(result) = data.get("result") {
        Ok(result.clone())
    } else {
        Err("Helius metadata unavailable".to_string())
    }
}

// Batch metadata for multiple tokens
pub async fn get_batch_metadata(
    state: &AppState,
    mints: Vec<String>,
) -> Vec<(String, Value)> {
    let mut results = vec![];
    for mint in mints {
        match get_helius_asset_metadata(state, &mint).await {
            Ok(meta) => results.push((mint, meta)),
            Err(_) => results.push((mint, serde_json::json!({"error": "metadata missing"}))),
        }
    }
    results
}
use std::sync::Arc;
use tokio::sync::Mutex;
use std::collections::HashMap;
use std::time::Instant;

// Re-export or combine with previous cache
pub type PortfolioCache = Arc<Mutex<HashMap<String, (serde_json::Value, Instant)>>>;

pub async fn get_batched_portfolio(
    state: &AppState,
    wallet: &str,
    cache: PortfolioCache,
) -> serde_json::Value {
    let cache_key = wallet.to_string();
    
    if let Some((cached, ts)) = cache.lock().await.get(&cache_key) {
        if ts.elapsed() < std::time::Duration::from_secs(60) {  // 1 min cache
            return cached.clone();
        }
    }

    // Parallel batch fetches
    let (sol_result, evm_result, metadata_result) = tokio::join!(
        crate::solana::get_multi_chain_balances(state, wallet),
        crate::evm::get_full_evm_portfolio(state, wallet, /* cache */),
        async {
            // Batch common tokens including LuminexCore
            let mints = vec![state.luminex_core_mint.clone(), /* other tokens */];
            crate::solana::get_batch_metadata(state, mints).await
        }
    );

    let portfolio = serde_json::json!({
        "wallet": wallet,
        "solana": sol_result.unwrap_or_else(|e| serde_json::json!({"error": e})),
        "evm": evm_result.unwrap_or_else(|e| serde_json::json!({"error": e})),
        "metadata": metadata_result,
        "timestamp": chrono::Utc::now().timestamp()
    });

    cache.lock().await.insert(cache_key, (portfolio.clone(), Instant::now()));
    portfolio
}
use leptos::*;
use leptos_router::use_params_map; // if needed

#[component]
pub fn TradeDesk() -> impl IntoView {
    let wallet = create_rw_signal(String::new());
    let (portfolio, set_portfolio) = create_signal(serde_json::json!({}));
    let (loading, set_loading) = create_signal(false);
    let cache = create_rw_signal::<PortfolioCache>(Arc::new(Mutex::new(HashMap::new())));

    let refresh = move || {
        let w = wallet.get();
        if w.is_empty() { return; }
        set_loading.set(true);

        spawn_local(async move {
            // leptos server function or direct call
            if let Ok(data) = server_get_portfolio(w).await {
                set_portfolio.set(data);
            }
            set_loading.set(false);
        });
    };

    view! {
        <div class="space-y-8">
            {/* Wallet Input */}
            <div class="flex gap-4">
                <input 
                    type="text"
                    class="flex-1 bg-zinc-800 border border-zinc-700 rounded-2xl px-6 py-4 text-white"
                    placeholder="Enter Solana/EVM wallet address..."
                    on:input=move |ev| wallet.set(event_target_value(&ev))
                />
                <button 
                    on:click=move |_| refresh()
                    class="bg-gold hover:bg-amber-300 px-10 py-4 rounded-2xl font-semibold text-black transition"
                >
                    {move || if loading.get() { "Loading..." } else { "Fetch Portfolio" }}
                </button>
            </div>

            <div class="grid grid-cols-1 xl:grid-cols-3 gap-6">
                {/* Live Video remains in LiveSection */}
                <div class="xl:col-span-2">
                    {/* Existing Live Video + Chat */}
                </div>

                {/* Integrated Trade Desk */}
                <div class="bg-zinc-900 rounded-3xl p-8 border border-gold/30 h-fit">
                    <h2 class="text-3xl font-bold text-gold mb-6">"TRADE DESK • LUMINEXCORE"</h2>

                    {move || {
                        let p = portfolio.get();
                        view! {
                            <div class="space-y-6">
                                // Solana + Token-2022
                                <div class="bg-black/60 p-6 rounded-2xl">
                                    <div class="text-white">"SOL: " {p["solana"]["SOL"].as_str().unwrap_or("0")}</div>
                                    <div class="text-gold">"LuminexCore: " {p["solana"]["LuminexCore"].as_str().unwrap_or("0")}</div>
                                    <div class="text-xs text-zinc-400 mt-2">
                                        "Token-2022 Extensions: Transfer Hook • Metadata • Helius Indexed"
                                    </div>
                                </div>

                                // EVM
                                <div class="bg-black/60 p-6 rounded-2xl">
                                    <div class="text-white">"ETH: " {p["evm"]["native"]["ETH"].as_str().unwrap_or("0")}</div>
                                    <div class="text-white">"BNB: " {p["evm"]["native"]["BNB"].as_str().unwrap_or("0")}</div>
                                    <div class="text-white">"ARB: " {p["evm"]["native"]["ARB"].as_str().unwrap_or("0")}</div>
                                </div>

                                // Metadata Preview
                                <div class="text-xs text-zinc-400 overflow-auto max-h-48">
                                    {p["metadata"].to_string()}
                                </div>
                            </div>
                        }
                    }}
                </div>
            </div>
        </div>
    }
}
#[component]
pub fn LiveSection() -> impl IntoView {
    view! {
        <div class="p-8">
            <TradeDesk />
            {/* Existing Live Video + Chat side-by-side or tabs */}
        </div>
    }
}
#[server(GetPortfolio, "/api", "GetJson")]
pub async fn get_portfolio(wallet: String) -> Result<serde_json::Value, ServerFnError> {
    let state = use_context::<Arc<AppState>>().ok_or(ServerFnError::ServerError("No state".into()))?;
    let cache = /* inject or global cache */;
    let data = crate::portfolio::get_batched_portfolio(&state, &wallet, cache).await;
    Ok(data)
}
cargo clean
cargo leptos watch
use leptos::*;
use leptos_server::ServerFnError;  // or ServerFnError from leptos
use std::sync::Arc;
use crate::{AppState, portfolio::get_batched_portfolio};  // Your portfolio fn

// Fixed & clean syntax
#[server(GetPortfolio, "/api")]
pub async fn get_portfolio(wallet: String) -> Result<serde_json::Value, ServerFnError> {
    // Extract shared state (AppState) via context
    let state = use_context::<Arc<AppState>>()
        .ok_or_else(|| ServerFnError::ServerError("AppState not found in context".into()))?;

    // Portfolio cache (inject via context or use a global/static with care)
    let cache = use_context::<PortfolioCache>()
        .unwrap_or_else(|| Arc::new(tokio::sync::Mutex::new(std::collections::HashMap::new())));

    let data = get_batched_portfolio(&state, &wallet, cache).await;
    Ok(data)
}
// In your Axum/Leptos setup
let app_state = AppState::new(redis_url, helius_rpc).await;
let portfolio_cache: PortfolioCache = Arc::new(tokio::sync::Mutex::new(HashMap::new()));

let leptos_options = get_leptos_options(); // your existing config

let app = Router::new()
    .leptos_routes_with_context(
        &leptos_options,
        move || {
            provide_context(app_state.clone());
            provide_context(portfolio_cache.clone());
        },
        |cx| view! { cx, <App /> }
    )
    // ... other routes
    .with_state(/* ... */);
    // Inside TradeDesk or LiveSection
let refresh = move || {
    let w = wallet.get();
    if w.is_empty() { return; }
    set_loading.set(true);

    spawn_local(async move {
        match get_portfolio(w).await {  // Direct call - Leptos handles client/server
            Ok(data) => set_portfolio.set(data),
            Err(e) => {
                // Handle error (e.g. show toast)
                leptos::logging::log!("Portfolio error: {:?}", e);
            }
        }
        set_loading.set(false);
    });
};
cargo clean
cargo leptos watch
use anchor_lang::prelude::*;
use spl_transfer_hook_interface::instruction::TransferHookInstruction;

declare_id!("YourHookProgram1111111111111111111111111111");

#[program]
pub mod luminex_hook {
    use super::*;

    pub fn transfer_hook(ctx: Context<TransferHook>, amount: u64) -> Result<()> {
        msg!("LuminexCore Transfer Hook: {} tokens", amount);

        // Example: Amount cap
        if amount > 1_000_000_000 {  // 1k tokens (adjust decimals)
            return err!(ErrorCode::TransferTooLarge);
        }

        // Example: Royalty or state update
        // Or check recipient against a whitelist PDA

        Ok(())
    }

    // Required for extra accounts resolution
    pub fn initialize_extra_account_meta_list(/* ... */) -> Result<()> { ... }
}

#[derive(Accounts)]
pub struct TransferHook<'info> {
    pub source_account: AccountInfo<'info>,
    pub mint: AccountInfo<'info>,
    pub destination_account: AccountInfo<'info>,
    pub owner: AccountInfo<'info>,  // or authority
    // Add more via extra metas
}

#[error_code]
pub enum ErrorCode {
    #[msg("Transfer amount too large")]
    TransferTooLarge,
}
spl-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \
  create-token --transfer-hook YOUR_HOOK_PROGRAM_ID
  use spl_token_2022::{
    extension::{cpi_guard::CpiGuard, BaseStateWithExtensions},
    instruction::enable_cpi_guard,
};
use solana_sdk::{instruction::Instruction, pubkey::Pubkey};

// Example: Enable on a token account
pub fn enable_cpi_guard_ix(
    token_account: &Pubkey,
    owner: &Pubkey,
) -> Instruction {
    enable_cpi_guard(
        &spl_token_2022::id(),
        token_account,
        owner,
        &[],  // multisig signers if needed
    )
}

// Check status
pub async fn is_cpi_guard_enabled(state: &AppState, token_account: &str) -> bool {
    let pubkey = Pubkey::try_from(token_account).unwrap();
    let account = state.rpc_client.get_account(&pubkey).await.unwrap();
    
    let parsed = StateWithExtensions::<spl_token_2022::state::Account>::unpack(&account.data).unwrap();
    parsed.get_extension::<CpiGuard>().is_ok()
}
// Inside portfolio display
<div class="flex items-center gap-2 text-green-400">
    "🛡️ CPI Guard (Token Guard): " 
    {move || if portfolio.get()["cpi_guard"].as_bool().unwrap_or(false) { "ENABLED" } else { "DISABLED" }}
</div>
use solana_sdk::{
    instruction::Instruction,
    pubkey::Pubkey,
    signature::Signer,
    transaction::Transaction,
};
use spl_token_2022::{
    extension::{cpi_guard::CpiGuard, BaseStateWithExtensions, StateWithExtensions},
    instruction::{enable_cpi_guard, disable_cpi_guard},
    state::Account as TokenAccount,
};
use crate::AppState;

// Initialize / Enable CPI Guard on a Token Account
pub fn create_enable_cpi_guard_instruction(
    token_program: &Pubkey,
    token_account: &Pubkey,
    owner: &Pubkey,
) -> Instruction {
    enable_cpi_guard(
        token_program,
        token_account,
        owner,
        &[], // multisig signers
    )
}

// Disable (if needed)
pub fn create_disable_cpi_guard_instruction(
    token_program: &Pubkey,
    token_account: &Pubkey,
    owner: &Pubkey,
) -> Instruction {
    disable_cpi_guard(
        token_program,
        token_account,
        owner,
        &[],
    )
}

// Check CPI Guard status (used in portfolio)
pub async fn check_cpi_guard_status(
    state: &AppState,
    token_account: &str,
) -> Result<bool, String> {
    let pubkey = Pubkey::try_from(token_account).map_err(|e| e.to_string())?;
    
    let account_info = state.rpc_client
        .get_account_with_commitment(&pubkey, solana_sdk::commitment_config::CommitmentConfig::confirmed())
        .await
        .map_err(|e| e.to_string())?;

    let parsed: StateWithExtensions<TokenAccount> = 
        StateWithExtensions::unpack(&account_info.value.data)
            .map_err(|e| e.to_string())?;

    Ok(parsed.get_extension::<CpiGuard>().is_ok())
}

// Example: Full transaction builder for enabling
// Extend get_batched_portfolio or get_multi_chain_balances
pub async fn enrich_with_security_extensions(
    state: &AppState,
    wallet: &str,
    token_account: Option<String>, // ATA for LuminexCore
) -> serde_json::Value {
    let cpi_guard_enabled = if let Some(ata) = token_account {
        check_cpi_guard_status(state, &ata).await.unwrap_or(false)
    } else { false };

    serde_json::json!({
        "cpi_guard": cpi_guard_enabled,
        "security_status": if cpi_guard_enabled { "Protected" } else { "Recommended: Enable CPI Guard" },
        "recommendation": "Enable CPI Guard + Transfer Hook for maximum security"
    })
}
// Inside TradeDesk view! { ... }
<div class="mt-6 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
    <h4 class="text-lg font-semibold text-green-400 mb-3">"🛡️ Token Security Extensions"</h4>
    
    <div class="flex items-center justify-between bg-zinc-900 p-4 rounded-2xl">
        <div>
            <div class="text-white">"CPI Guard (Token Guard)"</div>
            <div class="text-xs text-zinc-400">"Prevents malicious CPI drains"</div>
        </div>
        <div class="text-right">
            {move || {
                let enabled = portfolio.get()["cpi_guard"].as_bool().unwrap_or(false);
                if enabled {
                    view! { <span class="text-green-400 font-bold">"ENABLED ✓"</span> }
                } else {
                    view! { 
                        <button 
                            class="bg-red-600 hover:bg-red-500 px-5 py-2 rounded-xl text-sm"
                            on:click=move |_| { /* trigger enable flow */ }
                        >
                            "ENABLE CPI GUARD"
                        </button>
                    }
                }
            }}
        </div>
    </div>

    <div class="text-xs text-zinc-400 mt-4">
        "Combined with Transfer Hook for LuminexCore — Highly Recommended for Trade Desk"
    </div>
</div>
#[server(EnableCpiGuard, "/api")]
pub async fn enable_cpi_guard(wallet: String, token_account: String) -> Result<String, ServerFnError> {
    let state = use_context::<Arc<AppState>>()
        .ok_or_else(|| ServerFnError::ServerError("State missing".into()))?;

    // In real app: verify wallet ownership via signature, then return instructions or submit tx
    let tx = crate::solana::build_enable_cpi_guard_tx(&state, &token_account, &wallet).await
        .map_err(|e| ServerFnError::ServerError(e.into()))?;

    // For client-side signing: serialize and return to frontend
    Ok("Instructions ready for signing".to_string())
}
use leptos::*;
use wasm_bindgen::prelude::*;
use serde_json::Value;
use js_sys::Promise;

// JS interop for Phantom / Solflare / Backpack
#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = window)]
    fn solana() -> JsValue;  // Phantom global
}

#[component]
pub fn WalletConnector(wallet_address: RwSignal<String>) -> impl IntoView {
    let (connected, set_connected) = create_signal(false);

    let connect_wallet = move || {
        spawn_local(async move {
            let result = connect_solana_wallet().await;
            match result {
                Ok(addr) => {
                    wallet_address.set(addr.clone());
                    set_connected.set(true);
                }
                Err(e) => leptos::logging::error!("Wallet connect failed: {}", e),
            }
        });
    };

    view! {
        <button 
            on:click=move |_| connect_wallet()
            class="bg-gradient-to-r from-purple-600 to-gold text-black px-8 py-3 rounded-2xl font-semibold hover:scale-105 transition"
        >
            {move || if connected.get() { "Connected ✓" } else { "Connect Phantom / Solflare" }}
        </button>
    }
}

async fn connect_solana_wallet() -> Result<String, String> {
    let window = web_sys::window
    #[component]
pub fn TradeDesk() -> impl IntoView {
    let wallet_address = create_rw_signal(String::new());
    let (portfolio, set_portfolio) = create_signal(serde_json::json!({}));
    let (loading, set_loading) = create_signal(false);

    let enable_cpi_guard = move || {
        let wallet = wallet_address.get();
        if wallet.is_empty() { return; }
        set_loading.set(true);

        spawn_local(async move {
            match sign_and_enable_cpi_guard(wallet).await {
                Ok(tx_sig) => {
                    leptos::logging::log!("CPI Guard enabled! Tx: {}", tx_sig);
                    // Refresh portfolio
                }
                Err(e) => leptos::logging::error!("Signing failed: {}", e),
            }
            set_loading.set(false);
        });
    };

    view! {
        <div class="bg-zinc-900 rounded-3xl p-8">
            <WalletConnector wallet_address=wallet_address />

            {/* Security Section */}
            <div class="mt-8 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
                <h4 class="text-lg font-semibold text-green-400">"🛡️ Enable CPI Guard (Token Guard)"</h4>
                
                <button 
                    on:click=move |_| enable_cpi_guard()
                    class="mt-4 w-full bg-green-600 hover:bg-green-500 py-4 rounded-2xl font-bold disabled:opacity-50"
                    disabled=move || wallet_address.get().is_empty() || loading.get()
                >
                    {move || if loading.get() { "Signing Transaction..." } else { "Sign & Enable CPI Guard" }}
                </button>
                
                <p class="text-xs text-zinc-400 mt-3">
                    "This protects your LuminexCore tokens from malicious CPIs. Requires wallet signature."
                </p>
            </div>

            {/* Portfolio display (as before) */}
        </div>
    }
}
async fn sign_and_enable_cpi_guard(wallet: String) -> Result<String, String> {
    // 1. Get instructions from server
    let instructions = get_enable_cpi_guard_instructions(wallet.clone()).await
        .map_err(|e| e.to_string())?;

    // 2. Build transaction on client (or let server return partial tx)
    let tx = build_client_transaction(instructions).await?;

    // 3. Request wallet signature
    let solana_obj = /* get from window.solana */;
    let sign_promise = /* call signTransaction(tx) */;

    let signed_tx = wasm_bindgen_futures::JsFuture::from(sign_promise).await
        .map_err(|e| format!("{:?}", e))?;

    // 4. Send signed transaction to network (or to your server for relaying)
    let signature = send_raw_transaction(signed_tx).await?;

    Ok(signature)
}

// Helper: Call Leptos server fn to get unsigned instructions
#[server(GetCpiGuardIx, "/api")]
pub async fn get_enable_cpi_guard_instructions(wallet: String) -> Result<Vec<u8>, ServerFnError> {
    // Return serialized Instruction(s) for client
    let state = use_context::<Arc<AppState>>().ok_or(ServerFnError::ServerError("No state".into()))?;
    let token_account = /* derive ATA for LuminexCore */;
    
    let ix = crate::solana::create_enable_cpi_guard_instruction(
        &spl_token_2022::id(),
        &token_account,
        &Pubkey::try_from(wallet.as_str()).unwrap(),
    );

    // Serialize instruction for client
    Ok(bincode::serialize(&ix).map_err(|e| ServerFnError::ServerError(e.to_string()))?)
}
cargo clean
cargo leptos watch
const LUMINEX_CORE_MINT: &str = "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump";
const PUMP_MINT: &str = "AgTSWsyrSymK1h35LPhncvtyb9sgViJrwdxAvXjWpump";

pub fn is_special_token(mint: &str) -> bool {
    let mint_lower = mint.to_lowercase();
    mint_lower == LUMINEX_CORE_MINT.to_lowercase() || 
    mint_lower == PUMP_MINT.to_lowercase()
}
// Example display
{move || {
    let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("");
    if is_special_token(mint) {  // Use the same helper (you can expose via server or duplicate)
        view! {
            <div class="mt-6 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
                <h4 class="text-lg font-semibold text-green-400">
                    "🛡️ Token-2022 Extensions (LuminexCore & PUMP)"
                </h4>
                <div>"Mint: " {mint}</div>
                {/* CPI Guard toggle + client signing as before */}
            </div>
        }
    } else {
        view! { <div class="text-zinc-400">"Standard SPL Token"</div> }
    }
}}
cargo clean
cargo leptos watch
const LUMINEX_CORE_MINT: &str = "EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump";
const PUMP_MINT: &str = "AgTSWsyrSymK1h35LPhncvtyb9sgViJrwdxAvXjWpump";

pub fn is_special_token(mint: &str) -> bool {
    let mint_lower = mint.to_lowercase();
    mint_lower == LUMINEX_CORE_MINT.to_lowercase() || 
    mint_lower == PUMP_MINT.to_lowercase()
}
use solana_sdk::{instruction::Instruction, pubkey::Pubkey};
use spl_token_2022::{
    extension::{cpi_guard::CpiGuard, metadata_pointer::MetadataPointer, transfer_hook::TransferHook},
    instruction as token_instruction,
};

// Initialize ALL requested extensions for PUMP and LuminexCore
pub async fn initialize_special_token_extensions(
    state: &AppState,
    mint: &str,
    owner: &str,
    transfer_hook_program: Option<&str>,   // Your custom hook program ID for PUMP
) -> Result<Vec<Instruction>, String> {
    if !is_special_token(mint) {
        return Ok(vec![]);
    }

    let mint_pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    let owner_pubkey = Pubkey::try_from(owner).map_err(|e| e.to_string())?;
    let mut instructions = vec![];

    let ata = spl_associated_token_account::get_associated_token_address_with_program_id(
        &owner_pubkey, &mint_pubkey, &spl_token_2022::id()
    );

    // 1. CPI Guard (Token Guard)
    instructions.push(token_instruction::enable_cpi_guard(
        &spl_token_2022::id(),
        &ata,
        &owner_pubkey,
        &[],
    ));

    // 2. Metadata Pointer Extension
    instructions.push(token_instruction::initialize_metadata_pointer(
        &spl_token_2022::id(),
        &mint_pubkey,
        Some(owner_pubkey),           // Authority
        Some(mint_pubkey),            // Metadata address (can point to Token Metadata program)
    ));

    // 3. Transfer Hook (Enabled ONLY for PUMP)
    if mint.to_lowercase() == PUMP_MINT.to_lowercase() {
        if let Some(hook_program) = transfer_hook_program {
            let hook_pubkey = Pubkey::try_from(hook_program).map_err(|e| e.to_string())?;
            instructions.push(token_instruction::initialize_transfer_hook(
                &spl_token_2022::id(),
                &mint_pubkey,
                Some(owner_pubkey),
                Some(hook_pubkey),
            ));
        }
    }

    Ok(instructions)
}
pub async fn get_token_extensions_status(
    state: &AppState,
    mint: &str,
    token_account: &str,
) -> serde_json::Value {
    if !is_special_token(mint) {
        return serde_json::json!({"extensions": "standard"});
    }

    let token_name = if mint.to_lowercase() == LUMINEX_CORE_MINT.to_lowercase() {
        "LuminexCore"
    } else {
        "PUMP"
    };

    let cpi_guard = check_cpi_guard_status(state, token_account).await.unwrap_or(false);

    serde_json::json!({
        "token": token_name,
        "mint": mint,
        "extensions": "Token-2022",
        "cpi_guard": cpi_guard,
        "metadata_pointer": true,
        "transfer_hook": mint.to_lowercase() == PUMP_MINT.to_lowercase(),
        "security": if cpi_guard { "High" } else { "Enable CPI Guard" }
    })
}
{move || {
    let p = portfolio.get();
    let mint = p["solana"]["mint"].as_str().unwrap_or("");
    
    if is_special_token(mint) {
        view! {
            <div class="mt-6 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
                <h4 class="text-lg font-semibold text-green-400">"🛡️ Token-2022 Extensions"</h4>
                <div class="grid grid-cols-1 gap-4 mt-4">
                    <div class="bg-zinc-900 p-4 rounded-2xl">"CPI Guard: " {if p["cpi_guard"].as_bool().unwrap_or(false) { "✅" } else { "⚠️" }}</div>
                    <div class="bg-zinc-900 p-4 rounded-2xl">"Metadata Pointer: ✅"</div>
                    {if mint.to_lowercase() == PUMP_MINT.to_lowercase() {
                        view! { <div class="bg-zinc-900 p-4 rounded-2xl">"Transfer Hook: ✅ Active (PUMP)"</div> }
                    } else {
                        view! { <div class="bg-zinc-900 p-4 rounded-2xl">"Transfer Hook: Disabled (LuminexCore)"</div> }
                    }}
                </div>

                <button 
                    on:click=move |_| enable_cpi_guard()
                    class="mt-6 w-full bg-green-600 hover:bg-green-500 py-4 rounded-2xl font-bold"
                >
                    "Sign to Enable / Update Extensions"
                </button>
            </div>
        }
    } else { ... }
}}
cargo clean
cargo leptos watch
use leptos::*;
use wasm_bindgen::prelude::*;
use serde::{Deserialize, Serialize};
use js_sys::Promise;

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = window)]
    fn solana() -> JsValue;
}

#[derive(Serialize, Deserialize)]
pub struct ExtensionInstructions {
    pub instructions: Vec<u8>, // Serialized Vec<Instruction>
    pub recent_blockhash: String,
}

pub fn get_solana_wallet() -> Option<JsValue> {
    let window = web_sys::window()?;
    js_sys::Reflect::get(&window, &JsValue::from_str("solana")).ok()
}

#[component]
pub fn WalletConnector(wallet_address: RwSignal<String>, connected: RwSignal<bool>) -> impl IntoView {
    let connect = move || {
        spawn_local(async move {
            if let Some(solana) = get_solana_wallet() {
                if let Ok(promise) = js_sys::Reflect::get(&solana, &JsValue::from_str("connect")) {
                    if let Ok(connect_fn) = promise.dyn_into::<js_sys::Function>() {
                        let result = wasm_bindgen_futures::JsFuture::from(connect_fn.call0(&solana).unwrap())
                            .await
                            .unwrap();
                        
                        let pubkey = js_sys::Reflect::get(&result, &JsValue::from_str("publicKey"))
                            .and_then(|pk| js_sys::Reflect::get(&pk, &JsValue::from_str("toBase58")))
                            .and_then(|f| f.dyn_into::<js_sys::Function>())
                            .map(|f| f.call0(&JsValue::NULL).unwrap().as_string().unwrap())
                            .unwrap_or_default();

                        wallet_address.set(pubkey.clone());
                        connected.set(true);
                    }
                }
            }
        });
    };

    view! {
        <button on:click=move |_| connect() class="bg-gold hover:bg-yellow-300 text-black px-8 py-3 rounded-2xl font-semibold">
            {move || if connected.get() { "✓ Connected
            // src/wallet.rs
pub async fn sign_and_send_extensions(
    wallet_address: String,
    mint: String,
    extension_type: String, // "cpi_guard" | "metadata_pointer" | "transfer_hook"
) -> Result<String, String> {
    // 1. Get unsigned instructions from server
    let instructions = fetch_extension_instructions(wallet_address.clone(), mint.clone(), extension_type).await?;

    // 2. Build transaction on client
    let tx = build_transaction(instructions, wallet_address.clone()).await?;

    // 3. Request wallet signature
    let solana = get_solana_wallet().ok_or("No wallet found")?;
    
    let sign_promise: Promise = js_sys::Reflect::get(&solana, &JsValue::from_str("signTransaction"))
        .map_err(|_| "signTransaction not available")?
        .dyn_into()
        .map_err(|_| "Failed to cast")?;

    let signed = wasm_bindgen_futures::JsFuture::from(sign_promise.call1(&solana, &tx))
        .await
        .map_err(|e| format!("Signing failed: {:?}", e))?;

    // 4. Send signed transaction
    let signature = send_signed_transaction(signed).await?;

    Ok(signature)
}

// Helper: Fetch instructions from Leptos server
#[server(GetExtensionIx, "/api")]
pub async fn fetch_extension_instructions(
    wallet: String,
    mint: String,
    extension: String,
) -> Result<Vec<u8>, ServerFnError> {
    let state = use_context::<Arc<AppState>>()
        .ok_or(ServerFnError::ServerError("State missing".into()))?;

    let instructions = crate::solana::initialize_special_token_extensions(
        &state,
        &mint,
        &wallet,
        Some("YOUR_TRANSFER_HOOK_PROGRAM_ID_HERE"), // For PUMP
    ).await
    .map_err(|e| ServerFnError::ServerError(e.into()))?;

    // Serialize for client
    bincode::serialize(&instructions)
        .map_err(|e| ServerFnError::ServerError(e.to_string()))
}
#[component]
pub fn TradeDesk() -> impl IntoView {
    let wallet_address = create_rw_signal(String::new());
    let connected = create_rw_signal(false);
    let (portfolio, set_portfolio) = create_signal(serde_json::json!({}));
    let (signing, set_signing) = create_signal(false);

    let sign_extensions = move |ext_type: String| {
        let wallet = wallet_address.get();
        let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("").to_string();
        if wallet.is_empty() || mint.is_empty() { return; }

        set_signing.set(true);
        spawn_local(async move {
            match sign_and_send_extensions(wallet, mint, ext_type).await {
                Ok(sig) => leptos::logging::log!("Transaction successful: {}", sig),
                Err(e) => leptos::logging::error!("Error: {}", e),
            }
            set_signing.set(false);
        });
    };

    view! {
        <div class="bg-zinc-900 rounded-3xl p-8">
            <WalletConnector wallet_address=wallet_address connected=connected />

            {/* Extensions Panel - Only for PUMP & LuminexCore */}
            {move || {
                let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("");
                if crate::solana::is_special_token(mint) {
                    view! {
                        <div class="mt-8 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
                            <h4 class="text-xl font-bold text-green-400">"Token-2022 Extensions"</h4>
                            
                            <div class="space-y-4 mt-6">
                                <button 
                                    on:click=move |_| sign_extensions("cpi_guard".to_string())
                                    class="w-full bg-green-600 hover:bg-green-500 py-4 rounded-2xl font-semibold disabled:opacity-50"
                                    disabled=move || signing.get() || !connected.get()
                                >
                                    "Sign: Enable CPI Guard"
                                </button>

                                <button 
                                    on:click=move |_| sign_extensions("metadata_pointer".to_string())
                                    class="w-full bg-blue-600 hover:bg-blue-500 py-4 rounded-2xl font-semibold disabled:opacity-50"
                                    disabled=move || signing.get() || !connected.get()
                                >
                                    "Sign: Initialize Metadata Pointer"
                                </button>

                                {if mint.to_lowercase() == crate::solana::PUMP_MINT.to_lowercase() {
                                    view! {
                                        <button 
                                            on:click=move |_| 
                                            sign_extensions("transfer_hook".to_string())
                                            class="w-full bg-purple-600 hover:bg-purple-500 py-4 rounded-2xl font-semibold disabled:opacity-50"
                                            disabled=move || signing.get() || !connected.get()
                                        >
                                            "Sign: Enable Transfer Hook (PUMP Only)"
                                        </button>
                                    }
                                } else { view! { <div></div> }}
                                }
                            </div>
                        </div>
                    }
                } else {
                    view! { <div class="text-zinc-400 mt-4">"Standard SPL – No extensions available"</div> }
                }
            }}
        </div>
    }
}
cargo clean
cargo leptos watch
anchor init pump-transfer-hook
cd pump-transfer-hook
[dependencies]
anchor-lang = "0.30"
anchor-spl = "0.30"
spl-transfer-hook-interface = "0.6"
spl-tlv-account-resolution = "0.6"
spl-type-length-value = "0.6"
use anchor_lang::prelude::*;
use anchor_spl::token_interface::{Mint, TokenInterface};
use spl_transfer_hook_interface::instruction::TransferHookInstruction;
use spl_tlv_account_resolution::{
    account::ExtraAccountMeta, seeds::Seed, state::ExtraAccountMetaList,
};

declare_id!("YourHookProgramPubkeyHere111111111111111111111"); // Replace after first build

#[program]
pub mod pump_transfer_hook {
    use super::*;

    // Required: Initialize Extra Account Meta List PDA
    pub fn initialize_extra_account_meta_list(
        ctx: Context<InitializeExtraAccountMetaList>,
    ) -> Result<()> {
        let extra_account_metas = vec![]; // Add PDAs or accounts if needed for your logic

        ExtraAccountMetaList::init::<ExecuteInstruction>(
            &mut ctx.accounts.extra_account_meta_list.try_borrow_mut_data()?,
            &extra_account_metas,
        )?;
        Ok(())
    }

    // Main Transfer Hook Logic (called automatically on PUMP transfers)
    #[interface(spl_transfer_hook_interface::execute)]
    pub fn transfer_hook(ctx: Context<TransferHook>, amount: u64) -> Result<()> {
        msg!("🚀 PUMP Transfer Hook Executed - Amount: {}", amount);

        // Example Logic: Enforce max transfer size (e.g. anti-whale)
        const MAX_TRANSFER: u64 = 1_000_000_000_000; // Adjust decimals
        if amount > MAX_TRANSFER {
            return err!(ErrorCode::TransferTooLarge);
        }

        // Example: Royalty / Fee collection (simple log for now)
        if amount > 100_000_000_000 {
            msg!("🐋 Large PUMP transfer detected!");
        }

        // You can add more: whitelist checks, on-chain counters, etc.
        Ok(())
    }
}

#[derive(Accounts)]
pub struct InitializeExtraAccountMetaList<'info> {
    #[account(mut)]
    pub extra_account_meta_list: AccountInfo<'info>,
    pub mint: Account<'info, Mint>,
    pub authority: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct TransferHook<'info> {
    pub source_account: AccountInfo<'info>,
    pub mint: Account<'info, Mint>,
    pub destination_account: AccountInfo<'info>,
    pub owner: AccountInfo<'info>,
    // Add resolved extra accounts here if needed
}

#[error_code]
pub enum ErrorCode {
    #[msg("Transfer amount exceeds limit")]
    TransferTooLarge,
}
anchor deploy
// When initializing PUMP extensions
let hook_program_id = "YourDeployedHookProgramID"; // From anchor deploy
let instructions = initialize_special_token_extensions(
    &state,
    PUMP_MINT,
    &owner,
    Some(hook_program_id),   // Pass here
).await?;
cargo clean
cargo leptos watch
use anchor_lang::prelude::*;
use anchor_spl::token_interface::{Mint, TokenAccount, TokenInterface, Transfer};
use spl_transfer_hook_interface::instruction::TransferHookInstruction;
use spl_tlv_account_resolution::{
    account::ExtraAccountMeta,
    seeds::Seed,
    state::ExtraAccountMetaList,
};

declare_id!("YourDeployedHookProgramID11111111111111111111"); // ← Update after `anchor build`

#[program]
pub mod pump_transfer_hook {
    use super::*;

    // Initialize Extra Account Meta List (required for hooks)
    pub fn initialize_extra_account_meta_list(
        ctx: Context<InitializeExtraAccountMetaList>,
    ) -> Result<()> {
        let extra_metas = vec![
            // Royalty recipient (treasury) PDA or fixed account
            ExtraAccountMeta::new_with_seeds(
                &[Seed::Literal { bytes: b"royalty_treasury".to_vec() }],
                false, // not writable? adjust
                true,  // signer? no
            ),
        ];

        ExtraAccountMetaList::init::<ExecuteInstruction>(
            &mut ctx.accounts.extra_account_meta_list.try_borrow_mut_data()?,
            &extra_metas,
        )?;
        Ok(())
    }

    // Main Transfer Hook - Royalty Collection
    anchor build
anchor deploy
anchor run initialize-extra-metas
let hook_program_id = "YourDeployedHookProgramID";
let instructions = initialize_special_token_extensions(
    &state, PUMP_MINT, &owner, Some(hook_program_id)
).await?;
cargo clean && cargo leptos watch
use anchor_lang::prelude::*;
use anchor_spl::token_interface::{Mint, TokenAccount, TokenInterface, Transfer};
use spl_transfer_hook_interface::instruction::TransferHookInstruction;
use spl_tlv_account_resolution::{
    account::ExtraAccountMeta,
    seeds::Seed,
    state::ExtraAccountMetaList,
};

declare_id!("YourDeployedHookProgramID11111111111111111111"); // Update after deploy

#[program]
pub mod pump_transfer_hook {
    use super::*;

    // Initialize Config PDA + Extra Account Meta List
    pub fn initialize(
        ctx: Context<Initialize>,
        initial_royalty_bps: u16,   // e.g. 200 = 2.00%
    ) -> Result<()> {
        let config = &mut ctx.accounts.config;
        config.royalty_bps = initial_royalty_bps;
        config.treasury = ctx.accounts.treasury.key();
        config.authority = ctx.accounts.author
        use anchor_lang::prelude::*;
use anchor_spl::token_interface::{Mint, TokenAccount, TokenInterface, Transfer};
use spl_transfer_hook_interface::instruction::TransferHookInstruction;
use spl_tlv_account_resolution::{
    account::ExtraAccountMeta,
    seeds::Seed,
    state::ExtraAccountMetaList,
};

declare_id!("YourDeployedHookProgramID11111111111111111111"); // Update after deploy

#[program]
pub mod pump_transfer_hook {
    use super::*;

    // Initialize Config PDA + Extra Account Meta List
    pub fn initialize(
        ctx: Context<Initialize>,
        initial_royalty_bps: u16,   // e.g. 200 = 2.00%
    ) -> Result<()> {
        let config = &mut ctx.accounts.config;
        config.royalty_bps = initial_royalty_bps;
        config.treasury = ctx.accounts.treasury.key();
        config.authority = ctx.accounts.authority.key();
        config.bump = ctx.bumps.config;

        // Initialize Extra Account Meta List (required for hook)
        let extra_metas = vec![
            ExtraAccountMeta::new_with_pubkey(&ctx.accounts.treasury.key(), false, true), // royalty treasury
            ExtraAccountMeta::new_with_pubkey(&ctx.accounts.config.key(), false, false),  // config PDA
        ];

        ExtraAccountMetaList::init::<ExecuteInstruction>(
            &mut ctx.accounts.extra_account_meta_list.try_borrow_mut_data()?,
            &extra_metas,
        )?;

        msg!("PUMP Hook initialized with {} bps royalty", initial_royalty_bps);
        Ok(())
    }

    // Admin: Update royalty rate
    pub fn update_royalty_rate(
        ctx: Context<UpdateRoyaltyRate>,
        new_royalty_bps: u16,
    ) -> Result<()> {
        require!(new_royalty_bps <= 1000, ErrorCode::InvalidRoyaltyRate); // max 10%

        let config = &mut ctx.accounts.config;
        config.royalty_bps = new_royalty_bps;

        msg!("Royalty rate updated to {} bps", new_royalty_bps);
        Ok(())
    }

    // Main Transfer Hook with Configurable Royalty
    #[interface(spl_transfer_hook_interface::execute)]
    pub fn transfer_hook(ctx: Context<TransferHook>, amount: u64) -> Result<()> {
        let config = &ctx.accounts.config;
        let royalty_amount = (amount as u128 * config.royalty_bps as u128 / 10_000) as u64;

        msg!("PUMP Transfer Hook | Amount: {} | Royalty Rate: {} bps", amount, config.royalty_bps);

        if royalty_amount > 0 {
            let cpi_accounts = Transfer {
                from: ctx.accounts.source_account.to_account_info(),
                to: ctx.accounts.royalty_treasury.to_account_info(),
                authority: ctx.accounts.owner.to_account_info(),
            };

            let cpi_ctx = CpiContext::new(
                ctx.accounts.token_program.to_account_info(),
                cpi_accounts,
            );

            anchor_spl::token_interface::transfer(cpi_ctx, royalty_amount)?;
            msg!("💰 Royalty collected: {} PUMP", royalty_amount);
        }

        // Anti-whale (optional)
        if amount > 5_000_000_000_000 {
            msg!("🐋 Large transfer detected");
        }

        Ok(())
    }
}

// ==================== Accounts ====================

#[account]
#[derive(Default)]
pub struct HookConfig {
    pub royalty_bps: u16,
    pub treasury: Pubkey,
    pub authority: Pubkey,
    pub bump: u8,
}

#[derive(Accounts)]
#[instruction(initial_royalty_bps: u16)]
pub struct Initialize<'info> {
    #[account(
        init,
        payer = authority,
        space = 8 + 2 + 32 + 32 + 1,
        seeds = [b"config", mint.key().as_ref()],
        bump
    )]
    pub config: Account<'info, HookConfig>,

    #[account(mut)]
    pub extra_account_meta_list: AccountInfo<'info>,

    pub mint: Account<'info, Mint>,
    #[account(mut)]
    pub treasury: Account<'info, TokenAccount>,
    pub authority: Signer<'info>,
    pub system_program: Program<'info, System>,
    pub token_program: Interface<'info, TokenInterface>,
}

#[derive(Accounts)]
pub struct UpdateRoyaltyRate<'info> {
    #[account(
        mut,
        seeds = [b"config", mint.key().as_ref()],
        bump = config.bump,
        has_one = authority
    )]
    pub config: Account<'info, HookConfig>,

    pub mint: Account<'info, Mint>,
    pub authority: Signer<'info>,
}

#[derive(Accounts)]
pub struct TransferHook<'info> {
    pub source_account: Account<'info, TokenAccount>,
    pub mint: Account<'info, Mint>,
    pub destination_account: Account<'info, TokenAccount>,
    pub owner: Signer<'info>,

    #[account(mut)]
    pub royalty_treasury: Account<'info, TokenAccount>,

    #[account(
        seeds = [b"config", mint.key().as_ref()],
        bump = config.bump
    )]
    pub config: Account<'info, HookConfig>,

    pub token_program: Interface<'info, TokenInterface>,
}

#[error_code]
pub enum ErrorCode {
    #[msg("Royalty rate must be between 0 and 1000 bps (10%)")]
    InvalidRoyaltyRate,
}
anchor build
anchor deploy
anchor run initialize-hook
cargo clean && cargo leptos watch
// Add Freeze Authority support
pub async fn initialize_special_token_extensions(
    state: &AppState,
    mint: &str,
    owner: &str,
    transfer_hook_program: Option<&str>,
) -> Result<Vec<Instruction>, String> {
    if !is_special_token(mint) {
        return Ok(vec![]);
    }

    let mint_pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    let owner_pubkey = Pubkey::try_from(owner).map_err(|e| e.to_string())?;
    let mut instructions = vec![];

    let ata = spl_associated_token_account::get_associated_token_address_with_program_id(
        &owner_pubkey, &mint_pubkey, &spl_token_2022::id()
    );

    // Existing extensions
    instructions.push(spl_token_2022::instruction::enable_cpi_guard(
        &spl_token_2022::id(), &ata, &owner_pubkey, &[]
    ));

    instructions.push(spl_token_2022::instruction::initialize_metadata_pointer(
        &spl_token_2022::id(), &mint_pubkey, Some(owner_pubkey), Some(mint_pubkey)
    ));

    if mint.to_lowercase() == PUMP_MINT.to_lowercase() {
        if let Some(hook) = transfer_hook_program {
            let hook_pubkey = Pubkey::try_from(hook).map_err(|e| e.to_string())?;
            instructions.push(spl_token_2022::instruction::initialize_transfer_hook(
                &spl_token_2022::id(), &mint_pubkey, Some(owner_pubkey), Some(hook_pubkey)
            ));
        }
    }

    // === NEW: Token Freeze for Compliance ===
    // Set Freeze Authority (can be the compliance multisig or owner)
    instructions.push(spl_token_2022::instruction::set_authority(
        &spl_token_2022::id(),
        &mint_pubkey,
        Some(&owner_pubkey),           // New freeze authority
        spl_token_2022::instruction::AuthorityType::FreezeAccount,
        &owner_pubkey,
        &[],
    ));

    Ok(instructions)
}
// Freeze a specific token account (for compliance)
pub fn freeze_token_account_ix(
    token_account: &Pubkey,
    mint: &Pubkey,
    freeze_authority: &Pubkey,
) -> Instruction {
    spl_token_2022::instruction::freeze_account(
        &spl_token_2022::id(),
        token_account,
        mint,
        freeze_authority,
        &[],
    )
}

// Unfreeze
pub fn thaw_token_account_ix(
    token_account: &Pubkey,
    mint: &Pubkey,
    freeze_authority: &Pubkey,
) -> Instruction {
    spl_token_2022::instruction::thaw_account(
        &spl_token_2022::id(),
        token_account,
        mint,
        freeze_authority,
        &[],
    )
}
{ move || {
    let p = portfolio.get();
    let mint = p["solana"]["mint"].as_str().unwrap_or("");
    if is_special_token(mint) {
        view! {
            <div class="mt-8 bg-red-950/50 border border-red-500/30 rounded-3xl p-6">
                <h4 class="text-lg font-semibold text-red-400 mb-4">"⚠️ Compliance Controls"</h4>
                
                <div class="grid grid-cols-1 gap-4">
                    <button 
                        on:click=move |_| sign_freeze_action("freeze".to_string())
                        class="bg-red-600 hover:bg-red-700 py-4 rounded-2xl font-bold text-white"
                    >
                        "Freeze Token Account (Compliance)"
                    </button>
                    
                    <button 
                        on:click=move |_| sign_freeze_action("thaw".to_string())
                        class="bg-green-600 hover:bg-green-700 py-4 rounded-2xl font-bold text-white"
                    >
                        "Thaw / Unfreeze Token Account"
                    </button>
                </div>
                
                <p class="text-xs text-zinc-400 mt-4">
                    "Freeze functionality enabled for regulatory compliance on LuminexCore & PUMP only."
                </p>
            </div>
        }
    } else { view! { <div></div> }}
}}
let sign_freeze_action = move |action: String| {
    let wallet = wallet_address.get();
    let mint = /* from portfolio */;
    let token_account = /* derive ATA or input field */;

    spawn_local(async move {
        let result = if action == "freeze" {
            sign_and_send_compliance_action(wallet, mint, token_account, "freeze").await
        } else {
            sign_and_send_compliance_action(wallet, mint, token_account, "thaw").await
        };
        // handle result
    });
};
#[server(ComplianceFreeze, "/api")]
pub async fn get_freeze_instructions(
    wallet: String,
    mint: String,
    token_account: String,
    action: String, // "freeze" | "thaw"
) -> Result<Vec<u8>, ServerFnError> {
    let state = use_context::<Arc<AppState>>().ok_or(...) ?;

    let ix = if action == "freeze" {
        crate::solana::freeze_token_account_ix(
            &Pubkey::try_from(token_account.as_str()).unwrap(),
            &Pubkey::try_from(mint.as_str()).unwrap(),
            &Pubkey::try_from(wallet.as_str()).unwrap(),
        )
    } else {
        crate::solana::thaw_token_account_ix(...)
    };

    bincode::serialize(&vec![ix]).map_err(...) ?
}
cargo clean && cargo leptos watch
#[component]
pub fn TradeDesk() -> impl IntoView {
    let wallet_address = create_rw_signal(String::new());
    let connected = create_rw_signal(false);
    let (portfolio, set_portfolio) = create_signal(serde_json::json!({}));
    let (signing, set_signing) = create_signal(false);

    // New: Token Account Input
    let token_account_input = create_rw_signal(String::new());

    let sign_freeze_action = move |action: String| {
        let wallet = wallet_address.get();
        let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("").to_string();
        let token_account = token_account_input.get();

        if wallet.is_empty() || mint.is_empty() || token_account.is_empty() {
            leptos::logging::warn!("Missing wallet, mint, or token account");
            return;
        }

        set_signing.set(true);
        spawn_local(async move {
            match sign_and_send_compliance_action(wallet, mint, token_account, action).await {
                Ok(sig) => leptos::logging::log!("Compliance action successful: {}", sig),
                Err(e) => leptos::logging::error!("Error: {}", e),
            }
            set_signing.set(false);
        });
    };

    view! {
        <div class="bg-zinc-900 rounded-3xl p-8 space-y-8">
            <WalletConnector wallet_address=wallet_address connected=connected />

            {/* Extensions Panel */}
            {move || {
                let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("");
                if is_special_token(mint) {
                    view! {
                        <div class="mt-8 bg-zinc-950 border border-green-500/30 rounded-3xl p-6">
                            <h4 class="text-xl font-bold text-green-400">"Token-2022 Extensions (PUMP & LuminexCore)"</h4>
                            
                            {/* Existing extension buttons ... */}

                            {/* === NEW: Compliance Controls with Token Account Input === */}
                            <div class="mt-10 bg-red-950/50 border border-red-500/30 rounded-3xl p-6">
                                <h4 class="text-lg font-semibold text-red-400 mb-4">"⚠️ Compliance Controls"</h4>
                                
                                <div class="mb-6">
                                    <label class="block text-sm text-zinc-400 mb-2">"Token Account (ATA)"</label>
                                    <input 
                                        type="text"
                                        class="w-full bg-zinc-800 border border-zinc-700 rounded-2xl px-6 py-4 text-white font-mono text-sm"
                                        placeholder="Enter Token Account Address (e.g. ATA for this wallet)..."
                                        on:input=move |ev| token_account_input.set(event_target_value(&ev))
                                    />
                                </div>

                                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                    <button 
                                        on:click=move |_| sign_freeze_action("freeze".to_string())
                                        class="bg-red-600 hover:bg-red-700 py-4 rounded-2xl font-bold text-white disabled:opacity-50"
                                        disabled=move || signing.get() || !connected.get() || token_account_input.get().is_empty()
                                    >
                                        "❄️ Freeze Token Account"
                                    </button>
                                    
                                    <button 
                                        on:click=move |_| sign_freeze_action("thaw".to_string())
                                        class="bg-green-600 hover:bg-green-700 py-4 rounded-2xl font-bold text-white disabled:opacity-50"
                                        disabled=move || signing.get() || !connected.get() || token_account_input.get().is_empty()
                                    >
                                        "🔥 Thaw / Unfreeze Token Account"
                                    </button>
                                </div>
                                
                                <p class="text-xs text-zinc-400 mt-4">
                                    "Freeze/Thaw specific token accounts for regulatory compliance. Only works on LuminexCore & PUMP."
                                </p>
                            </div>
                        </div>
                    }
                } else {
                    view! { <div class="text-zinc-400">"Standard SPL Token – No advanced controls"</div> }
                }
            }}
        </div>
    }
}
pub async fn sign_and_send_compliance_action(
    wallet: String,
    mint: String,
    token_account: String,
    action: String,
) -> Result<String, String> {
    let instructions = fetch_compliance_instructions(wallet.clone(), mint, token_account, action).await?;
    // ... same signing flow as before (build tx, sign with wallet, send)
    // (reuse your existing sign_and_send_extensions logic)
    Ok("Transaction sent".to_string())
}
cargo clean
cargo leptos watch
pub async fn detect_mint_close_authority(
    state: &AppState,
    mint: &str,
) -> Result<Option<String>, String> {
    let mint_pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    
    let account = state.rpc_client
        .get_account_with_commitment(&mint_pubkey, CommitmentConfig::confirmed())
        .await
        .map_err(|e| e.to_string())?;

    let parsed: StateWithExtensions<spl_token_2022::state::Mint> = 
        StateWithExtensions::unpack(&account.value.data)
            .map_err(|e| e.to_string())?;

    if let Ok(close_auth) = parsed.get_extension::<spl_token_2022::extension::mint_close_authority::MintCloseAuthority>() {
        Ok(Some(close_auth.close_authority.to_string()))
    } else {
        Ok(None)
    }
}
pub async fn initialize_special_token_extensions(
    state: &AppState,
    mint: &str,
    owner: &str,
    transfer_hook_program: Option<&str>,
    multisig_freeze_auth: Option<&str>,   // New: Multisig for freeze
) -> Result<Vec<Instruction>, String> {
    if !is_special_token(mint) {
        return Ok(vec![]);
    }

    let mint_pubkey = Pubkey::try_from(mint).map_err(|e| e.to_string())?;
    let owner_pubkey = Pubkey::try_from(owner).map_err(|e| e.to_string())?;
    let mut instructions = vec![];

    let ata = /* ... */;

    // CPI Guard, Metadata Pointer, Transfer Hook (PUMP only) ...

    // Mint Close Authority
    instructions.push(spl_token_2022::instruction::initialize_mint_close_authority(
        &spl_token_2022::id(),
        &mint_pubkey,
        Some(&owner_pubkey),   // or a dedicated compliance key
    ));

    // Multisig Freeze Authority (recommended for compliance)
    if let Some(multisig) = multisig_freeze_auth {
        let multisig_pubkey = Pubkey::try_from(multisig).map_err(|e| e.to_string())?;
        instructions.push(spl_token_2022::instruction::set_authority(
            &spl_token_2022::id(),
            &mint_pubkey,
            Some(&multisig_pubkey),
            spl_token_2022::instruction::AuthorityType::FreezeAccount,
            &owner_pubkey,
            &[],
        ));
    }

    Ok(instructions)
}
// Inside TradeDesk component
let multisig_freeze_input = create_rw_signal(String::new());
let (close_auth, set_close_auth) = create_signal::<Option<String>>(None);

// Auto-detect on portfolio load
create_effect(move |_| {
    let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("");
    if is_special_token(mint) {
        spawn_local(async move {
            if let Ok(auth) = detect_mint_close_authority(&state, mint).await {
                set_close_auth.set(auth);
            }
        });
    }
});

view! {
    {/* ... existing extensions panel */}

    <div class="mt-8 bg-amber-950/50 border border-amber-500/30 rounded-3xl p-6">
        <h4 class="text-lg font-semibold text-amber-400">"🔐 Mint Close Authority (Auto-Detected)"</h4>
        <div class="mt-2 font-mono text-sm text-amber-300">
            {move || close_auth.get().unwrap_or_else(|| "Not Set".to_string())}
        </div>

        {/* Multisig Freeze Authority Input */}
        <div class="mt-6">
            <label class="block text-sm text-zinc-400 mb-2">"Multisig Freeze Authority Address"</label>
            <input 
                type="text"
                class="w-full bg-zinc-800 border border-zinc-700 rounded-2xl px-6 py-4 text-white font-mono"
                placeholder="Enter multisig address for freeze authority..."
                on:input=move |ev| multisig_freeze_input.set(event_target_value(&ev))
            />
        </div>

        <button 
            on:click=move |_| initialize_with_multisig(multisig_freeze_input.get())
            class="mt-4 w-full bg-amber-600 hover:bg-amber-500 py-4 rounded-2xl font-bold"
        >
            "Initialize with Multisig Freeze + Close Authority"
        </button>
    </div>
}
cargo clean && cargo leptos watch
// Inside TradeDesk component
<div class="mt-8 bg-amber-950/50 border border-amber-500/30 rounded-3xl p-6">
    <h4 class="text-lg font-semibold text-amber-400">"🔐 Mint Close Authority & Multisig Freeze"</h4>
    
    <div class="mt-3 font-mono text-sm bg-black/40 p-3 rounded-xl">
        "Close Authority (Auto-Detected): " {move || close_auth.get().unwrap_or_else(|| "Not Set".to_string())}
    </div>

    {/* Multisig Freeze Authority Input */}
    <div class="mt-6">
        <label class="block text-sm text-zinc-400 mb-2">"Multisig Freeze Authority Address"</label>
        <input 
            type="text"
            class="w-full bg-zinc-800 border border-zinc-700 rounded-2xl px-6 py-4 text-white font-mono text-sm"
            placeholder="Enter new multisig address for freeze authority..."
            on:input=move |ev| multisig_freeze_input.set(event_target_value(&ev))
        />
    </div>

    {/* === UPDATED BUTTON: Update Action === */}
    <button 
        on:click=move |_| update_multisig_freeze_authority(multisig_freeze_input.get())
        class="mt-6 w-full bg-amber-600 hover:bg-amber-500 py-4 rounded-2xl font-bold disabled:opacity-50"
        disabled=move || signing.get() || !connected.get() || multisig_freeze_input.get().is_empty()
    >
        {move || if signing.get() { "Updating Authority..." } else { "Update Multisig Freeze Authority" }}
    </button>

    <p class="text-xs text-zinc-400 mt-4">
        "Update the Freeze Authority to a multisig for better compliance control. Only for LuminexCore & PUMP."
    </p>
</div>
// In wallet.rs or TradeDesk
let update_multisig_freeze_authority = move |new_multisig: String| {
    let wallet = wallet_address.get();
    let mint = portfolio.get()["solana"]["mint"].as_str().unwrap_or("").to_string();

    if wallet.is_empty() || mint.is_empty() || new_multisig.is_empty() { return; }

    set_signing.set(true);
    spawn_local(async move {
        match update_freeze_authority(wallet, mint, new_multisig).await {
            Ok(sig) => leptos::logging::log!("Authority updated! Tx: {}", sig),
            Err(e) => leptos::logging::error!("Update failed: {}", e),
        }
        set_signing.set(false);
    });
};
#[server(UpdateFreezeAuthority, "/api")]
pub async fn update_freeze_authority(
    wallet: String,
    mint: String,
    new_multisig: String,
) -> Result<Vec<u8>, ServerFnError> {
    let state = use_context::<Arc<AppState>>()
        .ok_or(ServerFnError::ServerError("State missing".into()))?;

    let mint_pubkey = Pubkey::try_from(mint.as_str()).map_err(|e| ServerFnError::ServerError(e.to_string()))?;
    let new_auth = Pubkey::try_from(new_multisig.as_str()).map_err(|e| ServerFnError::ServerError(e.to_string()))?;
    let current_owner = Pubkey::try_from(wallet.as_str()).map_err(|e| ServerFnError::ServerError(e.to_string()))?;

    let update_ix = spl_token_2022::instruction::set_authority(
        &spl_token_2022::id(),
        &mint_pubkey,
        Some(&new_auth),
        spl_token_2022::instruction::AuthorityType::FreezeAccount,
        &current_owner,
        &[],
    );

    bincode::serialize(&vec![update_ix])
        .map_err(|e| ServerFnError::ServerError(e.to_string()))
}
cargo clean && cargo leptos watch
'use client';

import React, { useState, useEffect } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { WalletMultiButton } from '@solana/wallet-adapter-react-ui';
import { 
  Zap, TrendingUp, Users, DollarSign, Bell, Search, ExternalLink 
} from 'lucide-react';
import { motion, AnimatePresence } from 'framer-motion';
import { useQuery } from '@tanstack/react-query';

import AlertsDrawer from '@/components/AlertsDrawer';
import { connectToNexusCoreBridge } from '@/lib/websocket';

export default function NexusCoreCommandCenter() {
  const { publicKey, connected } = useWallet();
  const [showAlerts, setShowAlerts] = useState(false);
  const [tradeStatus, setTradeStatus] = useState('');
  const [searchQuery, setSearchQuery] = useState('');
  const [liveEvents, setLiveEvents] = useState<any[]>([]);

  // Connect to real-time Helius WebSocket bridge
  useEffect(() => {
    const socket = connectToNexusCoreBridge();

    const handleHeliusEvent = (e: any) => {
      const event = e.detail;
      setLiveEvents(prev => [event, ...prev].slice(0, 20)); // Keep last 20 events
    };

    window.addEventListener('helius-event', handleHeliusEvent);

    return () => {
      window.removeEventListener('helius-event', handleHeliusEvent);
    };
  }, []);

  // Data fetching
  const { data: stats } = useQuery({
    queryKey: ['stats'],
    queryFn: () => fetch('/api/stats').then(res => res.json()),
    refetchInterval: 30000,
  });

  const { data: activities = [] } = useQuery({
    queryKey: ['activities'],
    queryFn: () => fetch('/api/activities').then(res => res.json()),
    refetchInterval: 45000,
  });

  const bondingProgress = 67.4;

  // Explorer Search
  const handleExplorerSearch = async () => {
    if (!searchQuery) return;

    const isWallet = searchQuery.length > 30;
    const endpoint = isWallet 
      ? `/api/explorer/wallet?wallet=${searchQuery}` 
      : `/api/explorer/token?mint=${searchQuery}`;

    try {
      const res = await fetch(endpoint);
      const data = await res.json();
      setTradeStatus(JSON.stringify(data, null, 2));
    } catch (err) {
      setTradeStatus('Explorer search failed');
    }
    setTimeout(() => setTradeStatus(''), 8000);
  };

  return (
    <div className="min-h-screen bg-zinc-950 text-white">
      {/* Header */}
      <div className="sticky top-0 z-50 border-b border-zinc-800 bg-zinc-950/95 backdrop-blur-xl">
        <div className="max-w-7xl mx-auto px-6 py-4 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="w-9 h-9 rounded-xl bg-gradient-to-br from-yellow-500 to-amber-600 flex items-center justify-center">
              <Zap className="w-5 h-5 text-black" />
            </div>
            <div>
              <div className="font-bold text-2xl tracking-tight">NEXUSCORE</div>
              <div className="text-[10px] text-zinc-500 -mt-1">COMMAND CENTER • $PUMP</div>
            </div>
          </div>

          <div className="flex items-center gap-4">
            {/* Explorer Search */}
            <div className="flex items-center bg-zinc-900 border border-zinc-800 rounded-2xl px-3">
              <input
                type="text"
                placeholder="Search wallet or token..."
                className="bg-transparent px-3 py-2 text-sm outline-none w-72"
                value={searchQuery}
                onChange={(e) => setSearchQuery(e.target.value)}
                onKeyDown={(e) => e.key === 'Enter' && handleExplorerSearch()}
              />
              <Search className="w-4 h-4 text-zinc-500 cursor-pointer" onClick={handleExplorerSearch} />
            </div>

            {/* Alerts */}
            <button 
              onClick={() => setShowAlerts(true)}
              className="relative p-2 hover:bg-zinc-900 rounded-xl transition-colors"
            >
              <Bell className="w-5 h-5" />
              {liveEvents.length > 0 && (
                <div className="absolute top-1 right-1 w-2 h-2 bg-yellow-500 rounded-full animate-pulse" />
              )}
            </button>

            <WalletMultiButton className="!bg-zinc-900 !border !border-yellow-600/60 !rounded-2xl" />
          </div>
        </div>
      </div>

      <div className="max-w-7xl mx-auto px-6 pt-8 pb-16">
        {/* Status Toast */}
        <AnimatePresence>
          {tradeStatus && (
            <motion.div 
              initial={{ opacity: 0, y: -10 }} 
              animate={{ opacity: 1, y: 0 }} 
              exit={{ opacity: 0 }}
              className="mb-6 p-4 rounded-2xl bg-zinc-900 border border-yellow-600/30 text-center text-sm font-mono whitespace-pre-wrap"
            >
              {tradeStatus}
            </motion.div>
          )}
        </AnimatePresence>

        <div className="grid grid-cols-1 lg:grid-cols-12 gap-6">
          
          {/* Wallet Status */}
          <div className="lg:col-span-5">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="font-semibold flex items-center gap-2 mb-4">
                <Users className="w-5 h-5" /> WALLET STATUS
              </div>
              {!connected ? (
                <div className="text-center py-6">
                  <WalletMultiButton className="!w-full !py-4 !rounded-2xl !bg-gradient-to-r from-yellow-600 to-amber-600" />
                  <p className="text-xs text-zinc-500 mt-3">Phantom • Backpack • Solflare</p>
                </div>
              ) : (
                <div className="font-mono text-sm break-all">{publicKey?.toBase58()}</div>
              )}
            </div>
          </div>

          {/* Live Stats */}
          <div className="lg:col-span-7">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="font-semibold flex items-center gap-2 mb-5">
                <TrendingUp className="w-5 h-5" /> LIVE ON-CHAIN STATS
              </div>
              <div className="grid grid-cols-1 sm:grid-cols-3 gap-4">
                <div className="bg-zinc-950 rounded-xl p-4 border border-zinc-800">
                  <div className="text-xs text-zinc-500">TOTAL TRANSFERS</div>
                  <div className="text-3xl font-mono font-bold mt-1">
                    {stats?.totalTransfers?.toLocaleString() || '—'}
                  </div>
                </div>
                <div className="bg-zinc-950 rounded-xl p-4 border border-zinc-800">
                  <div className="text-xs text-zinc-500">TREASURY BALANCE</div>
                  <div className="text-3xl font-mono font-bold mt-1 text-yellow-400">
                    {stats?.treasuryBalance || '—'} $PUMP
                  </div>
                </div>
                <div className="bg-zinc-950 rounded-xl p-4 border border-zinc-800">
                  <div className="text-xs text-zinc-500">TRANSFER TAX</div>
                  <div className="text-3xl font-mono font-bold mt-1 text-yellow-400">1%</div>
                </div>
              </div>
            </div>
          </div>

          {/* Recent Activity + Live Helius Events */}
          <div className="lg:col-span-7">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="font-semibold mb-4 flex items-center justify-between">
                <span>RECENT ACTIVITY + LIVE HELIUS</span>
                <span className="text-xs text-yellow-400">REAL-TIME</span>
              </div>
              <div className="space-y-2 max-h-[320px] overflow-auto pr-2 text-sm">
                {[...activities, ...liveEvents].slice(0, 12).map((item, index) => (
                  <div key={index} className="p-3 bg-zinc-950 rounded-xl border border-zinc-800">
                    {item.type === 'helius-event' ? (
                      <div className="text-yellow-400">Helius Event: {item.channel}</div>
                    ) : (
                      <div>{item.type?.toUpperCase()} • {item.amount} $PUMP</div>
                    )}
                  </div>
                ))}
              </div>
            </div>
          </div>

          {/* Treasury */}
          <div className="lg:col-span-5">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="font-semibold mb-4 flex items-center gap-2">
                <DollarSign className="w-5 h-5" /> TAX &amp; TREASURY
              </div>
              <button 
                onClick={() => setTradeStatus('Withdrawal feature coming soon with Squads Multisig')}
                className="w-full py-4 bg-gradient-to-r from-yellow-600 to-amber-600 text-black font-bold rounded-2xl hover:from-yellow-500 hover:to-amber-500 transition-all"
              >
                WITHDRAW FEES
              </button>
            </div>
          </div>

          {/* Trade & Bridge */}
          <div className="lg:col-span-12">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="font-semibold mb-4">TRADE &amp; BRIDGE</div>
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                <button onClick={() => window.open('https://jup.ag', '_blank')} className="py-4 bg-gradient-to-r from-yellow-600 to-amber-600 text-black font-bold rounded-2xl">
                  SWAP ON JUPITER
                </button>
                <button onClick={() => window.open('https://wormhole.com', '_blank')} className="py-4 bg-zinc-800 hover:bg-zinc-700 border border-yellow-600/50 rounded-2xl font-bold">
                  WORMHOLE BRIDGE
                </button>
                <button onClick={() => window.open('https://layerzeroscan.com', '_blank')} className="py-4 bg-zinc-800 hover:bg-zinc-700 border border-yellow-600/50 rounded-2xl font-bold">
                  LAYERZERO (OFT)
                </button>
                <button onClick={() => window.open('https://app.uniswap.org', '_blank')} className="py-4 bg-zinc-800 hover:bg-zinc-700 border border-yellow-600/50 rounded-2xl font-bold">
                  UNISWAP (BASE)
                </button>
              </div>
            </div>
          </div>

          {/* Bonding Curve */}
          <div className="lg:col-span-12">
            <div className="bg-zinc-900/80 border border-zinc-800 rounded-2xl p-6">
              <div className="flex justify-between text-sm mb-3">
                <span className="font-semibold">BONDING CURVE PROGRESS</span>
                <span className="font-mono text-yellow-400">{bondingProgress}%</span>
              </div>
              <div className="w-full bg-zinc-800 rounded-full h-3 overflow-hidden">
                <div 
                  className="h-3 rounded-full bg-gradient-to-r from-yellow-500 to-amber-500 transition-all duration-700" 
                  style={{ width: `${bondingProgress}%` }} 
                />
              </div>
            </div>
          </div>

        </div>
      </div>

      {/* Alerts Drawer */}
      <AlertsDrawer isOpen={showAlerts} onClose={() => setShowAlerts(false)} />
    </div>
  );
}
'use client';

import React from 'react';
import { X, Bell } from 'lucide-react';
import { motion, AnimatePresence } from 'framer-motion';

interface AlertsDrawerProps {
  isOpen: boolean;
  onClose: () => void;
}

export default function AlertsDrawer({ isOpen, onClose }: AlertsDrawerProps) {
  // In production, this would come from state, WebSocket, or API
  const alerts = [
    {
      id: 1,
      type: 'security',
      title: 'Post-Quantum Security Active',
      message: 'Hybrid Dilithium2 + Ed25519 signatures enabled for all high-value actions.',
      timestamp: new Date().toISOString(),
    },
    {
      id: 2,
      type: 'system',
      title: 'Helius Real-time Connected',
      message: 'Live transaction streaming via Rust microservice is active.',
      timestamp: new Date(Date.now() - 1000 * 60 * 3).toISOString(),
    },
    {
      id: 3,
      type: 'event',
      title: 'New Token Launch Detected',
      message: 'A new token was launched via the bonding curve.',
      timestamp: new Date(Date.now() - 1000 * 60 * 12).toISOString(),
    },
  ];

  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 z-[100] flex justify-end">
      <div 
        className="absolute inset-0 bg-black/60" 
        onClick={onClose} 
      />
      
      <motion.div
        initial={{ x: '100%' }}
        animate={{ x: 0 }}
        exit={{ x: '100%' }}
        transition={{ type: 'spring', damping: 25, stiffness: 200 }}
        className="relative w-full max-w-md bg-zinc-900 border-l border-zinc-800 flex flex-col h-full"
      >
        {/* Header */}
        <div className="flex items-center justify-between px-6 py-5 border-b border-zinc-800">
          <div className="flex items-center gap-3">
            <Bell className="w-5 h-5 text-yellow-400" />
            <h2 className="text-xl font-semibold">Alerts</h2>
          </div>
          <button 
            onClick={onClose}
            className="p-2 hover:bg-zinc-800 rounded-xl transition-colors"
          >
            <X className="w-5 h-5" />
          </button>
        </div>

        {/* Alerts List */}
        <div className="flex-1 overflow-y-auto p-6 space-y-4">
          {alerts.length === 0 ? (
            <div className="text-center text-zinc-500 py-12">
              No alerts at the moment.
            </div>
          ) : (
            alerts.map((alert) => (
              <div 
                key={alert.id} 
                className="bg-zinc-950 border border-zinc-800 rounded-2xl p-4"
              >
                <div className="flex items-start justify-between">
                  <div>
                    <div className="font-medium text-yellow-400">{alert.title}</div>
                    <p className="text-sm text-zinc-400 mt-1">{alert.message}</p>
                  </div>
                </div>
                <div className="text-[10px] text-zinc-500 mt-3">
                  {new Date(alert.timestamp).toLocaleTimeString()}
                </div>
              </div>
            ))
          )}
        </div>

        <div className="p-6 border-t border-zinc-800 text-xs text-zinc-500">
          Alerts are powered by real-time Helius events and system monitoring.
        </div>
      </motion.div>
    </div>
  );
}
// frontend/src/lib/websocket.ts
import { io, Socket } from 'socket.io-client';

let socket: Socket | null = null;

export function connectToNexusCoreBridge() {
  if (socket) return socket;

  const backendUrl = process.env.NEXT_PUBLIC_BACKEND_URL || 'http://localhost:4000';

  socket = io(backendUrl, {
    transports: ['websocket'],
    reconnection: true,
    reconnectionAttempts: 5,
  });

  socket.on('connect', () => {
    console.log('[WebSocket] Connected to NexusCore Bridge');
  });

  socket.on('helius-event', (event) => {
    // Dispatch as custom event so any component can listen
    window.dispatchEvent(new CustomEvent('helius-event', { detail: event }));
  });

  socket.on('disconnect', () => {
    console.log('[WebSocket] Disconnected from NexusCore Bridge');
  });

  socket.on('connect_error', (err) => {
    console.error('[WebSocket] Connection error:', err);
  });

  return socket;
}

export function disconnectFromBridge() {
  if (socket) {
    socket.disconnect();
    socket = null;
  }
}

export default socket;
import AlertsDrawer from '@/components/AlertsDrawer';
import { connectToNexusCoreBridge } from '@/lib/websocket';
'use client';

import { useState, useEffect } from 'react';
import { connectToNexusCoreBridge } from '@/lib/websocket';

export interface HeliusEvent {
  channel: string;
  data: any;
  timestamp: number;
}

export function useHeliusEvents(maxEvents: number = 50) {
  const [events, setEvents] = useState<HeliusEvent[]>([]);

  useEffect(() => {
    // Ensure connection to the bridge
    connectToNexusCoreBridge();

    const handleEvent = (e: CustomEvent) => {
      const newEvent: HeliusEvent = {
        channel: e.detail.channel,
        data: e.detail.data,
        timestamp: e.detail.timestamp || Date.now(),
      };

      setEvents(prev => [newEvent, ...prev].slice(0, maxEvents));
    };

    window.addEventListener('helius-event', handleEvent as EventListener);

    return () => {
      window.removeEventListener('helius-event', handleEvent as EventListener);
    };
  }, [maxEvents]);

  return events;
}
const events = useHeliusEvents(30);

// Example: Show latest event
{events.length > 0 && (
  <div>Latest Helius Event: {events[0].channel}</div>
)}
use axum::{
    routing::{get, post},
    Router,
};
use std::sync::Arc;
use tracing::info;

mod handlers;
mod websocket;

use handlers::{assets, transactions};
use websocket::helius_ws_handler;

#[derive(Clone)]
pub struct AppState {
    pub helius: helius::Helius,
}

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt::init();

    let helius = helius::Helius::new("YOUR_API_KEY", helius::types::Cluster::MainnetBeta)
        .expect("Failed to create Helius client");

    let state = AppState { helius: helius.clone() };

    let app = Router::new()
        .route("/health", get(|| async { "ok" }))
        .route("/metrics", get(handlers::metrics::metrics_handler))
        .route("/assets/owner", post(assets::get_assets_by_owner))
        .route("/assets/search", post(assets::search_assets))
        .route("/assets/proof", post(assets::get_asset_proof))
        .route("/transactions/parse", post(transactions::parse_transactions))
        .route("/ws/helius", get(helius_ws_handler))
        .with_state(state);

    let listener = tokio::net::TcpListener::bind("0.0.0.0:8080").await.unwrap();
    info!("🚀 Helius Microservice running on port 8080");
    axum::serve(listener, app).await.unwrap();
}
use axum::extract::ws::{WebSocket, WebSocketUpgrade};
use axum::response::Response;
use futures::{sink::SinkExt, stream::StreamExt};
use tracing::info;

pub async fn helius_ws_handler(ws: WebSocketUpgrade) -> Response {
    ws.on_upgrade(handle_socket)
}

async fn handle_socket(mut socket: WebSocket) {
    info!("Helius WebSocket client connected");

    // In production: Connect to Helius WebSocket and forward events here
    // For now we keep connection alive and can push messages from other parts of the app

    while let Some(Ok(msg)) = socket.next().await {
        if let Ok(text) = msg.to_text() {
            if text == "ping" {
                let _ = socket.send(axum::extract::ws::Message::Text("pong".into())).await;
            }
        }
    }

    info!("Helius WebSocket client disconnected");
}

// Example function to broadcast to all connected clients (can be called from other modules)
pub async fn broadcast_event(event: serde_json::Value) {
    // In a real implementation, you would keep a list of connected sockets
    // and broadcast to them. This is a simplified version.
    tracing::info!("Broadcasting event: {:?}", event);
}
useEffect(() => {
  const handleHeliusEvent = (e: any) => {
    const event = e.detail;

    // Dispatch to Alerts system
    const alertEvent = new CustomEvent('new-alert', {
      detail: {
        id: Date.now(),
        type: 'helius',
        title: `Helius Event: ${event.channel}`,
        message: `New activity detected on chain`,
        timestamp: new Date().toISOString(),
      },
    });

    window.dispatchEvent(alertEvent);
  };

  window.addEventListener('helius-event', handleHeliusEvent);

  return () => {
    window.removeEventListener('helius-event', handleHeliusEvent);
  };
}, []);
use axum::{routing::{get, post}, Router};
use std::sync::Arc;
use tokio::sync::broadcast;
use tracing::info;

mod handlers;
mod websocket;

use handlers::{assets, transactions};
use websocket::{helius_ws_handler, AppState};

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt::init();

    let helius = helius::Helius::new("YOUR_API_KEY", helius::types::Cluster::MainnetBeta)
        .expect("Failed to create Helius client");

    // Broadcast channel for WebSocket messages (capacity = 100)
    let (tx, _rx) = broadcast::channel::<serde_json::Value>(100);

    let state = AppState {
        helius,
        broadcast_tx: tx.clone(),
    };

    let app = Router::new()
        .route("/health", get(|| async { "ok" }))
        .route("/metrics", get(handlers::metrics::metrics_handler))
        .route("/assets/owner", post(assets::get_assets_by_owner))
        .route("/assets/search", post(assets::search_assets))
        .route("/assets/proof", post(assets::get_asset_proof))
        .route("/transactions/parse", post(transactions::parse_transactions))
        .route("/ws/helius", get(helius_ws_handler))
        .with_state(state);

    // Example: You can send messages from anywhere using `state.broadcast_tx.send(...)`

    let listener = tokio::net::TcpListener::bind("0.0.0.0:8080").await.unwrap();
    info!("🚀 Helius Microservice with WebSocket broadcasting running on :8080");
    axum::serve(listener, app).await.unwrap();
}
use axum::extract::ws::{WebSocket, WebSocketUpgrade};
use axum::extract::State;
use axum::response::Response;
use futures::{sink::SinkExt, stream::StreamExt};
use tokio::sync::broadcast;
use tracing::info;

use crate::AppState;

pub async fn helius_ws_handler(
    ws: WebSocketUpgrade,
    State(state): State<AppState>,
) -> Response {
    ws.on_upgrade(move |socket| handle_socket(socket, state.broadcast_tx))
}

async fn handle_socket(mut socket: WebSocket, mut rx: broadcast::Receiver<serde_json::Value>) {
    info!("Helius WebSocket client connected");

    // Spawn task to forward broadcast messages to this client
    let mut send_task = tokio::spawn(async move {
        while let Ok(msg) = rx.recv().await {
            if socket
                .send(axum::extract::ws::Message::Text(msg.to_string()))
                .await
                .is_err()
            {
                break;
            }
        }
    });

    // Keep connection alive
    while let Some(Ok(_msg)) = socket.next().await {}

    send_task.abort();
    info!("Helius WebSocket client disconnected");
}
state.broadcast_tx.send(serde_json::json!({
    "type": "helius_event",
    "channel": "token_transfer",
    "data": payload
})).ok();
'use client';

import React, { useState, useEffect } from 'react';
import { X, Bell } from 'lucide-react';
import { motion } from 'framer-motion';

interface Alert {
  id: number;
  type: string;
  title: string;
  message: string;
  timestamp: string;
}

interface AlertsDrawerProps {
  isOpen: boolean;
  onClose: () => void;
}

export default function AlertsDrawer({ isOpen, onClose }: AlertsDrawerProps) {
  const [alerts, setAlerts] = useState<Alert[]>([
    {
      id: 1,
      type: 'security',
      title: 'Post-Quantum Security Active',
      message: 'Hybrid Dilithium2 signatures enabled for high-value actions.',
      timestamp: new Date().toISOString(),
    },
  ]);

  // Listen for real-time alerts from Helius / system
  useEffect(() => {
    const handleNewAlert = (e: CustomEvent) => {
      const newAlert: Alert = e.detail;
      setAlerts(prev => [newAlert, ...prev].slice(0, 30)); // Keep last 30
    };

    window.addEventListener('new-alert', handleNewAlert as EventListener);

    return () => {
      window.removeEventListener('new-alert', handleNewAlert as EventListener);
    };
  }, []);

  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 z-[100] flex justify-end">
      <div className="absolute inset-0 bg-black/60" onClick={onClose} />
      
      <motion.div
        initial={{ x: '100%' }}
        animate={{ x: 0 }}
        className="relative w-full max-w-md bg-zinc-900 border-l border-zinc-800 flex flex-col h-full"
      >
        <div className="flex items-center justify-between px-6 py-5 border-b border-zinc-800">
          <div className="flex items-center gap-3">
            <Bell className="w-5 h-5 text-yellow-400" />
            <h2 className="text-xl font-semibold">Alerts</h2>
          </div>
          <button onClick={onClose} className="p-2 hover:bg-zinc-800 rounded-xl">
            <X className="w-5 h-5" />
          </button>
        </div>

        <div className="flex-1 overflow-y-auto p-6 space-y-4">
          {alerts.length === 0 ? (
            <div className="text-center text-zinc-500 py-12">No alerts yet.</div>
          ) : (
            alerts.map((alert) => (
              <div key={alert.id} className="bg-zinc-950 border border-zinc-800 rounded-2xl p-4">
                <div className="font-medium text-yellow-400">{alert.title}</div>
                <p className="text-sm text-zinc-400 mt-1">{alert.message}</p>
                <div className="text-[10px] text-zinc-500 mt-3">
                  {new Date(alert.timestamp).toLocaleTimeString()}
                </div>
              </div>
            ))
          )}
        </div>
      </motion.div>
    </div>
  );
}
use axum::{extract::State, Json};
use serde_json::json;
use std::sync::atomic::{AtomicU64, Ordering};
use std::sync::Arc;
use tokio::sync::RwLock;

#[derive(Default)]
pub struct Metrics {
    pub requests_total: AtomicU64,
    pub websocket_connections: AtomicU64,
    pub helius_api_calls: AtomicU64,
    pub errors_total: AtomicU64,
}

pub async fn metrics_handler(State(state): State<crate::AppState>) -> Json<serde_json::Value> {
    let metrics = &state.metrics;

    Json(json!({
        "service": "helius-microservice",
        "timestamp": chrono::Utc::now().to_rfc3339(),
        "metrics": {
            "requests_total": metrics.requests_total.load(Ordering::Relaxed),
            "websocket_connections": metrics.websocket_connections.load(Ordering::Relaxed),
            "helius_api_calls": metrics.helius_api_calls.load(Ordering::Relaxed),
            "errors_total": metrics.errors_total.load(Ordering::Relaxed),
        }
    }))
}
#[derive(Clone)]
pub struct AppState {
    pub helius: helius::Helius,
    pub broadcast_tx: tokio::sync::broadcast::Sender<serde_json::Value>,
    pub metrics: Arc<crate::handlers::metrics::Metrics>,
}
.route("/metrics", get(handlers::metrics::metrics_handler))
- job_name: 'helius-service'
  static_configs:
    - targets: ['helius-service:8080']
  metrics_path: /metrics
  scrape_interval: 15s
[dependencies]
prometheus = "0.13"
lazy_static = "1.4"
use axum::response::Response;
use lazy_static::lazy_static;
use prometheus::{register_counter, register_gauge, Counter, Gauge, TextEncoder, Encoder};
use std::sync::atomic::{AtomicU64, Ordering};

lazy_static! {
    pub static ref REQUESTS_TOTAL: Counter = register_counter!(
        "helius_requests_total",
        "Total number of HTTP requests"
    ).unwrap();

    pub static ref WEBSOCKET_CONNECTIONS: Gauge = register_gauge!(
        "helius_websocket_connections",
        "Current number of active WebSocket connections"
    ).unwrap();

    pub static ref HELIUS_API_CALLS: Counter = register_counter!(
        "helius_api_calls_total",
        "Total calls made to Helius API"
    ).unwrap();

    pub static ref ERRORS_TOTAL: Counter = register_counter!(
        "helius_errors_total",
        "Total number of errors"
    ).unwrap();
}

pub async fn metrics_handler() -> Response<String> {
    let encoder = TextEncoder::new();
    let metric_families = prometheus::gather();
    let mut buffer = Vec::new();
    encoder.encode(&metric_families, &mut buffer).unwrap();

    Response::builder()
        .header("Content-Type", encoder.format_type())
        .body(String::from_utf8(buffer).unwrap())
        .unwrap()
}
.route("/metrics", get(handlers::metrics::metrics_handler))
use crate::handlers::metrics::{REQUESTS_TOTAL, HELIUS_API_CALLS};

REQUESTS_TOTAL.inc();
HELIUS_API_CALLS.inc();
use crate::handlers::metrics::WEBSOCKET_CONNECTIONS;

pub async fn helius_ws_handler(...) {
    WEBSOCKET_CONNECTIONS.inc();
    
    // ... connection logic ...

    WEBSOCKET_CONNECTIONS.dec(); // on disconnect
}
[dependencies]
moka = { version = "0.12", features = ["future"] }
tokio = { version = "1", features = ["full"] }
# ... other dependencies
use moka::future::Cache;
use std::time::Duration;

pub fn create_das_cache() -> Cache<String, serde_json::Value> {
    Cache::builder()
        .max_capacity(10_000)                    // Max 10k entries
        .time_to_live(Duration::from_secs(300))  // 5 minutes TTL
        .build()
}
use crate::cache::create_das_cache;

#[derive(Clone)]
pub struct AppState {
    pub helius: helius::Helius,
    pub broadcast_tx: tokio::sync::broadcast::Sender<serde_json::Value>,
    pub das_cache: moka::future::Cache<String, serde_json::Value>,
}

#[tokio::main]
async fn main() {
    // ... existing code

    let das_cache = create_das_cache();

    let state = AppState {
        helius,
        broadcast_tx: tx.clone(),
        das_cache,
    };

    // ... router setup
}
use axum::{extract::State, Json};
use helius::types::GetAssetsByOwnerRequest;
use serde::Deserialize;

#[derive(Deserialize)]
pub struct OwnerRequest {
    pub owner: String,
    pub page: Option<u32>,
    pub limit: Option<u32>,
}

pub async fn get_assets_by_owner(
    State(state): State<crate::AppState>,
    Json(payload): Json<OwnerRequest>,
) -> Json<serde_json::Value> {
    let cache_key = format!("assets_owner:{}:{}:{}", 
        payload.owner, 
        payload.page.unwrap_or(1), 
        payload.limit.unwrap_or(100)
    );

    // Try cache first
    if let Some(cached) = state.das_cache.get(&cache_key).await {
        return Json(cached);
    }

    let request = GetAssetsByOwnerRequest {
        owner_address: payload.owner.clone(),
        page: payload.page.unwrap_or(1),
        limit: payload.limit.unwrap_or(100),
        display_options: None,
    };

    match state.helius.rpc().get_assets_by_owner(request).await {
        Ok(response) => {
            let result = serde_json::json!({
                "success": true,
                "assets": response.items,
                "total": response.total
            });

            // Store in cache
            state.das_cache.insert(cache_key, result.clone()).await;

            Json(result)
        }
        Err(e) => Json(serde_json::json!({
            "success": false,
            "error": e.to_string()
        })),
    }
}
{
  "root": " Merkle root hash",
  "proof": ["hash1", "hash2", ...],
  "node_index": 123,
  "leaf": "leaf hash",
  "tree_id": "Merkle tree address"
}
pub async fn get_asset_proof(
    State(state): State<crate::AppState>,
    Json(payload): Json<helius::types::GetAssetProofRequest>,
) -> Json<serde_json::Value> {
    match state.helius.rpc().get_asset_proof(payload).await {
        Ok(proof) => Json(serde_json::json!({
            "success": true,
            "proof": proof
        })),
        Err(e) => Json(serde_json::json!({
            "success": false,
            "error": e.to_string()
        })),
    }
}

