# Solr Forward Authentication Plugin

[![continuous integration](https://github.com/solr-cool/solr-forward-authentication-plugin/actions/workflows/ci.yaml/badge.svg)](https://github.com/solr-cool/solr-forward-authentication-plugin/actions/workflows/ci.yaml)
[![Maven Central](https://img.shields.io/maven-central/v/cool.solr/solr-forward-authentication-plugin)](https://search.maven.org/artifact/cool.solr/solr-forward-authentication-plugin/)


A simple forward authentication plugin for Solr. _Forward authentication_ moves
the authentication process out of Solr into a reverse proxy like
[Traefik](https://doc.traefik.io/traefik/middlewares/http/forwardauth/) or
[Nginx](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-subrequest-authentication/) running in front of Solr.

After authentication, the authenticated user is sent to Solr via a HTTP header.
This plugins lets Solr accept this header and set the authenticated user
accordingly.


![Forward Authentication](https://doc.traefik.io/traefik/assets/img/middleware/authforward.png)

## How to use the plugin

> Before using the plugin, please be familiar with
> [Solr authentication and authorization](https://solr.apache.org/guide/8_11/authentication-and-authorization-plugins.html).

### Install the plugin

Drop the
[release jar](https://github.com/solr-cool/solr-forward-authentication-plugin/releases)
into the library directory of your Solr installation.

### Configure authentication

```json
{
    "authentication": {
        "class": "cool.solr.security.ForwardAuthPlugin",
        "httpUserHeader": "X-Forwarded-User"
    }
}
```

### Configure authorization

```json
{
    "authentication": {
        "class": "cool.solr.security.ForwardAuthPlugin",
        "httpUserHeader": "X-Forwarded-User"
    },
    "authorization": {
        "class": "cool.solr.security.DefaultRuleBasedAuthorizationPlugin",
        "defaultRole": "admin",
        "permissions": [
            {
                "name": "all",
                "role": "admin"
            }
        ]
    }
}
```

### Example

The [`examples`](examples/) folder contains a simple Docker Compose ensemble.
From inside the directory, launch the Solr/Zookeeper ensemble:

```shell
$ docker-compose up

# Test connectivity (should return 200 OK)
$ curl -I http://localhost:8983/solr/ping

# Activate security
$ docker exec -it solr solr zk cp file:/opt/solr/server/solr/security.json zk:/security.json -z zookeeper:2181
```

## Building the project

This should install the current version into your local repository

    $ ./mvn clean verify

## License

This project is licensed under the [Apache License, Version 2](http://www.apache.org/licenses/LICENSE-2.0.html).
