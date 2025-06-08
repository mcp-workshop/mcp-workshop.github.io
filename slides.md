---

marp: true
theme: marping
paginate: false
title: Taller de Agentes con MCP
description: Introducción, implementación práctica y observabilidad de MCP
header: 'Tech Day June 25'
footer: 'Taller de Agentes con MCP'
--------------------------------------------------------------------------
<!-- _class: portrait -->
# Taller de Agentes con MCP

## Objetivos

* Introducir el concepto de MCP.
* Implementar herramientas y agentes con Java, Python y JavaScript.
* Aprender y aplicar buenas prácticas.

---

## 💻 Requisitos:

- Portátil personal con 16 GB de RAM o más (mejor si tiene GPU)
- Ollama instalado: https://ollama.com/download
- Cargar y probar el modelo Qwen 2.5:  
  ```$ ollama run qwen2.5```
- Tener un IDE configurado para ejecutar proyectos Java, Node.js o Python (según tu elección)
- Instalar Node.js, incluidos los que hagáis el taller en Java y Python, se necesita para mcpinspector:  
  ```https://nodejs.org/es/download```

---

## 🗓️ Agenda

1. Introducción
2. Crear primera herramienta MCP
☕️ Descanso 5" ⏱️ 
3. Crear primer agente usando MCP
4. Crear y usar varios MCP, diferentes protocolos y cómo consumirlos
5. El futuro de MCP
☕️ Descanso 5" ⏱️ 
6. Route to production!
7. Preguntas y Cierre ❓
Apendices, Recursos y Tips

---

<!-- _class: lead -->

# 1. Introducción

---

## ¿Qué es MCP?

* **Model Context Protocol**
* Define cómo se comunican los agentes con modelos, recursos y herramientas.
* Inspirado en arquitecturas de microservicios y flujos de agentes LLM.
* Modular, extensible y agnóstico del lenguaje.

---

## ¿Por qué MCP?

* Modularidad y separación de responsabilidades
* Protocolo unificado para prompts, recursos y herramientas
* Facilita depuración, trazabilidad y despliegue
* Habilita la interoperabilidad entre diferentes tecnologías

---

## Implementaciones MCP

* Java: Spring Boot
* Node.js: Express + mcp-server
* Python: FastAPI + mcp-lib

🛠️ **Actividad**: Clonar proyecto base y ejecutar un ejemplo simple en cada lenguaje

> ![Github](github.png) **paso0**

---

## Introducción a MCP

* MCP soporta distintos mecanismos de transporte:

  * `STDIO`: para herramientas CLI
  * `SSE`: para streaming HTTP
  * `Streamable HTTP`: interacción continua

---
<!-- _class: lead -->

# 2. Crear primera herramienta MCP

---

## Función de llamada a AEMET

* Código ejecutable que el agente puede usar
* Pueden ser funciones locales o llamadas externas

🧪 **Demo**: Añadir una función que le pases el código AEMET y devuelva la respuesta de AEMET cruda

> ![Github](github.png) **paso1**

--- 

## Función de llamada a AEMET

1. Pedir apikey aquí: https://opendata.aemet.es/centrodedescargas/altaUsuario
2. Llamada a usar /api/prediccion/especifica/municipio/diaria/{municipio}?api_key={apikeyaemet}
3. Municipio de ejemplo: Las Rozas: 28127 o buscar uno en https://www.ine.es/daco/daco42/codmun/diccionario24.xlsx
4. Respuesta JSON con urls, llamar a la url respuesta.datos
5. Segunda llamada ya tiene los datos del tiempo de verdad
requests.get(data.get("datos"))

 Swagger: https://opendata.aemet.es/dist/index.html?#/predicciones-especificas/Predicci%C3%B3n%20por%20municipios%20diaria.%20Tiempo%20actual

--- 

## Crear servidor MCP y convertir la función en herramienta

🛠️ **Actividad**: Añadir una herramienta que use la función anterior

> ![Github](github.png) **paso2**

---

## Probar nuestra herramienta: mcp-inspector

```$ npx @modelcontextprotocol/inspector```

* Conectamos a la herramienta usando comando y argumentos
* Probamos la herramienta, usamos 28127 para probar

🛠️ **Actividad**: Instalar y usar mcp-inspector con el agente anterior

