# Overview

This is a full-stack web application for Magnum Torque Retifica, a Brazilian company specializing in torque converter reconditioning services. The application features a public marketing website showcasing their services and an administrative panel for product management. Built with modern web technologies, it provides both Portuguese-language customer-facing content and secure admin functionality.

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend Architecture
The frontend uses React with TypeScript, built on Vite for fast development and optimized production builds. The application leverages:

- **Component Library**: Radix UI components with custom styling via Tailwind CSS
- **Routing**: Wouter for lightweight client-side routing
- **State Management**: TanStack Query for server state management and caching
- **Styling**: Tailwind CSS with custom design tokens defined in theme.json
- **Forms**: React Hook Form with Zod validation for type-safe form handling
- **UI Framework**: shadcn/ui component system for consistent design patterns

## Backend Architecture
The backend is built with Express.js and follows a modular route structure:

- **Authentication**: Passport.js with local strategy for admin login
- **Session Management**: Express sessions with in-memory storage (MemoryStore)
- **API Design**: RESTful endpoints organized by feature (auth, products)
- **Middleware**: Custom admin authorization middleware for protected routes
- **Data Storage**: PostgreSQL database with full persistence using DatabaseStorage implementation

## Database Design
Now uses PostgreSQL database with a well-defined schema structure using Drizzle ORM:

- **Users Table**: Admin user management with username/password authentication
- **Products Table**: Product catalog with detailed specifications (car model, transmission, motorization, year)
- **Contact Form Table**: Contact message storage (schema exists but functionality redirects to WhatsApp)

The application has been fully migrated from in-memory storage to PostgreSQL for persistent data storage, with all CRUD operations implemented using Drizzle ORM.

## Authentication & Authorization
Implements a two-tier security model:

- **Public Access**: Marketing pages and product catalog are publicly accessible
- **Admin Access**: Protected routes requiring authentication and admin privileges
- **Session-Based**: Uses secure HTTP-only cookies for session management
- **Role-Based**: Admin flag in user model controls access to administrative features

## Key Features
- **Bilingual Content**: Portuguese-language interface optimized for Brazilian market
- **Product Management**: Full CRUD operations for torque converter product catalog
- **Contact Integration**: WhatsApp integration for customer inquiries
- **Responsive Design**: Mobile-first approach with Tailwind CSS
- **Admin Dashboard**: Comprehensive product management with filtering capabilities
- **Image Carousel**: Interactive carousel in About section using Embla Carousel with navigation controls and indicators

# Recent Changes

## Product Management System Overhaul (Janeiro 2026)
- **Dynamic Filter Options**: Model, Transmission, Motorization, and Year fields now use data from the database instead of hardcoded options
- **Combobox Inputs**: Users can select from existing database options OR type custom values to create new ones
- **Image Upload System**: Implemented file upload with multer, supporting JPG/PNG/GIF/WebP files up to 5MB
- **Visual Image Gallery**: Thumbnail grid showing all available images with selection and preview
- **New API Endpoints**: Added /api/filters/all-transmissions, /api/filters/all-motorizations, /api/filters/all-years for independent filter options
- **Storage Methods**: Added getAllTransmissions(), getAllMotorizations(), getAllYears() to DatabaseStorage
- **Improved UX**: Loading states, error handling, and responsive form layout

## Catalog Page Redesign (Janeiro 2026)
- **Complete Visual Overhaul**: Redesigned product catalog page with modern, professional look
- **Hero Section**: Dark gradient hero with centered title, search bar, and product count indicators
- **Filter System**: Redesigned sidebar with collapsible filters, icons, and better mobile experience
- **Product Cards**: Modern cards with image hover effects, specs display, and clear CTAs
- **Mobile Optimization**: Fully responsive layout with collapsible filters and mobile-specific CTA
- **Testimonials Update**: Replaced fake testimonials with 7 real Google reviews from customers
- **Partners Section**: Added Alto USA and Valeo as new partners, reorganized to 3 per row
- **10 Year Anniversary**: Updated company age and added anniversary logo throughout site

## Database Migration to PostgreSQL (Agosto 2025)
- **Migração Completa**: Substituído armazenamento em memória por banco PostgreSQL real
- **DatabaseStorage**: Implementada nova classe `DatabaseStorage` que substitui `MemStorage`
- **Persistência Total**: Todos os produtos, usuários e mensagens de contato agora são persistidos no banco
- **Operações CRUD**: Implementadas todas as operações de banco usando Drizzle ORM
- **Filtros Otimizados**: Consultas de filtro agora usam `selectDistinct` para melhor performance
- **Setup Fácil**: Configuração automática do banco com comando `npm run db:push`
- **Compatibilidade**: Mantida interface IStorage para compatibilidade com código existente

# Previous Changes

## Final Project Optimization (August 2025)
- **Code Cleanup**: Removed unused imports and console.logs from components
- **Navigation System**: Fixed footer navigation to work correctly between home and catalog pages
- **Mobile Optimization**: Extensively optimized Services and About sections for mobile devices
- **Testimonials Enhancement**: Added select-none to prevent text selection during carousel navigation
- **About Section Redesign**: Returned to compact 2-column layout as requested, maintaining professional appearance
- **Performance**: Cleaned up unnecessary code and optimized component structure
- **Image Assets**: Replaced product image with company logo in About section for better branding
- **Development Workflow**: Configured proper Replit workflows for development server startup

# Previous Changes

