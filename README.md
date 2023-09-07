# Rallly Self-Hosting Example

This repository contains all the necessary information and files to self-host your own instance of Rallly. Rallly is an open-source scheduling and collaboration tool designed to make organizing events and meetings easier.

## Table of Contents

- [Requirements](#requirements)
- [Version Management](#version-management)
- [Using a Reverse Proxy](#using-a-reverse-proxy)
- [Update Instructions](#update-instructions)
- [Links](#links)

## Requirements

To run this project you will need:

- Docker
- Access to an SMTP server
- x86-64 Architecture ([arm64 support has been suspended](https://github.com/lukevella/rallly/discussions/568))

## Version Management

Rallly uses semantic versioning and releases are published as tags on [Docker Hub](https://hub.docker.com/r/lukevella/rallly).
You can use `latest` to get the most recent release but it is recommended that you pin the image to a major version to avoid accidentally pulling in breaking changes.
You can set the version in `docker-compose.yml` by changing the following line:

```
- image: lukevella/rallly:<version>
```

Check the [releases](https://github.com/lukevella/rallly/releases) to see what versions are available.
We follow semver versioning so you may want to set your version to a major release (e.g. `lukevella/rallly:3`) to avoid pulling in breaking changes.

## Setup Instructions

### 1. Clone the repository

```
git clone https://github.com/lukevella/rallly-selfhosted.git
cd rallly-selfhosted
```

### 2. Add required config

In the root of this project you will find a file called `config.env`.
This is where you can set your environment variables to configure your instance.
This guide will go through the basics but, you can check out the full list of [configuration options](https://support.rallly.co/self-hosting/configuration-options) in the [self-hosting docs](https://support.rallly.co/self-hosting).

Start by generating a secret key. **Must be at least 32-characters long**.

```sh
openssl rand -base64 32
```

Open `config.env` and set `SECRET_PASSWORD` to your secret key.

Next, set `NEXT_PUBLIC_BASE_URL`. It should be the base url where this instance is accessible, including the scheme (eg. `http://` or `https://`), the domain name, and optionally a port. **Do not use trailing slashes or URLs with paths/subfolders.**.

### 3. Configure your SMTP Server

First, set `SUPPORT_EMAIL`. Your users will see this as the contact email for any support issues and it will also appear as the sender of all emails.

Next, use the following environment variables to configure your SMTP server:

- `SMTP_HOST` - The host address of your SMTP server
- `SMTP_PORT` - The port of your SMTP server
- `SMTP_SECURE` - Set to "true" if SSL is enabled for your SMTP connection
- `SMTP_USER` - The username (if auth is enabled)
- `SMTP_PWD` - The password (if auth is enabled)

### 4. Secure your instance

By default, anyone can register and login on your instance.
You can restrict users by setting `ALLOWED_EMAILS`.

If only you are planning on using this instance you can set `ALLOWED_EMAILS` to your email address.

```
ALLOWED_EMAILS="john.doe@example.com"
```

If you would like to allow multiple users, you separate each email address with a comma.

```
ALLOWED_EMAILS="john.doe@example.com,jane.doe@example.com"
```

You can also use wildcards to allow all emails from a domain.

```
ALLOWED_EMAILS="*@example.com"
```

### 6. Start the server

You can start the server by running:

```
docker compose up -d
```

This command will:

- Create a postgres database
- Run migrations to set up the database schema
- Start the Next.js server on port 3000

## Using a Reverse Proxy

By default the app will run unencrypted on port 3000. If you want to serve the app over HTTPS you will need to use a [reverse proxy](/reverse-proxy/README.md).

> After setting up a reverse proxy be sure to change this line `- 3000:3000` to - `127.0.0.1:3000:3000` in `docker-compose.yml` and restart the container for it to apply changes. This prevents Rallly from being accessed remotely using HTTP on port 3000 which is a security concern.

## Update Instructions

Rallly is constantly being updated but you will need to manually pull these updates and restart the server to run the latest version. You can do this by running the following commands from within this directory:

```sh
docker compose down
docker compose pull
docker compose up -d
```

## Links

- [Source code](https://github.com/lukevella/rallly)
