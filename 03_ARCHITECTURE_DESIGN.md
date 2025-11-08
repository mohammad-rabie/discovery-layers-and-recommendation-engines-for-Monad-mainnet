# System Architecture & Design

## 📋 Table of Contents
1. [System Overview](#system-overview)
2. [Architecture Diagram](#architecture-diagram)
3. [Component Design](#component-design)
4. [Database Schema](#database-schema)
5. [API Design](#api-design)
6. [Data Flow](#data-flow)
7. [Technology Integration](#technology-integration)
8. [Security Architecture](#security-architecture)

---

## 🏗️ System Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         USER LAYER                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ Desktop  │  │  Mobile  │  │  Tablet  │  │    VR    │  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  │
└───────┼─────────────┼─────────────┼─────────────┼─────────┘
        │             │             │             │
        └─────────────┴─────────────┴─────────────┘
                      │
        ┌─────────────▼─────────────┐
        │    CDN / Edge Network     │
        │      (Vercel/Netlify)     │
        └─────────────┬─────────────┘
                      │
┌─────────────────────▼─────────────────────────────────────┐
│                    FRONTEND LAYER                          │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         React Application (Next.js/Vite)            │  │
│  ├─────────────────────────────────────────────────────┤  │
│  │  UI Components │ 3D Scene │ State Mgmt │ Routing   │  │
│  └─────────────────────────────────────────────────────┘  │
└─────────────────────┬─────────────────────────────────────┘
                      │
        ┌─────────────▼─────────────┐
        │    Load Balancer          │
        │    (Optional)             │
        └─────────────┬─────────────┘
                      │
┌─────────────────────▼─────────────────────────────────────┐
│                    BACKEND LAYER                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │  REST API    │  │  WebSocket   │  │  GraphQL     │   │
│  │  (Express)   │  │  (Optional)  │  │  (Optional)  │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│         │                  │                  │            │
│  ┌──────▼──────────────────▼──────────────────▼───────┐  │
│  │          Business Logic Layer                       │  │
│  │  ┌──────────┐ ┌──────────┐ ┌────────────────────┐ │  │
│  │  │   Dapp   │ │   User   │ │   Recommendation   │ │  │
│  │  │ Service  │ │ Service  │ │      Engine        │ │  │
│  │  └──────────┘ └──────────┘ └────────────────────┘ │  │
│  └───────────────────────┬───────────────────────────┘  │
└──────────────────────────┼──────────────────────────────┘
                           │
        ┌──────────────────▼──────────────────┐
        │        DATA LAYER                   │
        │  ┌──────────┐    ┌──────────────┐  │
        │  │PostgreSQL│    │    Redis     │  │
        │  │ (Primary)│    │   (Cache)    │  │
        │  └──────────┘    └──────────────┘  │
        └──────────────────┬──────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│              BLOCKCHAIN LAYER                            │
│  ┌────────────────────────────────────────────────────┐ │
│  │           Monad Mainnet RPC Node                   │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────┐│ │
│  │  │ Protocol │  │ Protocol │  │    Protocol      ││ │
│  │  │ Contract │  │ Contract │  │    Contract      ││ │
│  │  └──────────┘  └──────────┘  └──────────────────┘│ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

        ┌──────────────────────────────────────┐
        │      EXTERNAL SERVICES               │
        │  ┌──────────┐    ┌──────────────┐   │
        │  │ Analytics│    │   DeFiLlama  │   │
        │  │ (Mixpanel)│   │     API      │   │
        │  └──────────┘    └──────────────┘   │
        └──────────────────────────────────────┘
```

---

## 🔷 Architecture Diagram

### Complete System Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        A[Web Browser]
        B[Mobile Browser]
        C[MetaMask Wallet]
    end
    
    subgraph "CDN"
        D[Vercel Edge Network]
    end
    
    subgraph "Frontend - React + Three.js"
        E[Pages/Routes]
        F[UI Components]
        G[3D Scene Manager]
        H[State Management]
        I[Web3 Provider]
    end
    
    subgraph "API Gateway"
        J[Express Server]
        K[Rate Limiter]
        L[Auth Middleware]
    end
    
    subgraph "Backend Services"
        M[Dapp Controller]
        N[User Controller]
        O[Recommendation Engine]
        P[Analytics Service]
        Q[Monad Integration Service]
    end
    
    subgraph "Data Layer"
        R[(PostgreSQL)]
        S[(Redis Cache)]
    end
    
    subgraph "Blockchain"
        T[Monad RPC]
        U[Smart Contracts]
    end
    
    subgraph "External APIs"
        V[DeFiLlama]
        W[Monad Ecosystem API]
    end
    
    A --> D
    B --> D
    D --> E
    E --> F
    E --> G
    E --> H
    C --> I
    I --> H
    
    F --> J
    G --> J
    H --> J
    
    J --> K
    K --> L
    L --> M
    L --> N
    L --> O
    
    M --> P
    M --> Q
    N --> P
    O --> P
    
    M --> R
    N --> R
    O --> R
    P --> S
    
    Q --> T
    T --> U
    
    Q --> V
    Q --> W
    
    style A fill:#e1f5fe
    style E fill:#fff9c4
    style J fill:#f3e5f5
    style R fill:#c8e6c9
    style T fill:#ffccbc
```

---

## 🎨 Component Design

### Frontend Component Hierarchy

```
App
├── Layout
│   ├── Header
│   │   ├── Logo
│   │   ├── Navigation
│   │   └── WalletConnect
│   ├── Sidebar (optional)
│   └── Footer
│
├── Pages
│   ├── HomePage
│   │   ├── HeroSection
│   │   ├── FeaturedDapps
│   │   ├── Categories
│   │   └── TrendingList
│   │
│   ├── ExplorePage
│   │   ├── SearchBar
│   │   ├── FilterSidebar
│   │   │   ├── CategoryFilter
│   │   │   ├── TVLRangeFilter
│   │   │   ├── TagFilter
│   │   │   └── SortOptions
│   │   ├── ViewToggle (Grid/List/3D)
│   │   └── DappGrid
│   │       └── DappCard[]
│   │
│   ├── DappDetailPage
│   │   ├── DappHeader
│   │   ├── DappStats
│   │   ├── DappDescription
│   │   ├── ContractInfo
│   │   ├── ChartsSection
│   │   └── SimilarDapps
│   │
│   ├── ImmersivePage (3D View)
│   │   ├── Three.js Canvas
│   │   ├── Camera Controls
│   │   ├── Dapp Nodes
│   │   ├── Connections
│   │   └── InfoOverlay
│   │
│   └── ProfilePage (optional)
│       ├── UserStats
│       ├── Achievements
│       ├── FavoriteDapps
│       └── ActivityHistory
│
├── Common Components
│   ├── Button
│   ├── Card
│   ├── Modal
│   ├── Toast
│   ├── Skeleton
│   ├── EmptyState
│   └── ErrorBoundary
│
└── 3D Components (React Three Fiber)
    ├── Scene
    ├── DappSphere
    ├── ConnectionLine
    ├── ParticleSystem
    ├── Lights
    └── PostProcessing
```

### Component Specifications

#### DappCard Component

```typescript
interface DappCardProps {
  dapp: {
    id: number;
    name: string;
    logo: string;
    category: string;
    description: string;
    tvl: number;
    users: number;
    tags: string[];
  };
  variant?: 'default' | 'featured' | 'compact';
  onClick?: () => void;
}

// Usage
<DappCard
  dapp={dappData}
  variant="featured"
  onClick={() => navigate(`/dapp/${dappData.id}`)}
/>
```

#### SearchBar Component

```typescript
interface SearchBarProps {
  placeholder?: string;
  onSearch: (query: string) => void;
  suggestions?: Dapp[];
  loading?: boolean;
}

// Features
- Real-time search
- Autocomplete
- Recent searches
- Keyboard navigation (↑↓ arrows, Enter, Esc)
```

#### FilterSidebar Component

```typescript
interface FilterSidebarProps {
  categories: string[];
  tags: string[];
  tvlRange: [number, number];
  onFilterChange: (filters: FilterState) => void;
  activeFilters: FilterState;
}

// Features
- Multi-select categories
- TVL range slider
- Tag cloud
- Clear all button
- Applied filters count badge
```

---

## 💾 Database Schema

### Entity Relationship Diagram

```mermaid
erDiagram
    DAPP ||--o{ INTERACTION : receives
    USER ||--o{ INTERACTION : performs
    DAPP ||--o{ TAG : has
    DAPP }o--|| CATEGORY : belongs_to
    USER ||--o{ FAVORITE : has
    FAVORITE }o--|| DAPP : references
    USER ||--o{ ACHIEVEMENT : earns
    
    DAPP {
        int id PK
        string name
        string slug
        string description
        string logo_url
        string website
        string contract_address
        int category_id FK
        decimal tvl
        int user_count
        int tx_count_24h
        decimal volume_24h
        json social_links
        timestamp created_at
        timestamp updated_at
        boolean is_active
    }
    
    CATEGORY {
        int id PK
        string name
        string slug
        string icon
        string color
        int dapp_count
    }
    
    TAG {
        int id PK
        int dapp_id FK
        string name
    }
    
    USER {
        int id PK
        string wallet_address UK
        string username
        string avatar
        json preferences
        int total_views
        int achievements_count
        timestamp last_active
        timestamp created_at
    }
    
    INTERACTION {
        int id PK
        int user_id FK
        int dapp_id FK
        string type
        int duration_seconds
        json metadata
        timestamp created_at
    }
    
    FAVORITE {
        int id PK
        int user_id FK
        int dapp_id FK
        timestamp created_at
    }
    
    ACHIEVEMENT {
        int id PK
        int user_id FK
        string achievement_type
        json metadata
        timestamp earned_at
    }
```

### Table Definitions

#### dapps

```sql
CREATE TABLE dapps (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    logo_url VARCHAR(500),
    website VARCHAR(500),
    contract_address VARCHAR(42),
    category_id INTEGER REFERENCES categories(id),
    tvl DECIMAL(20, 2) DEFAULT 0,
    user_count INTEGER DEFAULT 0,
    tx_count_24h INTEGER DEFAULT 0,
    volume_24h DECIMAL(20, 2) DEFAULT 0,
    social_links JSONB DEFAULT '{}',
    metadata JSONB DEFAULT '{}',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Indexes for performance
    INDEX idx_category (category_id),
    INDEX idx_tvl (tvl DESC),
    INDEX idx_users (user_count DESC),
    INDEX idx_slug (slug),
    INDEX idx_contract (contract_address),
    FULLTEXT INDEX idx_search (name, description)
);

-- Sample data
INSERT INTO dapps (name, slug, description, category_id, tvl, user_count) VALUES
('MonadSwap', 'monadswap', 'Leading DEX on Monad', 1, 50000000, 15000),
('NFTMarket', 'nftmarket', 'NFT marketplace for creators', 2, 5000000, 8000);
```

#### categories

```sql
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    slug VARCHAR(50) NOT NULL UNIQUE,
    icon VARCHAR(50),
    color VARCHAR(7), -- Hex color
    description TEXT,
    dapp_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Sample categories
INSERT INTO categories (name, slug, icon, color) VALUES
('DeFi', 'defi', '💰', '#4CAF50'),
('NFT', 'nft', '🎨', '#9C27B0'),
('Gaming', 'gaming', '🎮', '#FF5722'),
('Infrastructure', 'infrastructure', '🏗️', '#2196F3'),
('Social', 'social', '👥', '#FF9800'),
('DAO', 'dao', '🏛️', '#00BCD4');
```

#### tags

```sql
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    dapp_id INTEGER REFERENCES dapps(id) ON DELETE CASCADE,
    name VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(dapp_id, name),
    INDEX idx_dapp (dapp_id),
    INDEX idx_name (name)
);

-- Sample tags
INSERT INTO tags (dapp_id, name) VALUES
(1, 'DEX'),
(1, 'AMM'),
(1, 'Yield'),
(2, 'Marketplace'),
(2, 'Creator Tools');
```

#### users

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    wallet_address VARCHAR(42) UNIQUE NOT NULL,
    username VARCHAR(50),
    avatar VARCHAR(500),
    preferences JSONB DEFAULT '{}',
    stats JSONB DEFAULT '{"views": 0, "interactions": 0}',
    total_views INTEGER DEFAULT 0,
    achievements_count INTEGER DEFAULT 0,
    last_active TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_wallet (wallet_address),
    INDEX idx_last_active (last_active DESC)
);
```

#### interactions

```sql
CREATE TABLE interactions (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    dapp_id INTEGER REFERENCES dapps(id) ON DELETE CASCADE,
    interaction_type VARCHAR(20) NOT NULL, -- 'view', 'click', 'favorite', 'share'
    duration_seconds INTEGER DEFAULT 0,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user (user_id),
    INDEX idx_dapp (dapp_id),
    INDEX idx_type (interaction_type),
    INDEX idx_created (created_at DESC),
    INDEX idx_user_dapp (user_id, dapp_id)
);

-- This table powers:
-- - Recommendation engine
-- - Analytics
-- - User activity history
-- - Popular dapps calculation
```

#### favorites

```sql
CREATE TABLE favorites (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    dapp_id INTEGER REFERENCES dapps(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(user_id, dapp_id),
    INDEX idx_user (user_id),
    INDEX idx_dapp (dapp_id)
);
```

#### achievements

```sql
CREATE TABLE achievements (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    achievement_type VARCHAR(50) NOT NULL,
    metadata JSONB DEFAULT '{}',
    earned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user (user_id),
    INDEX idx_type (achievement_type),
    INDEX idx_earned (earned_at DESC)
);

-- Achievement types:
-- 'explorer' - Viewed 10 dapps
-- 'defi_master' - Explored all DeFi dapps
-- 'early_adopter' - Found dapp with <100 users
-- 'streak_7' - 7 day activity streak
-- etc.
```

---

## 🔌 API Design

### REST API Endpoints

#### Dapps Endpoints

```
GET    /api/v1/dapps
GET    /api/v1/dapps/:id
GET    /api/v1/dapps/:slug
GET    /api/v1/dapps/:id/similar
POST   /api/v1/dapps (admin only)
PUT    /api/v1/dapps/:id (admin only)
DELETE /api/v1/dapps/:id (admin only)
```

**GET /api/v1/dapps**
```typescript
// Query parameters
interface DappsQuery {
  page?: number;          // Default: 1
  limit?: number;         // Default: 20, Max: 100
  category?: string;      // Category slug
  tags?: string[];        // Array of tag names
  search?: string;        // Search term
  sort?: 'tvl' | 'users' | 'volume' | 'new' | 'name';
  order?: 'asc' | 'desc'; // Default: desc
  minTvl?: number;
  maxTvl?: number;
}

// Response
interface DappsResponse {
  data: Dapp[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
  meta: {
    filters: any;
    timestamp: string;
  };
}

// Example
GET /api/v1/dapps?category=defi&sort=tvl&limit=10
```

**GET /api/v1/dapps/:id**
```typescript
// Response
interface DappDetailResponse {
  data: {
    id: number;
    name: string;
    slug: string;
    description: string;
    logo: string;
    website: string;
    contractAddress: string;
    category: Category;
    tags: string[];
    metrics: {
      tvl: number;
      users: number;
      volume24h: number;
      txCount24h: number;
    };
    social: {
      twitter?: string;
      discord?: string;
      telegram?: string;
    };
    charts: {
      tvlHistory: DataPoint[];
      volumeHistory: DataPoint[];
    };
    createdAt: string;
    updatedAt: string;
  };
  meta: {
    timestamp: string;
  };
}

// Example
GET /api/v1/dapps/1
```

#### Search & Discovery

```
GET /api/v1/search
GET /api/v1/search/suggest
GET /api/v1/categories
GET /api/v1/tags
GET /api/v1/trending
```

**GET /api/v1/search**
```typescript
interface SearchQuery {
  q: string;              // Search query
  categories?: string[];
  tags?: string[];
  limit?: number;
}

// Response
interface SearchResponse {
  results: Dapp[];
  total: number;
  query: string;
  suggestions?: string[];
}

// Example
GET /api/v1/search?q=swap
```

**GET /api/v1/search/suggest**
```typescript
// Autocomplete suggestions
interface SuggestQuery {
  q: string;
  limit?: number; // Default: 5
}

// Response
interface SuggestResponse {
  suggestions: Array<{
    type: 'dapp' | 'category' | 'tag';
    name: string;
    slug: string;
    icon?: string;
  }>;
}

// Example
GET /api/v1/search/suggest?q=uni
// Returns: [
//   { type: 'dapp', name: 'Uniswap Clone', slug: 'uniswap-clone' },
//   { type: 'tag', name: 'DeFi', slug: 'defi' }
// ]
```

#### Recommendations

```
GET /api/v1/recommendations
GET /api/v1/recommendations/trending
GET /api/v1/recommendations/similar/:dappId
GET /api/v1/recommendations/personalized
```

**GET /api/v1/recommendations/personalized**
```typescript
// Requires authentication
// Headers: { Authorization: 'Bearer <token>' }

interface PersonalizedRecommendationsResponse {
  recommendations: Array<{
    dapp: Dapp;
    score: number;
    reason: string; // "Based on your interest in DeFi"
  }>;
  algorithm: 'collaborative' | 'content-based' | 'hybrid';
}

// Example
GET /api/v1/recommendations/personalized
// Authorization: Bearer eyJhbGc...
```

**GET /api/v1/recommendations/similar/:dappId**
```typescript
interface SimilarDappsResponse {
  dapp: Dapp; // Original dapp
  similar: Array<{
    dapp: Dapp;
    similarity: number; // 0-1 score
    commonTags: string[];
  }>;
}

// Example
GET /api/v1/recommendations/similar/1
```

#### User Endpoints

```
POST   /api/v1/users/auth
GET    /api/v1/users/me
PUT    /api/v1/users/me
GET    /api/v1/users/me/favorites
POST   /api/v1/users/me/favorites
DELETE /api/v1/users/me/favorites/:dappId
GET    /api/v1/users/me/achievements
POST   /api/v1/interactions
```

**POST /api/v1/users/auth**
```typescript
// Authenticate with wallet signature
interface AuthRequest {
  walletAddress: string;
  signature: string;
  message: string;
}

interface AuthResponse {
  token: string;
  user: {
    id: number;
    walletAddress: string;
    username: string;
    createdAt: string;
  };
}
```

**POST /api/v1/interactions**
```typescript
// Track user interactions
interface InteractionRequest {
  dappId: number;
  type: 'view' | 'click' | 'share';
  duration?: number; // seconds
  metadata?: any;
}

// Response: 201 Created
```

#### Analytics

```
GET /api/v1/analytics/stats
GET /api/v1/analytics/popular
GET /api/v1/analytics/new
```

**GET /api/v1/analytics/stats**
```typescript
interface PlatformStats {
  totalDapps: number;
  totalUsers: number;
  totalTVL: number;
  categories: Array<{
    name: string;
    count: number;
    tvl: number;
  }>;
  topDapps: Dapp[];
}
```

---

## 🔄 Data Flow

### User Discovery Flow

```mermaid
sequenceDiagram
    participant U as User
    participant F as Frontend
    participant A as API
    participant D as Database
    participant B as Blockchain
    participant R as Recommendation Engine
    
    U->>F: Visit homepage
    F->>A: GET /api/v1/dapps?sort=popular
    A->>D: Query top dapps
    D-->>A: Return dapp list
    A-->>F: JSON response
    F->>F: Render dapp cards
    F-->>U: Display dapps
    
    U->>F: Search "DEX"
    F->>A: GET /api/v1/search?q=DEX
    A->>D: Full-text search
    D-->>A: Matching dapps
    A-->>F: Search results
    F-->>U: Display results
    
    U->>F: Click dapp card
    F->>A: GET /api/v1/dapps/123
    A->>D: Get dapp details
    A->>B: Fetch live TVL
    B-->>A: On-chain data
    A->>R: Get similar dapps
    R->>D: Query interactions
    D-->>R: User behavior data
    R-->>A: Recommendations
    A-->>F: Complete dapp data
    F-->>U: Show dapp page
    
    F->>A: POST /api/v1/interactions
    A->>D: Save interaction
    Note over D,R: Powers future recommendations
```

### Recommendation Engine Flow

```mermaid
graph TD
    A[User Interaction] --> B[Track Event]
    B --> C{Event Type}
    
    C -->|View| D[Update View Count]
    C -->|Click| E[Record Click]
    C -->|Favorite| F[Save to Favorites]
    
    D --> G[Calculate Scores]
    E --> G
    F --> G
    
    G --> H{Algorithm Selection}
    
    H -->|New User| I[Content-Based]
    H -->|Returning User| J[Collaborative Filtering]
    H -->|Power User| K[Hybrid Model]
    
    I --> L[Use Dapp Metadata]
    J --> M[Use Similar Users]
    K --> N[Combine Both]
    
    L --> O[Generate Recommendations]
    M --> O
    N --> O
    
    O --> P[Score & Rank]
    P --> Q[Cache Results]
    Q --> R[Return to User]
    
    style A fill:#e1f5fe
    style O fill:#fff9c4
    style R fill:#c8e6c9
```

### 3D Scene Rendering Flow

```mermaid
graph LR
    A[Load Page] --> B[Fetch Dapp Data]
    B --> C[Initialize Three.js]
    C --> D[Create Scene]
    D --> E[Add Lights]
    E --> F[Create Camera]
    
    F --> G{For Each Dapp}
    G --> H[Create 3D Object]
    H --> I[Position in Space]
    I --> J[Apply Material]
    J --> K[Add to Scene]
    
    K --> G
    
    G --> L[Setup Controls]
    L --> M[Add Event Listeners]
    M --> N[Start Render Loop]
    
    N --> O{Animation Frame}
    O --> P[Update Objects]
    P --> Q[Update Camera]
    Q --> R[Render Scene]
    R --> O
    
    style A fill:#e1f5fe
    style N fill:#fff9c4
    style O fill:#ffccbc
```

### Authentication Flow

```mermaid
sequenceDiagram
    participant U as User
    participant F as Frontend
    participant W as MetaMask
    participant A as API
    participant D as Database
    
    U->>F: Click "Connect Wallet"
    F->>W: Request accounts
    W->>U: Show MetaMask popup
    U->>W: Approve connection
    W-->>F: Return wallet address
    
    F->>A: POST /api/v1/users/auth/nonce
    A->>D: Generate nonce
    D-->>A: Return nonce
    A-->>F: { nonce: "abc123" }
    
    F->>W: Sign message with nonce
    W->>U: Request signature
    U->>W: Approve signature
    W-->>F: Return signature
    
    F->>A: POST /api/v1/users/auth
    A->>A: Verify signature
    A->>D: Create/update user
    D-->>A: User data
    A-->>F: { token, user }
    
    F->>F: Store token
    F-->>U: Show "Connected"
    
    Note over F,A: Subsequent requests include<br/>Authorization: Bearer <token>
```

---

## 🔗 Technology Integration

### Monad Blockchain Integration

```typescript
// monadService.ts

import { ethers } from 'ethers';

class MonadService {
  private provider: ethers.providers.JsonRpcProvider;
  private chainId = 12345; // Monad mainnet chain ID
  
  constructor() {
    this.provider = new ethers.providers.JsonRpcProvider(
      process.env.MONAD_RPC_URL || 'https://rpc.monad.xyz'
    );
  }
  
  // Get protocol TVL from smart contract
  async getProtocolTVL(contractAddress: string): Promise<number> {
    try {
      const contract = new ethers.Contract(
        contractAddress,
        ['function totalValueLocked() view returns (uint256)'],
        this.provider
      );
      
      const tvl = await contract.totalValueLocked();
      return parseFloat(ethers.utils.formatEther(tvl));
    } catch (error) {
      console.error('Error fetching TVL:', error);
      return 0;
    }
  }
  
  // Get user count from protocol
  async getUserCount(contractAddress: string): Promise<number> {
    try {
      // Query events to count unique addresses
      const filter = {
        address: contractAddress,
        fromBlock: 0,
        toBlock: 'latest'
      };
      
      const logs = await this.provider.getLogs(filter);
      const uniqueUsers = new Set(logs.map(log => log.topics[1]));
      return uniqueUsers.size;
    } catch (error) {
      console.error('Error counting users:', error);
      return 0;
    }
  }
  
  // Get 24h transaction volume
  async get24hVolume(contractAddress: string): Promise<number> {
    try {
      const currentBlock = await this.provider.getBlockNumber();
      const blocksPerDay = 7200; // ~12s per block
      const startBlock = currentBlock - blocksPerDay;
      
      const contract = new ethers.Contract(
        contractAddress,
        ['event Swap(address indexed user, uint256 amount)'],
        this.provider
      );
      
      const events = await contract.queryFilter(
        contract.filters.Swap(),
        startBlock,
        currentBlock
      );
      
      const totalVolume = events.reduce((sum, event) => {
        return sum + parseFloat(ethers.utils.formatEther(event.args.amount));
      }, 0);
      
      return totalVolume;
    } catch (error) {
      console.error('Error fetching volume:', error);
      return 0;
    }
  }
  
  // Verify contract is deployed
  async isValidContract(address: string): Promise<boolean> {
    try {
      const code = await this.provider.getCode(address);
      return code !== '0x';
    } catch (error) {
      return false;
    }
  }
  
  // Get all protocol metrics
  async getProtocolMetrics(contractAddress: string) {
    const [tvl, users, volume] = await Promise.all([
      this.getProtocolTVL(contractAddress),
      this.getUserCount(contractAddress),
      this.get24hVolume(contractAddress)
    ]);
    
    return {
      tvl,
      users,
      volume24h: volume,
      lastUpdated: new Date().toISOString()
    };
  }
}

export default new MonadService();
```

### Web3 Frontend Integration

```typescript
// hooks/useWeb3.ts

import { useState, useEffect } from 'react';
import { ethers } from 'ethers';

export function useWeb3() {
  const [account, setAccount] = useState<string | null>(null);
  const [provider, setProvider] = useState<ethers.providers.Web3Provider | null>(null);
  const [chainId, setChainId] = useState<number | null>(null);
  const [isConnecting, setIsConnecting] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const MONAD_CHAIN_ID = 12345;
  
  useEffect(() => {
    if (window.ethereum) {
      const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
      setProvider(web3Provider);
      
      // Check if already connected
      checkConnection();
      
      // Listen for account changes
      window.ethereum.on('accountsChanged', handleAccountsChanged);
      window.ethereum.on('chainChanged', handleChainChanged);
    }
    
    return () => {
      if (window.ethereum) {
        window.ethereum.removeListener('accountsChanged', handleAccountsChanged);
        window.ethereum.removeListener('chainChanged', handleChainChanged);
      }
    };
  }, []);
  
  const checkConnection = async () => {
    try {
      const accounts = await window.ethereum.request({ 
        method: 'eth_accounts' 
      });
      
      if (accounts.length > 0) {
        setAccount(accounts[0]);
        const network = await provider?.getNetwork();
        setChainId(network?.chainId || null);
      }
    } catch (err) {
      console.error('Error checking connection:', err);
    }
  };
  
  const connectWallet = async () => {
    if (!window.ethereum) {
      setError('Please install MetaMask');
      return;
    }
    
    setIsConnecting(true);
    setError(null);
    
    try {
      const accounts = await window.ethereum.request({
        method: 'eth_requestAccounts'
      });
      
      setAccount(accounts[0]);
      
      // Check if on Monad network
      const network = await provider?.getNetwork();
      const currentChainId = network?.chainId;
      setChainId(currentChainId || null);
      
      if (currentChainId !== MONAD_CHAIN_ID) {
        await switchToMonad();
      }
    } catch (err: any) {
      setError(err.message);
    } finally {
      setIsConnecting(false);
    }
  };
  
  const switchToMonad = async () => {
    try {
      await window.ethereum.request({
        method: 'wallet_switchEthereumChain',
        params: [{ chainId: `0x${MONAD_CHAIN_ID.toString(16)}` }],
      });
    } catch (switchError: any) {
      // Chain not added, try adding it
      if (switchError.code === 4902) {
        await addMonadNetwork();
      }
    }
  };
  
  const addMonadNetwork = async () => {
    try {
      await window.ethereum.request({
        method: 'wallet_addEthereumChain',
        params: [{
          chainId: `0x${MONAD_CHAIN_ID.toString(16)}`,
          chainName: 'Monad Mainnet',
          nativeCurrency: {
            name: 'MONAD',
            symbol: 'MONAD',
            decimals: 18
          },
          rpcUrls: ['https://rpc.monad.xyz'],
          blockExplorerUrls: ['https://explorer.monad.xyz']
        }]
      });
    } catch (err: any) {
      setError('Failed to add Monad network');
    }
  };
  
  const disconnectWallet = () => {
    setAccount(null);
    setChainId(null);
  };
  
  const handleAccountsChanged = (accounts: string[]) => {
    if (accounts.length === 0) {
      disconnectWallet();
    } else {
      setAccount(accounts[0]);
    }
  };
  
  const handleChainChanged = () => {
    window.location.reload();
  };
  
  const isCorrectNetwork = chainId === MONAD_CHAIN_ID;
  
  return {
    account,
    provider,
    chainId,
    isConnecting,
    error,
    isConnected: !!account,
    isCorrectNetwork,
    connectWallet,
    disconnectWallet,
    switchToMonad
  };
}
```

### Three.js 3D Scene Setup

```typescript
// components/3d/Scene.tsx

import { useRef, useEffect, useState } from 'react';
import { Canvas } from '@react-three/fiber';
import { OrbitControls, PerspectiveCamera } from '@react-three/drei';
import DappSphere from './DappSphere';
import ConnectionLines from './ConnectionLines';
import ParticleField from './ParticleField';

interface SceneProps {
  dapps: Dapp[];
  onDappClick: (dapp: Dapp) => void;
}

export default function Scene({ dapps, onDappClick }: SceneProps) {
  const [positions, setPositions] = useState<Map<number, [number, number, number]>>(new Map());
  
  useEffect(() => {
    // Calculate positions for dapps in 3D space
    const newPositions = calculateDappPositions(dapps);
    setPositions(newPositions);
  }, [dapps]);
  
  return (
    <div className="w-full h-screen">
      <Canvas>
        {/* Camera */}
        <PerspectiveCamera makeDefault position={[0, 0, 50]} fov={75} />
        
        {/* Controls */}
        <OrbitControls
          enablePan={true}
          enableZoom={true}
          enableRotate={true}
          minDistance={10}
          maxDistance={100}
          autoRotate={false}
        />
        
        {/* Lighting */}
        <ambientLight intensity={0.5} />
        <directionalLight position={[10, 10, 5]} intensity={1} />
        <pointLight position={[-10, -10, -5]} intensity={0.5} />
        
        {/* Background particles */}
        <ParticleField count={1000} />
        
        {/* Render dapps as spheres */}
        {dapps.map((dapp) => {
          const position = positions.get(dapp.id) || [0, 0, 0];
          return (
            <DappSphere
              key={dapp.id}
              dapp={dapp}
              position={position}
              onClick={() => onDappClick(dapp)}
            />
          );
        })}
        
        {/* Connection lines between related dapps */}
        <ConnectionLines dapps={dapps} positions={positions} />
      </Canvas>
    </div>
  );
}

// Calculate positions using force-directed layout
function calculateDappPositions(dapps: Dapp[]): Map<number, [number, number, number]> {
  const positions = new Map();
  const radius = 30;
  
  dapps.forEach((dapp, index) => {
    // Cluster by category
    const categoryIndex = getCategoryIndex(dapp.category);
    const categoryAngle = (categoryIndex / 6) * Math.PI * 2;
    const spreadAngle = (index / dapps.length) * Math.PI * 2;
    
    const x = radius * Math.cos(categoryAngle) * Math.cos(spreadAngle);
    const y = radius * Math.sin(spreadAngle);
    const z = radius * Math.sin(categoryAngle) * Math.cos(spreadAngle);
    
    positions.set(dapp.id, [x, y, z]);
  });
  
  return positions;
}

function getCategoryIndex(category: string): number {
  const categories = ['DeFi', 'NFT', 'Gaming', 'Infrastructure', 'Social', 'DAO'];
  return categories.indexOf(category) || 0;
}
```

```typescript
// components/3d/DappSphere.tsx

import { useRef, useState } from 'react';
import { Mesh } from 'three';
import { useFrame } from '@react-three/fiber';
import { Text } from '@react-three/drei';

interface DappSphereProps {
  dapp: Dapp;
  position: [number, number, number];
  onClick: () => void;
}

export default function DappSphere({ dapp, position, onClick }: DappSphereProps) {
  const meshRef = useRef<Mesh>(null);
  const [hovered, setHovered] = useState(false);
  
  // Calculate size based on TVL
  const baseSize = 0.5;
  const size = baseSize + (Math.log(dapp.tvl + 1) / 20);
  
  // Color based on category
  const colors = {
    DeFi: '#4CAF50',
    NFT: '#9C27B0',
    Gaming: '#FF5722',
    Infrastructure: '#2196F3',
    Social: '#FF9800',
    DAO: '#00BCD4'
  };
  const color = colors[dapp.category] || '#888888';
  
  // Animate on hover
  useFrame(() => {
    if (meshRef.current) {
      if (hovered) {
        meshRef.current.scale.setScalar(1.2);
      } else {
        meshRef.current.scale.setScalar(1);
      }
    }
  });
  
  return (
    <group position={position}>
      {/* Sphere */}
      <mesh
        ref={meshRef}
        onClick={onClick}
        onPointerOver={() => setHovered(true)}
        onPointerOut={() => setHovered(false)}
      >
        <sphereGeometry args={[size, 32, 32]} />
        <meshStandardMaterial
          color={color}
          emissive={color}
          emissiveIntensity={hovered ? 0.5 : 0.2}
          metalness={0.8}
          roughness={0.2}
        />
      </mesh>
      
      {/* Label */}
      {hovered && (
        <Text
          position={[0, size + 1, 0]}
          fontSize={0.5}
          color="white"
          anchorX="center"
          anchorY="middle"
        >
          {dapp.name}
        </Text>
      )}
      
      {/* Glow effect */}
      {hovered && (
        <mesh>
          <sphereGeometry args={[size * 1.3, 32, 32]} />
          <meshBasicMaterial
            color={color}
            transparent
            opacity={0.2}
          />
        </mesh>
      )}
    </group>
  );
}
```

---

## 🔐 Security Architecture

### Security Layers

```
┌─────────────────────────────────────────────┐
│          Application Security               │
├─────────────────────────────────────────────┤
│                                             │
│  1. Input Validation                        │
│     ├─ SQL Injection Prevention             │
│     ├─ XSS Protection                       │
│     └─ Request Size Limits                  │
│                                             │
│  2. Authentication & Authorization          │
│     ├─ JWT Token Management                 │
│     ├─ Wallet Signature Verification        │
│     └─ Rate Limiting                        │
│                                             │
│  3. API Security                            │
│     ├─ CORS Configuration                   │
│     ├─ HTTPS Only                           │
│     └─ API Key Management                   │
│                                             │
│  4. Data Security                           │
│     ├─ Encrypted Connections                │
│     ├─ Secure Environment Variables         │
│     └─ No Sensitive Data in Logs            │
│                                             │
│  5. Frontend Security                       │
│     ├─ Content Security Policy (CSP)        │
│     ├─ Subresource Integrity (SRI)          │
│     └─ Safe DOM Manipulation                │
│                                             │
└─────────────────────────────────────────────┘
```

### Security Implementation

#### Input Validation

```typescript
// middleware/validation.ts

import { body, param, query, validationResult } from 'express-validator';

export const validateDappQuery = [
  query('page').optional().isInt({ min: 1 }),
  query('limit').optional().isInt({ min: 1, max: 100 }),
  query('category').optional().isAlpha(),
  query('search').optional().isString().trim().escape(),
  query('sort').optional().isIn(['tvl', 'users', 'volume', 'new', 'name']),
  
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  }
];

export const validateDappId = [
  param('id').isInt({ min: 1 }),
  
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  }
];
```

#### Authentication Middleware

```typescript
// middleware/auth.ts

import jwt from 'jsonwebtoken';
import { ethers } from 'ethers';

export const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(403).json({ error: 'Invalid token' });
  }
};

export const verifyWalletSignature = async (req, res, next) => {
  const { walletAddress, signature, message } = req.body;
  
  try {
    const recoveredAddress = ethers.utils.verifyMessage(message, signature);
    
    if (recoveredAddress.toLowerCase() !== walletAddress.toLowerCase()) {
      return res.status(403).json({ error: 'Invalid signature' });
    }
    
    req.verifiedAddress = recoveredAddress;
    next();
  } catch (error) {
    return res.status(403).json({ error: 'Signature verification failed' });
  }
};
```

#### Rate Limiting

```typescript
// middleware/rateLimit.ts

import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

export const apiLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rl:api:'
  }),
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests, please try again later',
  standardHeaders: true,
  legacyHeaders: false
});

export const searchLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rl:search:'
  }),
  windowMs: 60 * 1000, // 1 minute
  max: 30, // 30 searches per minute
  message: 'Too many search requests'
});

