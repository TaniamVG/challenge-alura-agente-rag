# Agente Virtual RAG - Centro Médico Vitalis
Un agente de inteligencia artificial con arquitectura **RAG (Retrieval-Augmented Generation)** diseñado para responder consultas frecuentes de pacientes sobre el Centro Médico Vitalis a partir de su documento interno de políticas y servicios.
Desarrollado como parte del **Challenge Alura Agente**.
## Arquitectura de la Solución

El sistema sigue un pipeline RAG local para evitar dependencias de cuotas externas y garantizar un rendimiento estable:

1. **Carga y Procesamiento de Documentos:**
   * **Fuente:** Documento en formato PDF (`consultorio_medico.pdf`).
   * **Lector:** `PyPDFLoader` de LangChain.
   * **División de Texto:** `RecursiveCharacterTextSplitter` con `chunk_size=800` y `chunk_overlap=80`.

2. **Base Vectorial y Embeddings:**
   * **Modelo de Embeddings:** `all-MiniLM-L6-v2` (HuggingFace).
   * **Base de Datos Vectorial:** `ChromaDB` cargada localmente.

3. **Modelo de Lenguaje (LLM) y Generación:**
   * **Modelo Local:** `Qwen/Qwen2.5-0.5B-Instruct` ejecutado en GPU (PyTorch/Transformers).
   * **Técnica Anti-Alucinaciones:** Prompting estricto delimitado por contexto y parámetro `temperature=0.0`.

---

## Tecnologías Utilizadas

* **Lenguaje:** Python 3.12
* **Framework de IA:** LangChain, HuggingFace Transformers
* **Base Vectorial:** ChromaDB
* **Modelos:** Qwen 2.5 (0.5B Instruct) & MiniLM-L6-v2
* **Interfaz de Usuario:** ipywidgets
* **Entorno de Ejecución:** Google Colab con aceleración T4 GPU / Oracle Cloud Infrastructure (OCI)

---

## Instrucciones de Ejecución

1. Abre el archivo `agente_rag_vitalis.ipynb` en Google Colab o en tu entorno preferido.
2. Asegúrate de seleccionar un entorno con **GPU activada** (`Entorno de ejecución > Cambiar tipo de entorno de ejecución > T4 GPU`).
3. Ejecuta las celdas en orden secuencial:
   * **Celda 1:** Instalación de dependencias.
   * **Celda 2:** Generación del documento PDF base.
   * **Celda 3:** Inicialización de embeddings, base vectorial ChromaDB y modelo LLM.
   * **Celda 4:** Despliegue de la interfaz interactiva para realizar preguntas.

---

## Ejemplos de Funcionamiento y Evidencias

### Preguntas Válidas (Basadas en el Documento)

> **Pregunta:** *¿Cuáles son los horarios de atención los sábados?*  
> **Pregunta:** *¿Qué pasa si llego 15 minutos tarde a mi consulta?*  
> **Pregunta:** *¿Tienen servicio de ambulancia nocturna?*

![Evidencia de Prueba 1](imgs/prueba_1.png)

---

### Caso Borde y Control de Alucinaciones

Durante la etapa de pruebas, se realizó una consulta fuera del contexto médico humano:

> **Pregunta fuera de contexto:** *¿Tienen servicio de veterinaria?*

* **Comportamiento Inicial:** El modelo generó una respuesta afirmativa inventada (*alucinación*), asumiendo erróneamente que el centro médico atendía mascotas.
* **Solución Implementada:** Se ajustó la plantilla de prompt a un formato determinista de regla estricta y se configuró la temperatura a 0, asegurando que ante información no presente en el PDF, el agente responda formalmente:  
  `"No dispongo de esa información en mis documentos,."`

![Prueba de Control de Alucinaciones](imgs/alucinacion_controlada.png)
