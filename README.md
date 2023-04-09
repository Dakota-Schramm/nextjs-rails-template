# Rails + Nextjs - Backend for Frontend Template

# TO START
- Initialize your dev directories
  - Install next template in app/frontend/etc. directory
  - Create API only project in /backend/api/etc. directory

# FRONTEND 
# Next.js + Tailwind CSS Example

This example shows how to use [Tailwind CSS](https://tailwindcss.com/) [(v3.2)](https://tailwindcss.com/blog/tailwindcss-v3-2) with Next.js. It follows the steps outlined in the official [Tailwind docs](https://tailwindcss.com/docs/guides/nextjs).

## Deploy your own

Deploy the example using [Vercel](https://vercel.com?utm_source=github&utm_medium=readme&utm_campaign=next-example) or preview live with [StackBlitz](https://stackblitz.com/github/vercel/next.js/tree/canary/examples/with-tailwindcss)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/git/external?repository-url=https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss&project-name=with-tailwindcss&repository-name=with-tailwindcss)

## How to use

Execute [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) with [npm](https://docs.npmjs.com/cli/init), [Yarn](https://yarnpkg.com/lang/en/docs/cli/create/), or [pnpm](https://pnpm.io) to bootstrap the example:

```bash
npx create-next-app --example with-tailwindcss with-tailwindcss-app
```

```bash
yarn create next-app --example with-tailwindcss with-tailwindcss-app
```

```bash
pnpm create next-app --example with-tailwindcss with-tailwindcss-app
```

Deploy it to the cloud with [Vercel](https://vercel.com/new?utm_source=github&utm_medium=readme&utm_campaign=next-example) ([Documentation](https://nextjs.org/docs/deployment)).

Notes on frontend:
  - If using getServersideProps, make sure that you use the appropriate backend service name instead of localhost in your fetch
    - Ex. 'http://api:3000'

# BACKEND
# README

### Initialize
```rails new <project-name> --database=postgresql --api```
should replace project name with api. If change, make sure to change in the docker-compose file as well.

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...

### Initial Setup

Quickstart: Compose and Rails

This Quickstart guide shows you how to use Docker Compose to set up and run a Rails/PostgreSQL app. Before starting, install Compose.

Define the project

Start by setting up the files needed to build the app. The app will run inside a Docker container containing its dependencies. Defining dependencies is done using a file called Dockerfile. To begin with, the Dockerfile consists of:

# syntax=docker/dockerfile:1
FROM ruby:2.5
RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
WORKDIR /myapp
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock
RUN bundle install

# Add a script to be executed every time the container starts.
COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
EXPOSE 3000

# Configure the main process to run when running the image
CMD ["rails", "server", "-b", "0.0.0.0"]
That'll put your application code inside an image that builds a container with Ruby, Bundler and all your dependencies inside it. For more information on how to write Dockerfiles, see the Docker user guide and the Dockerfile reference.

Next, open an editor and create a bootstrap Gemfile which just loads Rails. This will be overwritten in a moment by rails new.

source 'https://rubygems.org'
gem 'rails', '~>5'
Create an empty Gemfile.lock file to build our Dockerfile.

$ touch Gemfile.lock
Next, provide an entrypoint script to fix a Rails-specific issue that prevents the server from restarting when a certain server.pid file pre-exists. This script will be executed every time the container gets started. entrypoint.sh consists of:

#!/bin/bash
set -e

# Remove a potentially pre-existing server.pid for Rails.
rm -f /myapp/tmp/pids/server.pid

# Then exec the container's main process (what's set as CMD in the Dockerfile).
exec "$@"
Finally, docker-compose.yml is where the magic happens. This file describes the services that comprise your app (a database and a web app), how to get each one's Docker image (the database just runs on a pre-made PostgreSQL image, and the web app is built from the current directory), and the configuration needed to link them together and expose the web app's port.

services:
  db:
    image: postgres
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: password
  web:
    build: .
    command: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s -p 3000 -b '0.0.0.0'"
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
Tip

You can use either a .yml or .yaml extension for this file.
Build the project

With those files in place, you can now generate the Rails skeleton app using docker compose run:

$ docker compose run --no-deps web rails new . --force --database=postgresql
First, Compose builds the image for the web service using the Dockerfile. The --no-deps tells Compose not to start linked services. Then it runs rails new inside a new container, using that image. Once it's done, you should have generated a fresh app.

List the files.

$ ls -l

total 64
-rw-r--r--   1 vmb  staff   222 Jun  7 12:05 Dockerfile
-rw-r--r--   1 vmb  staff  1738 Jun  7 12:09 Gemfile
-rw-r--r--   1 vmb  staff  4297 Jun  7 12:09 Gemfile.lock
-rw-r--r--   1 vmb  staff   374 Jun  7 12:09 README.md
-rw-r--r--   1 vmb  staff   227 Jun  7 12:09 Rakefile
drwxr-xr-x  10 vmb  staff   340 Jun  7 12:09 app
drwxr-xr-x   8 vmb  staff   272 Jun  7 12:09 bin
drwxr-xr-x  14 vmb  staff   476 Jun  7 12:09 config
-rw-r--r--   1 vmb  staff   130 Jun  7 12:09 config.ru
drwxr-xr-x   3 vmb  staff   102 Jun  7 12:09 db
-rw-r--r--   1 vmb  staff   211 Jun  7 12:06 docker-compose.yml
-rw-r--r--   1 vmb  staff   184 Jun  7 12:08 entrypoint.sh
drwxr-xr-x   4 vmb  staff   136 Jun  7 12:09 lib
drwxr-xr-x   3 vmb  staff   102 Jun  7 12:09 log
-rw-r--r--   1 vmb  staff    63 Jun  7 12:09 package.json
drwxr-xr-x   9 vmb  staff   306 Jun  7 12:09 public
drwxr-xr-x   9 vmb  staff   306 Jun  7 12:09 test
drwxr-xr-x   4 vmb  staff   136 Jun  7 12:09 tmp
drwxr-xr-x   3 vmb  staff   102 Jun  7 12:09 vendor
If you are running Docker on Linux, the files rails new created are owned by root. This happens because the container runs as the root user. If this is the case, change the ownership of the new files.

$ sudo chown -R $USER:$USER .
If you are running Docker on Mac or Windows, you should already have ownership of all files, including those generated by rails new.

Now that you’ve got a new Gemfile, you need to build the image again. (This, and changes to the Gemfile or the Dockerfile, should be the only times you’ll need to rebuild.)

$ docker compose build
Connect the database

The app is now bootable, but you're not quite there yet. By default, Rails expects a database to be running on localhost - so you need to point it at the db container instead. You also need to change the database and username to align with the defaults set by the postgres image.

Replace the contents of config/database.yml with the following:

default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test
You can now boot the app with docker compose up. If all is well, you should see some PostgreSQL output:

$ docker compose up

rails_db_1 is up-to-date
Creating rails_web_1 ... done
Attaching to rails_db_1, rails_web_1
db_1   | PostgreSQL init process complete; ready for start up.
db_1   |
db_1   | 2018-03-21 20:18:37.437 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
db_1   | 2018-03-21 20:18:37.437 UTC [1] LOG:  listening on IPv6 address "::", port 5432
db_1   | 2018-03-21 20:18:37.443 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
db_1   | 2018-03-21 20:18:37.726 UTC [55] LOG:  database system was shut down at 2018-03-21 20:18:37 UTC
db_1   | 2018-03-21 20:18:37.772 UTC [1] LOG:  database system is ready to accept connections
Finally, you need to create the database. In another terminal, run:

$ docker compose run web rake db:create
Starting rails_db_1 ... done
Created database 'myapp_development'
Created database 'myapp_test'
View the Rails welcome page!

That's it. Your app should now be running on port 3000 on your Docker daemon.

On Docker Desktop for Mac and Docker Desktop for Windows, go to http://localhost:3000 on a web browser to see the Rails Welcome.

Rails example

Stop the application

To stop the application, run docker compose down in your project directory. You can use the same terminal window in which you started the database, or another one where you have access to a command prompt. This is a clean way to stop the application.

$ docker compose down

Stopping rails_web_1 ... done
Stopping rails_db_1 ... done
Removing rails_web_run_1 ... done
Removing rails_web_1 ... done
Removing rails_db_1 ... done
Removing network rails_default
Restart the application

To restart the application run docker compose up in the project directory.

Rebuild the application

If you make changes to the Gemfile or the Compose file to try out some different configurations, you need to rebuild. Some changes require only docker compose up --build, but a full rebuild requires a re-run of docker compose run web bundle install to sync changes in the Gemfile.lock to the host, followed by docker compose up --build.

Here is an example of the first case, where a full rebuild is not necessary. Suppose you simply want to change the exposed port on the local host from 3000 in our first example to 3001. Make the change to the Compose file to expose port 3000 on the container through a new port, 3001, on the host, and save the changes:

ports:
  - "3001:3000"
Now, rebuild and restart the app with docker compose up --build.

Inside the container, your app is running on the same port as before 3000, but the Rails Welcome is now available on http://localhost:3001 on your local host.
- Install rack-cors, and add your appropriate origins:
  - http://localhost:8000, http://app:8000
- Within config/environments/development, add config.hosts.clear to the bottom of the file
