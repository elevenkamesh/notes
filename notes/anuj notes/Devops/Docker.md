## What is Docker ?
Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security lets you run many containers simultaneously on a given host.
## What can we use Docker for ?
Docker streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. Containers are great for continuous integration and continuous delivery (CI/CD) workflows.

---
## Images and Containers
- **Image:** Snapshot of an environment that contains the files and instructions needed to build a Docker container. Images are immutable, meaning they can't be modified after creation.
- **Containers:** A mutable unit of software that runs the application in an isolated environment. Containers can be modified during runtime, and changes are isolated to that container.
#### Basic Command to start docker container:
**`docker run -it <image_name>`** -> runs in interactive mode and executes the command mentioned if any in the docker file
**`docker run -it <image_name> bash`** -> runs in interactive mode with bash opened and hence not running the default command if mentioned in the docker file

---
- [[Docker CLI]]
- [[Docker Custom Images]]
- [[Docker Networking]]
- [[Docker Bind Mounts]]
- [[Docker Volumes]]
- [[Docker Compose]]