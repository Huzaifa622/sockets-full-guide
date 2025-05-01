# sockets-full-guide

# 🔌 Real-time User Connection Broadcast with Socket.IO

This setup demonstrates how to broadcast a message to all connected users **except the newly connected one**, using **Socket.IO** with a Node.js backend and a React frontend.

---

## 🖥️ Backend (Node.js + Express + Socket.IO)

```js
// index.js or server.js

import dotenv from "dotenv";
import { app } from "./app.js";
import { createServer } from "http";
import { Server } from "socket.io";
// import { prisma } from "./utils/db.js"; // optional

dotenv.config();

const httpServer = createServer(app);

const io = new Server(httpServer, {
  cors: {
    origin: "http://localhost:3000", // Frontend origin
  },
});

httpServer.listen(process.env.PORT, () => {
  console.log(`Server is running at http://localhost:${process.env.PORT}`);
});

io.on("connection", (socket) => {
  // Broadcast to all other clients when a user connects
  socket.broadcast.emit("user-connect");

  console.log("user connected");

  socket.on("disconnect", () => {
    console.log("user disconnected"); // Disconnect message
  });
});

```

## 🖥️ Front End (React Socket.IO)

```js
// src/socket.ts
import { io } from "socket.io-client";

const URL = "http://localhost:4000"; // Your backend URL

export const socket = io(URL);



// In a React component (e.g., App.tsx or useEffect hook)

import { useEffect } from "react";
import { socket } from "./socket";

const App = () => {
  useEffect(() => {
    if (socket) {
      socket.on("user-connect", () => {
        console.log("a msg: user connected");
      });
    }

    // Clean up listener on unmount
    return () => {
      socket.off("user-connect");
    };
  }, []);

  return <div>Socket.IO Demo</div>;
};

export default App;

