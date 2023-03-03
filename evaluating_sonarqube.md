# Evaluating SonarQube for a Project Using Docker

- this is a summary of [this page](https://docs.sonarqube.org/latest/try-out-sonarqube/)
- this is for evaluation only, and will not persist any results outside of the docker container it is executing in

## Estimated Duration

- 5 minutes, unless you have to play around with the command lines they provide
  - provided command lines are for linux systems, with `$` bash substitutions
  - add another 5 minutes to clean those up

## Steps to Follow

- pull image from docker repo
  ```
  docker pull sonarqube
  docker run -d --name sonarqube -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:latest
  ```
- go to browser [local](localhost:9000) and wait for the server to start
- user/password = admin, can change to something simple, like "bob"
- follow the ["try out" path](https://docs.sonarqube.org/latest/try-out-sonarqube/) under `Analyzing a project`
  and try the manual route
  - to keep things simple, I chose the project name `bob` and then `fred` as the container is going to have a short life span
  - simplified setup
  - select options and will give you steps on how to manually scan
  - will ask type of project, OS, and then give you a command to execute
    - select docker and follow, see note below
- once the scan has completed, go back to the browser and examine results

## Notes

- in the provided steps on how to manually scan, replace `localhost` with `host.docker.internal` as both
  the server and the client are executing within Docker
- if you change the scan profile, you need to execute the scan workflow again
  - i.e. if you have one triggered rule that is dominating the output, note it down, copy a profile from
    `profile_name` to `profile_name2`, switch to that new profile, and remove the dominating rule

## Examples

- for non-dotnet solutions, was given these instructions (modified for windows) for project `bob`
  ```
  docker pull sonarsource/sonar-scanner-cli
  set SONARQUBE_URL=host.docker.internal:9000
  set YOUR_REPO=C:\Users\JackDeWinter\Downloads\pymarkdown-main\pymarkdown-main
  set YOUR_PROJECT_KEY=bob
  set SONAR_LOGIN=sqp_ae77eeda796a2ef449e597543372d2cec5e31922
  docker run --rm -e SONAR_HOST_URL="http://%SONARQUBE_URL%" -e SONAR_SCANNER_OPTS="-Dsonar.projectKey=%YOUR_PROJECT_KEY%" -e SONAR_LOGIN=%SONAR_LOGIN% -v "%YOUR_REPO%:/usr/src" sonarsource/sonar-scanner-cli
  ```

- without bringing down the container, these were the dotnet project instructions (modified for windows) for project `fred`
  ```
  set YOUR_PROJECT_KEY=fred
  set SONARQUBE_URL=host.docker.internal:9000
  set SONAR_LOGIN=sqp_cebf267dc926d1a9b230dfb483c1600e757c5674
  dotnet sonarscanner begin /k:"%YOUR_PROJECT_KEY%" /d:sonar.host.url="http://%SONARQUBE_URL%"  /d:sonar.login="%SONAR_LOGIN%"
  dotnet build
  dotnet sonarscanner end /d:sonar.login="%SONAR_LOGIN%"
  ```
