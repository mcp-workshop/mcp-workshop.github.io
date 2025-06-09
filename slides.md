---

marp: true
theme: marping
paginate: false
title: Taller de Agentes con MCP
description: Introducción, implementación práctica y observabilidad de MCP
header: 'Tech Day June 25'
footer: 'Taller de Agentes con MCP'
--------------------------------------------------------------------------

<!-- _class: lead -->

<div class="columns">
<div>

# Taller de Agentes con MCP

</div>
<div>

![MCP Diagram](images/robot.jpg)


</div>
</div>


---

<!-- _class: portrait -->
# Taller de Agentes con MCP

## Objetivos

* Introducir el concepto de agentes usando MCP. ¿Por qué es relevante MCP hoy?
* Implementar herramientas y agentes con Java, Python y JavaScript.
* Crear comunidad en ING

<!-- sanguino -->
---

## 💻 Requisitos:

- Portátil personal con 16 GB de RAM o más (mejor si tiene GPU)

<div class="columns">
<div>

Con ollama:

- Ollama instalado: https://ollama.com/download
- Cargar y probar el modelo Qwen 2.5: ```$ ollama run qwen2.5```

</div>
<div>

Sin ollama:

- Conectar con Antropic, porporcionamos el api key

</div>
</div>


- Tener un IDE configurado para ejecutar proyectos Java 21, Node.js o Python
- Instalar Node.js, incluidos los que hagáis el taller en Java y Python, se necesita para mcpinspector: ```https://nodejs.org/es/download```
- Postman 11 (opcional) ```https://www.postman.com/downloads/```

<!-- quitamos -->

---

## 🗓️ Agenda

1. Introducción
2. Crear primera servidor MCP
☕️ Descanso 5" ⏱️ 
3. Crear primer agente usando MCP
4. Crear y usar varios MCP, diferentes protocolos y cómo consumirlos
5. El futuro de MCP
☕️ Descanso 5" ⏱️ 
6. Route to production!
7. Preguntas y Cierre ❓
Apendices, Recursos y Tips

<!-- sanguino -->
---

<!-- _class: lead -->

# 1. Introducción

---

## ¿Qué es MCP?


<div class="columns">
<div>

![MCP Diagram](images/mcp.drawio.png)

</div>
<div>

* **Model Context Protocol**

* Define cómo se comunican los agentes con modelos, recursos y herramientas.
* Inspirado en arquitecturas de cliente-servidor , microservicios y flujos de agentes.
* Modular, extensible y agnóstico del lenguaje.
* Basado en JSON-RPC

</div>
</div>

<!-- josete -->

---

## ¿Por qué MCP?

* Modularidad y separación de responsabilidades
* Permite la reutilización y escalado
* Facilita el testing, evolución, depuración, trazabilidad y despliegue
* Habilita la interoperabilidad entre diferentes tecnologías
* Standard de facto

¿Quieres que tu agente en Python hable con una herramienta en Node? Con MCP es directo.

<!-- sanguino -->
---

## Arquitectura MCP


<div class="columns">
<div>


### Transportes:

* STDIO: “Ideal para scripts y herramientas locales.”
* SSE: “Para conexiones HTTP persistentes”
* Streamable HTTP: “El más moderno, permite respuestas en tiempo real y escalabilidad.”


</div>
<div>

### Primitivas:

* @tools
* @prompts
* @resources
* @sampling
* @roots

</div>
</div>
 
 ℹ️ https://modelcontextprotocol.io/docs/concepts/architecture

---

<!-- _class: lead -->

<div class="columns">
<div>

# Show me the code!

</div>
<div>

![MCP Diagram](images/showmethecode.png)

</div>
</div>

---

## Clonamos el taller

* Java: Spring AI
* Node.js: Langchain/graph + mcp sdk
* Python: Langchain/graph + mcp sdk

🛠️ **Actividad**: Clonar proyecto base y ejecutar un ejemplo simple en cada lenguaje

  * Java: https://github.com/mcp-workshop/java-xxxxxx
  * Node: https://github.com/mcp-workshop/taller-agentes-mcp-nodejs
  * Python: https://github.com/mcp-workshop/taller-agentes-mcp-python

<!-- xxxxx -->

---
<!-- _class: lead -->

# 2. Primer servidor MCP

ENLACES A LA VERSION JAVA Y NODE

> nos separamos en equipos



---

<!-- _class: lead -->

# Pasar a Java o Node - Python

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

<!-- sanguino -->

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

<!-- sanguino -->


--- 

## Prompts cuidados y herramientas afinadas

- **Prompt Engineering:** Es fundamental diseñar un buen prompt: como hemos visto, aunque tengamos los mejores datos, si el prompt es malo, los resultados serán pobres o irrelevantes. El prompt es la clave para obtener respuestas útiles y precisas.

- **Preparar las Respuestas de las Herramientas:** No basta con devolver todo el contenido "a lo bruto" y dejar que el agente lo gestione. Es mucho mejor limpiar, preparar y formatear las respuestas desde la propia herramienta. Todo lo que se pueda hacer con código para facilitar el trabajo al agente, ¡hazlo! Así se obtienen agentes más eficientes y resultados más útiles.

<!-- josete -->

---

## Pruebas y Actualizaciones

- **Deterministas:** Pruebas de regresión localizadas, validación más sencilla.
- **Reactivos:** Pruebas de regresión complejas, validación exhaustiva de nuevos comportamientos.
- **Actualizaciones:** Estrategias para minimizar impactos y asegurar funcionalidad.

<!-- sanguino -->

---

## Seguridad y Guardarraíles

- **Autenticación y Autorización:** Implementar medidas de seguridad en ejecución de herramientas
- **Guardarraíles:** Validación de entradas y salidas. Establecer límites, evitar acciones no deseadas, ética y enfoque de marca. Control de errores y respuestas maliciosas
- **Enfoque Reactivo:** Mayor atención a la seguridad dinámica y control de acceso en tiempo real.
- **Enfoque Workflow:** Mayor énfasis en la seguridad predefinida y validación de flujos de trabajo.

<!-- josete -->

---

## El tamaño del prompt

* ¿Qué pasa con el agente, no funciona?
* Si superamos los 32K tokens que admite Qwen 2.5, ¿qué hace el agente? 
```spoiler, se queda con los últimos 32k.```
* ¿Cómo podemos solucionar esto?

🛠️ **Actividad**: Vamos a hacer una poda a la respuesta. ¿Mejoran las respuestas? ¿Y el tiempo de ejecución?

> ![Github](images/github.png) **paso4**

<!-- sanguino -->

---

## Observabilidad con Langfuse

* Trazas, logs, métricas y analítica de ejecución de agentes
* Visualización y debug / troubleshooting en tiempo real

📟️ **Demo**:

1. Desplegar Langfuse con Docker
2. Instrumentar agente con Langfuse
3. Ver ejecución desde el dashboard

<!-- sanguino -->

---

## Buenas prácticas

✅ Separar planificación de ejecución suele ayudar, independientemente del patrón usado  
✅ Pequeños agentes especializados, como en todo  
✅ Pruebas e2e para validar, de componente para desarrollar, usa herramientas como mcp-inspector  
✅ Observabilidad desde el inicio, usa herramientas como Langfuse

<!-- josete -->

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
  * [Awesome MCP Servers](https://mcpservers.org/) ⚠️
  * [MCP.so](https://mcp.so/) ⚠️

<!-- josete -->

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
