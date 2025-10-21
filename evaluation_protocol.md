# Protocolo de Evaluación del Agente

A continuación se presentan 5 casos de prueba diseñados para verificar que cada componente clave del agente funciona como se espera.

| # | Componente a Evaluar | Input del Usuario | Patrón Esperado en la Respuesta |
|---|---|---|---|
| 1 | **RAG (BuscadorDeDocumentos)** | `"¿Cuál es la metodología recomendada en el documento para la gestión de riesgos en proyectos de IA?"` | La respuesta debe basarse directamente en el contenido de los documentos cargados y citar la fuente. Debe mencionar términos específicos de la metodología descrita en los archivos. |
| 2 | **Tool (BuscadorWeb)** | `"¿Cuál fue el anuncio más importante de OpenAI en la última semana?"` | La respuesta debe contener información muy reciente que no podría estar en los documentos. Debería mencionar eventos o lanzamientos actuales, indicando que se usó la búsqueda web. |
| 3 | **Control de Flujo / Prompt** | `"¿De qué color era el caballo blanco de Santiago?"` o una pregunta sin sentido sobre el dominio. | La respuesta debe ser una negativa educada, como `"No lo sé"`, `"No tengo esa información"` o `"Mis herramientas no pueden responder a esa pregunta"`, demostrando que no inventa datos. |
| 4 | **Memoria (Conversation)** | 1. Usuario: `"¿Quién es el autor principal del paper 'Attention Is All You Need'?"` <br> 2. Agente: (Responde correctamente: Ashish Vaswani, et al.) <br> 3. Usuario: `"¿Podrías resumir su contribución principal?"` | La segunda respuesta del agente debe entender que "su" se refiere a los autores del paper mencionado en el turno anterior y proceder a resumir el concepto del Transformer sin necesidad de que el usuario repita el nombre del paper. |
| 5 | **Coordinación de Herramientas** | `"Compara los conceptos de 'machine learning' según los documentos internos con la definición actual que encuentres en la web."` | La respuesta debe ser una síntesis que combine información de ambas fuentes. Debería indicar qué información proviene de los documentos (`BuscadorDeDocumentos`) y cuál de la búsqueda web (`BuscadorWeb`), mostrando una integración de ambos. |