export const authLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rl:auth:'
  }),
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 5, // 5 attempts per hour
  message: 'Too many authentication attempts'
});
```

#### CORS Configuration

```typescript
// server.ts

import cors from 'cors';

const corsOptions = {
  origin: process.env.NODE_ENV === 'production'
    ? ['https://yourdomain.com', 'https://www.yourdomain.com']
    : ['http://localhost:3000', 'http://localhost:5173'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 86400 // 24 hours
};

app.use(cors(corsOptions));
```

#### Content Security Policy

```typescript
// middleware/security.ts

import helmet from 'helmet';

export const securityHeaders = helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'", 'https://cdnjs.cloudflare.com'],
      styleSrc: ["'self'", "'unsafe-inline'", 'https://fonts.googleapis.com'],
      imgSrc: ["'self'", 'data:', 'https:'],
      connectSrc: ["'self'", 'https://rpc.monad.xyz', 'https://api.yourdomain.com'],
      fontSrc: ["'self'", 'https://fonts.gstatic.com'],
      objectSrc: ["'none'"],
      mediaSrc: ["'self'"],
      frameSrc: ["'none'"]
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
});
```

### Environment Variables Security

```bash
# .env.example

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/monad_discovery
DATABASE_SSL=true

# Redis
REDIS_URL=redis://localhost:6379

