# IMPLEMENTATION_GUIDELINES.md  
Guías de Desarrollo Futuro para SAGI

Estas guías aplican **a partir de la siguiente versión del proyecto**, cuando se añadan UI, base de datos y servicios web.  
El MVP actual **no** necesita estas capas, pero se tienen claras desde el inicio para asegurar escalabilidad.

---

## Principios Fundamentales

**Responsabilidad única:**  
Cada módulo debe tener una sola función.  
Ej: generación ≠ validación ≠ iteración ≠ exportación.

**Diseño testeable:**  
- Sin dependencias globales  
- Funciones puras donde sea posible  
- Capas desacopladas para poder mockear servicios externos

**Seguridad desde el inicio:**  
- Validación de inputs servidor-side siempre  
- API Keys fuera del repo  
- Rate limiting cuando exista API pública  
- Privacidad de contenido generados para menores

**Profundidad por fases:**  
Se desarrolla por **flujos completos**, no por “capas incompletas”:
1. Generar y validar
2. Iterar con trazas
3. Exportar
4. Añadir interfaz y persistencia

---

## Estándares de Código

- **No usar `any`** (si TS)
- Tipos explícitos en todas las funciones
- Nombres consistentes:
  - `Story`, `ValidationResult`, `TraceEntry`
- Constantes en mayúsculas:  
  `MAX_RETRIES = 3`
- Tests alineados al código:  
  `*.spec.ts` o `test_*.py`

---

## Flujo de Desarrollo Local (futuro)

Cuando el proyecto incorpore UI y backend completo:

1. Configurar `.env.local` (API Keys)
2. Instalar dependencias
3. Ejecutar scripts de setup
4. Tests antes de commit
5. Revisión y CI antes de merge

> **Nota:**  
> Esto no aplica al MVP actual, pero está definido para escalar sin deuda técnica.

---

## Manejo de Errores

- Errores tipados y con contexto
- Respuestas consistentes si hay API
- Registro de fallos (local → remoto futuro)
- Mensajes al usuario **solo cuando ayudemos a corregir**

---

## Optimización (cuando sea necesario)

Aplicable cuando el tráfico crezca:

- Token limit por solicitud
- Reuso de prompts comunes
- Carga de logs solo bajo demanda
- Métricas de generación y validación para mejoras

> Optimizar **después** de validar el negocio.

---

## Conclusión

Estas guías **no son parte del MVP**  
pero garantizan que SAGI evolucionará a una plataforma sólida, mantenible y segura.
