# Create a Great Local Python Development Environment with Docker

## Why Docker?

*   Docker is more than a virtual environment - it's a complete isolated Linux operating system. This lets you install all your project dependencies, including Python modules and other system dependencies, without polluting your host system.
*   You can easily add multiple services to your development environment using Docker.
*   Deploying your application to the cloud becomes simpler with Docker.
*   Sharing the same development environment with your co-workers is easy with Docker.
*   Docker allows you to quickly test different Python versions by pulling specific images from Docker Hub.

## To debug after setting up once
Make sure all containers are down.
fire docker-compose up
Hit 'Open Remote Window' icon at the bottom left of VS Code

## The Tutorial

This tutorial focuses on manually setting up a Docker development environment for a deeper understanding of the Docker commands and features, rather than using the beta "Dev Environment" feature.

## Building a FastAPI Application

1.  **Create a `requirements.txt` file:** List your project's Python dependencies. The tutorial uses a FastAPI application with dependencies on `fastapi` and `uvicorn`.
2.  **Create a project folder:** Name it "source" (as in the example) to hold your application code.
3.  **Create a `main.py` file inside the "source" folder:** This file will contain your application's code. The tutorial uses a simple FastAPI application example.

## Dockerizing the Application

1.  **Create a `Dockerfile` in the base directory of your project.**
2.  **Specify the base image:**  The example uses `python:3.10-slim` as the base image, a slimmed-down version of the official Python 3.10 image.
3.  **Create a working directory:** Set `/code` as the working directory inside the container.
4.  **Install dependencies:**
    *   Copy the `requirements.txt` file into the container.
    *   Install the dependencies using `pip install --no-cache-dir -r requirements.txt`. The `--no-cache-dir` flag prevents Docker from using its own cache.
5.  **Copy the application code:** Copy the "source" folder into the container.
6.  **Specify the entry point command:** Use `["uvicorn", "source.main:app", "--reload", "--host", "0.0.0.0", "--port", "80"]` to run the FastAPI application with Uvicorn. This command does the following:
    *   Starts the Uvicorn server.
    *   Loads the `app` object from `main.py` in the "source" directory.
    *   Enables automatic reloading when code changes.
    *   Listens on all available network interfaces (`0.0.0.0`) on port 80.

## Building the Docker Image

*   Use the command `docker build -t fastapi-image .` to build the Docker image. This command:
    *   Builds an image named `fastapi-image`.
    *   Uses the instructions from the `Dockerfile` in the current directory (`.`).

## Running the Application

*   Use the command `docker run --name fastapi-container -p 80:80 fastapi-image` to run the Docker image. This command:
    *   Creates a container named `fastapi-container` from the `fastapi-image` image.
    *   Maps port 80 of the container to port 80 on the host machine.
    *   Runs the application.
*   Access the application at `localhost:80` in your web browser.
*   Add the `-d` flag to run the container in detached mode (background): `docker run -d --name fastapi-container -p 80:80 fastapi-image`. This lets you continue using the terminal while the container runs.

## Syncing Code with Volumes

*   Docker volumes sync code changes between your host machine and the container. This ensures that changes made on your host machine are reflected in the containerized application immediately.
*   Add the volume mapping to the run command: `docker run -d --name fastapi-container -p 80:80 -v "$(pwd):/code" fastapi-image`. This maps your current working directory (`$(pwd)`) on the host machine to the `/code` directory inside the container.

## Setting Up an IDE Inside the Container

*   Install the Docker and Dev Containers extensions for VS Code.
*   Attach to the running container from VS Code using the icon on the bottom left called 'Open Remote Window'. This will launch a new VS Code instance connected to your container.
*   Install the Python extension inside the containerized VS Code instance. This provides features like autocompletion and debugging.
*   Develop your application inside this VS Code instance. You will have full IDE support and automatic synchronization of code changes with the running application.

## Simplifying with Docker Compose

*   Create a `docker-compose.yaml` file in your project's root directory.
*   Define your services in this file. The tutorial demonstrates:
    *   A `web` service built from the `Dockerfile`.
    *   A `redis` service using the `redis:alpine` image from Docker Hub.
*   Specify dependencies between services: The `web` service depends on the `redis` service in this example.
*   Start your application using `docker-compose up -d`. This command:
    *   Reads the `docker-compose.yaml` file.
    *   Builds any required images.
    *   Creates and starts the containers.
    *   Runs them in detached mode.
*   Stop your application using `docker-compose down`. This stops and removes the containers defined in the `docker-compose.yaml` file.

## Debugging with `debugpy`

*   Add `debugpy` to your `requirements.txt` file.
*   Expose port 5678 for `debugpy` in the `docker-compose.yaml` file.
*   Add a code snippet to your `main.py` to start the `debugpy` server: `import debugpy; debugpy.listen(("0.0.0.0", 5678))`.
*   Attach the VS Code debugger to the running container. Configure a "Remote Attach" debug configuration in VS Code, targeting `localhost:5678`.
*   You can now set breakpoints in your code and debug your application inside the Docker container.

## Further Resources:
- https://github.com/patrickloeber/python-docker-tutorial
- https://towardsdatascience.com/debugging-for-dockerized-ml-applications-in-python-2f7dec30573d
- https://github.com/Wyntuition/try-python-flask-redis-docker-compose
- https://docs.docker.com/compose/gettingstarted/
- https://www.docker.com/blog/containerized-python-development-part-1/