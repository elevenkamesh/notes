## Docker Networking
#### Available Network Drivers:
- **Bridge:** The default network driver. If you don't specify a driver, this is the type of network you are creating. Bridge networks are commonly used when your application runs in a container that needs to communicate with other containers on the same host.
  Containers on bridge network can communicate with each other, but can't be pinged from the host machine, that's the reason we need to do port mapping.
  Two types of Bridge Networks:
  - **Default:** created automatically, and newly-started containers connect to it unless otherwise specified.
  - **User-defined:** User-defined bridge networks are superior to the default `bridge` network.Containers on the default bridge network can only access each other by IP addresses, unless you use the `--link` option, which is considered legacy. On a user-defined bridge network, containers can resolve each other by name or alias. 
    
    Imagine an application with a web front-end and a database back-end. If you call your containers `web` and `db`, the web container can connect to the db container at `db`, no matter which Docker host the application stack is running on.
    
    If you run the same application stack on the default bridge network, you need to manually create links between the containers (using the legacy `--link` flag). These links need to be created in both directions, so you can see this gets complex with more than two containers which need to communicate. Alternatively, you can manipulate the `/etc/hosts` files within the containers, but this creates problems that are difficult to debug.
    - **User-defined bridges provide better isolation**
      All containers without a `--network` specified, are attached to the default bridge network. This can be a risk, as unrelated stacks/services/containers are then able to communicate. Using a user-defined network provides a scoped network in which only containers attached to that network are able to communicate.
      
    - **Containers can be attached and detached from user-defined networks on the fly**
      During a container's lifetime, you can connect or disconnect it from user-defined networks on the fly. To remove a container from the default bridge network, you need to stop the container and recreate it with different network options.
    
    Creating a **user-defined** `bridge`:
    1. `docker network create <network_name>`
    2. `docker network ls`: this will show the newly created network too
    3. `docker run -it --network <network_name> -rm --name thor ubuntu`
    
    To connect a container to user-defined `bridge` on the fly:
    - `docker network connect <network_name> <container_name>`

- **Host:** This removes the isolation between the container and docker host. Uses host's networking directly. Since the container uses host's networking, there's no separate IP generated for the container and hence there's no need of port mapping. 
  When to use:
  - To optimize performance
  - When container needs to handle a large range of ports
	How to start:
	`docker run --rm -it --name host-container --net=host nginx`

- **Overlay:** Overlay networks connect multiple Docker daemons together and enable Swarm services and containers to communicate across nodes. This strategy removes the need to do OS-level routing. This creates a distributed network among docker daemons. 
  This network sits on top of (overlays) the host-specific networks, allowing containers connected to it to communicate securely when encryption is enabled. Docker transparently handles routing of each packet to and from the correct Docker daemon host and the correct destination container.
  
  We can create user-defined overlay network using `docker network create` similar to how we created user-defined bridge network.
  #### Creating an Overlay Network:
  We must ensure that participating nodes can communicate over the network. The following table lists ports that need to be open to each host participating in an overlay network:
  - To create an overlay network that containers on other Docker hosts can connect to, run the following command:
	  `docker network create -d overlay --attachable my-attachable-overlay`
	  The `--attachable` option enables both standalone containers and Swarm services to connect to the overlay network. Without this option, only Swarm services can connect to the network. The `-d` flag specifies the network driver type, if not passed then `bridge` is chosen by default.
  - Joining an overlay network:
	  `docker run --network my-attachable-overlay busybox sh`

| Ports                  | Description                                                                              |
| :--------------------- | :--------------------------------------------------------------------------------------- |
| `2377/tcp`             | The default Swarm control plane port, configurable with `docker swam join --listen-addr` |
| `4789/udp`             | The default overlay traffic port, configurable with `docker swarm init --data-path-addr` |
| `7946/tcp`, `7946/udp` | Used for communication among nodes, not configurable                                     |

- **IPvlan:** IPvlan networks give users total control over both IPv4 and IPv6 addressing. The VLAN driver builds on top of that in giving operators complete control of layer 2 VLAN tagging and even IPvlan L3 routing for users interested in underlay network integration.

- **Macvlan:** Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses. Using the `macvlan` driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host's network stack.

- **None:** Completely isolate a container from the host and other containers. `none` is not available for Swarm services.