## Docker-Compose

### Template with Description


```
version: '3.8'  # Specify the version of the Docker Compose file syntax

# Define services (containers) for your application

services:
  # Service 1
  service1:
    image: <image_name>  # Specify the Docker image to use for this service
    container_name: <container_name>  # Optionally, specify a custom container name
    ports:
      - "<host_port>:<container_port>"  # Map host port to container port (optional)
    volumes:
      - "<host_path>:<container_path>"  # Mount volumes from host to container (optional)
    environment:
      - <key>=<value>  # Set environment variables (optional)
    depends_on:
      - <dependency_service>  # Specify services that this service depends on (optional)
    restart: <restart_policy>  # Specify restart policy for the container (optional)


  # Service 2
  service2:
    image: <image_name>
    # Other configurations...

# Define networks for communication between services (optional)
networks:
  my_network:
    driver: bridge  # Specify the network driver (optional)





# Define volumes for persisting data (optional)
volumes:
  my_volume:
    driver: local  # Specify the volume driver (optional)
    driver_opts:
      type: <volume_type>  # Specify volume type (optional)



```


### A WordPress with Database Example


```
version: '3.3'

services:
  db:
    image: mysql
    container_name: wordpress_db
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example_root_password
      MYSQL_DATABASE: example_database
      MYSQL_USER: example_user
      MYSQL_PASSWORD: example_password

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    depends_on:
      - db
    ports:
      - "8080:80"
    volumes:
      - wordpress_data:/var/www/html
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: example_database
      WORDPRESS_DB_USER: example_user
      WORDPRESS_DB_PASSWORD: example_password

volumes:
  db_data:
  wordpress_data:

```




