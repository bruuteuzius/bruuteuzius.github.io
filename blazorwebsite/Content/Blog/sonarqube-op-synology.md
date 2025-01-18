---
title: SonarQube op Synology
published: 2021-04-08
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

In deze post leg ik vast hoe ik SonarQube aan de praat heb gekregen voor mijn C# .Net Core project waarin ik wedstrijdplanning voor de volleybalvereniging automagisch maak. Er is wel een project met tests, maar ik heb al een tijdje geen nieuwe tests meer gemaakt voor nieuwe code en heb geen idee wat de coverage is.

Eerst maar eens beginnen met lokaal de Code Coverage in te regelen. Hiervoor begin ik in het Test project met :

```
dotnet add package coverlet.msbuild   
```

Daarna kun je een output file genereren met :

```
dotnet test VolleybalTasks.sln /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
```

De output ziet er dan zo uit :

![](media/afbeelding.png)

Dat is best schrikken... Niet echt veel... gemiddeld 18% coverage op methods. Maar ik wil meer informatie en die kan SonarQube me geven.

Om SonarQube te installeren op mijn NAS met een docker container heb ik de volgende docker-compose gemaakt :

```
version: "3"

services:
  sonarqube:
    image: sonarqube:8-community
    depends_on:
      - db
    environment:
      SONAR_SEARCH_JAVAADDITIONALOPTS: "-Dbootstrap.system_call_filter=false"
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

Ik heb ook geprobeerd om expliciet volumes te benoemen maar dan kreeg ik foutmeldingen dat de rechten op die folders niet kloppen. Verder kwam ik nog deze melding tegen in de logging tijdens het opstarten van SonarQube (waardoor hij weer stopte) :

```
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

Dit heb ik opgelost door op de Synology host dit commando uit te voeren :

sudo sysctl -w vm.max\_map\_count=262144

Hierna zou de SonarQube docker container (en de postgres container) moeten starten en kun je de installatie van SonarQube doorlopen.

Eerst had ik nog geprobeerd om github aan te sluiten, maar daarvoor zou ik volgens mij mn SonarQube installatie naar buiten moeten openstellen en daar heb ik geen zin in. Dus dan maar een "manual project" starten.

Je project krijgt een naam en een token, die je vervolgens (lokaal, dus niet op de NAS) kan gebruiken om een connectie te leggen met de SonarQube installatie. Ik heb de commando's samengevat in een shell script :

```
#!/bin/bash
dotnet tool install --global dotnet-sonarscanner
dotnet sonarscanner begin /k:"VolleybalTasks" /d:sonar.host.url="http://192.168.178.202:9000" /d:sonar.login="projecttoken" /d:sonar.cs.opencover.reportsPaths="VolleybalTasks.Tests/coverage.opencover.xml"
dotnet build VolleybalTasks.sln
dotnet test VolleybalTasks.sln /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
dotnet sonarscanner end /d:sonar.login="projecttoken"
rm -rf .sonarqube
```

Die laatste regel is nodig om de volgende run te kunnen uitvoeren met een schone lei.

Met een `chmod u+x sonarqube.sh` maak ik het script uitvoerbaar.

If all goes well, kom ik uiteindelijk op iets meer informatie over mijn c# .net core project :

![](media/afbeelding-1.png)

En dan kan ik doorklikken op de 16,4% coverage voor meer info :

![](media/afbeelding-2.png)

Da's mooi! Nouwja dat het werkt dan, de coverage is om te huilen. Nu kan ik aan de slag, maar ik wil niet iedere keer handmatig en lokaal het sonarqube.sh script runnen. Het liefst wil ik dit in Jenkins stoppen, dus daar gaat een andere blogpost over 😜
