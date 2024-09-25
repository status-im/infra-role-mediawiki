# Description

This role deploys and instance of [MediaWiki](https://www.mediawiki.org/wiki/MediaWiki), which is a collaborative editing software written in PHP.

# Configuration

A bare minimum configuration would include:
```yaml
mediawiki_domain: 'wiki.example.org'
mediawiki_email: 'devops@example.org'
mediawiki_sitename: 'Logos'
mediawiki_bitnami_debug: false
mediawiki_admin_username: 'admin'
mediawiki_admin_password: 'super-secret-admin-password'
mediawiki_db_pass: 'super-secret-db-password'
mediawiki_secret_key:  'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'
mediawiki_upgrade_key: 'bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb'
```
You should also configure email sending:
```yaml
mediawiki_smtp_enabled: true
mediawiki_smtp_host: 'smtp.example.org'
mediawiki_smtp_port: 587
mediawiki_smtp_user: 'username'
mediawiki_smtp_password: 'super-secret-password'
```
In addition custom skinds and assets are installed using:
```yaml
mediawiki_skin_repo_url: 'https://github.com/example-org/mediawiki-skin.git'
```
Unfortunately this does not support an actual skin repo but instead assumes the repo contains a folders called `Vector`, `languages` and `resources`. This of course makes absolutely no sense, but it is what it is.

# Management

## Service

Deployment and management is done using [Docker Compose](https://docs.docker.com/compose/):
```
admin@node-01.do-ams3.wiki.logos:/docker/mediawiki % docker-compose ps
    Name                   Command                  State                      Ports
---------------------------------------------------------------------------------------------------
mediawiki-app   /opt/bitnami/scripts/media ...   Up             0.0.0.0:8080->8080/tcp, 8443/tcp
mediawiki-db    docker-entrypoint.sh --per ...   Up (healthy)   127.0.0.1:3306->3306/tcp, 33060/tcp
```

## Users

Two plugins have been installed to manage users: `` and ``

They make available two special pages under:

* https://wiki.example.org/wiki/Special:UserManager
* https://wiki.example.org/wiki/Special:GroupManager

# Known Issues

The [initialization scripts](https://github.com/bitnami/containers/blob/fd485db5011eca0c92b4d630c0328ae56236652c/bitnami/mediawiki/1/debian-11/rootfs/opt/bitnami/scripts/libmediawiki.sh#L113-L121) in `bitnami` Docker image do not run [`install.php`](https://github.com/wikimedia/mediawiki/blob/1.40.0/maintenance/install.php) if any volumes under `/bitnami/mediawiki` are mounted. For that reason we do not add the volumes at first startup, and allow the bootstrapping process - especially database schema creation - to run. Once that is done we can create and mount our own config and skins.

# Links

* https://github.com/status-im/infra-logos-office/issues/13