# API Keys (never commit actual keys)
JWT_SECRET=your-super-secret-jwt-key-change-this
MONAD_RPC_URL=https://rpc.monad.xyz
DEFILLAMA_API_KEY=optional-api-key

# Frontend URL (for CORS)
FRONTEND_URL=https://yourdomain.com

# Node Environment
NODE_ENV=production

# Port
PORT=3001
```

**Security Best Practices:**
```bash
# Never commit .env file
echo ".env" >> .gitignore

# Use strong secrets
openssl rand -base64 32

# Rotate secrets regularly
# Use environment-specific secrets
# Never log sensitive data
```

---

## 📊 Performance Optimization

### Caching Strategy

```mermaid
graph TD
    A[Request] --> B{Check Redis Cache}
    B -->|Hit| C[Return Cached Data]
    B -->|Miss| D[Query Database]
    D --> E[Process Data]
    E --> F[Store in Cache]
    F --> G[Return Data]
    
    H[Cache Invalidation] --> I{Event Type}
    I -->|Dapp Updated| J[Invalidate Dapp Cache]
    I -->|New Interaction| K[Invalidate Recommendations]
    I -->|Time Expired| L[Invalidate Stale Data]
    
    style B fill:#fff9c4
    style C fill:#c8e6c9
    style F fill:#ffccbc
