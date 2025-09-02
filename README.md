# Installing a Docker and Building a Micro-Container Service

This hands-on project focuses on deploying a Python Flask web
application incorporated with a Redis cache and PostgreSQL database
within Docker containers. We learnt the process of using Docker Compose for
orchestrating multi-container setups.

**Execution Steps**

### 1. Installing Docker Desktop

**macOS** - Downloaded Docker Desktop for Mac by selecting the correct chip:
Apple Silicon. 

Verifying my Installation:

``` bash
docker -v
```

### 2. PostgreSQL Setup with Docker

a.  Pulled the PostgreSQL Image:

``` bash
docker pull postgres
```

b.  Created and Started a PostgreSQL Instance:

``` bash
docker run -d -p 5432:5432 --name postgres1 -e POSTGRES_PASSWORD=pass12345 postgres
```

c.  Opened a Terminal to the Container:

``` bash
docker exec -it postgres1 bash
```

d.  Interacted with PostgreSQL using psql:

``` bash
psql -d postgres -U postgres
```

### 3. Build a Python Web App using Docker Compose in VSCode

a.  Defined application dependencies in `requirements.txt`

``` txt
flask
redis
```

b.  Defining application in `app.py`

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
    return f'Hello World! I have been seen {count} times.\n'.format(count)
```

c.  Created a `Dockerfile` to conatainerize the application

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

d.  Defining services in `compose.yaml` file

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

e.  Building and Running the Application:

``` bash
docker compose up
```

f.  Tested in Chrome Browser:\
    by pasting the below mentioned URL:\
    <http://localhost:8000>

Screenshots of the whole assignment including that of Docker Desktop is documented
in **Screenshots** file.

### Push to GitHub

Initialized a Git repository:

``` bash
git init
```

Adding and committing files:

``` bash
git add .
git commit -m "Initial commit - Docker based Python Web App"
```

Pushed to GitHub:

``` bash
git remote add origin https://github.com/sbal2911/Docker_and_Multi-Container_Microservice.git
git push -u origin main
```

### ❗ Logged Issues in GitHub

Some issues were observed during the code which were fixed by minor tweaks in code indentation.
Screenshots of it are present in the Screenshot file.

------------------------------------------------------------------------

**What I Learned** - Successfully installed and verified Docker
Desktop on a local machine. - Gained practical experience in running
PostgreSQL and Redis containers. - Learned to structure a simple Python
Flask app and containerize it using Docker. - Explored how to
orchestrate multiple services using Docker Compose. - Practiced
real-world issue logging via GitHub. - Improved
understanding of container networking and service dependencies. -
Experienced and observed the flow of installing, building, deploying, testing, 
and documenting a containerized application.

------------------------------------------------------------------------

