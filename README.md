# Meltano Project with Docker Compose, PostgreSQL, and pgAdmin

This repository contains the configuration for setting up a Meltano project with PostgreSQL using Docker Compose. Additionally, it provides a web-based UI for PostgreSQL management via pgAdmin.

## Prerequisites

Ensure you have the following installed on your machine:
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Setup Instructions

1. **Clone the Repository**

   ```bash
   git clone https://github.com/masfworld/training-meltano.git
   ```

2. **Create Docker Containers**

   ```bash
   docker compose up -d
   ```

3. **Log in meltano container**
   ```bash
   docker exec -it training-meltano-meltano-1 /bin/bash
   ```
4. **Init Project**
   ```bash
   meltano init my_project
   export MELTANO_PROJECT_ROOT=/project/my_project
   ```
5. **Adding extractor**
   ```bash
   meltano add extractor tap-google-sheets
   meltano config tap-google-sheets set --interactive
   ```
   You need to configure
   * `Client ID`
   * `Client Secret`
   * `Refresh Token`
   * `Sheet id`
6. **Adding Loader**
   ```bash
   meltano add loader target-postgres
   meltano config target-postgres set --interactive
   ```
      You need to configure
   * `host = postgres`
   * `database = meltano_loader`
   * `username = myuser`
   * `password = mypassword`
   * `port = 5432`

7. **Execute pipeline**
   ```bash
   meltano elt tap-google-sheets target-postgres
   ```