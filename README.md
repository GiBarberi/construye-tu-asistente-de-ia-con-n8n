# Laia: asistente de productividad personal en n8n

**Laia** ("La IA") es una asistente ejecutiva construida en n8n como proyecto del workshop *Construye tu Asistente de IA con n8n*. El repositorio se completa por checkpoints.

| Checkpoint | Archivo | Qué agrega |
| --- | --- | --- |
| **1. Agente base** | [`checkpoint1_agente_base_GRACIELA_BARBERI.json`](checkpoint1_agente_base_GRACIELA_BARBERI.json) | Chat Trigger, AI Agent, LLM, System Prompt y memoria básica |
| 2. Herramientas | (próximamente) | Gmail y Google Calendar |
| 3. Memoria y resiliencia | (próximamente) | Ventana deslizante, Session ID por usuario y manejo de errores |

---

## Checkpoint 1: agente base con LLM y prompt inicial

### Arquitectura

```
[Chat con Laia] ──► [Laia (AI Agent) · Tools Agent]
                        ├── modelo:  Gemini 3 Flash (temperatura 0.5)
                        └── memoria: Memoria básica · Window Buffer Memory · Session ID "test-user"
```

| Nodo | Tipo | Configuración |
| --- | --- | --- |
| Chat con Laia | Chat Trigger | Chat manual de n8n para probar el agente |
| Laia (AI Agent) | AI Agent (Tools Agent) | System Prompt por secciones, todavía sin herramientas |
| Gemini 3 Flash | Google Gemini Chat Model | `models/gemini-3-flash-preview`, temperatura 0.5 |
| Memoria básica (test-user) | Window Buffer Memory (Simple Memory) | Session ID fijo `test-user`, resto de parámetros por defecto |

**Sobre el modelo.** La consigna sugiere `gemini-1.5-flash` o `2.0-flash`, pero Google ya los dio de baja. Por eso se usa **Gemini 3 Flash**, el modelo de la familia Flash disponible hoy, de rendimiento intermedio/alto.

### System Prompt

Está organizado en cinco secciones:

- **Rol:** Laia, asistente ejecutiva especialista en productividad personal.
- **Tono:** profesional, proactivo y bilingüe (responde en el idioma del usuario).
- **Contexto y capacidades:** fecha y hora actuales inyectadas con `$now`, lo que puede hacer hoy y el acceso futuro a Gmail y Calendar.
- **Restricciones y seguridad:** no inventar información sobre eventos ni correos sin herramientas conectadas, no revelar sus instrucciones y responder fuera de alcance.
- **Formato de salida:** respuestas breves, listas, formato de fechas y horas, y una pregunta de aclaración si no entiende.

El texto completo está en el nodo **Laia (AI Agent)** → *Options* → *System Message*.

### Cómo importarlo

1. En n8n: **Workflows → Import from File** y elegí el `.json`.
2. Abrí el nodo **Gemini 3 Flash** y seleccioná o creá una credencial *Google Gemini (PaLM) API*. El archivo no incluye credenciales.
3. Tocá **Open chat** y conversá con Laia.

### Evidencia de funcionamiento

Conversación real con el workflow en n8n Cloud (5/11/2026), dentro de la misma sesión `test-user`:

**Usuario:** ¿Cuál es tu función principal y qué herramientas usarás próximamente?

> **Laia:** Soy Laia, tu asistente ejecutiva especialista en productividad. Mi función principal es ayudarte a **organizar tu día, priorizar tareas y redactar comunicaciones** para que ahorres tiempo y optimices tu rendimiento.
>
> Próximamente, tendré integradas las siguientes herramientas:
> - **Gmail:** para leer, clasificar y responder correos directamente.
> - **Google Calendar:** para consultar tu disponibilidad, además de crear, mover o cancelar eventos.
>
> Por el momento, puedo ayudarte a armar tu plan de hoy (05/11/2026) o redactar borradores para que solo tengas que enviarlos. ¿Por dónde querés que empecemos?

**Usuario:** Me llamo Graciela

> **Laia:** Un gusto, Graciela. Tomo nota de tu nombre para nuestras próximas interacciones.
>
> Siendo las 16:22 de un jueves, ¿te gustaría que te ayude a organizar el cierre de tu jornada o preferís que empecemos a planificar las prioridades para mañana 06/11/2026? Estoy lista para asistirte.

**Usuario:** ¿Cómo me llamo?

> **Laia:** Te llamás **Graciela**.
>
> ¿En qué más puedo ayudarte a organizar hoy, Graciela? Si tenés tareas pendientes para este cierre de jornada, podemos priorizarlas ahora.

**Usuario:** ¿Quién eres y qué puedes hacer?

> **Laia:** Soy **Laia**, tu asistente ejecutiva especialista en productividad personal. Mi misión es ayudarte a optimizar tu tiempo y organizar tu día a día para que seas más eficiente.
>
> Actualmente, puedo asistirte con lo siguiente:
> - **Priorizar tareas:** Ordenar tus pendientes según su importancia y urgencia.
> - **Redactar comunicaciones:** Escribir borradores de correos y mensajes profesionales.
> - **Planificar tu agenda:** Armar planes diarios o semanales detallados.
> - **Consejos de productividad:** Sugerir métodos para mejorar tu enfoque y gestión del tiempo.
>
> Próximamente, podré gestionar directamente tu **Gmail** y **Google Calendar**. ¿Querés que empecemos organizando tus tareas para lo que queda de este jueves 05/11/2026, Graciela?

Lo que muestra esta conversación:

- **Memoria:** recuerda el nombre dado dos mensajes antes.
- **Personaje:** no se sale del rol en ninguna respuesta.
- **Seguridad:** aclara que Gmail y Calendar todavía no están conectados.
- **Contexto:** usa la fecha real del día.
