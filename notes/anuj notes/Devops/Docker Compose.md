## What is Docker Compose?
Compose is a tool for defining and running multi-container Docker applications. With compose, we use a YAML file to configure our application's services. Then, with a single command, we create and start all the services from our configuration. 

**Example:** 
Consider an application split into a frontend web application and a backend service. The frontend is configured at runtime with an HTTP configuration file managed by infrastructure, providing an external domain name, and an HTTPS server certificate injected by the platform's secured secret store. The backend stores data in a persistent volume.
Both services communicate with each other on an isolated back-tier network, while the frontend is also connected to a front-tier network and exposes port 443 for external usage.
![[Docker Compose example Infrastructure.png]]
The example application is composed of the following parts:
- 2 services, backed by Docker images: `webapp` and `database`
- 1 secret (HTTPS certificate), injected into the frontend
- 1 configuration (HTTP), injected into the frontend
- 1 persistent volume, attached to the backend
- 2 networks

**Compose File for this example (docker-compose.yml):**
```YAML
services:
  frontend:
    image: example/webapp
    ports:
      - "443:8043"
    networks:
      - front-tier
      - back-tier
    configs:
      - httpd-config
    secrets:
      - server-certificate

  backend:
    image: example/database
    volumes:
      - db-data:/etc/data
    networks:
      - back-tier

volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

configs:
  httpd-config:
    external: true

secrets:
  server-certificate:
    external: true

networks:
  # The presence of these objects is sufficient to define them
  front-tier: {}
  back-tier: {}
```

**Compose file example for local Wordpress (docker-compose.yml):**
```YAML
version: '3.1'
services:
  wordpress-website:
	image: wordpress
	restart: always
	ports:
	  - 3000:80
	environment:
	  WORDPRESS_DB_HOST: wordpress-database
	  WORDPRESS_DB_USER: anujthakur
	  WORDPRESS_DB_PASSWORD: secret
	  WORDPRESS_DB_NAME: mydb
	volumes:
	  - wordpress-data:/var/www/html
	networks:
	  - milkyway
	depends_on: # this will basically build after building mentioned services
	  - wordpress-database
	  
  wordpress-database:
	image: mysql:5.7
	# for apple silicon chips:
	platform: linux/amd64
	environment:
	  MYSQL_DATABASE: mydb
	  MYSQL_USER: anujthakur
	  MYSQL_PASSWORD: secret
	  MYSQL_RANDOM_ROOT_PASSWORD: "1"
	volumes:
	  - db:/var/lib/mysql
	networks:
	  - milkyway

  volumes:
	wordpress-data:
	db:

  networks:
    milkyway:
```

To start containers using compose file:
`docker compose up`
To stop and remove everything:
`docker compose down`