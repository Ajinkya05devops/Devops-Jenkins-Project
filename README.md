# CI/CD Pipeline with Jenkins and Docker

A small Flask app with a Jenkins pipeline that **tests, builds, and deploys it automatically** every time code is pushed to GitHub.

## Pipeline flow

```
GitHub push -> Jenkins -> Test (pytest) -> Build (Docker image) -> Deploy (container on port 5001)
```

If the Test stage fails, the pipeline stops and nothing is deployed.

## Tools used

- Python (Flask) and Pytest
- Docker
- Jenkins (declarative pipeline, Pipeline from SCM)
- Git and GitHub

## Project structure

```
app.py              Flask app with / and /health routes
test_app.py         Pytest tests
requirements.txt    Python dependencies
Dockerfile          Packages the app as a Docker image
Jenkinsfile         Pipeline: Checkout, Test, Build, Deploy
jenkins-setup/      Dockerfile for a Jenkins image that has Docker and Python
```

## Run the app locally

```
pip install -r requirements.txt
pytest
python app.py
```

Open http://localhost:5000

## Run with Docker

```
docker build -t myapp .
docker run -p 5001:5000 myapp
```

Open http://localhost:5001

## Set up Jenkins

```
cd jenkins-setup
docker build -t my-jenkins .
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  my-jenkins
docker exec -u root jenkins chmod 666 /var/run/docker.sock
```

Then open http://localhost:8080, unlock Jenkins with
`docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`,
install the suggested plugins, and create a **Pipeline** job:

- Definition: Pipeline script from SCM
- SCM: Git, with this repo's URL, branch `*/main`
- Script Path: `Jenkinsfile`

Jenkins checks GitHub every 2 minutes (`pollSCM`), so no webhook is needed.

## Note

The `chmod 666` on the Docker socket is for a local learning setup only. Do not do this in production.

## Screenshots

Add your screenshots here:

1. Jenkins Stage View with all stages green
2. A failed build stopped at the Test stage
3. The app running at http://localhost:5001
4. `docker ps` showing the `myapp-live` container
