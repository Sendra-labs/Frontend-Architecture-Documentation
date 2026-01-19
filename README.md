# Sendra Labs - Frontend

**Modular, Arbitrum-native DeFi infrastructure combining execution, analytics, and strategy-level accounting.**

![TypeScript](https://img.shields.io/badge/TypeScript-100%25-3178C6?style=flat-square)
![React](https://img.shields.io/badge/React-19-61DAFB?style=flat-square)
![Arbitrum](https://img.shields.io/badge/Arbitrum-One-28A0F0?style=flat-square)

---

## Overview

Sendra Labs is a modular, Arbitrum-native DeFi infrastructure that combines execution, analytics, and strategy-level accounting into a single operational layer. We are building the platform where liquidity learns, strategies execute, and DeFi evolves.

**Current Status:** MVP fully deployed on Arbitrum mainnet with all core components operational.

### Architecture Overview

Sendra Labs is structured around four interoperable layers: Intelligence, Execution, Accounting, and Social. Each built as an independent module but designed to work together seamlessly.

### Links

- **Website:** [sendralabs.com](https://sendralabs.com)
- **Twitter:** [@SendraLabs](https://x.com/SendraLabs)
- **Contact:** sendralabs.eth@gmail.com

---

## Tech Stack

### Core

| Technology | Version | Purpose |
|------------|---------|---------|
| React | 19 | UI Framework |
| TypeScript | 5.5+ | Type Safety (100% coverage) |
| Vite | 7.1.9 | Build Tool |
| TailwindCSS | 3.4.1 | Styling |

### Web3

| Technology | Version | Purpose |
|------------|---------|---------|
| Wagmi | 2.18.1 | React Hooks for Ethereum |
| Viem | 2.38.3 | Ethereum Library |
| RainbowKit | 2.2.0 | Wallet Connection |
| TanStack Query | 5.90.5 | Async State Management |

### Additional

- **React Router** 7.9.3 - Client-side routing
- **i18next** 25.5.3 - Internationalization (EN/ES)
- **Lightweight Charts** 5.0.9 - Real-time price tracking
- **ReactFlow** 11.11.4 - Interactive graph visualizations
- **Axios + SWR** - Data fetching
- **Lucide React** 0.344.0 - Icon system

### Infrastructure

**RPC Resilience:**
- Multiple RPC providers: Arbitrum official, Ankr, LlamaNodes
- Robust fallback system
- 99.9% uptime target
- Optimized batch reads via Viem multicall

---

## Architecture

### Feature-Based Structure

The frontend is organized using a feature-based architecture where each feature is a self-contained module:

```
src/
├── features/                    # Feature modules
│   ├── bundles/                # Trading strategies
│   ├── portfolio/              # Position management
│   ├── dashboard/              # Analytics
│   └── smart-contracts-docs/   # Documentation
│
├── components/                  # Shared components
│   └── ui/                     # Design system primitives
│
├── services/                    # Business logic
│   ├── contracts/              # Smart contract interactions
│   ├── bundles/                # Strategy services
│   └── swap/                   # DEX integration
│
├── constants/                   # Configuration
│   ├── ABI/                    # Smart contract ABIs
│   └── contracts/              # Deployed addresses
│
├── contexts/                    # Global state
├── hooks/                       # Custom hooks
├── config/                      # App configuration
├── lib/                         # Utilities
└── pages/                       # Route components
```

**Benefits:**
- Independent development and testing per feature
- Clear separation of concerns
- Easy to scale and maintain
- No circular dependencies

---

## Smart Contract Integration

### Deployed Contracts (Arbitrum One)

```
MainReader:      0xe989C151391F51b5544bF15acb54a551C6B68a87
ProxyManager:    0xf575Cd9B522bf95bE2fA0ff20B4Bc9eBaA718FfA
GMXPrices:       0xa2C1dE158d8916dCaa07f3A72762910E4a3f39D4
USDC (Native):   0xaf88d065e77c8cC2239327C5EDb3A432268e5831
```

### GMX Integration

- **26 tokens** combinable to create pair trading strategies
- **325 possible combinations** running on Arbitrum's infrastructure
- Deep integration handling GMX asynchronicity with callbacks
- Position registration and lifecycle management
- Proxy-based system for position isolation

### Integration Pattern

Each contract interaction follows this pattern:

```typescript
// Custom hook wrapping Wagmi
export function useIsProxyNeeded(
  userAddress?: `0x${string}`,
  marketLong?: string,
  marketShort?: string
) {
  const { data, isLoading, isError } = useReadContract({
    address: MAIN_READER_ADDRESS,
    abi: MAIN_READER_ABI,
    functionName: 'isProxyNeeded',
    args: [userAddress, marketLong, marketShort],
    chainId: ACTIVE_CHAIN.id,
  });

  return {
    isProxyNeeded: data?.[0],
    availableProxyAddress: data?.[1],
    isLoading,
    isError,
  };
}
```

**Key aspects:**
- Type-safe with Viem types
- Automatic loading/error states
- Optimized RPC calls with caching
- Pre-transaction validation

---

## Features

### 1. Trading Strategies (Bundles)

**User Experience:** Execute institutional-grade pair trading strategies in just 3 clicks

**Features:**
- Real-time performance metrics (APY, Sharpe Ratio, Win Rate)
- Risk analysis (Low/Medium/High)
- Historical statistics with heatmaps
- Entry recommendations
- Position breakdown

**Technical:**
- Custom hooks for contract interaction
- Optimized multicall for fetching multiple strategies
- Real-time price updates
- Precise PnL calculations with BigInt

### 2. Portfolio Management

**Features:**
- Interactive graph visualization (ReactFlow)
- Real-time unrealized earnings with live prices
- Position history and audit trail
- Export functionality (PDF/CSV)

**Technical:**
- Batch RPC calls for efficient data fetching
- Custom position decoding from contract state
- Graph algorithms for relationship mapping
- Lightweight Charts integration

### 3. Dashboard & Analytics

**Features:**
- Global PnL calculator across all positions
- Calendar view with daily/monthly heatmaps
- Performance breakdown by strategy type
- Position size distribution
- Financial objectives tracking

**Technical:**
- Custom chart components
- Efficient data aggregation
- Real-time updates via TanStack Query

---

## Project Structure

```
src/
├── features/                    # Feature modules
│   ├── bundles/                # Trading strategies
│   ├── portfolio/              # Position management
│   ├── dashboard/              # Analytics
│   └── smart-contracts-docs/   # Documentation
│
├── components/                  # Shared components
│   └── ui/                     # Design system primitives
│
├── services/                    # Business logic layer
│   ├── contracts/              # Smart contract interactions
│   ├── bundles/
│   ├── swap/
│   └── tokens/
│
├── constants/                   # Configuration
│   ├── ABI/                    # Smart contract ABIs
│   └── contracts/              # Deployed addresses
│
├── contexts/                    # Global state
├── config/                      # App configuration
├── hooks/                       # Custom hooks
├── lib/                         # Utility functions
├── i18n/                        # Translations (EN/ES)
└── pages/                       # Route components
```

---

## Performance

### Bundle Size (Production)

| Bundle | Size (gzipped) |
|--------|----------------|
| Initial JS | ~300KB |
| Initial CSS | ~40KB |
| Vendor (React) | ~80KB |
| Vendor (Web3) | ~100KB |
| Feature Chunks | 50-100KB each |

### Optimizations

- **Code Splitting:** Route-based lazy loading with React.lazy
- **Tree Shaking:** Vite eliminates unused code
- **CSS Purging:** TailwindCSS JIT removes unused styles
- **Image Optimization:** Lazy loading and WebP format
- **Memoization:** Strategic use of useMemo/useCallback
- **Virtual Scrolling:** For large lists (TanStack Virtual)

---

## Testing

### Strategy

```
Testing Pyramid:

        /\
       /E2E\      10%  Critical user flows
      /------\
     /  Int   \   20%  Feature workflows
    /----------\
   /    Unit    \ 70%  Functions, hooks, utils
  /--------------\
```

### Planned Coverage

| Test Type | Target Coverage | Focus Areas |
|-----------|----------------|-------------|
| **Unit Tests** | 80%+ | Utils, hooks, pure functions |
| **Integration Tests** | 70%+ | Feature workflows, contract interactions |
| **E2E Tests** | Key flows | Critical user journeys |

### Tools

- **Vitest** - Unit and integration testing
- **React Testing Library** - Component testing
- **Playwright** - E2E testing
- **MSW** - API mocking

---

## Code Quality

### TypeScript

- **Strict mode** enabled
- **100% TypeScript** coverage
- No `any` types
- Comprehensive interfaces for all data structures

### Linting

- ESLint with TypeScript rules
- React Hooks rules
- React Refresh rules

### Code Organization

- Feature-based architecture
- Single Responsibility Principle
- Separation of concerns (UI, Logic, Data)
- Consistent naming conventions

---

## Internationalization

Currently supporting:
- 🇬🇧 English (100%)
- 🇪🇸 Spanish (100%)

Built with react-i18next for easy expansion to additional languages.

---

## License

MIT License - See [LICENSE](LICENSE) for details

---

## Contact

- **Email:** sendralabs.eth@gmail.com
- **Twitter:** [@SendraLabs](https://x.com/SendraLabs)
- **Website:** [sendralabs.com](https://sendralabs.com)

---

**Built on Arbitrum • Powered by GMX**
