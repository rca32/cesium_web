# Cesium Web Frontend

[![Build Status](https://travis-ci.org/cesium-ml/cesium_web.svg?branch=master)](https://travis-ci.org/cesium-ml/cesium_web)

## About 

Web frontend for the [`cesium` library](https://github.com/cesium-ml/cesium). Within the browser, users can upload time series data data, extract features, fit a model, and generate predictions for new data.

## Getting started

The easiest way to try the web app is to run it through Docker:

1. Download the docker-compose file for Cesium:
   `curl -Lo docker-compose.yml https://raw.githubusercontent.com/cesium-ml/cesium_web/master/docker-compose/docker-compose.yml`

2. Ensure you have Docker Compose up and running, then:
   `docker-compose up`

3. Wait a few seconds and navigate to `http://localhost:9000`

4. Create a project and go! If you want some test data, an example header file and time series data are available at
   ```
   curl -Lo example-headers.dat https://raw.githubusercontent.com/cesium-ml/cesium-data/master/asas_training/asas_training_subset_classes.dat
   curl -Lo example-series.tar.gz https://raw.githubusercontent.com/cesium-ml/cesium-data/master/asas_training/asas_training_subset.tar.gz
   ```

## Running the app locally
1. Install the following dependencies:

- supervisor
- nginx
- postgresql (including libpq-dev on Debian)
- npm

### MacOS
Using [Homebrew](http://brew.sh/):

`brew install supervisor nginx postregsql node`

### Linux
On Debian or Ubuntu:
```
sudo apt-get install nginx supervisor postgresql libpq-dev npm nodejs-legacy
```

2. Install Python dependencies: `pip install -r requirements.txt`
3. On Linux, it may be necessary to configure your database permissions: at the end of your `pg_hba.conf` (typically in `/etc/postgresql/9.5/main`), add the following lines:

```
local   all             postgres                                peer
local cesium cesium trust
local cesium_test cesium trust
```
and restart `postgresl` (`sudo service postgresql restart`).

4. Initialize the database with `make db_init`
5. Run `make` to start the server and install additional dependencies, and navigate to `localhost:5000`.

## Dev Tips
To execute the test suite:

- Install ChromeDriver from:
  https://sites.google.com/a/chromium.org/chromedriver/home
  (The binary has to be placed somewhere on your path.)
- Install Chrome or Chromium
- Optional: install xfvb for headless tests (only available on Linux)
- `make test_headless` or `make test`

Debugging:

- Run `make log` to watch log output
- Run `make debug` to start webserver in debug mode
- Run `make attach` to attach to output of webserver, e.g. for use with `pdb.set_trace()`

## Standards
To ensure that JavaScript & JSX code conforms with industry style
recommendations, after adding or modifying any .js or .jsx files, run ESLint with
`node_modules/eslint/bin/eslint.js -c .eslintrc --ext .jsx,.js public/scripts/`.
To automatically run ESLint when you make changes to your JavaScript code, add
a pre-commit hook by adding the following to your .git/hooks/pre-commit:

```
#!/bin/bash
# Pre-commit Git hook to run ESLint on JavaScript files.
#
# If you absolutely must commit without testing,
# use: git commit --no-verify (git commit -n)

filenames=($(git diff --cached --name-only HEAD))

for i in "${filenames[@]}"
do
    if [[ $i =~ \.js$ ]] || [[ $i =~ \.jsx$ ]] ;
    then
        echo node_modules/eslint/bin/eslint.js -c .eslintrc $i
        node_modules/eslint/bin/eslint.js -c .eslintrc $i
        if [ $? -ne 0 ];
        then
            exit 1
        fi
    fi
done
```

## Docker images
Run `make docker-images` to build and push to Docker hub.