```

#### Cache Implementation

```typescript
// services/cacheService.ts

import Redis from 'ioredis';

class CacheService {
  private redis: Redis;
  
  constructor() {
    this.redis = new Redis(process.env.REDIS_URL);
  }
  
  // Cache dapp list (5 minutes)
  async cacheDappList(key: string, data: any, ttl = 300) {
    await this.redis.setex(key, ttl, JSON.stringify(data));
  }
  
  async getCachedDappList(key: string) {
    const cached = await this.redis.get(key);
    return cached ? JSON.parse(cached) : null;
  }
  
  // Cache dapp details (10 minutes)
  async cacheDappDetail(dappId: number, data: any) {
    const key = `dapp:${dappId}`;
    await this.redis.setex(key, 600, JSON.stringify(data));
  }
  
  async getCachedDappDetail(dappId: number) {
    const key = `dapp:${dappId}`;
    const cached = await this.redis.get(key);
    return cached ? JSON.parse(cached) : null;
  }
  
  // Cache recommendations (1 hour)
  async cacheRecommendations(userId: number, data: any) {
    const key = `recommendations:${userId}`;
    await this.redis.setex(key, 3600, JSON.stringify(data));
  }
  
  async getCachedRecommendations(userId: number) {
    const key = `recommendations:${userId}`;
    const cached = await this.redis.get(key);
    return cached ? JSON.parse(cached) : null;
  }
  
