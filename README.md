# Directus & Monk

This repository contains Monk.io template to deploy Directus system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Redis cache and Postgres database configured to work with Directus out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Local Deployment

This template is available directly from Monkhub.io therefore if you want a quick deploy simply run below command after launching `monkd`:

```bash
➜  monk list directus
✔ Got the list
Type      Template           Repository  Version  Tags
runnable  directus/cache     local       -        -
runnable  directus/database  local       -        -
runnable  directus/directus  local       -        -
group     directus/stack     local       -        -

➜  monk run directus/stack

✔ Started directus/stack
```

This will start the entire Directus stack with a database and cache. Running `directus/stack` will always pull the latest Directus release.

## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name directus-deployment
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add
? Cloud provider gcp
? GOOGLE_APPLICATION_CREDENTIALS is set. Try load it? Yes
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Name for the new instance my-instance
? Tags (split by whitespace) directus
? Instance region (gcp) europe-west2
? Instance zone (gcp) europe-west2-c
? Instance type (gcp) e2-standard-2
? Disk Size (GBs) 60
? Number of instances (or press ENTER to use default = 1) 2
✔ Creating a new instance(s) on gcp...
✔ Syncing nodes DONE
✔ Cluster grown successfully
```

Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).

```bash
➜  monk run directus/stack
? Select tag to run on: directus
```

## Configuration

You can add/remove or override current configuration of the template or create a brand new one which could inherit the components that you require.

The current variables can be found in `directus/stack/variables` section

```yaml
variables:
    defines: variables
    postgres-user:
        type: string
        value: "directus"
    postgres-password:
        type: string
        value: "directus"
    postgres-db:
        type: string
        value: "directus"
    admin-email:
        type: string
        value: "admin@admin.admin"
    admin-password:
        type: string
        value: "d1r3ctu5"
    key:
        type: string
        value: "255d861b-5ea1-5996-9aa3-922530ec40b1"
    secret:
        type: string
        value: "6116487b-cda1-52c2-b5b5-c8022c45e263"
```

**It is STRONGLY advised to change these values when running Directus in production!**

To override the settings or add new ones for your own template you can inherit it in this way:

```yaml
namespace: my-directus

directus-prod:
    defines: process-group
    inherits: directus/stack
    variables:
        postgres-db-user: secret-user
        postgres-db-password: my-directus-postgres-pass
        admin-email: admin@yoursite.io
        admin-password: hacker-typer
        key: 355d861b-6ea1-5996-9aa3-922530ec40b2
        secret: 1546487b-asd1-52c2-b5b5-c8022c45e462
```

Apart from the variables exposed by Monk, you can tune all Directus settings by providing a JSON config in `config` variable like so:

```yaml
namespace: my-directus

directus-prod:
    defines: process-group
    inherits: directus/stack
    variables:
        postgres-db-user: secret-user
        postgres-db-password: my-directus-postgres-pass
        admin-email: admin@yoursite.io
        admin-password: hacker-typer
        key: 355d861b-6ea1-5996-9aa3-922530ec40b2
        secret: 1546487b-asd1-52c2-b5b5-c8022c45e462
        # --- example:
        config: |
            {
              "STORAGE_LOCATIONS": "mystorage",
              "STORAGE_MYSTORAGE_DRIVER": "local",
              "STORAGE_MYSTORAGE_ROOT": "/directus/mystorage"
            }
```

Learn more about Directus environment variables here: [Environment Variables](https://docs.directus.io/reference/environment-variables/)

## Upgrading

To update a running instance of `directus/stack` just do:

```bash
monk update directus/stack
```

This will always pull the latest available version and replace the running one seamlessly.

### Logs & Shell

```bash
# show Directus logs
➜  monk logs -l 1000 -f directus/latest

# access shell in the container running Directus
➜  monk shell directus/latest
```
