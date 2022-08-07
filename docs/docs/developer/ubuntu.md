---
id: ubuntu
title: Ubuntu
sidebar_label: Ubuntu
---
---


## Preparing the installation

The Erxes installation guide requires at least four software prerequisites to be already installed on your computer:

- <a href="https://github.com/git-guides/install-git" target="_blank">Git</a>
- [Node.js](https://nodejs.org): only LTS versions are supported (v14 and v16). Other versions of Node.js may not be compatible with the latest release of erxes. The 14.x version is most recommended by erxes.
- [npm](https://docs.npmjs.com/cli/v6/commands/npm-install) (v6 only) or [yarn](https://yarnpkg.com/getting-started/install) to run the CLI installation scripts.
- <a href="https://docs.docker.com/engine/install/">Docker</a>


### Installing erxes


1. in terminal, run the following command:
```
git clone git@github.com:erxes/erxes.git
```

2. switch a federation branch
```
git checkout federation
```

3. installing node modules
```
cd erxes
yarn install
```

4. installing pm2
```
sudo npm install -g pm2
```

### Installing dependencies using docker

include the below scripts in docker-compose.yml

```
version: '3.6'
services:
  mongo:
    hostname: mongo
    image: mongo:4.0.10
    # container_name: mongo
    ports:
      - "27017:27017"
    networks:
      - erxes-net
    healthcheck:
      test: test $$(echo "rs.initiate().ok || rs.status().ok" | mongo --quiet) -eq 1
      interval: 2s
      timeout: 2s
      retries: 200
    command: ["--replSet", "rs0", "--bind_ip_all"]
    extra_hosts:
      - "mongo:127.0.0.1"
    volumes:
      - ./data/db:/data/db

  redis:
    image: 'redis'
    # container_name: redis
    # command: redis-server --requirepass pass
    ports:
      - "6379:6379"
    networks:
      - erxes-net

  rabbitmq:
    image: rabbitmq:3.7.17-management
    # container_name: rabbitmq
    restart: unless-stopped
    hostname: rabbitmq
    ports:
      - "15672:15672"
      - "5672:5672"
    networks:
      - erxes-net
    # RabbitMQ data will be saved into ./rabbitmq-data folder.
    volumes:
      - ./rabbitmq-data:/var/lib/rabbitmq

networks:
  erxes-net:
    driver: bridge
```

Тэгээд уг файл байгаа хавтас дотороо доорх коммандыг ажиллуулах

```
docker-compose up -d
```

### Running erxes

```
cd erxes/cli
yarn install
```

configs.json.sample файлыг copy - хийж configs.json файл болгож доорх байдлаар тохируулна.

```
{
	"jwt_token_secret": "token",
	"client_portal_domains": "",
	"elasticsearch": {},
	"redis": {
		"password": "pass"
	},
	"mongo": {
		"username": "",
		"password": ""
	},
	"rabbitmq": {
		"cookie": "",
		"user": "",
		"pass": "",
		"vhost": ""
	},
	"plugins": [
		{
			"name": "logs"
		}
	]
}
```


Дараах коммандаар асаана.
```
./bin/erxes.js dev --bash --deps
```