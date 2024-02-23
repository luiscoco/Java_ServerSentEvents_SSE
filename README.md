# Java Server Sent Events (SSE)

Server-Sent Events (SSE) is a technology that allows a server to push real-time updates to a client over a single, long-lived HTTP connection

Sever Sent Events (SSEs), a connection where a client establishes a steady and long-term connection with the server

Then, the server uses the connection to send the data. Please note, that the client can’t send data to the server and would require a separate technology if required

A client just uses regular HTTP to make requests and wait for a response

SSEs are the best options when the server generates the data in a loop and sends multiple events to the clients and if we need real-time traffic from the server to the client



