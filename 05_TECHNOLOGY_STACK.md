# Technology Stack & Tools

## 📋 Table of Contents
1. [Complete Stack Overview](#complete-stack-overview)
2. [Frontend Technologies](#frontend-technologies)
3. [Backend Technologies](#backend-technologies)
4. [Database & Caching](#database--caching)
5. [3D & Visualization](#3d--visualization)
6. [Blockchain Integration](#blockchain-integration)
7. [Development Tools](#development-tools)
8. [Deployment & Infrastructure](#deployment--infrastructure)

---

## 🎯 Complete Stack Overview

### Technology Decision Matrix

```
┌─────────────────────────────────────────────────────────┐
│              RECOMMENDED TECH STACK                      │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Frontend                                                │
│  ├─ Framework: React 18 + Vite                          │
│  ├─ Language: TypeScript                                │
│  ├─ Styling: Tailwind CSS + Framer Motion              │
│  ├─ State: Zustand + React Query                       │
│  ├─ 3D: Three.js + React Three Fiber                   │
│  └─ Web3: wagmi + viem + RainbowKit                    │
│                                                          │
│  Backend                                                 │
│  ├─ Runtime: Node.js 18+                               │
│  ├─ Framework: Express.js                              │
│  ├─ Language: TypeScript (optional)                    │
│  ├─ Validation: Zod + express-validator                │
│  └─ Blockchain: ethers.js v6                           │
│                                                          │
│  Database                                                │
│  ├─ Primary: PostgreSQL 15                             │
│  ├─ ORM: Prisma                                        │
│  └─ Cache: Redis 7                                     │
│                                                          │
│  Deployment                                              │
│  ├─ Frontend: Vercel                                   │
│  ├─ Backend: Railway                                   │
│  ├─ Database: Supabase                                 │
│  └─ CI/CD: GitHub Actions                              │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Tech Stack Comparison

| Category | Option A (Recommended) | Option B | Option C |
|----------|------------------------|----------|----------|
| **Frontend Framework** | React + Vite ⭐ | Next.js 14 | Vue 3 |
| **3D Library** | React Three Fiber ⭐ | Three.js vanilla | Unity WebGL |
| **Backend** | Node + Express ⭐ | Python + FastAPI | Nest.js |
| **Database** | PostgreSQL ⭐ | MongoDB | MySQL |
| **Styling** | Tailwind CSS ⭐ | styled-components | Material-UI |
| **State Mgmt** | Zustand ⭐ | Redux Toolkit | Jotai |

**⭐ = Recommended for this project**

---

## 🎨 Frontend Technologies

### Core Framework: React 18 + Vite

**Why React?**
- ✅ Most popular, lots of resources
- ✅ Excellent ecosystem
- ✅ Great 3D integration (R3F)
- ✅ Fast development
- ✅ Team likely familiar

**Why Vite over Create React App?**
- ⚡ 10-100x faster dev server
- ⚡ Instant HMR (Hot Module Replacement)
- ⚡ Optimized build
- ⚡ Better TypeScript support
- ⚡ Modern tooling

**Setup:**
```bash
# Create project
npm create vite@latest monad-discovery -- --template react-ts

cd monad-discovery
npm install

# Install core dependencies
npm install react-router-dom @tanstack/react-query axios zustand

# Install UI libraries
npm install tailwindcss postcss autoprefixer framer-motion
npm install lucide-react # Icons

# Install Web3 libraries
npm install wagmi viem @rainbow-me/rainbowkit ethers

# Install 3D libraries
npm install three @react-three/fiber @react-three/drei

# Dev dependencies
npm install -D @types/three
```

### Alternative: Next.js 14

**When to choose Next.js:**
- Need SSR (Server-Side Rendering)
- Want built-in API routes
- SEO is critical
- Team experienced with Next

**Trade-offs:**
- More opinionated
- Slightly heavier
- Learning curve if new to Next

```bash
# Next.js setup
npx create-next-app@latest monad-discovery --typescript --tailwind --app
```

---

### Styling: Tailwind CSS

**Why Tailwind?**
- ✅ Utility-first, rapid development
- ✅ No CSS file switching
- ✅ Consistent design system
- ✅ Excellent documentation
- ✅ Great with React

**Configuration:**
```javascript
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        monad: {
          purple: '#8A3CFF',
          blue: '#3C8AFF',
          dark: '#1A1A2E',
          light: '#F8F9FA'
        }
      },
      animation: {
        'float': 'float 3s ease-in-out infinite',
        'glow': 'glow 2s ease-in-out infinite alternate'
      },
      keyframes: {
        float: {
          '0%, 100%': { transform: 'translateY(0)' },
          '50%': { transform: 'translateY(-10px)' }
        },
        glow: {
          '0%': { boxShadow: '0 0 5px #8A3CFF' },
          '100%': { boxShadow: '0 0 20px #8A3CFF' }
        }
      }
    }
  },
  plugins: []
};
```

### Animation: Framer Motion

**Why Framer Motion?**
- ✅ Declarative animations
- ✅ Great React integration
- ✅ Gesture support
- ✅ Layout animations
- ✅ SVG support

**Examples:**
```tsx
import { motion } from 'framer-motion';

// Fade in animation
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }}
>
  <DappCard />
</motion.div>

// Hover effect
<motion.button
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.95 }}
>
  Connect Wallet
