# REQUIREMENTS.md
Requisitos del MVP — SAGI

Este prototipo demuestra el **flujo central** de generación automatizada con validación y reintentos.

---

## Funcionalidades obligatorias del MVP

### Entrada de datos
- Parámetros definidos por el usuario:
  - Tema / tono
  - Protagonista(s)
  - Personajes mínimos (≥3)
  - Longitud objetivo (~500 palabras)
- Entrada mediante **consola** (sin UI aún)

### Generación de historia
- Llamada a **OpenAI API**
- Relato con estructura básica y ~500 palabras

### Validación automática
El sistema verifica:
1. **Longitud**: entre 450 y 550 palabras  
2. **Estructura**: se detectan **tres actos** (por párrafos)  
3. **Personajes**: ≥3 nombres propios

Resultado:
- Aceptado → se entrega al usuario
- Rechazado → lista clara de motivos

### Reintentos con trazabilidad
- Máximo **3 iteraciones**
- Cada intento registra:
  - fecha/hora
  - prompt utilizado
  - resultado
- Ajustes automáticos del prompt según el fallo anterior

### Exportación (opcional)
- **PDF simple** descargable/local

---

## Fuera de alcance del MVP (futuro)
A implementar solo si el proyecto avanza:

- Interfaz web con panel del proceso
- Persistencia en base de datos
- Historial por usuario autenticado
- Dashboard con métricas
- Rate limiting y seguridad avanzada
- Observabilidad (Sentry, logs remotos)

> El MVP se limita a **comprobar que el proceso editorial automatizado es posible**.

---

## Requisitos técnicos del MVP

- Tiempo total de generación <= 45s
- Validación en < 5s
- Registro de trazas en archivo local
- API keys solo en variables de entorno locales
- Código organizado en módulos con responsabilidad única

---

## Principios de arquitectura

- **Responsabilidad única**: generar ≠ validar ≠ iterar
- **Testeable**: servicios desacoplados para permitir mocks
- **Valor primero**: UI y persistencia vienen después, no antes

---

## Estado actual ✅
Todos los requisitos del MVP han sido implementados y demostrados.
