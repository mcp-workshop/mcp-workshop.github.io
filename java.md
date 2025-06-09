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
# Taller de Agentes con MCP - Java Edition

---

## Función de llamada a open meteo

* Fución para llamar a open meteo: https://open-meteo.com/en/docs

> ![Github](images/github.png) **paso1**

--- 

## Función de llamada a open meteo

1. En open meteo puedes elegir que datos quieres:
https://open-meteo.com/en/docs
2. Llamada a usar https://api.open-meteo.com/v1/forecast?daily=precipitation_probability_max,wind_speed_10m_max,uv_index_max,temperature_2m_min,temperature_2m_max,rain_sum&timezone=Europe%2FBerlin&latitude={latitude}&longitude={longitud}
3. Usar latitud y longitud de donde querais
https://www.latlong.net/

---

## Crear servidor MCP y convertir la función en herramienta

* Pueden ser funciones locales o llamadas externas, sincronas o asincronas.
* Cualrquier función puede ser una tool.

🛠️ **Actividad**: Añadir una herramienta que use la función anterior

> ![Github](images/github.png) **paso2**

---

## Probar nuestra herramienta: mcp-inspector

```$ npx @modelcontextprotocol/inspector```

* Conectamos a la herramienta usando comando y argumentos
* Probamos la herramienta

🛠️ **Actividad**: Instalar y usar mcp-inspector con el mcp anterior

> Opcional: Si está postman 11, probar lo mismo con postman.

> ![Github](images/github.png) **paso2**

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

> ![Github](images/github.png) **paso3**

---

## El tamaño del prompt

* ¿Qué pasa con el agente, no funciona?
* Si superamos los 32K tokens que admite Qwen 2.5, ¿qué hace el agente? 
```spoiler, se queda con los últimos 32k.```
* ¿Cómo podemos solucionar esto?

🛠️ **Actividad**: Vamos a hacer una poda a la respuesta de AEMET. ¿Mejoran las respuestas? ¿Y el tiempo de ejecución?

> ![Github](images/github.png) **paso4**

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

> ![Github](images/github.png) **paso5**

---

## Usar las dos tools desde el agente

* Actualizamos el agente para poder hacer consultas compuestas

🛠️ **Actividad**: Haz que tu agente use las dos herramientas en una sola consulta

> ![Github](images/github.png) **paso6**

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

> volvemos a juntarnos despues del descanso

---