> ![Github](github.png) **paso2**

---

<!-- _class: lead -->

# ☕️ Descanso 5" ⏱️ 

---

<!-- _class: lead -->

# 3. Crear primer agente usando MCP

---

## Crear un agente básico. Patrones

  * **Reasoning and Acting (ReAct)**: Respuesta inmediata a estímulos.
  * **Workflow**: Secuencias predefinidas.
  * **Planificador/Ejecutor**: Decisión separada de ejecución.
  * **Supervisor**: Monitoreo y corrección.
  * **Colaborativo**: Coordinación con otros agentes o humanos.
  * **Híbrido**: Combinación de enfoques.

🛠️ **Actividad**: Creamos un agente react, que es el más sencillo de desarrollar, y que llame a la herramienta anterior.

> ![Github](github.png) **paso3**

---

## El tamaño del prompt

* ¿Qué pasa con el agente, no funciona?
* Si superamos los 32K tokens que admite Qwen 2.5, ¿qué hace el agente? 
```spoiler, se queda con los últimos 32k.```
* ¿Cómo podemos solucionar esto?

🛠️ **Actividad**: Vamos a hacer una poda a la respuesta de AEMET. ¿Mejoran las respuestas? ¿Y el tiempo de ejecución?

> ![Github](github.png) **paso4**

---

<!-- _class: lead -->

# 4. Crear y usar varios MCP, diferentes protocolos y cómo consumirlos

---

## Crear una tool de calendario

* Hacer lo mismo pero llamando a un calendario
* Pista: usar librería para entender CalDAV
* Exponerla como REST en vez de STDIO

🛠️ **Actividad**: Añadir una función que llame a un calendario ICS y devuelva un JSON con tus eventos

CALENDAR_URL=https://calendar.google.com/calendar/ical/0f7e8a7191ceda59262822a5fbed28f9dedae882137d0af94eddbbbdae292bd4%40group.calendar.google.com/public/basic.ics

> ![Github](github.png) **paso5**

---

## Usar las dos tools desde el agente

* Actualizamos el agente para poder hacer consultas compuestas

🛠️ **Actividad**: Haz que tu agente use las dos herramientas en una sola consulta

> ![Github](github.png) **paso6**

💡 Si os da tiempo, llamad a los MCP de otro compañero en otro lenguaje desde vuestro agente.

---

<!-- _class: lead -->

# 5. El futuro de MCP

---

## @resources

* Variables globales: credenciales, configuraciones…
* Útiles para separar lógica de entorno
* Aún no están disponibles en casi ningún framework
* LangGraph permite cargar @resources, pero hay que integrarlos manualmente en los agentes

📟️ **Demo**: Definir el listado de códigos de AEMET y que sea el agente quien busque el código de tu localidad

---

## @prompts y @roots

* Prompts reutilizables por el agente
* Diseño modular de tareas
* Define el flujo principal del agente
* Composición de herramientas, recursos y prompts
* No están disponibles en la mayoría de frameworks
* LangGraph permite cargar @prompts, no @roots

📟️ **Demo**: Para qué usamos un prompt
📟️ **Demo**: Crear un MCP que liste archivos de una carpeta

---
<!-- _class: lead -->

# ☕️ Descanso 5" ⏱️ 

---

<!-- _class: lead -->

# 6. Route to production!

---

## Route to Production

- **Objetivos Claros:** Definir el caso de uso y metas.
- **Elección del Enfoque:** Agentes reactivos vs. workflows vs ...
- **Pruebas Automatizadas:** Establecer un plan robusto de pruebas (regresión y progresión).
- **Monitoreo y Logging:** Implementar herramientas desde el inicio.
- **Escalabilidad y Seguridad:** Diseñar arquitectura escalable y segura.
- **Costes:** Estimar los costes de creación, run y mantenimiento.

---

## Comparación de Enfoques: Reactivos vs. Workflows

**Reactivos:**
- **Ventajas:** Flexibilidad, adaptación rápida.
- **Desventajas:** Complejidad en pruebas y mantenimiento.

**Workflows:**
- **Ventajas:** Control, previsibilidad, facilidad de depuración.
- **Desventajas:** Menor flexibilidad ante cambios.

&nbsp;

👮‍♂️ ¡El caso de uso manda!

--- 