</motion.button>

// Staggered list
<motion.ul
  variants={{
    hidden: { opacity: 0 },
    show: {
      opacity: 1,
      transition: {
        staggerChildren: 0.1
      }
    }
  }}
  initial="hidden"
  animate="show"
>
  {dapps.map(dapp => (
    <motion.li key={dapp.id} variants={itemVariants}>
      <DappCard dapp={dapp} />
    </motion.li>
  ))}
</motion.ul>
```

---

### State Management: Zustand

**Why Zustand over Redux?**
- ✅ Much simpler API
- ✅ Less boilerplate
- ✅ Smaller bundle size
- ✅ No Provider wrapper needed
- ✅ Great TypeScript support

**Example Store:**
```typescript
// stores/dappStore.ts
import { create } from 'zustand';

interface DappStore {
  dapps: Dapp[];
  filters: FilterState;
  loading: boolean;
  setDapps: (dapps: Dapp[]) => void;
  setFilters: (filters: FilterState) => void;
  fetchDapps: () => Promise<void>;
}

export const useDappStore = create<DappStore>((set) => ({
  dapps: [],
  filters: {},
  loading: false,
  
  setDapps: (dapps) => set({ dapps }),
  
  setFilters: (filters) => set({ filters }),
  
  fetchDapps: async () => {
    set({ loading: true });
    try {
      const response = await fetch('/api/v1/dapps');
      const data = await response.json();
      set({ dapps: data.data, loading: false });
    } catch (error) {
      set({ loading: false });
    }
  }
}));

// Usage in component
function DappList() {
  const { dapps, loading, fetchDapps } = useDappStore();
  
  useEffect(() => {
    fetchDapps();
  }, []);
  
  if (loading) return <Loading />;
  return <div>{dapps.map(d => <DappCard key={d.id} dapp={d} />)}</div>;
}
```

### Server State: React Query

**Why React Query?**
- ✅ Automatic caching
- ✅ Background refetching
- ✅ Optimistic updates
- ✅ Loading/error states
- ✅ DevTools

**Example:**
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Fetch dapps
function useDapps(filters: FilterState) {
  return useQuery({
    queryKey: ['dapps', filters],
    queryFn: () => fetchDapps(filters),
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 10 * 60 * 1000 // 10 minutes
  });
}

// Add to favorites
function useFavoriteDapp() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (dappId: number) => addFavorite(dappId),
    onSuccess: () => {
      queryClient.invalidateQueries(['favorites']);
    }
  });
}

// Usage
function DappList() {
  const { data, isLoading, error } = useDapps({ category: 'defi' });
  const favoriteMutation = useFavoriteDapp();
  
  if (isLoading) return <Skeleton />;
  if (error) return <Error message={error.message} />;
  
  return (
    <div>
      {data.dapps.map(dapp => (
        <DappCard
          key={dapp.id}
          dapp={dapp}
          onFavorite={() => favoriteMutation.mutate(dapp.id)}
        />
      ))}
    </div>
  );
}
```

