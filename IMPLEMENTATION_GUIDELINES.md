### Principios Fundamentales

**Cada cosa hace una cosa:**
No mezclar la lógica de generación de IA con la persistencia en BD. No mezclar validación con orquestación. Cada servicio tiene responsabilidad única. Esto hace que sea fácil testear cada parte por separado.

**Diseño que sea testeable:**
Inyectar dependencias en lugar de hardcodearlas. Evitar variables globales. Permitir que cualquier servicio pueda reemplazarse por un mock para tests sin necesidad de llamar APIs reales.

**Seguridad desde el inicio:**
Las API keys nunca van en el código. Los inputs siempre se validan en servidor, no solo en cliente. Los usuarios siempre están autenticados. El rate limiting está desde el primer día.

**Implementación profunda:**
En lugar de tocar un poco de todo en cada sprint, implementamos en profundidad. Primer sprint: todo lo de setup. Segundo sprint: todo lo de generación. Tercero: validación. Cuarto: UI. No empezamos la UI hasta que el backend sea sólido.

### Estándares de Código

**Nombres consistentes:**
Los servicios se llaman algo así: `StoryGenerationService`, `StoryValidationService`. Los tipos de datos: `Story`, `ValidationResult`, `IterationTrace`. Las constantes en mayúscula: `MAX_RETRIES = 3`, `MIN_WORD_COUNT = 450`. Los archivos en minúscula con guiones: `story-generator.service.ts`.

**TypeScript estricto:**
Nunca usamos `any`. Si no sabemos el tipo, usamos `unknown` y lo refinamos. Las funciones siempre declaran qué retornan, no confiamos en inferencia. Usamos `Pick<>` y `Omit<>` para derivar tipos, no duplicamos.

**Tests junto al código:**
Cada servicio tiene su archivo de tests al lado. Los servicios de validación y generación se testean sin llamar a OpenAI real (usamos mocks). Buscamos al menos 80% de cobertura en la carpeta de servicios. Los nombres de archivos de tests siguen patrón: `story.service.spec.ts`.

**Commits descriptivos:**
Los mensajes son claros: "feat: agregar reintento automático en StoryIteratorService" o "fix: corregir conteo de palabras para palabras con guiones" o "test: agregar tests para validación de 3 personajes".

### Flujo de Desarrollo Local

Cuando alguien se sienta a trabajar, hace lo siguiente: primero clona el repo e instala dependencias. Copia el archivo `.env.example` a `.env.local` y rellena las API keys. Luego corre `npx prisma generate` para que Prisma prepare todo, y `npx prisma db push` para crear las tablas. Finalmente `npm run dev` arranca el servidor local en puerto 3000.

Durante el desarrollo, si modifica archivos en la carpeta `services/`, corre `npm run test` para verificar tests. Si modifica componentes React, recarga el navegador. Si modifica el schema de BD (en `prisma/schema.prisma`), corre `npx prisma migrate dev` para crear la migración. Antes de hacer commit, corre `npm run lint` para linting, `npm run type-check` para verificar tipos, y `npm run test` para tests.

Cuando hace pull request, los tests corren automáticamente en CI. Si pasan, puede mergear a rama `main`. Cuando mergea a `production`, Vercel despliega automáticamente a producción.

### Manejo de Errores

En los servicios, lanzamos excepciones tipadas. Por ejemplo, en lugar de throw new Error("algo pasó"), creamos una clase `class ValidationError extends Error` e incluimos contexto: `throw new ValidationError("Word count invalid", { actual: 300, expected: "450-550" })`.

En las rutas API, capturamos los errores y siempre retornamos un objeto JSON consistente: `{ success: boolean, data?: algo, error?: string }`. Si algo sale mal, registramos en Sentry.

En el cliente, si el error es información útil (ej: "La historia no tiene suficientes personajes"), lo mostramos al usuario. Si es error inesperado (ej: "timeout de base de datos"), lo registramos silenciosamente en Sentry.

### Optimización

Para llamadas a OpenAI, usamos temperature 0.7 para balance entre creatividad y consistencia. Limitamos a 800 tokens (suficiente para ~500 palabras). El timeout es 30 segundos. No hacemos múltiples llamadas seriales si podemos evitarlo.

Para la base de datos, creamos índices en los campos que buscamos frecuentemente: `userId`, `createdAt`, `status`. Las queries especifican qué columnas necesitan (no `SELECT *`). Las trazas se cargan de forma lazy (solo cuando el usuario lo pide).

En el frontend, dividimos el código por páginas (code splitting). Si hay imágenes futuro, las optimizamos. Guardamos las historias generadas en memoria de sesión (no localStorage, es data sensible).