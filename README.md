# Ollama Web App - Deploy Ollama with Open WebUI using Docker

This repository provides a **Docker setup** for deploying the **Ollama Web App** alongside **Open WebUI** for a powerful and intuitive interface. Ollama is a tool for leveraging AI models, and the Open WebUI serves as the frontend to interact with them.

This setup allows you to easily run Ollama and Open WebUI in Docker containers with persistent storage and configuration.

## Prerequisites

Before using this repository, make sure you have the following installed:

- **Docker** (latest version)
- **Docker Compose** (optional, but recommended)

## Features

- Runs **Ollama** in a container to manage AI models.
- Provides a user-friendly **Web UI** for interacting with Ollama models.
- Easy setup with environment variable support.
- Customizable through Docker volumes and environment variables.
- Automatically restarts containers on failure.

## Installation

Follow these steps to deploy Ollama Web App and Open WebUI:

### 1. Clone the repository

Clone this repository to your local machine:

```bash
git clone https://github.com/NotBeCursed/Ollama-Web-App.git
cd Ollama-Web-App
```

### 2. Configure the setup

The `docker-compose.yml` file includes both **Ollama** and **Open WebUI** services. Here are some key configurations you can modify:

- **OLLAMA_BASE_URL**: Set this to specify the base URL for Ollama. Default is `/ollama`.
- **WEBUI_SECRET_KEY**: You can specify a secret key to secure your Web UI (leave it empty for no key).

Example `docker-compose.yml` file:

```yaml
services:
  ollama:
    volumes:
      - ollama:/root/.ollama
    container_name: ollama
    pull_policy: always
    tty: true
    restart: unless-stopped
    image: ollama/ollama

  open-webui:
    build:
      context: .
      args:
        OLLAMA_BASE_URL: '/ollama'
      dockerfile: Dockerfile
    image: ghcr.io/open-webui/open-webui
    container_name: open-webui
    volumes:
      - open-webui:/app/backend/data
    depends_on:
      - ollama
    ports:
      - 11434:8080
    environment:
      - 'OLLAMA_BASE_URL=http://ollama:11434'
      - 'WEBUI_SECRET_KEY='
    extra_hosts:
      - host.docker.internal:host-gateway
    restart: unless-stopped

volumes:
  ollama: {}
  open-webui: {}
```

In this file:
- **Ollama Service** (`ollama`): This service runs the Ollama container.
- **Web UI Service** (`open-webui`): This service provides the frontend for interacting with Ollama.
- **Volumes**: Two volumes are created for persistent data storage (`ollama` and `open-webui`).
- **Ports**: The Web UI is accessible on port `11434` (you can change this if necessary).

### 3. Build and Start the Docker Containers

Once your configuration is complete, you can build and start the containers using the following command:

```bash
docker-compose up -d
```

Alternatively, if you're not using Docker Compose, you can manually run the containers with the following commands:

```bash
# Run Ollama container
docker run -d --name ollama -v ollama:/root/.ollama ollama/ollama

# Run Web UI container
docker build -t open-webui .
docker run -d --name open-webui -p 11434:8080 --link ollama --env OLLAMA_BASE_URL=http://ollama:11434 open-webui
```

### 4. Access the Web UI

Once the containers are up and running, you can access the **Open WebUI** by navigating to:

```
http://localhost:11434
```

### 5. Customizing the Setup

You can customize the setup by modifying the following:

- **Ollama Model Data**: All data and models for Ollama will be stored in the Docker volume `ollama`.
- **Web UI Data**: Configuration and data for the Web UI will be stored in the `open-webui` volume.
- You can modify the `docker-compose.yml` or Docker commands to suit your needs, such as changing ports, adding environment variables, or adjusting volume locations.

## Troubleshooting

Here are a few troubleshooting steps if you run into any issues:

1. **Check container logs**:
   If the services fail to start or behave unexpectedly, check the logs for both services:

   ```bash
   docker-compose logs ollama
   docker-compose logs open-webui
   ```

2. **Accessing Web UI**:
   If you cannot access the Web UI, ensure that there is no firewall or network issue blocking port `11434`.

3. **Permissions issues**:
   Ensure that the Docker volumes `ollama` and `open-webui` have the correct file permissions, especially if you're running Docker as a non-root user:

   ```bash
   sudo chown -R $USER:$USER ./data
   ```

4. **Restarting the containers**:
   If needed, restart the containers:

   ```bash
   docker-compose restart
   ```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Credits

This project uses **[Open WebUI](https://github.com/open-webui/open-webui)** as the web interface for interacting with **Ollama**. Thanks to the contributors of the **Open WebUI** project for their work.