---

## 🔧 Backend Technologies

### Runtime: Node.js 18+

**Why Node.js?**
- ✅ JavaScript everywhere
- ✅ Fast development
- ✅ Huge ecosystem
- ✅ Great for APIs
- ✅ Excellent Web3 support

**Setup:**
```bash
mkdir monad-discovery-backend
cd monad-discovery-backend
npm init -y

# Core dependencies
npm install express cors helmet dotenv
npm install pg redis ioredis
npm install ethers jsonwebtoken bcrypt
npm install express-validator express-rate-limit

# Dev dependencies
npm install -D nodemon typescript @types/node @types/express
npm install -D ts-node @types/cors @types/jsonwebtoken

# Initialize TypeScript
npx tsc --init
```

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### Alternative: Python + FastAPI

**When to choose Python:**
- ML engineer leading backend
- Heavy data processing
- Team Python-focused

**Pros:**
- Great ML ecosystem
- Fast async performance
- Auto-generated docs
- Type hints

**Cons:**
- Separate language from frontend
- Different deployment
- Less Web3 tooling

```bash
# FastAPI setup
pip install fastapi uvicorn sqlalchemy psycopg2
pip install web3 python-jose redis
pip install pydantic python-multipart
```

---

## 💾 Database & Caching

### Primary Database: PostgreSQL 15

**Why PostgreSQL?**
- ✅ Robust and reliable
- ✅ ACID compliant
- ✅ JSON support (JSONB)
- ✅ Full-text search
- ✅ Great performance
- ✅ Free tier available

**Setup with Prisma ORM:**
```bash
npm install @prisma/client
npm install -D prisma

# Initialize Prisma
npx prisma init

# Define schema
```

**prisma/schema.prisma:**
```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Dapp {
  id              Int      @id @default(autoincrement())
  name            String
  slug            String   @unique
  description     String?
  logoUrl         String?
  website         String?
  contractAddress String?
  categoryId      Int
  tvl             Decimal  @default(0)
  userCount       Int      @default(0)
  txCount24h      Int      @default(0)
  volume24h       Decimal  @default(0)
  socialLinks     Json     @default("{}")
  isActive        Boolean  @default(true)
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  
  category        Category @relation(fields: [categoryId], references: [id])
  tags            Tag[]
  interactions    Interaction[]
  favorites       Favorite[]
  
  @@index([categoryId])
  @@index([tvl(sort: Desc)])
  @@index([userCount(sort: Desc)])
}

model Category {
  id          Int      @id @default(autoincrement())
  name        String   @unique
  slug        String   @unique
  icon        String?
  color       String?
  description String?
  dappCount   Int      @default(0)
  createdAt   DateTime @default(now())
  
  dapps       Dapp[]
}

model Tag {
  id        Int      @id @default(autoincrement())
  dappId    Int
  name      String
  createdAt DateTime @default(now())
  
  dapp      Dapp     @relation(fields: [dappId], references: [id], onDelete: Cascade)
  
  @@unique([dappId, name])
  @@index([name])
}

model User {
  id              Int      @id @default(autoincrement())
  walletAddress   String   @unique
  username        String?
  avatar          String?
  preferences     Json     @default("{}")
  stats           Json     @default("{}")
  totalViews      Int      @default(0)
  achievementsCount Int    @default(0)
  lastActive      DateTime @default(now())
  createdAt       DateTime @default(now())
  
  interactions    Interaction[]
  favorites       Favorite[]
  achievements    Achievement[]
  
  @@index([walletAddress])
}

model Interaction {
  id              Int      @id @default(autoincrement())
  userId          Int
  dappId          Int
  interactionType String
  durationSeconds Int      @default(0)
  metadata        Json     @default("{}")
  createdAt       DateTime @default(now())
  
  user            User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  dapp            Dapp     @relation(fields: [dappId], references: [id], onDelete: Cascade)
  
  @@index([userId])
  @@index([dappId])
  @@index([interactionType])
}

model Favorite {
  id        Int      @id @default(autoincrement())
  userId    Int
  dappId    Int
  createdAt DateTime @default(now())
  
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  dapp      Dapp     @relation(fields: [dappId], references: [id], onDelete: Cascade)
  
  @@unique([userId, dappId])
}

model Achievement {
  id              Int      @id @default(autoincrement())
  userId          Int
  achievementType String
  metadata        Json     @default("{}")
  earnedAt        DateTime @default(now())
  
  user            User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@index([userId])
}
```

