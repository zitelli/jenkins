## 🧩 CI/CD pipeline testing using Jenkins (in a Docker container)

<div align="justify">We build a complete CI/CD pipeline where Jenkins (running in a Docker container) automatically detects a Git push, builds the app, runs tests, and deploys the new version as a Docker container on the same host.</div>

### 🚀 Create the Jenkins Pipeline Job

This is a Multibranch Pipeline because it automatically discovers branches with a Jenkinsfile.

New Item → Multibranch Pipeline (name it myapp-pipeline).

- Branch Sources → Add source → GitHub

  Credentials: select github-token

  Repository HTTPS URL: https://github.com/#your-username#/#your-repo#.git

- Build Configuration

  Mode: by Jenkinsfile

  Script Path: Jenkinsfile

- Scan Repository Triggers

  Check Periodically if not otherwise run and set e.g. 1 minute (Poll SCM).
  Alternatively, if you have a public URL (using ngrok or similar), set up a GitHub webhook – see Step 7.

- Save. Jenkins will immediately scan the repository and find the main branch.

bash:

```
# Create a persistent volume for Jenkins data

$ docker volume create jenkins_home

$ docker stop jenkins && docker rm jenkins

$ docker build -t myjenkins -f Dockerfile.jenkins .

# Run Jenkins
$ docker run -d   --name jenkins   --restart unless-stopped   -u root   -p 8080:8080 -p 50000:50000   -v jenkins_home:/var/jenkins_home   -v /home/junior/.docker/desktop/docker.sock:/var/run/docker.sock   myjenkins

$ git add Jenkinsfile

$ git commit -m "pipeline usando CLI docker dentro do Jenkins"

$ git push origin main
```

Test:

```
$ curl http://localhost:8081
Hello CI/CD!
```

### 👥 Contribuição

Sinta-se à vontade para usar este projeto como base para estudos ou extensões.

### 📜 Licença

Este projeto é de uso livre para fins educacionais.
