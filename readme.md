# Microservice in Go

[![Duplicated Lines (%)](https://sonarcloud.io/api/project_badges/measure?project=awenzelhuemer_fh-mc-go-mux&metric=duplicated_lines_density)](https://sonarcloud.io/summary/new_code?id=awenzelhuemer_fh-mc-go-mux)
[![Lines of Code](https://sonarcloud.io/api/project_badges/measure?project=awenzelhuemer_fh-mc-go-mux&metric=ncloc)](https://sonarcloud.io/summary/new_code?id=awenzelhuemer_fh-mc-go-mux)
[![Code Smells](https://sonarcloud.io/api/project_badges/measure?project=awenzelhuemer_fh-mc-go-mux&metric=code_smells)](https://sonarcloud.io/summary/new_code?id=awenzelhuemer_fh-mc-go-mux)
[![Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=awenzelhuemer_fh-mc-go-mux&metric=vulnerabilities)](https://sonarcloud.io/summary/new_code?id=awenzelhuemer_fh-mc-go-mux)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=awenzelhuemer_fh-mc-go-mux&metric=bugs)](https://sonarcloud.io/summary/new_code?id=awenzelhuemer_fh-mc-go-mux)

[Link to Github Repo](https://github.com/awenzelhuemer/fh-mc-go-mux)



## Create pipeline with github actions

1. Code needs to be checked out
2. Docker container needs to get started
3. Wait for postgres
4. Build go project
5. Run tests
6. Analyze with sonarcloud (SONAR_TOKEN has to be set for this repository)

```yml

on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Start PostgreSQL
      run: docker-compose -f "docker-compose.yml" up -d
      env:
        POSTGRES_USER: postgres
        POSTGRES_PASSWORD: postgres

    - name: Wait for PostgreSQL to start
      run: docker-compose -f docker-compose.yml exec -T postgres /bin/sh -c 'while ! nc -z localhost 5432; do sleep 1; done;'
      
    - name: Set up Go
      uses: actions/setup-go@v3
      with:
        go-version: 1.19

    - name: Build
      run: go build -v ./...

    - name: Test
      run: go test -v ./...
      env:
        APP_DB_USERNAME: postgres
        APP_DB_PASSWORD: postgres
        APP_DB_NAME: postgres
      
    - name: Analyze
      uses: sonarsource/sonarcloud-github-action@master
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

## Add tags

1. With github actions build status is directly integrated into github
2. It is possible to add sonarcloud code analyzis information.
