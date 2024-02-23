# Java Server Sent Events (SSE)

Server-Sent Events (SSE) is a technology that allows a server to push real-time updates to a client over a single, long-lived HTTP connection

Sever Sent Events (SSEs), a connection where a client establishes a steady and long-term connection with the server

Then, the server uses the connection to send the data. Please note, that the client can’t send data to the server and would require a separate technology if required

A client just uses regular HTTP to make requests and wait for a response

SSEs are the best options when the server generates the data in a loop and sends multiple events to the clients and if we need real-time traffic from the server to the client

## 1. Create the Spring Boot application with VSCode


## 2. Input the code for the Server application

This is the projec folder and files structure

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/63573345-e66a-454c-afcf-27dee28e4512)

We firt should input the application main code

**SsedemoApplication.java**

```java
package com.example.ssedemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SsedemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SsedemoApplication.class, args);
	}

}
```

We also have to create the application controller

**SseController.java**

```java
package com.example.ssedemo;

import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.mvc.method.annotation.SseEmitter;

import java.io.IOException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

@RestController
public class SseController {

    private final ExecutorService executor = Executors.newSingleThreadExecutor();

    @GetMapping("/sse")
    public SseEmitter handleSse() {
        final SseEmitter emitter = new SseEmitter();
        executor.execute(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    Thread.sleep(1000); // simulate delay
                    emitter.send(SseEmitter.event().data("message " + i, MediaType.TEXT_PLAIN));
                }
                emitter.complete();
            } catch (IOException | InterruptedException e) {
                emitter.completeWithError(e);
            }
        });

        return emitter;
    }
}
```

We load the dependencies and libraries in the pom.xml file, in this case we only load **Spring Web** dependency

```xml 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.2.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>ssedemo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>ssedemo</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>21</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

## 3. Input the code for the Client application

We create the client HTML file inside this path: /src/main/resources/static/sse.test.html

```html
<!DOCTYPE html>
<html>

<head>
    <title>SSE Test</title>
</head>

<body>
    <h1>Server-Sent Events Test</h1>
    <div id="messages"></div>
    <script>
        // Note: If your SSE endpoint is "/sse", you don't need to specify the full URL here,
        // just use the path since this HTML is served by the same Spring Boot application.
        var eventSource = new EventSource("/sse");
        eventSource.onmessage = function (event) {
            var messages = document.getElementById("messages");
            var message = document.createElement("p");
            message.textContent = "Message: " + event.data;
            messages.appendChild(message);
        };
    </script>
</body>

</html>
```

## 4. Run and Test the application



