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

# 🟩 Java Edition

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
2. Llamada a usar: https://api.open-meteo.com/v1//forecast?latitude={latitude}&longitude={longitude}&hourly=temperature_2m,precipitation_probability,rain&timezone=Europe/Berlin
3. Usar latitud y longitud de donde queráis:
https://www.latlong.net/

---

## Crear servidor MCP y convertir la función en herramienta

1. Añadimos la dependencia de spring-ai
2. Convertimos la funcion en @tool
3. Creamos el bean de exposicion de @tools


🛠️ **Actividad**: Añadir una herramienta que use la función anterior

``` xml
<dependencies>
	<dependency>
		<groupId>org.springframework.ai</groupId>
		<artifactId>spring-ai-starter-mcp-server</artifactId>
	</dependency>
</dependencies>
  <dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.ai</groupId>
      <artifactId>spring-ai-bom</artifactId>
      <version>1.0.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
````

---

 En la configuracion, le decimos a Spring que no somos una aplicacion WEB y que no queremos log por consola, porque  colisionaria con la entrada/salida del server, ! ya que se comunican por stdin/stdout !

 ``` yaml
 spring:
  main:
    web-application-type: none
logging:
  pattern:
    console:
  file:
    name: /tmp/server.log
```   

---

Y ahora es donde convertimos nuestra funcion, a una @tool de MCP :

``` java

@Service
public class WeatherService {
   @Tool(description =
      "Get the temperatures (in celsius), the rain percentage and the precipitation probability for a specific location for the next 7 days, hourly")
  public WeatherPrediction getPrediction(
      @ToolParam(description = "The location latitude") double latitude,
      @ToolParam(description = "The location longitude") double longitude,
      @ToolParam(description = "The local datetime of the prediction") LocalDateTime time)
      {
        .....
      }
}        
```

Y , actualmente en Spring AI 1.0.0, debemos decirle a Spring que `registre` estas tools en su contexto, de la siguiente manera.

```java
  @Bean
  MethodToolCallbackProvider methodToolCallbackProvider(WeatherService weatherService) {
    return MethodToolCallbackProvider
    .builder()
    .toolObjects(weatherService)
    .build();
  }
  ```

---

Y ahora, ! probemos la aplicacion con mcp-inspector !


> ![Github](images/github.png) **paso2**

---

## Probar nuestra herramienta: mcp-inspector

```$ npx @modelcontextprotocol/inspector```

* Conectamos a la herramienta usando comando y argumentos
* Probamos la herramienta

🛠️ **Actividad**: Usar mcp-inspector con el MCP anterior

> Opcional: Si está Postman 11, probar lo mismo con Postman.

> ![Github](images/github.png) **paso2**

---

<!-- _class: lead -->

# ☕️ Descanso 5" ⏱️ 

---

<!-- _class: lead -->

# 3. Crear primer agente usando MCP

🛠️ **Actividad**: Creamos un agente.

Para crear un agente, necesitamos una aplicacion minima de spring-boot, con las siguientes dependencias:

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-mcp-client</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-ollamac</artifactId>
  </dependency>
</dependencies>
```

Y ahora , tenemos que decirle al Cliente que servidores MCP va a usar, y para ellos vamos a usar el formato estandar definido por Anthropic

```json
{
  "mcpServers": {
    "google-calendar": {
      "command": "java",
      "args": [
        "-jar",
        "/tmp/OpenWeather-1.0-SNAPSHOT.jar"
      ],
      "env": {}
    }
  }
}
```
---

Ahora, vamos a decirle a Spring que use esta configuracion.

```yaml
logging:
    level:
        io:
            modelcontextprotocol:
                client: DEBUG
                spec: DEBUG
    file:
        name: /tmp/client.log
    pattern:
        console:
spring:
    ai:
        ollama:
          base-url: http://localhost:11434
          chat:
              model: qwen2.5
              options:
                  seed: 123456789
                  temperature: 0.9
        mcp:
            client:
                stdio:
                    servers-configuration: "classpath:/mcp-servers-config.json"
    application:
        name: AssistantClient
    main:
        web-application-type: none
        banner-mode: log        
```

---

El ultimo paso , es instanciar un `ChatClient`, el objeto de Spring que interactua con los servidores MCP, y con el LLM.

```java
  @Bean
  ChatClient chatClient(
      ChatClient.Builder chatClientBuilder, ToolCallbackProvider tools) {
      return chatClientBuilder
        .defaultSystem(prompt)
        .defaultToolCallbacks(tools)
        .build();
      }
  ```

Ahora, ya estamos preparados para interactuar :

```java
@Autowired private ChatClient chatClient;
.
.
String comando = "¿ Que probabilidad de lluvia habrá mañana en Logroño? ";
String response = chatClient
    .prompt(comando)
    .call()
    .content();
System.out.println(response);    
```
---

## Patrones de agente básico

  * **Reasoning and Acting (ReAct)**: Respuesta inmediata a estímulos.
  * **Workflow**: Secuencias predefinidas.
  * **Planificador/Ejecutor**: Decisión separada de ejecución.
  * **Supervisor**: Monitoreo y corrección.
  * **Colaborativo**: Coordinación con otros agentes o humanos.
  * **Híbrido**: Combinación de enfoques.

  https://spring.io/blog/2025/01/21/spring-ai-agentic-patterns


> ![Github](images/github.png) **paso3**

---

<!-- _class: lead -->

# 4. Crear y usar varios MCP, diferentes protocolos y cómo consumirlos

---

## Crear una tool de calendario usando HTTP-SSE

* Hacer lo mismo pero llamando a un calendario
* Exponerla como REST en vez de STDIO

🛠️ **Actividad**: Vamos a añadir MCP SSE a una aplicacion de google calendar.

Los pasos son los mismos que el servidor STDIO, pero con la diferencia de que la dependencia a importar ya no es `spring-ai-starter-mcp-server`, sino `spring-ai-starter-mcp-server-webmvc`.

Veamoslo en accion !

---


> ![Github](images/github.png) **paso5**

---

## Usar las dos tools desde el agente


Ahora, habrémos de añadir el servidor 'remoto' a nuestro Cliente MCP.

``` yaml
spring:
    ai:
        mcp:
            client:
                sse:
                    connections:
                        calendar:
                            url: http://localhost:8080
```


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