**Commands:**
```bash
# Generate Prisma client
npx prisma generate

# Create migration
npx prisma migrate dev --name init

# Open Prisma Studio (GUI)
npx prisma studio

# Seed database
npx prisma db seed
```

---

### Caching: Redis 7

**Why Redis?**
- ✅ In-memory, blazing fast
- ✅ Reduces database load
- ✅ Session storage
- ✅ Rate limiting
- ✅ Real-time features

**Setup:**
```bash
npm install ioredis
```

**Usage:**
```typescript
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

// Cache dapp list
await redis.setex('dapps:popular', 300, JSON.stringify(dapps));

// Get cached data
const cached = await redis.get('dapps:popular');

// Cache with tags
await redis.set('dapp:123', JSON.stringify(dapp), 'EX', 600);

// Invalidate cache
await redis.del('dapp:123');
```

---

## 🎮 3D & Visualization

### Three.js + React Three Fiber

**Why R3F over vanilla Three.js?**
- ✅ React components for 3D
- ✅ Easier state management
- ✅ Better performance
- ✅ Excellent ecosystem (@react-three/drei)
- ✅ Less boilerplate

**Setup:**
```bash
npm install three @types/three
npm install @react-three/fiber @react-three/drei
npm install @react-three/postprocessing
```

**Basic Scene:**
```tsx
import { Canvas } from '@react-three/fiber';
import { OrbitControls, Stars } from '@react-three/drei';

function Scene() {
  return (
    <Canvas camera={{ position: [0, 0, 50], fov: 75 }}>
      <color attach="background" args={['#1A1A2E']} />
      
      <ambientLight intensity={0.5} />
      <pointLight position={[10, 10, 10]} />
      
      <Stars />
      
      <OrbitControls />
      
      {/* Your 3D content */}
      <DappNodes />
    </Canvas>
  );
}
```

**Useful @react-three/drei Helpers:**
```tsx
import {
  OrbitControls,      // Camera controls
  PerspectiveCamera,  // Camera
  Stars,              // Starfield
  Text,               // 3D text
  Html,               // 2D HTML in 3D
  useGLTF,           // Load 3D models
  Float,              // Floating animation
  MeshDistortMaterial, // Cool materials
  Environment         // HDRI lighting
} from '@react-three/drei';
```

---

### Alternative: Unity WebGL

**When to choose Unity:**
- Team has Unity experience
- Need complex game mechanics
- Want VR/AR support
- Heavy 3D focus

**Pros:**
- Powerful 3D engine
- Visual editor
- Asset store
- C# scripting

**Cons:**
- Large file size (10-50MB+)
- Longer load times
- Complex integration
- Separate workflow

**Setup:**
```
1. Create Unity project (2022 LTS)
2. Switch platform to WebGL
3. Install Web3.Unity SDK
4. Build and export
5. Host build files
```

---

## ⛓️ Blockchain Integration

