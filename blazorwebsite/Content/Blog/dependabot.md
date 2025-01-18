---
title: Dependabot
published: 2020-06-15
tags: [code] 
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Voor onze volleybalvereniging heb ik een webapp gemaakt waarmee we vrij eenvoudig een wedstrijdplanning kunnen genereren voor de thuiswedstrijden. Het is een .NET Core 3.1 project met een paar nuget packages. Om de zoveel tijd komt er een update voor 1 van die packages en dat vond ik nogal een gedoe om steeds met de hand te updaten.

Gelukkig is daar iets voor, namelijk [Dependabot](https://dependabot.com/). Als je zoals ik github gebruikt, kun je die extension daar zo installeren en krijg je netjes pull requests voor je updates :

![](media/dependabot.png)

Goh, valt me nu pas op dat ik een nuget package voor EntityFrameworkCore in mijn project heb zitten, maar die gebruik ik helemaal niet want ik heb NHibernate... Hmmmz...

Wat ik nog niet direct voor elkaar heb is dat dependabot zn eigen pullrequests ook merged zodra de unittests zijn gelukt. Ga ik nog even uitzoeken, kom ik nog op terug.

Maar is dikke tip : dependabot!
