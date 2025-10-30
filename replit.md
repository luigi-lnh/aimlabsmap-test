# Overview

This is a 3D web application built with React Three Fiber that creates an interactive 3D environment inspired by Aimlabs (aim training). The application features a first-person camera controller with mouse-based rotation and pointer lock functionality, rendering a custom 3D map with platforms and lighting in a Three.js scene. The project uses a full-stack TypeScript architecture with Express backend, Vite for frontend bundling, and PostgreSQL with Drizzle ORM for database management.

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend Architecture

**Problem/Requirement**: Need to create an interactive 3D environment with first-person camera controls and user interface overlays.

**Solution**: React-based frontend using React Three Fiber (@react-three/fiber) for 3D rendering with the following structure:

- **3D Rendering Layer**: Uses React Three Fiber to wrap Three.js functionality, providing declarative 3D scene management
- **Component Structure**:
  - `AimlabsMap`: Defines the 3D environment geometry (platforms, floor, grid)
  - `CameraController`: Implements first-person mouse-look controls with pointer lock
  - `Lights`: Configures scene lighting (ambient, directional, shadows)
  - `Interface`: UI overlay components for game controls
- **State Management**: Zustand stores for game state and audio management
  - `useGame`: Manages game phases (ready, playing, ended)
  - `useAudio`: Controls audio playback and mute state
- **Styling**: Tailwind CSS with custom theme configuration using HSL color variables
- **UI Components**: Comprehensive shadcn/ui component library (Radix UI primitives)

**Alternatives Considered**: Native Three.js (rejected for lack of React integration), Babylon.js (rejected due to team familiarity with Three.js)

**Pros**: Declarative 3D scene management, seamless React integration, strong ecosystem
**Cons**: Performance overhead from React reconciliation, learning curve for Three.js concepts

## Backend Architecture

**Problem/Requirement**: Need server-side routing and API endpoints with development hot-reload support.

**Solution**: Express.js server with custom Vite integration:

- **Server Structure**:
  - `server/index.ts`: Express application setup with middleware
  - `server/routes.ts`: API route registration (currently stubbed)
  - `server/vite.ts`: Vite development server integration with HMR
- **Middleware Stack**:
  - Request logging with response time tracking
  - JSON body parsing
  - Error handling with status code extraction
- **Development Mode**: Vite middleware mode for HMR and asset serving
- **Production Mode**: Serves pre-built static assets from dist/public
- **Storage Layer**: Abstracted storage interface with in-memory implementation

**Pros**: Fast development iteration, type-safe full-stack development, familiar Express patterns
**Cons**: Additional complexity from Vite integration, middleware order sensitivity

## Data Layer

**Problem/Requirement**: Need type-safe database schema and query interface with migration support.

**Solution**: Drizzle ORM with PostgreSQL dialect:

- **Schema Definition**: Single shared schema file (`shared/schema.ts`) accessible to both client and server
- **Database**: PostgreSQL via Neon serverless driver (@neondatabase/serverless)
- **Current Schema**:
  - `users` table: id (serial primary key), username (unique text), password (text)
- **Validation**: Zod schemas generated from Drizzle schema for runtime validation
- **Storage Abstraction**: 
  - `IStorage` interface defines CRUD operations
  - `MemStorage` provides in-memory implementation for development/testing
  - Design allows easy swap to database-backed implementation
- **Migrations**: Drizzle Kit configured to output to ./migrations directory

**Pros**: Full TypeScript type inference, lightweight ORM, schema-driven validation
**Cons**: Less mature ecosystem than Prisma, manual migration management

## Build System

**Problem/Requirement**: Need unified build process for TypeScript client and server code with asset handling.

**Solution**: Vite for frontend, esbuild for backend:

- **Frontend Build**: Vite with React plugin
  - Root: `client/` directory
  - Output: `dist/public/`
  - GLSL shader support via vite-plugin-glsl
  - Large asset support (GLTF, GLB, audio files)
  - Path aliases: `@/` → client/src, `@shared/` → shared
- **Backend Build**: esbuild bundler
  - Entry: `server/index.ts`
  - Output: `dist/index.js`
  - Platform: node, Format: ESM
  - External packages (not bundled)
- **TypeScript**: Strict mode, ESNext module resolution, bundler module resolution
- **PostCSS**: Tailwind CSS processing with autoprefixer

**Pros**: Fast build times, modern ESM support, integrated HMR
**Cons**: Dual build system complexity, esbuild limitations for advanced transforms

# External Dependencies

## Database Service
- **Neon Serverless PostgreSQL**: Managed PostgreSQL accessed via `DATABASE_URL` environment variable
- Connection pooling handled by @neondatabase/serverless driver
- Required for production; MemStorage fallback available for development

## UI Component Libraries
- **Radix UI**: Unstyled, accessible component primitives (30+ components)
- **shadcn/ui**: Pre-styled components built on Radix UI primitives
- **Lucide React**: Icon library for UI elements

## 3D Graphics Stack
- **Three.js**: Core 3D rendering engine
- **@react-three/fiber**: React renderer for Three.js
- **@react-three/drei**: Helper components and utilities for R3F
- **@react-three/postprocessing**: Post-processing effects

## State Management
- **Zustand**: Lightweight state management with middleware support
- **@tanstack/react-query**: Server state management and caching (configured but not actively used)

## Development Tools
- **Drizzle Kit**: Database migration and schema management
- **tsx**: TypeScript execution for development server
- **@replit/vite-plugin-runtime-error-modal**: Development error overlay

## Styling
- **Tailwind CSS**: Utility-first CSS framework
- **class-variance-authority**: Variant-based component styling
- **clsx & tailwind-merge**: Conditional className utilities

## Session Management
- **connect-pg-simple**: PostgreSQL session store (dependency present but not actively configured)