# Java Server Sent Events (SSE)

See the source code for this sample in this github repo: https://github.com/luiscoco/Java_ServerSentEvents_SSE

Server-Sent Events (SSE) is a technology that allows a server to push real-time updates to a client over a single, long-lived HTTP connection

Sever Sent Events (SSEs), a connection where a client establishes a steady and long-term connection with the server

Then, the server uses the connection to send the data. Please note, that the client can’t send data to the server and would require a separate technology if required

A client just uses regular HTTP to make requests and wait for a response

SSEs are the best options when the server generates the data in a loop and sends multiple events to the clients and if we need real-time traffic from the server to the client

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/c83f1647-1f47-4097-a3b8-d53816914328)

## 1. Create the Spring Boot application with VSCode

We create the application folder and we open it with VSCode

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/7be63248-4ac9-4f15-9a7b-6de1c22cdb02)

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/23e02c30-278a-4849-9f6c-0d2d2d108656)

We press **Ctrl+Shift+P** for invoking the SpringBoot Initializaer

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/ba10aee0-3a13-44d4-84c6-a52bd7a898a9)

We select the version

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/f086af8c-22c5-4710-9099-7e874df4ed18)

We select the language

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/debc492f-8f37-4814-a338-36c53a7fb670)

We select the Group Id

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/f13e11fc-4554-4acf-b9d5-e5c22ea60d6c)

We input the Artifact Id for your project

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/e553b68d-67c1-4cde-b371-384121e1a3d1)

We set the packing type JAR

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/9f1a0bb8-5770-487b-8f6f-f91bec0420a7)

We select the Java version 21

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/bdf2f10f-ec2d-4a90-b8fc-8153d0c18aad)

We select the project dependency **Spring Web** and we press enter

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/b5cc63ac-5668-45a4-961e-bad43d0dc740)

We select the project folder

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/d38e23a9-5df6-4b76-a868-ec6bca2d40d9)

We open the solution in another VSCode instance

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/8a3b5fa9-6b62-4465-a8a9-52bced1c5d44)

This is the default project structure

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/3b820720-a757-4c56-9cf1-73682bca9244)

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

We press the SpringBoot button in the left hand side menu

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/1b078c80-05c6-4a00-b65d-e6fa064d485a)

We press the run button to start the application

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/6ca1431b-eaf6-42ed-a206-7d5c15d71eb6)

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/9ae6f5bb-e472-4574-973f-2d11854fe64e)

We navigate to the application URL: **http://localhost:8080/sse**

![image](https://github.com/luiscoco/Java_ServerSentEvents_SSE/assets/32194879/2752044b-6343-4e3e-8a70-24ad1b446c7f)

## 4. References

What is Server-Sent Events (SSE) and how to implement it?:

https://medium.com/deliveryherotechhub/what-is-server-sent-events-sse-and-how-to-implement-it-904938bffd73

How to Use It in Java Spring Boot?:

https://medium.com/codimis/what-is-server-sent-event-sse-and-how-to-use-it-in-java-spring-boot-7f4ffa828882

Chapter 17. Server-Sent Events (SSE) Support:

https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/sse.html