### Web3 Stack: wagmi + viem

**Why wagmi?**
- ✅ React hooks for Ethereum
- ✅ TypeScript-first
- ✅ Automatic type inference
- ✅ Built-in caching
- ✅ Great DX

**Setup:**
```bash
npm install wagmi viem @rainbow-me/rainbowkit
```

**Configuration:**
```typescript
// config/wagmi.ts
import { getDefaultConfig } from '@rainbow-me/rainbowkit';
import { monadChain } from './chains';

export const config = getDefaultConfig({
  appName: 'Monad Discovery',
  projectId: 'YOUR_PROJECT_ID',
  chains: [monadChain],
  ssr: false
});

// chains.ts
import { defineChain } from 'viem';

export const monadChain = defineChain({
  id: 12345, // Replace with actual Monad chain ID
  name: 'Monad',
  nativeCurrency: { name: 'Monad', symbol: 'MONAD', decimals: 18 },
  rpcUrls: {
    default: { http: ['https://rpc.monad.xyz'] }
  },
  blockExplorers: {
    default: { name: 'MonadScan', url: 'https://explorer.monad.xyz' }
  }
});
```

**Usage:**
```typescript
import { useAccount, useConnect, useDisconnect } from 'wagmi';

function WalletButton() {
  const { address, isConnected } = useAccount();
  const { connect, connectors } = useConnect();
  const { disconnect } = useDisconnect();
  
  if (isConnected) {
    return (
      <div>
        <span>{address?.slice(0, 6)}...{address?.slice(-4)}</span>
        <button onClick={() => disconnect()}>Disconnect</button>
      </div>
    );
  }
  
  return (
    <button onClick={() => connect({ connector: connectors[0] })}>
      Connect Wallet
    </button>
  );
}
```

### Backend: ethers.js v6

```typescript
import { ethers } from 'ethers';

// Connect to Monad
const provider = new ethers.JsonRpcProvider('https://rpc.monad.xyz');

// Read contract data
const contract = new ethers.Contract(
  contractAddress,
  ['function totalValueLocked() view returns (uint256)'],
  provider
);

const tvl = await contract.totalValueLocked();
```

---

## 🛠️ Development Tools

### Code Quality

**ESLint + Prettier:**
```bash
npm install -D eslint prettier
npm install -D eslint-config-prettier
npm install -D @typescript-eslint/eslint-plugin

# .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier"
  ]
}

# .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

### Testing

**Vitest (Frontend):**
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

**Jest (Backend):**
```bash
npm install -D jest @types/jest ts-jest supertest @types/supertest
```

### Project Management

**Recommended Tools:**
- **Linear** - Issue tracking
- **Notion** - Documentation
- **Figma** - Design
- **GitHub Projects** - Sprint planning

---

## 🚀 Deployment & Infrastructure

### Frontend: Vercel

**Why Vercel?**
- ✅ Zero configuration
- ✅ Automatic deploys
- ✅ Preview deployments
- ✅ Edge network (fast)
- ✅ Free tier generous

**Setup:**
```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel --prod
```

**vercel.json:**
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "env": {
    "VITE_API_URL": "@api-url"
  }
}
```

---

### Backend: Railway

**Why Railway?**
- ✅ Easy Node.js deployment
- ✅ PostgreSQL included
- ✅ Redis add-on
- ✅ Auto-scaling
- ✅ Free $5/month credit

**Setup:**
1. Connect GitHub repo
2. Add PostgreSQL database
3. Add Redis service
4. Set environment variables
5. Deploy

**railway.json:**
```json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "npm start",
    "restartPolicyType": "ON_FAILURE"
  }
}
```

---

### CI/CD: GitHub Actions

**.github/workflows/ci.yml:**
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, dev]
  pull_request:
    branches: [main, dev]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

---

**Last Updated:** November 8, 2025  
**Next Document:** `06_RECOMMENDATION_ENGINE.md`

----------------------------------------------------------------------------------