  // Invalidate cache
  async invalidateDapp(dappId: number) {
    await this.redis.del(`dapp:${dappId}`);
    // Also invalidate related list caches
    await this.redis.del('dapps:popular');
    await this.redis.del('dapps:trending');
  }
  
  async invalidateAllRecommendations() {
    const keys = await this.redis.keys('recommendations:*');
    if (keys.length > 0) {
      await this.redis.del(...keys);
    }
  }
}

export default new CacheService();
```

### Database Query Optimization

```sql
-- Indexes for performance

-- Dapp queries
CREATE INDEX idx_dapps_category ON dapps(category_id);
CREATE INDEX idx_dapps_tvl ON dapps(tvl DESC);
CREATE INDEX idx_dapps_users ON dapps(user_count DESC);
CREATE INDEX idx_dapps_active ON dapps(is_active) WHERE is_active = true;

-- Full-text search
CREATE INDEX idx_dapps_search ON dapps USING GIN(to_tsvector('english', name || ' ' || description));

-- Interactions (for recommendations)
CREATE INDEX idx_interactions_user ON interactions(user_id);
CREATE INDEX idx_interactions_dapp ON interactions(dapp_id);
CREATE INDEX idx_interactions_type ON interactions(interaction_type);
CREATE INDEX idx_interactions_created ON interactions(created_at DESC);
CREATE INDEX idx_interactions_composite ON interactions(user_id, dapp_id, interaction_type);

