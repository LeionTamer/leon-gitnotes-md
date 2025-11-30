# Overview

The antigravity-demo-project is a full-stack project which has a separate frontend and backend using a monorepo.

# Docker

`RUN bun install --frozen-lockfile` is similar to `npm ci` and `yarn install --frozen-lockfile`

Syntax for COPY is `COPY <source1> <source2> ... <destination>` 

## Difference between RUN and CMD:

**RUN** is for BUILD
**CMD** is for RUNTIME

If you put bun install in CMD: Your container would try to install dependencies every single time it starts up. This is slow, inefficient, and bad practice.
If you put bun src/index.ts in RUN: Your build process would hang forever because it would try to start your server during the build phase, and the build would never finish.

# Node

## How to kill all node processes

`taskkill /F /IM node.exe`

**taskkill**: This is the command utility used to terminate tasks or processes.
**/F**: This flag stands for Force. It tells the system to forcefully terminate the process, which is useful if the program is stuck, unresponsive, or refuses to close normally.
**/IM**: This flag stands for Image Name. It tells the command that you are about to specify the name of the program (the "image") you want to kill.
**node.exe**: This is the actual name of the program you are targeting. Since we are running a web server using Node.js, the process name is node.exe.