## Site Structure Reorganization (August 2025)
- **Navigation Flow**: Reorganized website sections from Home→About→Services→Testimonials→Products→Partners→Contact to Home→Services→Products→Partners→About→Testimonials→Contact
  - More logical customer journey: Services first (main offering), then Products (complementary), Credibility (Partners), Company background (About), Social proof (Testimonials), and final Call-to-action (Contact)
  - Updated all navigation components (desktop header, mobile menu, alternative links) to maintain consistency
- **Hero Section Interactivity**: Fixed scroll behavior for call-to-action buttons
  - Replaced static anchor links with smooth scroll functionality
  - Added JavaScript event handlers for "Entre em Contato" and "Nossos Serviços" buttons
  - Improved user experience with seamless navigation to targeted sections

## About Section Visual Improvements (August 2025)
- **Screen Fit Optimization**: Adjusted padding and spacing to fit content properly within screen viewport
  - Reduced vertical padding from py-28/py-32 to py-12/py-16/py-20/py-24
  - Added min-h-screen with flex centering for better viewport utilization
  - Decreased header margin-bottom from mb-16/mb-20 to mb-8/mb-12
  - Reduced main grid gaps from gap-12/gap-16/gap-20 to gap-8/gap-12/gap-16
- **Content Reorganization**: Improved information architecture and reduced redundancy
  - Moved "Especialistas há mais de 9 anos" from header badge to differential card
  - Simplified statistics grid from 4 to 2 cards (removed duplicate experience and success rate)
  - Updated service timeline from "3-4" to "3-5" days for conclusion
- **Carousel to Single Image**: Replaced interactive carousel with simple product image
  - Removed entire ImageCarousel component and embla-carousel dependencies
  - Replaced with static conversor_01.jpg image maintaining same visual treatment
  - Kept subtle gradient background blur effect for visual consistency
- **Subtle Visual Enhancements**: Added minimal decorative elements without being excessive
  - Geometric patterns with low opacity (5%) for visual interest
  - Floating dots pattern with strategic positioning
  - Subtle gradient underline accent on "Magnum Torque" title
  - Enhanced hover animations on all interactive cards
  - Smooth transform effects (scale, translate) on statistics and feature cards
- **Interactive Improvements**: All cards now respond to hover with elevation and border color changes
  - Statistics cards: Scale effect on numbers with hover elevation
  - Feature cards: Icon scale and background color transitions
  - Maintained subtle and professional aesthetic

## About Section Creative Redesign (August 2025)
- **Storytelling Approach**: Complete restructure focusing on company narrative and visual storytelling
- **Visual Hierarchy**: Center-aligned header with large gradient title and compelling subtitle
- **Content Organization**: Story-driven layout with engaging elements:
  - Hero-style badge with "Especialistas há mais de 9 anos"
  - Large gradient headline "Conheça a Magnum Torque"
  - Company story card with "Nossa História" section and rotated background effect
  - Differential cards highlighting unique techniques and extended warranty
  - Statistics grid with large numbers (9+ years, 100% success rate, 3-4 days, 6 months warranty)
- **Visual Design**: 
  - Subtle gradient background (slate-50 to gray-50) with soft decorative blur elements
  - Card-based layout with glass-morphism effects (backdrop-blur-sm)
  - Rotated background elements for dynamic visual interest
  - Professional shadow and border treatments
- **Mobile Experience**: Carousel hidden on smaller screens, stats grid remains responsive
- **Engagement Elements**: Hover effects, gradient accents, and visual depth through layered design

## Site Optimization and Performance Improvements (August 2025)
- **Mobile UX**: Fixed carousel positioning in About section - now appears after content on mobile devices for better user experience
- **SEO Enhancement**: Comprehensive SEO optimization with meta tags, Open Graph tags, Twitter cards, and structured data (Schema.org)
- **Performance**: Replaced external Cloudinary image URLs with local optimized images for faster loading
- **Accessibility**: Added lazy loading to all images, improved ARIA labels, better focus indicators, and enhanced contrast ratios
- **CSS Optimization**: Added performance optimizations including smooth scrolling, reduced-motion preferences, and improved focus visibility
- **Technical**: Removed Font Awesome CDN dependency to reduce external requests and improve load times

## Image Carousel Implementation (August 2025)
- **Component**: Replaced static images in About section with interactive carousel
- **Technology**: Implemented using embla-carousel-react library
- **Features**: Navigation arrows, dot indicators, auto-loop functionality
- **Images**: Added three product images (conversor_01.jpg, conversor_02.jpg, transmissao_01.jpg)
- **UI/UX**: Enhanced visual appeal with smooth transitions and responsive design
- **Error Handling**: Added console logging for image load status and fallback for failed loads

# External Dependencies

## Database Services
- **Neon Database**: PostgreSQL-compatible serverless database (configured but currently using in-memory storage)
- **Drizzle ORM**: Database toolkit for TypeScript with migration support

## Authentication & Security
- **bcryptjs**: Password hashing for secure user authentication
- **express-session**: Session management middleware
- **passport**: Authentication middleware with local strategy

## UI & Styling
- **Radix UI**: Headless UI components for accessibility and customization
- **Tailwind CSS**: Utility-first CSS framework
- **Lucide React**: Icon library for consistent iconography
- **Google Fonts**: Montserrat and Open Sans font families

## Development & Build Tools
- **Vite**: Fast build tool and development server
- **TypeScript**: Type safety across the application
- **ESBuild**: Fast JavaScript bundler for production builds
- **Replit Plugins**: Development environment optimizations

## External Services
- **WhatsApp Business**: Direct customer communication integration
- **Font Awesome**: Additional icon library for enhanced UI
- **Social Media**: Integration links for Facebook, Instagram, and TikTok

The application is structured for easy deployment on Replit with proper environment variable configuration and build processes optimized for both development and production environments.