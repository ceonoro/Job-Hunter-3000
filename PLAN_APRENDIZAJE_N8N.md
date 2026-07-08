# Plan de Aprendizaje n8n (5 días): De Cero a Automatizar Cualquier Proceso

Objetivo final: al terminar el día 5 podrás mirar cualquier proceso manual (tuyo, de tu trabajo o de tu búsqueda de empleo) y automatizarlo en n8n, incluyendo agentes de IA con memoria (buffers), RAG y GraphRAG.

Dedicación sugerida: 4-6 horas por día. Cada día sigue la misma estructura: **Teoría corta → Tutorial práctico → Reto propio → Checkpoint**.

## Cómo se seleccionaron los recursos

Se priorizaron, en este orden:
1. **Documentación y Academia oficial de n8n** (`docs.n8n.io`, `learn.n8n.io`) — siempre actualizada y gratuita.
2. **Cursos con mayor volumen de estudiantes y mejor calificación** en Udemy/Class Central (ej. curso con 49,000+ estudiantes; curso "Bestseller" con 4.7/5 sobre 262 calificaciones).
3. **Canales de YouTube más recomendados** para tutoriales prácticos gratuitos.
4. **Documentación y artículos técnicos específicos** para RAG y GraphRAG, validados cruzando la documentación oficial de n8n con guías de la comunidad (The AI Automators, Nodus Labs/InfraNodus).

---

## Día 1 — Fundamentos de n8n (de cero a tu primer workflow)

**Objetivos:** entender la interfaz, tipos de nodos, triggers, estructura de datos (JSON) y expresiones básicas (`{{ }}`).

