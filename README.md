# Game of Life - Developer Docs and Workspace Configuration

Open Workspace [![Open Workspace!](images/workspaces.svg)](https://codeready-openshift-workspaces.apps.cluster-11b4.11b4.sandbox1706.opentlc.com/f?url=https://github.com/pittar-gameoflife/developers)


## Table of Contents

1. [Architecture](#architecture)
2. [Cloud Development - CodeReady Workspaces](#cloud)
3. [Local Development](#local-development)

### <a href="#architecture">Architecture</a>

This application is composed of three individual services that are loosly coupled using Kafka.

**Web-UI:** This component displays the world grid. It also sends the current world state to the `supervisor` service using the `work-grids` topic.  It receives the next world state on the `display-grids` topic, then sends that result to the user's browser using *ServerSentEvents*.

**Supervisor:** The supervisor service takes world state from the `work-grids` topic, then creates a `Lifeform` object for each cell in the grid (default 60 x 40).  Each `Lifeform` will have some basic info, such as it's coordinates in the world and how many living neighbours it has.  The supervisor then sends each `Lifeform` on a `life-questions` topic.  Effetively sending 2400 messages as fast as possible.  The Supervisor also listens on the `life-answers` topic for a reply to find out if each `Lifeform` will be alive or not in the next iteration of the world.

**Decisions:** This service listens on the `life-questions` topic for `Lifeform`s.  As it receives `Lifeform`s, it applies the rules from [Conway's Game of Life]() and sends the next version of the `Lifeform` back out on the `life-answers` topic.  Since the Supervisor will be sending 2400 messages, there will be 2400 messages sent back.

This is intentionally over engineered!  The purpose is to demonstrate developing and deploying an Event Driven Architcture with some imperative components both locally and in a cloud environment in a consistent and easy to consume fasion.

### <a href="#cloud">Cloud Development</a>

Simply click the "Open Workspace" link at the top of this page to launch your personal workspace in CodeReady Workspaces. This will include:

* IDE
* All required git repositories
* A single-node Kafka instance
* Standard IDE plugins
* Commands to build and start each service.

### <a href="#local-development">Local Development</a>

Provided you have JDK11, Maven 3.6, and docker compose installed locally, you can run the application by:

* Cloning all repositories
* Starting a local single-node Kafaka instance with docker-compose.
* Start each service, making sure to specificy a different debug port for each.

