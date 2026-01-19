# BlogCraft - AI-Powered Blog Content Management System

## Overview

BlogCraft is a full-stack web application for automated blog content creation and management. It helps users generate SEO-optimized articles through AI, manage keyword research, schedule content via a calendar interface, and configure article generation settings. The system uses an onboarding wizard to collect website information before generating targeted content strategies.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **Build Tool**: Vite with custom development plugins for Replit integration
- **State Management**: TanStack Query (React Query) for server state, local React state for UI
- **Styling**: Tailwind CSS with shadcn/ui component library (New York style variant)
- **Routing**: Single-page application with view-based navigation (no client router)
- **Component Structure**: Feature-based organization with reusable UI components in `client/src/components/ui/`

### Backend Architecture
- **Runtime**: Node.js with Express 5
- **Language**: TypeScript with ESM modules
- **API Pattern**: RESTful JSON APIs under `/api/*` prefix
- **Build**: esbuild for server bundling, Vite for client bundling
- **Development**: tsx for TypeScript execution, Vite middleware for HMR

### Data Layer
- **ORM**: Drizzle ORM with PostgreSQL dialect
- **Schema Location**: `shared/schema.ts` - shared between client and server
- **Validation**: Zod schemas generated from Drizzle schemas via drizzle-zod
- **Migrations**: Drizzle Kit with `db:push` command for schema synchronization

### Core Data Models
- **Websites**: Stores user website configuration including URL, name, language, target audiences, competitors
- **Keywords**: Keyword research results with search volume, difficulty, CPC, and intent
- **Articles**: Generated content with title, body, meta description, SEO score, and publish status
- **Calendar Events**: Content scheduling tied to keywords and articles
- **Article Settings**: Per-website article generation preferences

### AI Integration Layer
The application includes Replit AI Integrations in `server/replit_integrations/`:
- **Chat**: Conversation management with OpenAI-compatible API
- **Audio**: Voice recording, speech-to-text, and text-to-speech capabilities
- **Image**: Image generation via gpt-image-1 model
- **Batch**: Rate-limited batch processing utilities for LLM operations

### Storage Pattern
- **Interface**: `IStorage` in `server/storage.ts` defines data access contract
- **Implementation**: Currently uses in-memory storage with database schema ready for PostgreSQL

## External Dependencies

### Database
- **PostgreSQL**: Primary database (configured via `DATABASE_URL` environment variable)
- **connect-pg-simple**: Session storage for Express

### AI Services
- **OpenAI API**: Used through Replit AI Integrations for content generation
- **Environment Variables**: `AI_INTEGRATIONS_OPENAI_API_KEY`, `AI_INTEGRATIONS_OPENAI_BASE_URL`

### UI Component Library
- **shadcn/ui**: Comprehensive set of accessible React components built on Radix UI primitives
- **Radix UI**: Unstyled, accessible component primitives
- **Lucide React**: Icon library

### Key Runtime Dependencies
- **express**: Web server framework
- **drizzle-orm**: Database ORM
- **@tanstack/react-query**: Async state management
- **zod**: Runtime type validation
- **date-fns**: Date manipulation utilities

### External SEO Services
- **DataForSEO API**: Used for comprehensive keyword research and site analysis
  - Environment Variables: `DATAFORSEO_LOGIN`, `DATAFORSEO_PASSWORD`
  - Endpoints Used:
    - `/v3/dataforseo_labs/google/related_keywords/live` - Related keywords
    - `/v3/dataforseo_labs/google/keyword_suggestions/live` - Keyword suggestions
    - `/v3/dataforseo_labs/google/keyword_ideas/live` - Keyword ideas
    - `/v3/serp/google/autocomplete/live/advanced` - Autocomplete suggestions
    - `/v3/serp/google/organic/live/advanced` - SERP with People Also Ask
    - `/v3/content_generation/generate_sub_topics/live` - Subtopics
    - `/v3/keywords_data/google_ads/keywords_for_site/live` - Keywords for Site

## Recent Changes (January 2026)

### Features Implemented
1. **Onboarding Wizard**: 6-step wizard for website setup
   - Website URL input with real-time website analysis
   - AI-powered business info extraction (uses OpenAI to extract business name and description)
   - SSRF protection (blocks private IPs, validates DNS, checks redirects)
   - Business details configuration (pre-filled from analysis)
   - Target audience and competitor input
   - Blog configuration (sitemap, blog URL, article examples)
   - Article preferences (style, internal links, image style)

