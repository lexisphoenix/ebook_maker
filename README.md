# SAGI — Sistema Automatizado de Generación Infantil  
**Prototipo de Generador Automático de Relatos Infantiles**

## Introducción

Crear un cuento infantil breve y de calidad puede llevar entre 8 y 12 horas:  
redacción, revisión narrativa, corrección, maquetación…

**SAGI** automatiza la parte repetitiva del proceso:  
El usuario define **tema, tono y personajes mínimos**, y el sistema genera una historia que **se valida sola**.  
Si no cumple los criterios, **reintenta hasta 3 veces** con ajustes automáticos.

Este prototipo demuestra que **la generación editorial automatizada es viable** y escalable.

---

## Stack Técnico del MVP

Este MVP se ejecuta **localmente** y utiliza solo lo necesario para comprobar la idea:

| Componente | Elección | Razón |
|----------|----------|------|
| Lenguaje | TypeScript | Rápido para prototipar |
| IA | OpenAI API (GPT-4 Turbo u equivalente) | Calidad inicial del texto |
| Validación | Reglas internas simples | Control y transparencia |
| Iteraciones | Script automatizado | Asegurar criterios de calidad |
| Trazas | Consola + archivo local | Auditoría mínima |
| PDF (opcional) | pdf-lib / reportlab | Valor añadido |

> Sin interfaz gráfica, sin autenticación y sin base de datos,  
> porque en esta fase **no aportan valor al objetivo del reto**.

---

## Flujo del Proceso

```
Entrada → Generación IA → Validación → Iteración → Resultado Final
```

---

## Fase 1: Entrada

Variables definidas por usuario:
- Tema / género
- Protagonista(s)
- Número mínimo de personajes (≥3)
- Duración aproximada (500 palabras)

---

## Fase 2: Validación Automática

Se aplican estos criterios:

| Criterio | Comprobación |
|--------|--------------|
| Longitud | 500 palabras ±10% |
| Estructura | 3 actos detectables por párrafos |
| Personajes | ≥3 nombres propios |

El sistema devuelve:
- `valid: true` cuando todo cumple
- Si falla: `valid: false` + lista de errores específicos

---

## Fase 3: Iteración y Trazas

Hasta **3 intentos**:

- Ajuste de prompt según motivos de fallo
- Registro de cada paso y resultado
- Transparencia total del proceso

> El valor no es solo generar, sino **garantizar estándares narrativos mínimos**.

---

## Diagrama Simplificado del MVP

```
Input → Generar → Validar
                ↓ valido?
              Sí → Mostrar resultado / Exportar PDF*
              No → Ajustar → Reintentar (máx. 3)
                                 *PDF opcional
```

---

## Estado del Proyecto

### ✅ Completado: Diseño y Planificación

- Arquitectura definida
- Flujo de trabajo diseñado
- Criterios de validación especificados
- Documentación técnica completa

### 🔨 Pendiente: Implementación del Código

El MVP está **diseñado** pero **no implementado aún**.  
Incluirá:

- Generación con IA
- Validación automática
- Sistema de iteraciones con trazas
- Exportación a PDF (opcional)

> Este proyecto demuestra que la automatización editorial es **viable conceptualmente**.  
> El siguiente paso es implementar el código.

---

## Futuro del Proyecto (si avanza)

- Interfaz web para edición, revisión y descarga
- Base de datos con historial de iteraciones
- Generación de portadas temáticas
- Versionado y colaboración en tiempo real

> SAGI no sustituye la imaginación.  
> La acelera y la hace accesible.

---

## Próximos Pasos

Una vez implementado, el proyecto se ejecutará con:

```bash
npm install
npm start
```

o en Python:

```bash
python main.py
```

**Nota:** El código aún no está implementado. Esta sección muestra cómo será la ejecución futura.

---

**Contacto técnico:**  
idealex21@gmail.com
629 614 689
anieto-m
https://github.com/lexisphoenix/ebook_maker
