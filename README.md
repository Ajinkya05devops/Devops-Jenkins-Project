# CI/CD Pipeline with Jenkins and Docker on AWS EC2

A hands-on DevOps project. Every time I push code to GitHub, Jenkins automatically tests the app, builds a Docker image, and deploys it to a server on AWS EC2.

## How it works

```
GitHub push → Jenkins (checks for changes every 2 minutes) → Test → Build → Deploy
```

1. **Checkout:** Jenkins pulls the latest code from GitHub
2. **Test:** runs the pytest tests
3. **Build:** builds a Docker image tagged with the build number
4. **Deploy:** replaces the running container with the new version on port 5001

If the Test stage fails, Build and Deploy are skipped, so broken code never goes live.

## Tools used

- Jenkins (declarative pipeline, Pipeline script from SCM)
- Docker
- AWS EC2 (Ubuntu)
- Python, Flask, Pytest
- Git and GitHub

## Project files

| File | Purpose |
|---|---|
| `app.py` | Small Flask app with `/` and `/health` routes |
| `test_app.py` | Pytest tests for the app |
| `requirements.txt` | Python dependencies |
| `Dockerfile` | Packages the app as a Docker image |
| `Jenkinsfile` | The pipeline: Checkout, Test, Build, Deploy |

## Screenshots

### Successful pipeline
SCREENSHOT: Stage View #1 and #2

### Pipeline stopped by a failing test
I changed a test on purpose to see what happens. Test failed, and Build and Deploy were skipped.

SCREENSHOT: Stage View #1 to #4
SCREENSHOT: Console Output #3

### App before and after a code change
Pushing a change to `app.py` triggered a new build and the app updated automatically.

SCREENSHOT: app before
SCREENSHOT: app after

### Container running on EC2
SCREENSHOT: docker ps

### Jenkins configuration
SCREENSHOT: Jenkins config (repo URL)

## Problems I ran into and how I fixed them

- **App would not load in the browser:** port 5001 was blocked. I added an inbound rule to the EC2 security group.
- **Jenkins could not run Docker:** I added the `jenkins` user to the `docker` group and restarted Jenkins.
- **Test stage needed Python venv:** I installed `python3-venv` on the server.
- **Server ran low on memory:** I added a 2 GB swap file and moved to a larger instance (t3.small).

## What I learned

- How a CI/CD pipeline connects Git, Jenkins, and Docker
- How a failing test blocks a deployment
- How to read Jenkins console output to find errors
- How EC2 security groups control access to a server

## Run it locally

```
pip install -r requirements.txt
pytest
python app.py
```

Or with Docker:

```
docker build -t myapp .
docker run -p 5001:5000 myapp
```

## Next steps

- Push images to Docker Hub
- Add Slack or email notifications
- Add a manual approval step before deploy
- Create the server with Terraform
