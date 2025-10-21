# Guía de Importación y Configuración del Agente en Flowise

Esta guía detalla los pasos para importar, configurar y utilizar el flujo del agente de IA en Flowise.

### Paso 1: Importar el Flujo

1.  **Abre Flowise**: Inicia sesión en tu instancia de Flowise.
2.  **Ve a "Chatflows"**: Haz clic en la sección de "Chatflows" en la barra de navegación.
3.  **Añadir Nuevo Flujo**: Haz clic en el botón `Add New`.
4.  **Importar Flujo**: Se abrirá un menú. Selecciona la opción `Import Chatflow` y elige el archivo `flowise_agent_export.json` que has descargado.
5.  **Confirma la importación**: El lienzo de Flowise se cargará con el diagrama del agente pre-construido.

### Paso 2: Configurar Credenciales y Secretos

Necesitarás configurar las claves API para que el agente funcione.

1.  **Crea las Credenciales**:
    *   En el menú de la izquierda, ve a `Credentials`.
    *   Haz clic en `Create Credential`.
    *   **Para OpenAI**:
        *   **Credential Name**: `OpenAI API Key`
        *   **Credential Type**: `OpenAI API Key`
        *   **OpenAI Api Key**: Pega tu clave de OpenAI aquí (ej. `sk-...`).
    *   **Para SerpAPI**:
        *   **Credential Name**: `SerpAPI Key`
        *   **Credential Type**: `SerpAPI Api Key`
        *   **SerpAPI Api Key**: Pega tu clave de SerpAPI aquí.

2.  **Asigna las Credenciales en los Nodos**:
    *   **Nodo `ChatOpenAI`**: Haz clic en este nodo. En el campo `OpenAI API Key`, selecciona la credencial `OpenAI API Key` que acabas de crear.
    *   **Nodo `SerpAPI`**: Haz clic en este nodo. En el campo `SerpAPI Key`, selecciona la credencial `SerpAPI Key`.
    *   **Nodo `OpenAI Embeddings`** (parte del RAG): Asegúrate de que este nodo también esté configurado para usar tu credencial de `OpenAI API Key`.

### Paso 3: Configurar el RAG (Document Search)

Esta es la parte más importante para que tu agente pueda usar documentos propios.

1.  **Añade el Sub-flujo RAG**:
    *   Haz clic en el `+ Add New` en el lienzo.
    *   Busca y añade los siguientes nodos: `Folder with Files`, `RecursiveCharacterTextSplitter`, `OpenAI Embeddings`, `Chroma DB`, y `Retrieval QA Chain`.
2.  **Conecta el Flujo de Indexación**:
    *   Conecta `Folder with Files` -> `RecursiveCharacterTextSplitter` -> `OpenAI Embeddings` -> `Chroma DB`.
    *   En el nodo `Chroma DB`, configúralo en modo **Upsert** y asígnale un nombre de colección (ej. `proyectos_ia_docs`).
3.  **Configura la Carpeta de Documentos**:
    *   En el nodo `Folder with Files`, especifica la ruta a una carpeta en tu máquina donde subirás los archivos PDF, TXT y MD.
4.  **Conecta el Flujo de Recuperación**:
    *   Añade un segundo nodo `Chroma DB` y configúralo en modo **Load**. Usa el mismo nombre de colección (`proyectos_ia_docs`).
    *   Conecta este nodo `Chroma DB` (Load) al `Retrieval QA Chain`.
    *   Conecta un nodo `ChatOpenAI` al `Retrieval QA Chain`.
5.  **Conecta el RAG al Agente Principal**:
    *   Finalmente, conecta la salida del `Retrieval QA Chain` a la entrada del nodo `Tool` llamado `BuscadorDeDocumentos`.

### Paso 4: Cargar y Reindexar Documentos

1.  **Carga tus Archivos**: Añade los archivos PDF, TXT o MD a la carpeta que especificaste en el nodo `Folder with Files`.
2.  **Inicia la Indexación (Upsert)**:
    *   Guarda el Chatflow.
    *   En la esquina superior derecha, verás un botón con un icono de "engranaje" o similar para acciones adicionales. Haz clic en él.
    *   Ejecuta la acción `Upsert` o `Load` en el nodo `Chroma DB` (Upsert). Flowise procesará los archivos y los guardará en la base de datos vectorial.
3.  **Reindexar**: Para añadir o actualizar documentos, simplemente repite los pasos 1 y 2. Flowise actualizará la base de datos vectorial con los nuevos contenidos.

### Paso 5: Configurar la Interfaz de Chat (UI)

1.  **Guarda el Chatflow**: Asegúrate de que todos los cambios estén guardados.
2.  **Abre el Chat**: Haz clic en el botón de "burbuja de chat" en la esquina superior derecha.
3.  **Configura la UI**:
    *   En la pestaña de configuración del chat, puedes establecer:
        *   **Chatflow Title**: `Asistente de Proyectos de IA`
        *   **Description**: `Este asistente puede responder preguntas sobre la gestión de proyectos de IA usando documentos internos y búsqueda web.`
        *   **Example Prompts (Hints)**:
            *   `¿Cuál es el primer paso para definir un proyecto de IA?`
            *   `Resume el paper sobre "Attention is All You Need".`
            *   `¿Quién es el CEO actual de Nvidia?`

### Paso 6: Usar el Agente

¡Ya está todo listo! Usa la interfaz de chat para interactuar con tu agente. Puedes hacer preguntas que requieran búsqueda en los documentos, búsqueda web o memoria de la conversación. Observa los logs en Flowise para ver qué herramientas (`BuscadorDeDocumentos` o `BuscadorWeb`) está utilizando el agente para cada pregunta.
