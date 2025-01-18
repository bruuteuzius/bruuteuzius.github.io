---
title: Een blogpost over een blog
published: 2025-01-18
tags: [blog] 
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Deze blogpost is een test om te kijken of het lukt om met een commit van een md file op deze repo, een html gedeployed wordt als github page. 
Zoals je ziet is dat gelukt. Het is in feite erg simpel maar het duurde even voordat ik het door had.

Hier mijn stappenplan :
- Allereerst heb ik een export gemaakt van mijn gratis wordpress blog.
![](media/exportwordpress.png)
- Daarna de export van wordpress hernoemen naar export.xml
- Toen [deze](https://github.com/lonekorean/wordpress-export-to-markdown) tool gebruikt om de export.xml om te smurfen naar .md files
- Daarna heb ik de .md files nog wat moeten aanpassen omdat deze niet overeen kwam met de voorbeelden van [blazorstatic](https://github.com/BlazorStatic).
- In de repo voor github pages heb ik alle html verwijderd en vervangen door wat er nu staat onder blazorwebsite.
- De github workflow heb ik aangepast zodat de blazorwebsite gebouwd werd. De html in de output-folder word gekopieerd naar de branch `gh-pages`.
- De `gh-pages` branch dient als basis voor de github.io deployment dankzij deze [action](https://github.com/JamesIves/github-pages-deploy-action).
- Die action ging een paar keer mis, maar uiteindelijk is het gelukt om met het maken van deze `.md` een html blogpost toe te voegen aan mijn nieuw blog website.

![](media/100actions.png)
