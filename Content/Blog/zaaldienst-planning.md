---
title: Zaaldienst planning
published: 2020-11-10
tags: [planningalgoritme,volleybal]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Zoals ik al in een eerdere post aangaf, heb ik momenteel een writersblock of een software equivalent daarvan. Dus ik ga proberen (zoals ik wel vaker doe) om te gaan documenteren en zo tot een oplossing te komen.

Waar ik me tot aan de wedstrijdplanning makkelijk kon verliezen in het schrijven van code om te zorgen dat ik een automatisch gegenereerde wedstrijd scheidsrechter planning kon maken, heb ik nu moeite om me er toe te zetten om ook de zaaldienst planning te maken.

In eerste instantie dacht ik om het op dezelfde manier te doen als de wedstrijdplanning : Een boel regels om tot score per scheidsrechter per wedstrijd te komen en daar CP-SAT algoritme op los laten.

Het probleem is alleen dat je niet per wedstrijd een team nodig hebt voor zaaldienst, maar voor een dagdeel. In de ochtend zijn er bijvoorbeeld 2 wedstrijden en de middag nog 2. Dan hoeft niet 1 team een hele dag zaaldienst te doen (velden opbouwen en afbreken, banken klaar zetten, enzovoort). Daarnaast is het niet handig dat al te jonge jeugdspelers zoals C-tjes en mini's hun eigen velden gaan opzetten. De spandoeken met reclame kunnen ze bijvoorbeeld al niet ophangen. Ook kun je niet aan vrijwilligers gaan vragen die bijvoorbeeld al bardienst draaien of fluiten, ook nog eens zaaldienst aan uitdelen. Tenminste zo werkt dat niet bij ons.

Je zou nu kunnen zeggen : wat is het probleem? Ik lees zo al 4 regels waarop je een score kan baseren :

1. Een team krijgt +1 als het zelf moet spelen in dat dagdeel
2. Een team krijgt +1 als het een seniorteam is
3. Een team krijgt +1 als het bij de wedstrijdplanning al teveel heeft gedaan
4. Personen mogen niet zaaldienst doen, het moet een team zijn.

Toch heb ik dit uitgetest en ik kwam inderdaad in de knoei. Mede doordat wedstrijden soms door een scheidsrechter (een enkel persoon) vaker op 1 dag werd ingepland en dat kan dus ook niet.

Dus heb ik eerst dat opgelost met behulp van de OnlyEnforceIf functie eerder besproken in deze [blogpost](https://bruuteuziusbuilds.wordpress.com/2020/09/20/nieuwe-constraint-toegevoegd/).

Dit werkt voor nu toevallig goed en lost voor nu het probleem op. Als je meer dan 5 wedstrijden op 1 dag hebt, kan dit voor problemen gaan zorgen.

Helemaal weer energie gekregen van deze oplossing voor de wedstrijden, zodat ik ook vrij snel een oplossing had voor het zaaldienst probleem.

Je kan niet alle wedstrijden en teams los met scores zomaar los tegen het planningsalgoritme aangooien want dan zou je dus meerdere zaaldiensten voor verschillende wedstrijden krijgen. Dus heb ik de wedstrijden gegroupeerd op wedstrijddag (een thuisdag) :

```
var thuisdagen = ThuisWedstrijden.GroupBy(w => w.BeginDatumTijd.Date)
                    .Select(g=> new KeyValuePair<DateTime, List<Wedstrijd>>(g.Key, g.ToList())).ToList();
```

Dit levert een lijst met key-value pairs op met een Datum (de thuisdag) met een lijst van wedstrijden. En tataaaaa gooi em door de plannerengine en ik heb netjes per thuisdag een team dat zaaldienst doet, maar niet vaker dan 1 dag achter elkaar 😁
