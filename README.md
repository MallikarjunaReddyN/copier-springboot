# Spring Boot Template

A [Copier](https://github.com/copier-org/copier) template for a Spring Boot projects.

This template aims at making it easier to configure spring boot projects with **Java 17, checkstyle, databases (MySQL and PostgreSQL), docker, continous integration (Github Actions and Azure Pipelines), sonarqube, Helm chart and ArgoCD (Supports with Github Actions only)**. 

### Prerequisites

****

##### For generating template:

- **Python** - [Download Python | Python.org](https://www.python.org/downloads/)

- **copier** - [copier · PyPI](https://pypi.org/project/copier/3.1.0/)

##### For running generated project:

- Java - [Java Archive Downloads - Java SE 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)

- Docker - [Docker Desktop](https://www.docker.com/products/docker-desktop/)

- Intellij (Optional) - [IntelliJ IDEA – the Leading Java and Kotlin IDE](https://www.jetbrains.com/idea/)

### Usage

****

- Create a new project folder, for instance `example-service`.

- Open your favourite terminal at newly created folder location.

- Run below command:
  
  for windows:
  
  ```
  python3 -m copier copy --vcs-ref main https://github.com/MallikarjunaReddyN/copier-springboot.git .
  ```
  
  for macOS and linux:
  
  ```
  copier copy --vcs-ref main https://github.com/MallikarjunaReddyN/copier-springboot.git .
  ```

- And answer the questions.



### Publish project on github

    Create repository and add below secrets before pushing:

- SONAR_HOST_URL

- SONAR_TOKEN

- DOCKER_USERNAME

- DOCKER_PASSWORD

- WORKFLOW_TOKEN (required only for ArgoCD)

Note: Use Personal Access Token as WORKFLOW_TOKEN value

Repository secret location: 

    select repository -> settings -> Secrets and variables -> Actions



  

      


