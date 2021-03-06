# Server configuration

## Setup
```bash
SITE=tom.moulard.org docker-compose up -d
```

Now you have my own server configuration.

To be a little more consistent with the management, you can use a `.env` file and do:
```bash
cp .env.default .env
```

and edit the file to use the correct site url.

### Tear down
```bash
docker-compose down
```

## TODO
### New ideas
 - [X] traefik
 - [X] gitlab
    - [X] CI/CD worker(s)
 - [X] nextcloud
 - [X] nginx
 - [X] weechat
 - [X] transmission
 - [X] vpn
 - [X] jupyter
 - [ ] readthedoc / [DokuWiki](https://hub.docker.com/r/mprasil/dokuwiki)
 - [X] pastebin
 - [ ] image / screenshot hosting
 - [ ] [hackmd](https://github.com/hackmdio/docker-hackmd) [main repo](https://github.com/hackmdio/codimd)
 - [X] [prometheus](https://www.brianchristner.io/how-to-monitor-traefik-reverse-proxy-with-prometheus/) / [EFK](https://docs.fluentd.org/v0.12/articles/docker-logging-efk-compose) / [filebeat](https://medium.com/the-sysadmin/visualize-traefik-logs-in-kibana-c53fb2aac070) / ELK
 - [ ] proxy
 - [ ] [RSS agregator server](https://www.freshrss.org/)
 - [ ] url shortener
 - [ ] factorio server
 - [ ] news group server
 - [ ] vlc server
 - [X] blog using [hugo](http://gohugo.io)
 - [ ] MOOC
 - [ ] Latex online editor
 - [X] [Bazarr](https://hub.docker.com/r/linuxserver/bazarr) (subs), [lidarr](https://hub.docker.com/r/linuxserver/lidarr) (music), [sonarr](https://hub.docker.com/r/linuxserver/sonarr) (shows), [jackett](https://hub.docker.com/r/linuxserver/jackett) (interface)
 - [X] Bitwarden

[more](https://github.com/Kickball/awesome-selfhosted)

### List
 - [ ] which database ? maria / mysql / mongo / postgres
    - [ ] gitlab postgresSQL / MySQL - MariaDB
    - [ ] nextcloud postgresSQL / MySQL - MariaDB / Oracle
 - [X] nginx.conf
 - [ ] create a git repository auto in gitlab for // FIXME
 - [ ] Create a Dockerfile for a mail server
 - [X] reverse proxy with ssl
 - [ ] multi files configuration
 - [ ] Testing
    - [X] traefik
    - [X] gitlab
    - [X] nextcloud
    - [X] nginx
    - [ ] weechat
    - [X] transmission
    - [X] vpn
    - [X] jupyter
    - [X] pastebin
    - [X] ELK
    - [ ] sharelatex / Overleaf
    - [X] blog

### Configuration files
 - [ ] have default configuration files
    - [X] traefik
    - [X] gitlab
    - [X] gitlab runner
    - [X] transmission
    - [ ] pastebin
    - [ ] nextcloud
    - [X] nginx
    - [X] ELK (WIP)

## Configuration
Don't forget to change db passwords. (migth not be needed since they are beyond
the reverse proxy).
Fill vpn secrets(if none provided, they are generated directly).
Configuration files are: `docker-compose.yml`, `nginx.conf`

To set the password:
```bash
echo "USERS=$USER:$(openssl passwd -apr1)" >> .env
```

You can add a new set of credentials by editing the .env file like
```env
USERS=toto:pass,tata:pass, ...
```

### Scalling up
```bash
docker-compose scale nginx=2
```

### Adress table
| Status | Address | port(s)|
|:--:|--|--|
| [X] | traefik.${SITE} | 80, 443 (redirect 80 to 443) |
| [X] | gitlab.${SITE} | 22, 80, 443 |
| [X] | cloud.${SITE} | 80, 443 |
| [X] | ${SITE} | 80, 443 |
| [ ] | mail.${SITE} | 25(recv mail), 465(ssl), 587(TLS), 143(IMAP), 993(IMAP), 110(POP3), 995(POP3) |
| [X] | torrent.${SITE} | 80, 443 (redirect 80 to 443) |
| [X] | vpn.${SITE} | 500, 4500 |
| [X] | jupiter.${SITE} | 80, 443 (redirect 80 to 443) |
| [X] | paste.${SITE} | 80, 443 (redirect 80 to 443) |
| [X] | video.${SITE} | 80, 443 (redirect 80 to 443) |
| [ ] | irc.${SITE} | ?? |

### Gitlab runner
#### Get the Registration Token
Find your runner registration token ($REGISTRATION_TOKEN) at `http://GITLAB_HOST/$PROJECT_GROUP/$PROJECT_NAME/settings/ci_cd`.

There is **two** way to register the runner:

##### Register via config file
Register the Registration Token to have a Runner Token
```bash
curl -X POST 'http://gitlab.${SITE}/api/v4/runners' --form 'token=$REGISTRATION_TOKEN' --form 'description=The Best Runner'
```

###### Change runner configuration
Now change the token in the [configuration file](gitlab/runner/config.toml).
```toml
[[runners]]
    token = "XXXXXXXXXXXXXXXXXXXX"
```
and run the runner
```bash
docker-compose up -d runner
```

##### Register via CLI
Steps:
 - up the runner `docker-compose up -d runner`
 - register the runner
```bash
docker-compose exec runner gitlab-runner register \
    --non-interactive \
    --executor "docker" \
    --docker-image alpine:latest \
    --url "http://gitlab/" \
    --registration-token "$REGISTRATION_TOKEN" \
    --description "The Best Runner" \
    --tag-list "docker,aws" \
    --run-untagged="true" \
    --locked="false" \
    --access-level="not_protected"
```

# Authors
Main author:
 - [Tom](http://tom.moulard.org)

Gitlab helper:
 - [michel_k](mailto:thomas.michelot@epita.fr)
