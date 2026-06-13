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
