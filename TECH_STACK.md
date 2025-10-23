## PARTE 3: HERRAMIENTAS Y TECNOLOGÍAS (TECH STACK)

**Frontend:**
Usamos React 18 como librería UI, Next.js 14 como framework (con App Router), TypeScript 5 para tipos. Tailwind CSS para estilos. React Hook Form para manejar formularios con Zod para validación. Zustand para estado global (es más simple que Redux).

**Backend:**
Node.js 20+ corriendo en Vercel. El framework es Next.js (API Routes, no Express separado). Para la base de datos usamos Prisma como ORM. OpenAI SDK para llamadas a GPT-4 Turbo. Clerk para autenticación.

**Infraestructura:**
Todo en Vercel (hosting). PostgreSQL en un servicio manejado (Neon o Railway, ambos funcionan bien con Vercel). Sentry para monitoreo de errores. Vercel Analytics para analíticos básicos.

**Desarrollo:**
npm como package manager. Vitest para tests con Testing Library. ESLint para linting y Prettier para formato. TypeScript en modo estricto. GitHub para versionado, GitHub Actions para CI, Vercel para CD (deployment automático).

**Librerías principales:**
`openai` (cliente de OpenAI), `prisma` (ORM), `@clerk/nextjs` (autenticación), `zod` (validación de esquemas), `react-hook-form` (formularios), `zustand` (estado), `date-fns` (manejo de fechas), `lucide-react` (iconos simples).