2. **Content Calendar**: Monthly calendar view for content scheduling
   - Keyword scheduling with visual date picker
   - Generate Keywords button to create content strategy via DataForSEO
   - Article generation from scheduled keywords

3. **Enhanced Keyword Research**: Comprehensive multi-source keyword discovery
   - Calls 6 DataForSEO APIs in parallel:
     - Related Keywords, Keyword Suggestions, Keyword Ideas
     - Autocomplete, People Also Ask (PAA), Subtopics
   - AI-powered keyword categorization:
     - Quick Wins (high volume, low difficulty)
     - Authority Builders (high difficulty, high volume)
     - Emerging Topics (low volume opportunities)
     - Intent Signals (questions/PAA keywords)
     - Semantic Topics (autocomplete/subtopics)
   - Semantic clustering with hub & spoke content strategy
   - Category filter tabs to focus on specific keyword types
   - Source badges showing keyword origin (Related, Suggested, PAA, etc.)
   - Sort by search volume and difficulty
   - Bulk add keywords to calendar
   - Direct "Generate Article" button per keyword row

4. **Article Editor**: Surfer SEO-style content editor
   - Two-column layout (editor left, SEO panel right)
   - Large circular SVG SEO score with animated stroke
   - Content Guidelines with gradient progress bars (Words, Paragraphs, Headings, Images)
   - NLP Term Suggestions with usage indicators and checkmarks
   - SEO Checklist with passed/total badge
   - Article Stats grid (Words, Min Read, Headings, Links)
   - Formatting toolbar (Bold, Italic, Headings, Lists, Links, Quote, Code)
   - Auto-save with 30-second debounce and save status indicator
   - Keyboard shortcuts (Cmd/Ctrl+S to save)
   - Title with character counter and SEO feedback
   - Meta description with visual progress bar
   - Fullscreen editing mode
   - Featured image and YouTube video embedding
   - Draft/publish workflow with toast notifications

5. **Dashboard Overview**: Stats and quick access
   - Published/Draft/Scheduled article counts with gradient icon backgrounds
   - Recent articles list
   - Top keywords by search volume

### Design System (Surfer SEO-inspired)
- **Primary Color**: Violet (#7C3AED, HSL: 262 83% 58%)
- **Secondary Color**: Teal (HSL: 175 84% 32%)
- **Accent Color**: Orange (HSL: 24 95% 53%)
- **Sidebar**: Dark theme (HSL: 232 47% 12%) with gradient logo
- **Fonts**: Inter, Plus Jakarta Sans

## File Structure

```
client/src/
├── App.tsx                    # Main app with routing logic
├── components/
│   ├── app-sidebar.tsx        # Dashboard sidebar navigation
│   ├── content-calendar.tsx   # Monthly content calendar
│   ├── article-editor.tsx     # Article editing interface
│   ├── keyword-research.tsx   # Keyword research panel
│   └── onboarding/            # Onboarding wizard components
│       ├── onboarding-wizard.tsx
│       ├── step-website.tsx
│       ├── step-analyzing.tsx
│       ├── step-business.tsx
│       ├── step-audience.tsx
│       ├── step-blog.tsx
│       └── step-articles.tsx
├── pages/
│   └── dashboard.tsx          # Main dashboard view
└── lib/
    └── types.ts               # TypeScript interfaces

server/
├── routes.ts                  # API route definitions
├── storage.ts                 # Data storage interface
└── replit_integrations/       # AI service integrations
```

## API Endpoints

### Websites
- `GET /api/websites` - List all websites
- `POST /api/websites` - Create website
- `PATCH /api/websites/:id` - Update website
- `DELETE /api/websites/:id` - Delete website

### Keywords
- `GET /api/keywords/:websiteId` - Get keywords for website
- `POST /api/keywords` - Create keyword
- `POST /api/keywords/bulk` - Create multiple keywords
- `PATCH /api/keywords/:id` - Update keyword

### Articles
- `GET /api/articles/:websiteId` - Get articles for website
- `POST /api/articles` - Create article
- `PATCH /api/articles/:id` - Update article
- `POST /api/articles/:id/publish` - Publish article

### Calendar Events
- `GET /api/calendar-events/:websiteId` - Get events (supports year/month query params)
- `POST /api/calendar-events` - Create event
- `POST /api/calendar-events/bulk` - Create multiple events

### Strategy Generation
- `POST /api/websites/:id/research-keywords` - Research keywords via DataForSEO
- `POST /api/websites/:id/generate-strategy` - Generate content strategy