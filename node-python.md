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

# Taller de Agentes con MCP - 

# 🟩 Node - Python 🐍 Edition

---

## Función de llamada a open meteo

* Fución para llamar a open meteo: https://open-meteo.com/en/docs

``` json
{
  ...
  "daily": {
    "time": ["2025-06-09"],
    "precipitation_probability_max": [20],
    "wind_speed_10m_max": [12.8],
    "uv_index_max": [8.20],
    "temperature_2m_min": [18.1],
    "temperature_2m_max": [35.5],
    "rain_sum": [0.00]
  }
}
```

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

``` python
mcp = FastMCP("XXXX MCP Server")

@mcp.tool()
def load_bar(foo: str) -> dict:
    """
    Descripcion de lo que hace la herramienta, params, etc
    """
    # return codigo 
```

> ![Github](images/github.png) **paso2**

---

``` python
from mcp.server.fastmcp import FastMCP
from openweather import get_weather_data

mcp = FastMCP("XXXX MCP Server")

@mcp.tool()
def load_bar(foo: str) -> dict:
    """
    Descripcion de lo que hace la herramienta, params, etc
    """
    # return codigo 

if __name__ == "__main__":
    mcp.run()

```

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
``` python
model = ChatOllama(model="qwen2.5")

server_params = StdioServerParameters(
  command="uv",
  args=["run", "python", "tool.py")],
)

async with stdio_client(server_params) as (read, write):
  async with ClientSession(read, write) as session:
      await session.initialize()
      tools = await load_mcp_tools(session)


      agent = create_react_agent(model, tools)
              
      messages = [
        {"role": "system", "content": "Eres un agente"},
        {"role": "user", "content": "¿Qué temperatura va a hacer mañana?"}
      ]

      agent_response = None
      async for agent_response in agent.astream({"messages": messages}):
        print(agent_response)
        print("-----------------")
```

---

## El tamaño del prompt

* ¿Qué pasa con el agente, no funciona?
* Si superamos los 32K tokens que admite Qwen 2.5, ¿qué hace el agente? 
```spoiler, se queda con los últimos 32k.```
* ¿Cómo podemos solucionar esto?

🛠️ **Actividad**: Vamos a hacer una poda a la respuesta. ¿Mejoran las respuestas? ¿Y el tiempo de ejecución?

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

``` python
client = MultiServerMCPClient({
  "weather": {
    "command": "uv",
    "args": ["run", "python", os.path.join(os.path.dirname(__file__), "../tools/weather/main.py")],
    "transport": "stdio",
  },
  "calendar": {
    "url": "http://127.0.0.1:8000/sse",
    "transport": "sse",
  },
})

tools = await client.get_tools()
```
🛠️ **Actividad**: Haz que tu agente use las dos herramientas en una sola consulta. 
💡 probad desde vuestro agente los MCP de otro compañero en otro lenguaje.


> ![Github](images/github.png) **paso6**


---
<!-- _class: lead -->

# ☕️ Descanso 5" ⏱️ 

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;

> volvemos a juntarnos despues del descanso

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