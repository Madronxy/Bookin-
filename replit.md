# BOOKin - Business Management Dashboard

## Overview

BOOKin is a comprehensive business management platform that enables appointment booking, client management, and business operations through QR codes and WhatsApp integration. The application features a dark-themed dashboard with neon accent colors (teal and purple gradients) for business owners to manage bookings, clients, staff, services, and billing. The platform includes a marketing landing page, a full-featured business dashboard with real-time analytics, dual QR code generation (booking + WhatsApp), and n8n webhook automation integration.

## Recent Updates (January 2025)

✓ Fixed DATABASE_URL environment variable configuration and resolved startup issues
✓ Completely redesigned landing page with clean, modern aesthetic matching user-provided design
✓ Updated theme from dark neon to light, professional design with clean cards and typography
✓ Implemented new color scheme: white background, gray text hierarchy, black accent buttons
✓ Added modern hero section with AI-powered booking system badge and star ratings
✓ Created comprehensive features section with 6 key feature cards
✓ Built "How BOOKin Works" section with 3-step process visualization
✓ Redesigned pricing section with clear Free/Starter/Pro tiers and feature comparison
✓ Added call-to-action section and clean footer with proper information architecture
✓ Generated 4 professional, high-quality images aligned with booking management purpose
✓ Integrated hero image showing professional tablet booking interaction
✓ Added process step images: dashboard analytics, QR code scanning, appointment management
✓ Replaced all placeholder content with attractive, contextual imagery
✓ Maintained all existing functionality while updating visual design completely

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React with TypeScript using Vite as the build tool
- **Routing**: Wouter for client-side routing with authentication-based route protection
- **UI Components**: Radix UI primitives with shadcn/ui component library for consistent design
- **Styling**: Tailwind CSS with custom dark theme featuring neon gradient accents (teal #00F5A0 and purple #8A2BE2)
- **State Management**: TanStack React Query for server state management and caching
- **Form Handling**: React Hook Form with Zod validation for type-safe form processing

### Backend Architecture
- **Runtime**: Node.js with Express.js framework
- **Language**: TypeScript with ES modules
- **API Design**: RESTful API with structured route handlers
- **Middleware**: Custom logging, error handling, and authentication middleware
- **Development**: Hot reload with Vite integration for seamless development experience

### Authentication System
- **Provider**: Replit Auth with OpenID Connect integration
- **Session Management**: Express sessions with PostgreSQL storage using connect-pg-simple
- **Security**: HTTP-only cookies with secure flags and CSRF protection
- **User Flow**: Automatic redirection based on authentication status

### Database Architecture
- **Primary Database**: PostgreSQL with Neon serverless driver
- **ORM**: Drizzle ORM for type-safe database operations and migrations
- **Schema Management**: Drizzle Kit for database schema migrations and version control
- **Connection**: Connection pooling with @neondatabase/serverless for scalability

### Core Data Models
- **Users**: Authentication and profile management (mandatory for Replit Auth)
- **Businesses**: Business profiles with subscription plans and QR code integration
- **Staff**: Employee management with roles and specialties
- **Services**: Service offerings with pricing and duration
- **Clients**: Customer database with contact information and booking history
- **Bookings**: Appointment scheduling with status tracking and service association
- **Sessions**: Secure session storage for authentication persistence

### Business Logic Layer
- **Storage Interface**: Abstracted data access layer with comprehensive CRUD operations
- **Authentication Flow**: Integrated Replit Auth with automatic user provisioning
- **Authorization**: Role-based access control with business ownership validation
- **Data Validation**: Zod schemas for runtime type checking and API validation

### UI/UX Design System
- **Theme**: Dark background (#0D0D0D) with glassmorphism effects
- **Accent Colors**: Neon gradient system with CSS custom properties
- **Typography**: Poppins font family for modern, bold headlines
- **Components**: Comprehensive component library with hover animations and accessibility features
- **Responsive Design**: Mobile-first approach with adaptive layouts

### QR Code Integration
- **Generation**: Canvas-based QR code generation utility for booking links
- **Business Integration**: Unique QR codes per business for direct booking access
- **Download Features**: PNG export functionality for marketing materials

### Development Workflow
- **Build Process**: Vite for frontend bundling, esbuild for server compilation
- **Development Server**: Integrated Vite dev server with Express backend
- **Hot Reload**: Full-stack hot reload with error overlay for development
- **Type Safety**: End-to-end TypeScript with shared schemas between client and server

## External Dependencies

### Database Services
- **Neon Database**: Serverless PostgreSQL hosting with connection pooling
- **Drizzle ORM**: Database toolkit with migration management and type safety

### Authentication
- **Replit Auth**: OpenID Connect provider for user authentication and session management

### UI Framework
- **Radix UI**: Accessible component primitives for complex UI interactions
- **Tailwind CSS**: Utility-first CSS framework with custom design system
- **Lucide React**: Icon library for consistent iconography

### Development Tools
- **Vite**: Build tool with development server and hot module replacement
- **TanStack React Query**: Data fetching and caching library for server state
- **React Hook Form**: Form library with validation integration
- **Zod**: Schema validation library for runtime type checking

### Styling and Animation
- **Class Variance Authority**: Utility for component variant management
- **Tailwind Merge**: Utility for merging Tailwind classes efficiently
- **CSS Custom Properties**: Dynamic theming system for neon gradients and dark mode

### Communication
- **WhatsApp Integration**: Business communication channel for appointment confirmations