## Prompts cuidados y herramientas afinadas

- **Prompt Engineering:** Es fundamental diseñar un buen prompt: como hemos visto, aunque tengamos los mejores datos, si el prompt es malo, los resultados serán pobres o irrelevantes. El prompt es la clave para obtener respuestas útiles y precisas.

- **Preparar las Respuestas de las Herramientas:** No basta con devolver todo el contenido "a lo bruto" y dejar que el agente lo gestione. Es mucho mejor limpiar, preparar y formatear las respuestas desde la propia herramienta. Todo lo que se pueda hacer con código para facilitar el trabajo al agente, ¡hazlo! Así se obtienen agentes más eficientes y resultados más útiles.

---

## Pruebas y Actualizaciones

- **Deterministas:** Pruebas de regresión localizadas, validación más sencilla.
- **Reactivos:** Pruebas de regresión complejas, validación exhaustiva de nuevos comportamientos.
- **Actualizaciones:** Estrategias para minimizar impactos y asegurar funcionalidad.

---

## Seguridad y Guardarraíles

- **Autenticación y Autorización:** Implementar medidas de seguridad en ejecución de herramientas
- **Guardarraíles:** Validación de entradas y salidas. Establecer límites, evitar acciones no deseadas, ética y enfoque de marca. Control de errores y respuestas maliciosas
- **Enfoque Reactivo:** Mayor atención a la seguridad dinámica y control de acceso en tiempo real.
- **Enfoque Workflow:** Mayor énfasis en la seguridad predefinida y validación de flujos de trabajo.

---

## Observabilidad con Langfuse

* Trazas, logs, métricas y analítica de ejecución de agentes
* Visualización y debug / troubleshooting en tiempo real

📟️ **Demo**:

1. Desplegar Langfuse con Docker
2. Instrumentar agente con Langfuse
3. Ver ejecución desde el dashboard

---

## Buenas prácticas

✅ Separar planificación de ejecución suele ayudar, independientemente del patrón usado  
✅ Pequeños agentes especializados, como en todo  
✅ Pruebas e2e para validar, de componente para desarrollar, usa herramientas como mcp-inspector  
✅ Observabilidad desde el inicio, usa herramientas como Langfuse

---

<!-- _class: lead -->

# 7. Preguntas❓ y Cierre!

---

## Recursos:

  * [modelcontextprotocol.io](https://modelcontextprotocol.io/)
  * [GitHub MCP](https://github.com/modelcontextprotocol)
  * [mcp-inspector](https://github.com/modelcontextprotocol/inspector)
  * [Langfuse](https://langfuse.com)
  * [Claude](https://claude.ai/login?returnTo=%2F%3F#features)
  * [Awesome MCP Servers](https://mcpservers.org/)

---

## Tips:

  * Si usais mcps para claude, casi todos los de [Awesome MCP Servers](https://mcpservers.org/), suelen tener config por variables de entorno, pero no usan dotenv, desde la config de langchain podeis pasar variables de entorno así:

  ``` python
      "weather": {
        "command": "uv",
        "args": ["run", "python", "weather.py"],
        "transport": "stdio",
        "env":
          "AEMET_API_KEY": "eyJhbGciOiJI.....",
      },
  ```

---

## Apéndice: listado de pasos y actividades

🛠️ **Actividad paso0**: Clonar proyecto base y ejecutar un ejemplo simple en cada lenguaje
🛠️ **Actividad paso1**: Añadir una función que le pases el código AEMET y devuelva la respuesta de AEMET cruda
🛠️ **Actividad paso2**: Añadir una herramienta que use la función anterior
🛠️ **Actividad paso3**: Creamos un agente react, que es el más sencillo de desarrollar, y que llame a la herramienta anterior.
🛠️ **Actividad paso4**: Vamos a hacer una poda a la respuesta de AEMET. ¿Mejoran las respuestas? ¿Y el tiempo de ejecución?
🛠️ **Actividad paso5**: Añadir una función que llame a un calendario ICS y devuelva un JSON con tus eventos
🛠️ **Actividad paso6**: Haz que tu agente use las dos herramientas en una sola consulta
🛠️ **Demo paso7**: Uso de LangFuse

---

## Apendice: snippets Java

---

## Apendice: snippets Nodejs

---

## Apendice: snippets Python

