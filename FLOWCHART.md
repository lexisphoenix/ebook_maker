### Flujo de Datos

El usuario llena el formulario con tema, protagonista, etc. En cliente se valida con Zod. Si pasa, envía POST a `/api/stories/generate` con esos datos. La ruta API autentica al usuario (obtiene el ID del JWT de Clerk). Luego llama `StoryIteratorService.iterate()`.

El iterator es el orquestador. Para cada intento (1-3): primero llama `StoryGenerationService.generate()` que habla con OpenAI y obtiene el relato crudo. Luego `StoryValidationService.validate()` que analiza si cumple criterios. `TraceLoggerService.log()` guarda qué pasó. ¿Pasó validación? Si sí, retorna resultado. Si no, reintenenta con prompt ajustado.

Una vez que pasa validación, `StoryRepository.create()` guarda la historia final en BD. La API retorna JSON: `{ success: true, data: { id, content, wordCount, etc } }`. El frontend muestra el resultado.

┌─────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                   │
│  (Next.js Pages + React Components + Forms)             │
│  - pages/stories/new                                    │
│  - pages/stories/[id]                                   │
│  - components/StoryForm.tsx                             │
│  - components/StoryViewer.tsx                           │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                     API LAYER                           │
│  (Next.js API Routes - Endpoints)                       │
│  - api/stories/generate (POST)                          │
│  - api/stories/[id] (GET)                               │
│  - api/stories/list (GET)                               │
│  - api/health (GET)                                     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                   SERVICE LAYER                         │
│  (Business Logic - Independiente de framework)          │
│  - services/StoryGenerationService.ts                   │
│  - services/StoryValidationService.ts                   │
│  - services/StoryIteratorService.ts                     │
│  - services/TraceLoggerService.ts                       │
│  - services/ExportService.ts                            │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                 REPOSITORY LAYER                        │
│  (Data Access - Abstracción sobre DB)                   │
│  - repositories/StoryRepository.ts                      │
│  - repositories/IterationRepository.ts                  │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                  PERSISTENCE LAYER                      │
│  (Database - PostgreSQL via Prisma)                     │
│  - Prisma ORM                                           │
│  - Database Schema (stories, iterations)                │
└─────────────────────────────────────────────────────────┘