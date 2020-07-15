# Run Fathom on Dokku

This repo guides you on how to run [Fathom](https://usefathom.com/) on your [Dokku](http://dokku.viewdocs.io/dokku/).

### What is Fathom?

> Fathom is a simple, light-weight, privacy-focused alternative to Google Analytics. So, stop scrolling through pages of reports and collecting gobs of personal data about your visitors, both of which you probably don’t need. Our website stats show up on a single, blazingly fast dashboard so you can make business decisions quickly. - [Fathom website](https://usefathom.com/)

### What is Dokku?

> The smallest PaaS implementation you've ever seen. Dokku helps you build and manage the lifecycle of applications - [Dokku website](http://dokku.viewdocs.io/dokku/)

## Prerequisites

- [Dokku](http://dokku.viewdocs.io/dokku/)
- [Dokku Postgres plugin](https://github.com/dokku/dokku-postgres)
- [Dokku's Letencrypt plugin](https://github.com/dokku/dokku-letsencrypt)

## Setup

### Clone this repo

```
git clone git@github.com:alvinsj/fathom_on_dokku.git
```

### Create a new Dokku app

```
cd fathom_on_dokku
dokku apps:create fathom
```

### Create a new postgres service

```
dokku postges:create fathom
```

### Link postgres to the app

```
dokku postgres:link fathom
```

`DATABASE_URL` will be added to `dokku config`.

### Configure Fathom with environment variables

```
dokku config:set FATHOM_DATABASE_DRIVER=postgres
dokku config:set FATHOM_DATABASE_HOST=dokku.postgres.fathom:5432
dokku config:set FATHOM_DATABASE_NAME=fathom
dokku config:set FATHOM_DATABASE_PASSWORD=<password>
dokku config:set FATHOM_DATABASE_USER=postgres
dokku config:set FATHOM_GZIP=true
dokku config:set FATHOM_SECRET=$(echo `openssl rand -base64 45` | tr -d \=+ | cut -c 1-32)
dokku config:set FATHOM_SERVER_ADDR=:8080
```

You can find the password in `DATABASE_URL`. e.g. `postgres://postgres:<password>@dokku-postgres-fathom:5432/fathom`

### Deploy

```
git push dokku master
```

#### Troubleshooting

In case of deployment error, check Docker options to make sure the option `--link` is correct.

```
dokku docker-options:report
```

You should see

```
=====> fathom docker options information
       Docker options build:          --link dokku.postgres.fathom:dokku-postgres-fathom
       Docker options deploy:         --link dokku.postgres.fathom:dokku-postgres-fathom
       Docker options run:            --link dokku.postgres.fathom:dokku-postgres-fathom
```

and make sure the config is set correctly to `FATHOM_DATABASE_HOST=dokku.postgres.fathom:5432`

### Letsencrypt

```
dokku letsencrypt
```

### Create user

Enter fathom docker container

```
dokku enter fathom
```

Create a user

```
./fathom user add --email=<login email> --password=<login password>
```

### Done

Your fathom instance should be avaiable on `https://fathom.<your domain name>`

## Reference:

- [Host your own analytics with Fathom](https://www.kn8.lt/blog/hosting-your-own-analytics-with-fathom/)
- [Grafana on Dokku](https://github.com/D1ceWard/grafana_on_dokku)
