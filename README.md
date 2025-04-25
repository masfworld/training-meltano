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
   meltano add extractor tap-google-sheets --variant singer-io
   meltano config tap-google-sheets set --interactive
   ```
   You need to configure
   * `Client ID`
   * `Client Secret`
   * `Refresh Token`
   * `Spreedsheet id`
   * `Start_date` -> You can set `2011-01-01T00:00:00.000Z`

   You can get all of these values in multiple steps (it's not super simple 😔):
   1. You need to access to Google Cloud Console 
      - Enable [Google Sheet API](https://console.cloud.google.com/apis/library/sheets.googleapis.com)
      - Go to `APIs and Services` -> `Credentials` -> `Create Credentials` -> `OAuth client ID` -> `Application type` -> `Web application`
      - You will need to set `Authorise redirect APIs` to https://developers.google.com/oauthplayground 
      - You need to add your gmail user as a Test User in `Audience`
      Ok, at this point, you have a `Client ID` and `Client Secret`. Let's see how to get `Refresh Token`
   2. Go to: [OAuth 2.0 Playground](https://developers.google.com/oauthplayground )
	   - Click the gear icon ⚙️ (top right) and:
	      - Check “Use your own OAuth credentials”
	      - Paste your Client ID and Client Secret
	      - Scroll and select Google Sheets API v4
	      - Then check https://www.googleapis.com/auth/spreadsheets.readonly
	      - Click “Authorize APIs”
	      - Log in with your Google account and allow access.
	      - Click “Exchange authorization code for tokens”
	      - Now you’ll see:
	         - Refresh Token 🎉
   3. Check your configuration executing `meltano config tap-google-sheets test`

6. **Adding Loader**
   ```bash
   meltano add loader target-postgres
   meltano config target-postgres set --interactive
   ```
      You need to configure
   * `host = postgres` (option 12)
   * `database = meltano_loader` (option 4)
   * `user = myuser` (option 36)
   * `password = mypassword` (option 15)
   * `port = 5432` (option 16)

7. **Execute pipeline**
   ```bash
   meltano el tap-google-sheets target-postgres
   ```
8. **Check ETL**
   1. Go to http://localhost:8080
   2. Log in in postgres with these parameters:
      - `server=postgres`
      - `user=myuser`
      -  `password=mypassword`
      - `database=meltano_loader`
