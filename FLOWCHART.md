# FLOWCHART.md
Flujo del MVP — SAGI

Este diagrama representa el flujo implementado en el MVP actual.  
Todo ocurre **localmente** y sin servicios externos adicionales a OpenAI.

```
INPUT USUARIO
(Tema, tono, personajes mínimos)
        |
        v
GENERACIÓN IA
- Llamada a OpenAI
- Primer borrador de historia
        |
        v
VALIDACIÓN
- Conteo de palabras
- Detección mínima de estructura
- ≥3 personajes
        |
   +----+----+
   |         |
   v         v
VALIDO   INVALIDO
   |         |
   |         +--> Registrar trazas (log local)
   |         +--> Ajustar prompt
   |         +--> Reintentar (máx. 3)
   |
   +--> RESULTADO FINAL
        - Mostrar en consola
        - Exportar PDF (opcional)
```

> El valor no está en generar, sino en **garantizar estándares de calidad mínimos**.

---

## Futuro del flujo (cuando el proyecto escale)

- UI web con formularios
- Persistencia en base de datos
- Historial de iteraciones visible al usuario
- Autenticación y seguridad para contenido sensible
- Exportación avanzada con portada
