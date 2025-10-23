# SAGI - Sistema Automatizado de Generación Infantil

## Introducción

**SAGI** es una plataforma que genera relatos infantiles usando inteligencia artificial. El usuario proporciona tema, protagonista, número mínimo de personajes y tono, y el sistema genera una historia validada automáticamente. Si falla la validación, reintenta hasta 3 veces con ajustes inteligentes.

### Stack Técnico

Construido con: **React + Next.js + TypeScript + OpenAI + Prisma + PostgreSQL**

Los datos fluyen desde el componente React → API → Servicios de lógica → Repositorios → Base de datos.

### Características Clave

- **Arquitectura en capas** independientes que se testean por separado
- **Código seguro**: TypeScript + validación server-side
- **Optimizado**: Queries eficientes en BD y uso inteligente de OpenAI
- **Transparente**: Trazas de cada intento registradas

---

## Las Tres Fases: El Flujo Central

### Fase 1: Entrada de Datos

El usuario quiere crear una historia. Puede llegar de una de tres formas:

1. **Formulario web**: Rellena tema, protagonista, número de personajes, etc.
2. **Archivo** (futuro): Sube un documento Markdown con la entrada
3. **API** (futuro): Un script externo llama a nuestro endpoint con JSON

Para el **MVP**, es solo el formulario. Simple, limpio, directo.

#### Parámetros Capturados

- Tema o género
- Protagonista(s)
- Número mínimo de personajes
- Preferencias de tono (aventurero, misterioso, cómico)
- Duración aproximada (default: ~500 palabras)

Se almacena en base de datos. Cada intento de generación es un registro. Cada iteración es un campo más. Así el usuario después puede ver el "antes y después".

---

### Fase 2: Validación

Aquí es donde la magia ocurre.

Cuando el relato sale de OpenAI, no lo damos por bueno inmediatamente. Lo analizamos contra tres criterios:

#### Criterio 1: Longitud (~500 palabras, ±10%)

Es matemático. Contar palabras. Si es 480 o 520, perfecto. Si es 300, rechazar.

#### Criterio 2: Estructura en 3 actos

Este es más delicado. No es perfectamente automatizable, pero usamos heurísticas:

- ¿Hay al menos 2 párrafos separados? (pausa narrativa)
- ¿El último párrafo cierra la historia? (desenlace)
- ¿Hay un conflicto evidente en el medio?

No es ciencia exacta, pero funciona el 85% de las veces.

#### Criterio 3: Al menos 3 personajes

Buscar nombres propios. Extraer entidades nombradas. Contar.

**Resultado**: Si todo pasa, retornamos `valid: true`. Si algo falla, retornamos exactamente qué falló y por qué.

---

### Fase 3: Iteración y Trazas

Si la validación falla, no nos rendimos.

Aquí entra la orquestación: **reintentamos hasta 3 veces**. En cada intento, ajustamos el prompt:

- **Si falló la longitud**: "Genera una historia de exactamente 500 palabras"
- **Si falló la estructura**: "Asegúrate de contar en tres actos claramente separados"
- **Si falló personajes**: "Incluye exactamente estos personajes: X, Y, Z"

Cada intento queda registrado. Cada prompt refinado queda anotado. Así el usuario (y nosotros) vemos exactamente qué pasó:
- Por qué se rechazó la versión 1
- Por qué la versión 2 fue mejor
- Por qué finalmente la 3 pasó todos los criterios

**Esto es crucial**: Las trazas no son solo para debugging. Son educación. Son transparencia.

---

## El Flujo: Visualizado de Manera Simple

```
USUARIO INICIA GENERACION
         |
         v
    FORMULARIO (Fase 1)
    - Tema: "Dragon"
    - Protagonista: "Sofia"
    - Personajes: 3 minimo
         |
         v
  GUARDAMOS EN BD
  status: "pending"
  iteration: 1
         |
         v
 LLAMAMOS OPENAI (Fase 2)
 - Enviamos prompt + contexto
 - Recibimos relato
         |
         v
 VALIDAMOS RELATO (Fase 2)
 - Contamos palabras
 - Buscamos estructura
 - Extraemos personajes
         |
    +----+----+
    |         |
    v         v
 VALIDO   INVALIDO
    |         |
    |         +---> GUARDAMOS RECHAZO EN TRACE
    |         |     "Longitud: 350 (< 450 minimo)"
    |         |
    |         +---> REINTENTAMOS (Fase 3)
    |         |     iteration: 2
    |         |     prompt_adjusted: true
    |         |
    |         +---> VOLVEMOS A VALIDAR
    |         |     (loop hasta 3 intentos)
    |         |
    |         +---> SI SIGUE INVALIDO TRAS 3 INTENTOS
    |               Retornamos error a usuario
    |               Guardamos todos los intentos
    |
    +--------> GUARDAMOS RELATO FINAL EN BD
              status: "completed"
              iterations: 2
              created_at: timestamp
              validated_at: timestamp
              trace: [log1, log2, ...]
              |
              v
        USUARIO RECIBE RESULTADO
        - Relato limpio
        - Botones: Descargar PDF, Regenerar, Editar
        - Sidebar: Ver todas las iteraciones
```

**Este flujo es el corazón del sistema. Todo lo demás son detalles.**

---

## Decisiones de Diseño Justificadas

### ¿Por qué Repository Pattern?

Aisla lógica de BD de servicios. Si mañana cambio de Prisma a una API, solo modifico repositories. Servicios no saben que existe BD.

### ¿Por qué Clerk y no JWT propio?

Auth es complejo. Clerk maneja MFA, OAuth, rate limiting por usuario, etc. Código más seguro, menos bugs.

### ¿Por qué Zustand en lugar de Redux/Context?

Redux es overkill. Context causa re-renders innecesarios. Zustand es minimalista, perfecto para este scope.

### ¿Por qué Zod en cliente y servidor?

Validar en cliente es UX. Validar en servidor es seguridad. Zod es 1 definición, ambos lugares. DRY principle.

### ¿Por qué no usar GraphQL?

REST es más simple para MVP. GraphQL agrega complejidad sin retorno en 5 endpoints.