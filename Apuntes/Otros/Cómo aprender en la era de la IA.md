#Learning
**1. Invierte el orden: teoría antes que código, en bloques pequeños** Antes de implementar cada componente (parsing de intents, memoria, tool calling, el bucle de razonamiento...), dedica 30-60 min a entender el concepto en abstracto: ¿Qué problema resuelve?, ¿qué alternativas existen?, ¿por qué esta y no otra? Solo entonces codificas.

**2. Deja de pedirle a la IA que resuelva — pídele que explique** En vez de "hazme un agente con Ollama que use tools", pregunta "¿cómo funciona el tool calling internamente en un LLM? ¿qué pasa exactamente entre el prompt y la respuesta?". Usa la IA como tutor socrático, no como autor.

**3. Reimplementa desde cero lo que ya "funciona"** Coge una función que la IA te generó y que usas sin entenderla del todo, ciérrala, y reescríbela tú solo mirando solo la documentación oficial (Ollama, Qwen). Si no puedes, ahí está el hueco de conocimiento real.

**4. Un "cuaderno de por qué"** Cada decisión de arquitectura (Python puro vs LangGraph, Qwen3 8B, etc.) anótala con el razonamiento, no solo la decisión. Te obliga a articular el porqué, que es donde realmente se fija el concepto.

**5. Lee papers/fuentes primarias, no solo blogs** Para un agente local con tool calling y memoria, cosas como el paper de React, la documentación de function calling, o cómo funciona el context window de Qwen3 te dan fundamentos que un tutorial no da.

**6. Código sin autocompletado asistido, al menos en las partes core** Deja el copiloto para boilerplate, pero el bucle del agente, el manejo de contexto y las decisiones de diseño, escríbelos tú sin sugerencias, aunque tardes más.
