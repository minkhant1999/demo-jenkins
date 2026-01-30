# DemoJenkins

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 16.2.16.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Deploy with Jenkins (test pipeline → Firebase)

### 1. Prerequisites

- **Docker** running on your machine (Docker Desktop on Mac/Windows, or Docker Engine on Linux).
- **Firebase CI token:** Run `firebase login:ci` and copy the token (you’ll add it in Jenkins as a credential).

### 2. Start Jenkins

```bash
cd /path/to/demo-jenkins
docker compose build --no-cache
docker compose up -d
```

- **On Mac:** If the build fails with "Cannot connect to Docker" or permission errors, in `docker-compose.yml` uncomment the line `user: root` under the `jenkins` service, then run `docker compose up -d` again.
- Open Jenkins at **http://localhost:8080**

### 3. Configure Jenkins (first time)

1. **Unlock Jenkins** (if prompted): get the initial admin password:
   ```bash
   docker exec demo_jenkins cat /var/jenkins_home/secrets/initialAdminPassword
   ```
2. **Create a pipeline job:** New Item → name (e.g. `demo_jenkins`) → **Pipeline** → OK.
3. **Pipeline config:**
   - **Definition:** Pipeline script from SCM
   - **SCM:** Git
   - **Repository URL:** `https://github.com/minkhant1999/demo-jenkins.git`
   - **Branch:** `*/dev` (or your branch)
   - **Script Path:** `Jenkinsfile`
4. **Add Firebase token credential:**
   - Manage Jenkins → Credentials → (domain) → Add Credentials
   - Kind: **Secret text**
   - Secret: paste your Firebase CI token
   - **ID:** `firebase-token` (must match the Jenkinsfile)
   - Save.

### 4. Run the pipeline

- Open your pipeline job → **Build Now**.
- In Build History click the build number → **Console Output** to watch logs.
- Success: you’ll see "Deploying to Firebase Hosting…" then "Angular app deployed successfully to Firebase!".
- Check your Firebase Hosting URL (e.g. `https://fir-jenkins-5b8a1.web.app` from `.firebaserc`) to confirm the app is live.

### 5. If something fails

- **"docker: not found" or "Cannot connect to Docker"** → On Mac, uncomment `user: root` in `docker-compose.yml` and restart. On Linux, ensure the host Docker group GID matches `group_add` (often 999).
- **"firebase-token" credential not found** → Create a credential with ID exactly `firebase-token` (Secret text) and your Firebase CI token.
- **Firebase deploy error** → Confirm the token is valid (`firebase login:ci` again) and the project in `.firebaserc` is correct.

---

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
