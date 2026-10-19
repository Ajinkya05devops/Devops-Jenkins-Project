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
<img width="1408" height="881" alt="first stag done " src="https://github.com/user-attachments/assets/8691b3d8-213d-4c94-9411-2e717dd0fe46" />


### Pipeline stopped by a failing test
I changed a test on purpose to see what happens. Test failed, and Build and Deploy were skipped.

<img width="1408" height="881" alt="overall pipeline state" src="https://github.com/user-attachments/assets/084c21df-11b5-43f1-9dc9-93980f708431" />
<img width="1408" height="881" alt="pipeline failure " src="https://github.com/user-attachments/assets/b19401c8-d786-4ddc-871b-346fe5153f8c" />

### App before and after a code change
Pushing a change to `app.py` triggered a new build and the app updated automatically.

<img width="2813" height="1655" alt="app run before change " src="https://github.com/user-attachments/assets/d6b01271-2ead-4382-869f-bd6609e8bb90" />
<img width="1408" height="881" alt="successful deploy after change " src="https://github.com/user-attachments/assets/b9bfc600-1812-4982-9d2b-04542797311f" />


### Container running on EC2
<img width="2812" height="1544" alt="container-running-state" src="https://github.com/user-attachments/assets/5cd7a1da-25a6-47ea-9d86-bbfd994adb41" />


### Jenkins configuration
<img width="2813" height="1655" alt="Git-Url" src="https://github.com/user-attachments/assets/dd10ab2f-5fd7-46a9-89fa-193a03110067" />

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