-- Composite index for common queries
CREATE INDEX idx_dapps_category_tvl ON dapps(category_id, tvl DESC);
```

### Frontend Performance

```typescript
// Lazy loading routes
import { lazy, Suspense } from 'react';

const HomePage = lazy(() => import('./pages/HomePage'));
const ExplorePage = lazy(() => import('./pages/ExplorePage'));
const DappDetailPage = lazy(() => import('./pages/DappDetailPage'));
const ImmersivePage = lazy(() => import('./pages/ImmersivePage'));

// Code splitting for 3D components
const Scene3D = lazy(() => import('./components/3d/Scene'));

// Image optimization
<img
  src={dapp.logo}
  alt={dapp.name}
  loading="lazy"
  srcSet={`${dapp.logo}?w=300 300w, ${dapp.logo}?w=600 600w`}
  sizes="(max-width: 768px) 100vw, 300px"
/>

// Virtualization for long lists
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={dapps.length}
  itemSize={200}
  width="100%"
>
  {({ index, style }) => (
    <div style={style}>
      <DappCard dapp={dapps[index]} />
    </div>
  )}
</FixedSizeList>
```

---

## 🔧 Development Tools

### Project Configuration Files

#### package.json (Frontend)

```json
{
  "name": "monad-discovery-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\"",
    "test": "vitest"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "@tanstack/react-query": "^5.0.0",
    "ethers": "^6.9.0",
    "wagmi": "^2.0.0",
    "@rainbow-me/rainbowkit": "^2.0.0",
    "three": "^0.158.0",
    "@react-three/fiber": "^8.15.0",
    "@react-three/drei": "^9.90.0",
    "zustand": "^4.4.0",
    "axios": "^1.6.0",
    "framer-motion": "^10.16.0",
    "lucide-react": "^0.294.0",
    "recharts": "^2.10.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@types/three": "^0.158.0",
    "@vitejs/plugin-react": "^4.2.0",
    "typescript": "^5.3.0",
    "vite": "^5.0.0",
    "tailwindcss": "^3.3.0",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0",
    "eslint": "^8.54.0",
    "prettier": "^3.1.0",
    "vitest": "^1.0.0"
  }
}
```

#### package.json (Backend)

```json
{
  "name": "monad-discovery-backend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "nodemon src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "jest",
    "migrate": "node scripts/migrate.js",
    "seed": "node scripts/seed.js"
  },
  "dependencies": {
    "express": "^4.18.0",
    "pg": "^8.11.0",
    "ioredis": "^5.3.0",
    "ethers": "^6.9.0",
    "jsonwebtoken": "^9.0.0",
    "bcrypt": "^5.1.0",
    "cors": "^2.8.0",
    "helmet": "^7.1.0",
    "express-validator": "^7.0.0",
    "express-rate-limit": "^7.1.0",
    "rate-limit-redis": "^4.2.0",
    "dotenv": "^16.3.0",
    "winston": "^3.11.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.0",
    "@types/node": "^20.10.0",
    "typescript": "^5.3.0",
    "nodemon": "^3.0.0",
    "ts-node": "^10.9.0",
    "jest": "^29.7.0",
    "@types/jest": "^29.5.0"
  }
}
```

---

**Last Updated:** November 8, 2025  
**Next Document:** `04_TEAM_STRUCTURE.md`