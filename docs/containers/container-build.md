---
layout: default
title:  "Bygg en container från en app"
parent: "Containers"
nav_order: 2
---
# Paketera en applikation i en container
{: .no_toc }

<details open markdown="block">
  <summary>
    Innehåll
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

## Inledning

För att visa hur det går till att paketera en applikation i en container kommer vi att utgå från Dockers egna introduktionsexempel. Det är en enkel To Do-app som är skriven i node.js. 

![VS Code](/assets/images/app_folder.png)

Så här ser den ut när den kör lokalt på datorn (Windows med Ubuntu WSL):

![Running app](/assets/images/running_app.png)

Applikationen har en hel del beroenden för att kunna köra. Det enklaste är att först installera node.js om man inte redan gjort det. Därefter kan man använda npm (package manager för node) och installera det man behöver:

- sqlite3
- body-parser
- uuid

Skapa därefter en tom file i /etc/todos/todo.db så ska applikationen starta.

## Dockerfile

Men vi vill ju enkelt kunna distribuera och köra applikationen även i andra system. Därför ska vi nu paketera den i en container. Det första vi gör är att skapa en fil som heter `Dockerfile` i samma mapp som package.json med följande innehåll:

```
# syntax=docker/dockerfile:1
FROM node:12-alpine
RUN apk add --no-cache python g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

Vad denna fil säger är att vi vill utgå från en existerande container som heter node:12-alpine. Det är en redan paketerad Alpine Linux med node.js installerad. Vi kan inspektera den genom att köra `docker run -it node:12-alpine /bin/ash` 

![alpine image](/assets/images/alpine-image.png)

Det öppnar ett shell där vi kan kolla filsystemet med `ls -a` och även bekräfta att node.js är installerat.

I Dockerfile ser vi sen att 3 stycken program blir installerade med apk (Alpine Linux Package Manager), python, g++ och make.
WORKDIR sätts till `/app`. Notera att `/app` inte fanns när med när vi listade innehållet i vår image.

Därefter kopierar vi allt i . (nuvarande bibliotek) till `/app`.
Sen kör vi yarn som är en annan package manager lik npm. Den läser alla dependencies i package.json och installerar de som saknas.

Slutligen så startar vi applikationen med node och pekar på index.js i /app/src biblioteket. 

## Docker build

Nu är det dags att bygga själva containern och det gör man med `docker build -t to-do .`. "to-do" är namnet vi ger containern och -t är flaggan som låter oss sätta namnet. Vi avslutar med "." för att visa att vi ska bygga i nuvarande bibliotek.

```bash
anders@DESKTOP-LSQIE59:~/getting-started/app$ docker build -t to-do .
[+] Building 46.0s (14/14) FINISHED
 => [internal] load build definition from Dockerfile                                                        0.0s
 => => transferring dockerfile: 213B                                                                        0.0s
 => [internal] load .dockerignore                                                                           0.1s
 => => transferring context: 2B                                                                             0.0s
 => resolve image config for docker.io/docker/dockerfile:1                                                 10.7s
 => docker-image://docker.io/docker/dockerfile:1@sha256:9e2c9eca7367393aecc68795c671f93466818395a2693498de  2.0s
 => => resolve docker.io/docker/dockerfile:1@sha256:9e2c9eca7367393aecc68795c671f93466818395a2693498debe83  0.0s
 => => sha256:9e2c9eca7367393aecc68795c671f93466818395a2693498debe831fd67f5e89 2.00kB / 2.00kB              0.0s
 => => sha256:cb8d6fa06268f199b41ccc0c2718caca915f74c640f3cd044af8e205e8d616cf 528B / 528B                  0.0s
 => => sha256:b1e2fdbfa8cb359e5e566c70344df94749db2e6419e06ac542ed14a348c3ce81 1.21kB / 1.21kB              0.0s
 => => sha256:8d9a8cad598f6c5e97bcb90aab70cd2e868d3dc0d514fa9b60468bf72bf32338 9.67MB / 9.67MB              1.3s
 => => extracting sha256:8d9a8cad598f6c5e97bcb90aab70cd2e868d3dc0d514fa9b60468bf72bf32338                   0.4s
 => [internal] load .dockerignore                                                                           0.0s
 => [internal] load build definition from Dockerfile                                                        0.0s
 => [internal] load metadata for docker.io/library/node:12-alpine                                           0.0s
 => [1/5] FROM docker.io/library/node:12-alpine                                                             0.4s
 => [internal] load build context                                                                           2.3s
 => => transferring context: 64.99MB                                                                        2.0s
 => [2/5] RUN apk add --no-cache python g++ make                                                            9.2s
 => [3/5] WORKDIR /app                                                                                      0.1s
 => [4/5] COPY . .                                                                                          1.5s
 => [5/5] RUN yarn install --production                                                                    18.8s
 => exporting to image                                                                                      2.3s
 => => exporting layers                                                                                     2.3s
 => => writing image sha256:f5a335e9e60bd050b83e6dd19bf905583dbc51843c31a393b605fdcfac4a5aaa                0.0s
 => => naming to docker.io/library/to-do                                                                    0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

Nu har vi byggt vår container och kan starta den med `$ docker run -d -p 3000:3000 to-do` där -d betyder att vi vill köra containern i bakgrunden (daemon) och -p är att vi kopplar containers port 3000 till 3000 på hosten. Därmed kan vi komma åt applikationen i en browser på localhost:3000 på precis samma sätt som när vi körde lokalt.

Om vi vill dela vår container med andra eller köra den på ett annat system så kan vi spara vår image i en tar-fil:

```bash
    $ docker image save to-do -o to-do-container.tar
```

Nu har vi en fil som vi kan dela med oss av. Men ett ännu enklare sätt är att ladda upp vår image till ett så kallat registry där det enklaste, beroende på vilka krav man har, är att använda Docker Hub som är integrerat med docker-verktygen.