# S13 Training Camps

A self-contained student training website that runs as one Docker container.

## Single-container architecture

The `app` container provides everything:

- Responsive HTML, CSS, and JavaScript website
- Course catalog API
- Student registration and login API
- Enrollment API
- File-based demo persistence through the `app-data` Docker volume

The application uses only the Node.js standard library and has no package dependencies. The GitHub Actions workflow builds and pushes the Docker image on every branch push to Docker Hub as `devopseasylearning/s13-training-camps`, tagged with the full commit SHA and `latest`. It then starts the pushed image temporarily, checks the health and courses APIs, and shows the image details, container status, and logs in the Actions run. Pull requests build the image without pushing it.

## Tools used

- HTML5, CSS3, and vanilla JavaScript
- Node.js 22
- Docker and Docker Compose
- GitHub Actions
- Git and curl

## Run

```bash
docker build -t s13-training-camps:latest .
docker volume create s13-training-camps-data
docker run -d --name s13-training-camps --restart unless-stopped -p 8080:8080 -v s13-training-camps-data:/data s13-training-camps:latest
```

Open <http://localhost:8080>.

## Verify

```bash
docker ps --filter name=s13-training-camps
curl http://localhost:8080/health
curl http://localhost:8080/api/courses
```

## Stop

```bash
docker rm -f s13-training-camps
```

Use `docker volume rm s13-training-camps-data` only when you intentionally want to delete saved demo users and enrollments.

## CI/CD

`.github/workflows/docker-build.yml` builds and pushes the image to Docker Hub on every branch push using GitHub's hosted runner, then runs a temporary container to check `/health` and `/api/courses` and show its status and logs. Pull requests only build the image. Add a Docker Hub access token with write permission as the `DOCKERHUB_TOKEN` Actions repository secret. The Docker Hub repository `devopseasylearning/s13-training-camps` must exist and allow pushes. The temporary container only runs for the duration of the CI job; this is not a persistent deployment.
