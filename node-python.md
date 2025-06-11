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

## Función de llamada a Open Meteo

* Función para llamar a Open Meteo: https://open-meteo.com/en/docs

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

## Función de llamada a Open Meteo

1. En Open Meteo puedes elegir qué datos quieres:
https://open-meteo.com/en/docs
2. Añadir los datos que querais y formar la url
3. Usar latitud y longitud de donde queráis:
https://www.latlong.net/

---

## 🐍 Crear servidor MCP y convertir la función en herramienta

* Pueden ser funciones locales o llamadas externas, síncronas o asíncronas.
* Cualquier función puede ser una tool.

🛠️ **Actividad**: Añadir una herramienta que use la función anterior

``` python
mcp = FastMCP("XXXX MCP Server")

@mcp.tool()
def load_bar(foo: str) -> dict:
    """
    Descripción de lo que hace la herramienta, parámetros, etc.
    """
    # return código 
```

> ![Github](images/github.png) **paso2**

---

## 🐍 Código completo

``` python
from mcp.server.fastmcp import FastMCP
from tool import get_xxxx

mcp = FastMCP("XXXX MCP Server")

@mcp.tool()
def load_bar(foo: str) -> dict:
    """
    Descripción de lo que hace la herramienta, parámetros, etc.
    """
    # return código 

if __name__ == "__main__":
    mcp.run()

```

---

## 🟩 Crear servidor MCP y convertir la función en herramienta

* Pueden ser funciones locales o llamadas externas, síncronas o asíncronas.
* Cualquier función puede ser una tool.

🛠️ **Actividad**: Añadir una herramienta que use la función anterior

``` js
const server = new McpServer({ name: "XXXX MCP Server", version: "1.0.0"});

server.tool("load_bar",
  { foo: z.string().default("default") },
  async ({ foo }) => {
    // return codigo
  }
);

```

> ![Github](images/github.png) **paso2**

---

## 🟩 Código completo

``` js
import { getXXXX } from './tool.js';
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({ name: "XXXX MCP Server", version: "1.0.0"});

server.tool("load_bar",
  { foo: z.string().default("default") },
  async ({ foo }) => {
    // return codigo
  }
);
await server.connect(new StdioServerTransport());

```

---



## Probar nuestra herramienta: mcp-inspector

```$ npx @modelcontextprotocol/inspector```

* Conectamos a la herramienta usando comando y argumentos
* Probamos la herramienta

🛠️ **Actividad**: Usar mcp-inspector con el MCP anterior

> Opcional: se puede usar Postman 11

> ![Github](images/github.png) **paso2**

---

<!-- _class: lead -->

# ☕️ Descanso 5" ⏱️ 

---

<!-- _class: lead -->

# 3. Crear primer agente usando MCP

---

## Patrones de agente básico

  * **Reasoning and Acting (ReAct)**: Respuesta inmediata a estímulos.
  * **Workflow**: Secuencias predefinidas.
  * **Planificador/Ejecutor**: Decisión separada de ejecución.
  * **Supervisor**: Monitoreo y corrección.
  * **Colaborativo**: Coordinación con otros agentes o humanos.
  * **Híbrido**: Combinación de enfoques.

  https://spring.io/blog/2025/01/21/spring-ai-agentic-patterns

🛠️ **Actividad**: Creamos un agente React, que es el más sencillo de desarrollar, y que llame a la herramienta anterior.

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
🐍

---

``` js
const model = new ChatOllama({ model: "qwen2.5" });

const client = new MultiServerMCPClient({
  mcpServers: {
    weather: {
      transport: "stdio",
      command: "node",
      args: [path.join(__dirname, "../tools/weather/index.js")],
    },
  },
});
const tools = await client.getTools();

const agent = createReactAgent({llm: model, tools});


const messages = [
  {"role": "system", "content": "Eres un agente"},
  {"role": "user", "content": "¿Qué temperatura va a hacer mañana?"}
];

const stream = await agent.stream({ messages });
let agent_response
for await (agent_response of stream) {
  console.log(agent_response);
}
await client.close();
```
🟩

---

## Inferencia en local vs inferencia por api

🐍 Cambiar de modelo es muy sencillo:
```python
model = ChatOllama(model="qwen2.5")
model = ChatGoogleGenerativeAI(model="gemini-2.5-flash")
model = ChatAnthropic(model="claude-3-5-haiku-latest")
```
&nbsp;

🟩 Cambiar de modelo es muy sencillo:
```js
const model = new ChatOllama({ model: "qwen2.5" });
const model = new ChatGoogleGenerativeAI({ model: "gemini-2.5-flash" });
const model = new ChatAnthropic({ model: "claude-3-5-haiku-latest" });
```
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

## 🐍 Usar las dos tools desde el agente

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

<!-- _class: peque -->

## 🟩 Usar las dos tools desde el agente

``` js
const client = new MultiServerMCPClient({
  mcpServers: {
    weather: {
      transport: "stdio",
      command: "node",
      args: [path.join(__dirname, "../tools/weather/index.js")],                              
    },
    calendar: {
      url: "http://127.0.0.1:8000/sse",
      transport: "sse",
    },
  },
});
const tools = await client.getTools();
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


> volvemos a juntarnos despues del descanso
