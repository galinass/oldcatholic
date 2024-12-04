# Old Catholic Monitoring Project

This project aims to set up a monitoring solution for the website www.oldcatholic.bg. The solution leverages Docker containers and Ansible playbooks to provide efficient monitoring of WordPress, MySQL, memory usage, and system resources. Metrics are collected via Prometheus and visualized through Grafana. The following services and tools are used in this project:

- **Docker**: To containerize different services like WordPress, MySQL, Prometheus, Grafana, and exporters.
- **Ansible**: For automation and management of container setups, configurations, and deployments.
- **Prometheus**: To collect metrics from WordPress, MySQL, and system resources.
- **Grafana**: To visualize metrics collected by Prometheus.
- **Node Exporter, MySQL Exporter, WordPress Exporter**: For exporting metrics from the respective services.

## Requirements

- Docker and Docker Compose must be installed.
- Ansible must be installed for executing playbooks.
- SSH access must be configured for automating the tasks.

## Project Setup

1. Clone the repository:
   ```sh
   git clone git@github.com:galinass/oldcatholic.git
   cd oldcatholic
   ```

2. Create a Docker network for container communication:
   ```sh
   docker network create catholic
   ```

3. Run the containers for the different services. Examples:
   - **MySQL**:
     ```sh
     docker run --name mysql_container --network catholic -e MYSQL_ROOT_PASSWORD=galinas -e MYSQL_DATABASE=oldcatholic -e MYSQL_USER=galinas -e MYSQL_PASSWORD=galinas -d mysql:5.7
     ```
   - **WordPress**:
     ```sh
     docker run --name wordpress_container --network catholic -e WORDPRESS_DB_HOST=mysql_container:3306 -e WORDPRESS_DB_NAME=oldcatholic -e WORDPRESS_DB_USER=galinas -e WORDPRESS_DB_PASSWORD=galinas -p 8080:80 -d wordpress:latest
     ```
   - **Prometheus**:
     ```sh
     docker run --name prometheus_container --network catholic -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml -p 9090:9090 -d prom/prometheus:latest
     ```
   - **Grafana**:
     ```sh
     docker run --name grafana_container --network catholic -e GF_SECURITY_ADMIN_PASSWORD=galinas -p 3000:3000 -d grafana/grafana:latest
     ```

## Running the Ansible Playbook

Ansible is used to automate the deployment and configuration of the containers. To run the Ansible playbook for deploying the project, use the following command:

```sh
ansible-playbook playbooks/site.yml -i /ansible/inventory/host.yml
```

## Available Services

| Service       | Description                               | URL                   |
|---------------|-------------------------------------------|-----------------------|
| WordPress     | Website                                    | http://localhost:8080 |
| Prometheus    | Metrics Collection                         | http://localhost:9090 |
| Grafana       | Metrics Visualization                      | http://localhost:3000 |
| Node Exporter | System Metrics Exporter                    | Exposed on Port 9101  |
| MySQL Exporter| MySQL Metrics Exporter                     | Exposed on Port 9104  |
| WordPress Exporter | WordPress Specific Metrics Exporter   | Exposed on Port 9117  |

## Notes

- The default credentials for accessing Grafana are `admin` as the username and `galinas` as the password.
- Ensure all required ports are open for communication between services.
- To troubleshoot any issues, you can inspect logs using Docker:
  ```sh
  docker logs <container_name>
  ```

## License

This project is licensed under the MIT License. See the LICENSE file for details.
