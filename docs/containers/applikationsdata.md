---
layout: default
title:  "Applikationsdata och containers"
parent: "Containers"
nav_order: 3
---

# Applikationsdata och containers

En av poängerna med containers är att de ska vara ett lätt sätt att paketera, distribuera och köra applikationer. Det ska gå att snabbt skala ut genom att man helt enkelt startar flera. Därför sparar man normalt inte applikationsdata direkt i en container då datan inte kommer att vara en del av container-imagen.

Lösningen på detta är att använda bind mounts eller volumes för data som man vill spara:

- bind mounts - Här delar containern en filyta med host-systemet. Det innebär att både container och andra processer kan uppdatera filerna. Vid systemutveckling kan det vara användbart då man kan uppdatera en applikation utanför en container. Detta är det ursprungliga sättet att spara data i containers. 
- volumes - detta är det rekommenderade sättet då containern, eller egentligen runtime, kontrollerar access till filerna.

## Databas som exempel

Låt oss ta en databas som exempel. Vi börjar med att utgå från vår desktop där vi har installerat docker. Databasen vi behöver är en graph-databas och vi väljer neo4j och att följa deras [developer guide](https://neo4j.com/developer/docker-run-neo4j/) . Vi kör följande kommando i vårt bash-shell:

```bash
docker run \
    --name cspeardb \
    -p7474:7474 -p7687:7687 \
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/neo4j/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    --env NEO4J_AUTH=neo4j/br1tn3y \
    neo4j:latest
```

En snabb genomgång av vad ovanstående betyder:

- `docker run` startar vår container
- `--name` sätter namnet på containern till cspeardb vilket är valt efter projekt/applikation
- `-p7474 ...` sätter vilka portar vi ska använda för att komma åt databasen där 7474 är HTTP och 7687 för Bolt (neo4j)
- `-d` säger att vi vill köra i bakgrunden (daemon)
- `-v $HOME/ ...` -v är kortvarianten av -mount och sätter var vi vill att data ska skrivas

När containern är uppe och kör så kan vi passa på att kolla konfigurationen med `docker inspect cspeardb`

```bash
[
    {
        "Id": "0070133a3610554ba1639476b82a121fd01d00e27e56ac65c33df5a3a0455b26",
        "Created": "2021-09-02T12:43:59.4902831Z",
        "Path": "/sbin/tini",
        "Args": [
            "-g",
            "--",
            "/docker-entrypoint.sh",
            "neo4j"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
```
Scrolla ner till noden för "Mounts" och där kan vi se att vad vi skapat är av typen `bind`. Det innebär alltså att det är vår host som kontrollerar datafilerna. Just för en databas så är detta kanske inte en fördel men om man t ex vill använda sin vanliga IDE (typ VS Code) för att koda direkt i containern så passar är detta bra då vi kan låta processer även utanför vår container ha åtkomst till datan.

```bash
...
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/anders/neo4j/data",
                "Destination": "/data",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
...
```

## bind till volume

För att istället för bind mount använda volume så är kommandot i princip detsamma. Skillnaden ligger i att men med hjälp av Docker först skapar och registrerar en volume.

```bash
$ docker volume create neo4j
```

En volume hanteras av docker och kan delas mellan containers. Använd `docker volume ls` för att lista de volumes som finns tillgängliga på hosten. Om vi nu kör:

```bash
docker run \
    --name cspeardb_vol \ 
    -p7474:7474 \
    -p7687:7687 \
    -d \
    -v neo4j:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    --env NEO4J_AUTH=neo4j/br1tn3y 
    neo4j:latest
```
Notera att vi på första -v bara refererar till volume-namnet. För att bekräfta att det fungerat så kollar vi docker inspect och ser att Type nu är volume:

```bash
        "Mounts": [
            {
                "Type": "volume",
                "Name": "neo4j",
                "Source": "/var/lib/docker/volumes/neo4j/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
```

## Sammanfattning

I bägge fallen ovan har vi skapat ett sätt att spara data även om containerns tas bort. Det rekommenderade sättet att göra det på är så kallad volume. Dvs man skapar med hjälp av docker en volume som man kopplar till containern. Men om man till exempel för att kunna koda direkt in i en container vill kunna läsa och skriva data utanför så är en bind mount att föredra.