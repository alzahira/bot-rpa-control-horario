# 🤖 RPA Bot: Gestión de Cuadrantes y Control Horario (n8n + IA)

Un flujo de trabajo automatizado (RPA) construido en **n8n** que actúa como un asistente inteligente a través de Telegram. El bot permite a los empleados enviar sus cuadrantes de turnos en formato imagen o reportar sus horas diarias en lenguaje natural, encargándose de procesar la información, aplicar las reglas de convenio laboral y registrar los datos estructurados en una base de datos.

## 🚀 Arquitectura y Tecnologías (Tech Stack)

Este proyecto emplea una arquitectura síncrona e híbrida, combinando nodos de orquestación, scripts de validación y llamadas a APIs de Inteligencia Artificial:

* **n8n:** Motor central de orquestación del flujo.
* **Google Gemini (1.5 Flash / Vision):** Extracción de datos tabulares a partir de fotografías de los cuadrantes físicos (OCR multimodal).
* **Groq (LLM):** Procesamiento de lenguaje natural para interpretar mensajes de texto y extraer los días y horas trabajadas.
* **JavaScript (ES6):** Transformación de datos (ETL), mapeo de JSON y control de excepciones mediante bloques `try-catch`.
* **Telegram API:** Interfaz de usuario (Frontend) para la entrada y salida de datos.
* **Google Sheets API:** Base de datos relacional para almacenar el horario previsto y el registro de horas reales (Operaciones de `Append` y `Upsert`).

## ⚙️ Lógica de Negocio Destacada

El flujo no se limita a transferir datos, sino que implementa reglas de negocio avanzadas directamente en la capa de código:

1.  **Cálculo Automático de Descansos:** Descuento automático de horas de descanso según la longitud de la jornada laboral (> 8h o > 6h) mediante condicionales en JavaScript.
2.  **Excepciones de Convenio (Domingos):** Lógica inyectada en el prompt del LLM mediante variables de entorno (`$now.weekday`) para computar doble las horas reportadas en domingo (como jornada normal y como horas extra).
3.  **Aplanamiento de Datos (Array Mapping):** Capacidad para recibir múltiples reportes de días en un solo mensaje de texto, transformando la respuesta de la IA en un *Array* de objetos para ejecutar bucles de inserción masiva en la base de datos.

## 🛡️ Control de Errores y Resiliencia

Para asegurar la integridad de la base de datos y la continuidad del flujo en producción, se han implementado múltiples capas de seguridad:

* **Idempotencia (Upsert):** Uso de claves primarias (Fechas) para evitar duplicidad de registros si un usuario reporta el mismo día dos veces.
* **Try-Catch Local:** Escudos de código para evitar la interrupción del flujo si los modelos de IA devuelven un JSON corrupto, texto plano o alucinaciones (Ej. imágenes que no son cuadrantes).
* **Validación de Formatos:** Nodos `Switch` configurados para rechazar archivos no soportados (Audios, PDFs, Stickers) antes de consumir cuota de API.
* **Gestión de Reintentos:** Configuración `Retry on Fail` en las peticiones HTTP/API para mitigar microcortes de red o saturaciones del servidor.

## 📁 Estructura del Repositorio

* `/workflows` -> Contiene el archivo `.json` exportable con el flujo completo de n8n.
* `/docs` -> Contiene el **PDD (Process Design Document)** con el análisis funcional, casos de uso y plan de contingencia.
* `/assets` -> Capturas de pantalla de la arquitectura en el lienzo de n8n y demos de uso en Telegram.

## 👨‍💻 Autor

**Javier Jaenes Molina**
*Desarrollador de Software y Técnico RPA*

Especializado en la creación de soluciones de automatización de procesos, desarrollo backend y diseño de arquitecturas robustas orientadas a la eficiencia operativa.
* **Stack Principal:** Java, C#, Python, JavaScript, n8n, SQL, MongoDB.