**Recursos:**
- [n8n Academy – Level One](https://docs.n8n.io/courses/level-one/) (`learn.n8n.io`) — curso oficial gratuito, ~2h, mezcla teoría corta con ejercicios de "constrúyelo ahora". Es el punto de partida recomendado por la propia documentación de n8n.
- [Learning paths de n8n](https://docs.n8n.io/learning-paths) — mapa oficial de rutas de aprendizaje.
- YouTube: "Master n8n in 2 Hours: Complete Beginner's Guide for 2026" y la playlist oficial "n8n Beginner course".

**Práctica:**
1. Instala n8n (n8n Cloud trial o self-hosted con Docker: `docker run -it --rm -p 5678:5678 n8nio/n8n`).
2. Crea tu primer workflow: Webhook Trigger → nodo Set → nodo If → respuesta.
3. Explora los nodos utilitarios clave: `Set`, `If`, `Merge`, `Code`, `Schedule Trigger`.

**Checkpoint del día:** puedes crear, sin ayuda, un workflow con un trigger, una transformación de datos y una rama condicional.

---

## Día 2 — Integraciones, APIs y lógica avanzada

**Objetivos:** webhooks, autenticación de APIs (credenciales), expresiones avanzadas, manejo de errores, sub-workflows.

**Recursos:**
- [n8n Academy – Level Two](https://docs.n8n.io/courses/level-two/) — requiere n8n instalado, ~2h.
- Udemy: **"n8n - AI Agents, AI Automations & AI Voice Agents (No-code!)"** de Damian Danelczyk y Krystian Wojtarowicz — el curso con más estudiantes inscritos (49,000+), reseñas destacan que explica conceptos complejos de forma muy accesible. Ve las secciones de APIs/webhooks/credenciales.
- "Real-World Automation Course" (~6,000 estudiantes) — cubre webhooks, APIs, scraping y casos reales aplicables de inmediato.

**Práctica:**
1. Automatiza un proceso real con al menos 2 integraciones externas (ej. leer correos → clasificar → guardar en Google Sheets/Notion).
2. Agrega manejo de errores con un `Error Trigger` workflow.
3. Configura credenciales reutilizables y prueba un sub-workflow (`Execute Workflow` node).

**Checkpoint del día:** tienes un workflow productivo con 2+ servicios conectados y manejo de errores explícito.

---

## Día 3 — Agentes de IA y Memoria (Buffers)

**Objetivos:** nodo AI Agent, tools/herramientas, prompt engineering en n8n, y memoria conversacional — aquí es donde entran los **buffers**.

**Qué es el "buffer" en n8n:** el nodo **Simple Memory** (internamente `memoryBufferWindow`, "Buffer Window Memory" de LangChain) guarda el historial de conversación de un agente. Se configura con una `Session Key` y un `Context Window Length` (cuántas interacciones previas recordar). Es la memoria por defecto para casi cualquier chat agent, pero **no se debe usar en producción con "queue mode"** activado (workers distribuidos), porque no hay garantía de que la misma sesión llegue siempre al mismo worker — para eso se usan memorias externas (Postgres, Redis, MongoDB Chat Memory).

**Recursos:**
- [Simple Memory (Buffer Window) — docs oficiales](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.memorybufferwindow)
- [Chat Memory Manager — docs oficiales](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.memorymanager) (para patrones custom sobre cualquier backend de memoria)
- [Redis Chat Memory](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.memoryredischat) / [Postgres Chat Memory](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.memorypostgreschat) — para producción con workers distribuidos.
- [n8n-io/skills – MEMORY.md](https://github.com/n8n-io/skills/blob/main/plugins/n8n-skills/skills/n8n-agents/references/MEMORY.md) — referencia técnica oficial de n8n sobre memoria en agentes.
- Curso gratuito "Build & Sell n8n AI Agents" (8+ horas) para ver agentes de IA aplicados a casos reales.

**Práctica:**
1. Construye un chatbot con `AI Agent` + `Simple Memory` (Buffer Window), ajustando `Context Window Length` y observando el trade-off (más contexto = más tokens/costo, menos contexto = el bot "olvida" rápido).
2. Agrega al menos una `Tool` al agente (ej. HTTP Request Tool o Code Tool).
3. Cambia la memoria a Postgres o Redis Chat Memory y compara el comportamiento.

**Checkpoint del día:** tu chatbot mantiene contexto en una conversación de varios turnos, y puedes explicar cuándo usar memoria en buffer (memoria interna) vs. memoria externa (Redis/Postgres) en producción.

---

## Día 4 — RAG (Retrieval-Augmented Generation)

**Objetivos:** embeddings, text splitters, vector stores, y el patrón de dos workflows (ingesta + consulta).

**Recursos:**
- [RAG in n8n — docs oficiales](https://docs.n8n.io/advanced-ai/rag-in-n8n/)
- [n8n.io/rag — página oficial de producto](https://n8n.io/rag/)
- [Build a Custom Knowledge RAG Chatbot — n8n Blog](https://blog.n8n.io/rag-chatbot/)
- Plantilla oficial: ["Basic RAG chat"](https://n8n.io/workflows/5028-basic-rag-chat/)

**Arquitectura a construir (2 workflows):**
- **Ingesta:** Document Loader (Google Drive/HTTP/archivo) → Text Splitter (chunking) → Embeddings (OpenAI/Cohere/Vertex AI) → Vector Store en modo "insert" (Pinecone, Qdrant, Supabase, PGVector, o In-Memory para pruebas).
- **Consulta:** Pregunta del usuario → AI Agent o Basic LLM Chain conectado a un Vector Store Retriever (sub-nodo) → el retriever devuelve el top-k de fragmentos relevantes → el LLM genera la respuesta usando ese contexto.

**Práctica:**
1. Construye el workflow de ingesta con tus propios documentos (PDF, notas, CVs, etc.).
2. Construye el workflow de consulta y valida que las respuestas citen o reflejen el contenido real de tus documentos.
3. Compara resultados cambiando el tamaño de chunk y el `top-k` del retriever.

**Checkpoint del día:** tienes un chatbot RAG funcional que responde preguntas sobre documentos propios, con respuestas verificables (no alucinadas).

---

## Día 5 — GraphRAG + Proyecto Final

**Objetivos:** entender GraphRAG (grafos de conocimiento) vs. RAG vectorial, y aplicar todo lo aprendido en un proyecto integrador.

**Qué es GraphRAG:** en lugar de (o además de) buscar por similitud vectorial, construye un grafo de conceptos/entidades y sus relaciones; el agente "recorre" el grafo para razonar sobre relaciones entre ideas, lo cual da mejores respuestas en dominios donde el contexto relacional importa más que la similitud semántica pura.

**Recursos:**
- [Make your AI Agents 10x Smarter with GraphRAG (n8n) — The AI Automators](https://www.theaiautomators.com/make-ai-agents-10x-smarter-with-graphrag/)
- Plantilla oficial: ["Create custom reasoning patterns for AI agents with GraphRAG & knowledge ontology"](https://n8n.io/workflows/6816-create-custom-reasoning-patterns-for-ai-agents-with-graphrag-and-knowledge-ontology/)
- Plantilla oficial: ["Chat with PDF / MD / text files using GraphRAG (no vector store needed)"](https://n8n.io/workflows/4755-chat-with-pdf-md-text-files-using-graphrag-no-vector-store-needed/)
- [Build a Custom Knowledge Base Graph RAG AI Chatbot con n8n — Nodus Labs](https://support.noduslabs.com/hc/en-us/articles/20266671215388-Build-a-Custom-Knowledge-Base-Graph-RAG-AI-Chatbot-with-n8n)
- [GraphRAG con Qdrant y Neo4j — Qdrant docs](https://qdrant.tech/documentation/examples/graphrag-qdrant-neo4j/) (para una implementación más "code-heavy" si quieres profundizar).

**Práctica:**
1. Importa y ejecuta una plantilla de GraphRAG (InfraNodus o Neo4j+Qdrant) y compara sus respuestas contra tu chatbot RAG del día 4 usando las mismas preguntas.
2. **Proyecto final:** elige un proceso manual real (tuyo, de tu trabajo o de tu búsqueda de empleo — este repo se llama Job-Hunter-3000, así que un buen candidato es automatizar parte de tu proceso de búsqueda de empleo) y constrúyelo de punta a punta en n8n combinando:
   - Trigger (schedule, webhook o manual)
   - Al menos 2 integraciones externas
   - Lógica condicional y manejo de errores
   - Un AI Agent con memoria en buffer
   - RAG o GraphRAG para dar contexto propio al agente (opcional pero recomendado)

**Checkpoint final:** tienes un workflow productivo, documentado, que automatiza un proceso real de principio a fin, y puedes explicar por qué elegiste buffer memory, RAG o GraphRAG en cada parte.

---

## Metodología diaria sugerida

| Bloque | Tiempo | Actividad |
|---|---|---|
| 1 | 30 min | Ver/leer el recurso teórico del día |
| 2 | 2-3 h | Replicar el tutorial práctico paso a paso |
| 3 | 1-2 h | Modificar el ejemplo para un caso propio |
| 4 | 30 min | Documentar lo aprendido (notas propias, capturas del workflow) |

## Consejos generales

- Usa n8n self-hosted con Docker para practicar sin límites del plan gratuito de n8n Cloud.
- Únete a la comunidad oficial en `community.n8n.io` y al subreddit r/n8n para resolver dudas puntuales.
- Guarda cada workflow como plantilla exportada (JSON) para construir tu propia librería reutilizable.
