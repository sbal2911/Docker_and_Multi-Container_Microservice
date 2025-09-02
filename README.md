# Docker-Based Flask Web App with Redis & PostgreSQL

This hands-on project focuses on deploying a Python Flask web
application integrated with a Redis cache and PostgreSQL database, all
within Docker containers. You'll also learn to use Docker Compose for
orchestrating multi-container setups and practice pushing your project
and issues to GitHub.

📚 **Learning Goals**

-   Understand the fundamentals of working with containerized
    environments.
-   Gain hands-on experience in setting up and configuring services
    locally or in the cloud.
-   Learn how to connect and orchestrate multiple system components.
-   Develop skills in testing, monitoring, and documenting deployed
    applications.

🛠️ **What You'll Do** - Install and verify Docker Desktop - Run a
PostgreSQL container - Set up a Flask app with Redis cache - Build and
connect services using Docker Compose - Test the app in your browser and
take screenshots - Push the project to GitHub - Create GitHub Issues for
errors encountered during the hands-on

> **Note:** Some errors are intentionally included in the code. Log them
> under GitHub Issues.

🧑‍💻 **Prerequisites** - Docker Desktop installed and verified - Code
editor (e.g., VS Code) - Git and GitHub account - Internet connection

⚙️ **Execution Steps**

### 1️⃣ Install Docker Desktop

**Windows** - Download Docker Desktop for Windows. - Run
`Docker Desktop Installer.exe` with default settings. - Start Docker
from the Start Menu. - Enable WSL 2 if prompted, then restart your
system.

**macOS** - Download Docker Desktop for Mac (choose the correct chip:
Apple Silicon or Intel). - Open the `.dmg` file and drag `Docker.app`
into Applications. - Launch Docker and follow any security prompts.

✅ Verify Installation:

``` bash
docker -v
```

### 2️⃣ PostgreSQL Setup with Docker

a.  Pull the PostgreSQL Image:

``` bash
docker pull postgres
```

b.  Start a PostgreSQL Instance:

``` bash
docker run -d -p 5432:5432 --name postgres1 -e POSTGRES_PASSWORD=pass12345 postgres
```

c.  Access the Container:

``` bash
docker exec -it postgres1 bash
```

d.  Connect to PostgreSQL:

``` bash
psql -d postgres -U postgres
```

### 3️⃣ Build a Flask + Redis App using Docker Compose

a.  Create `requirements.txt`

``` txt
flask
redis
```

b.  Create `app.py`

``` python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return f'Hello World! I have been seen {count} times.\n'
```

c.  Create a `Dockerfile`

``` dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

d.  Create `compose.yaml`

``` yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
```

e.  Build and Run the Application:

``` bash
docker compose up
```

f.  Test in Browser:\
    Open your browser and go to:\
    <http://localhost:8000>

Take screenshots of: - The running application output - Docker Desktop
showing running containers

### 📤 Push to GitHub

Initialize a Git repository:

``` bash
git init
```

Add and commit files:

``` bash
git add .
git commit -m "Initial commit - Docker Flask App"
```

Push to GitHub:

``` bash
git remote add origin <your-repo-URL>
git push -u origin main
```

### ❗ Log Issues in GitHub

Some errors are purposely introduced. You should: - Identify and
document the issues - Create entries under the "Issues" tab in your
GitHub repo

Include: - Error message - Screenshot (if applicable) - How you tried to
resolve it

------------------------------------------------------------------------

🎯 **What I Learned** - Successfully installed and verified Docker
Desktop on a local machine. - Gained practical experience in running
PostgreSQL and Redis containers. - Learned to structure a simple Python
Flask app and containerize it using Docker. - Explored how to
orchestrate multiple services using Docker Compose. - Practiced
real-world debugging and issue logging via GitHub. - Improved
understanding of container networking and service dependencies. -
Experienced the complete flow of deploying, testing, and documenting a
containerized application.

✅ **Final Notes** - Always follow submission guidelines and
deadlines. - Reach out if you face any technical issues---we're here to
help!

🚀 Happy Coding!
