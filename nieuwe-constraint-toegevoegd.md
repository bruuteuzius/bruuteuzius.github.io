---
title: Nieuwe constraint toegevoegd
published: 2020-09-20
tags: [planningalgoritme, volleybal]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Tijdens het testen van de gemaakte wedstrijd fluit planning kwam ik er achter dat het soms voor kwam dat zowel teams als individuele scheidsrechters soms 2x op een dag fluiten en dan soms dus ook nog hetzelfde tijdstip. Dat kan natuurlijk niet!

Dus heb ik een constraint toegevoegd waarbij ik over de rijen en kolommen itereer, en bij een kolom (een scheidsrechter of team) dus het volgende moet afspreken :

Voor de huidige scheidsrechter op deze wedstrijd, wil ik bepalen dat wanneer de vorige wedstrijd en deze wedstrijd allebei gekozen worden om te gaan fluiten door de huidige scheidsrechter, wil ik dat het systeem de volgende wedstrijd pakt voor deze scheidsrechter om te fluiten.

In C# gebruik ik daarvoor de [OnlyEnforceIf](https://developers.google.com/optimization/cp/channeling) constraint :

model.Add(x\[prev,c\] == x\[r,c\]).OnlyEnforceIf(x\[next,c\]);

prev is hier een integer die de vorige positie is (r-1)  
next is hier een integer die de volgende positie is (r+1)  
x is de IntVar variabele met op iedere positie een boolean variabele met een naam.

Et voila, geen situaties meer waarbij scheidsrechters 2 wedstrijden op hetzelfde moment moeten fluiten.

Dit is natuurlijk misschien niet helemaal waterdicht want stel je voor dat een vereniging zo groot is dat je soms wel 15 wedstrijden op 1 dag hebt.

Hiervoor heb ik al wat bedacht maar dat moet ik nog uitwerken. Mijn idee is om wedstrijden te groeperen in thuisdagen. Iedere thuisdag krijgt dan een uniek nummer ten opzichte van de andere thuisdagen. Alle wedstrijden op die thuisdag krijgen dan hetzelfde thuisdagnummer. Vervolgens itereren we weer over de wedstrijden en scheidsrechters en verzamelen de wedstrijden voor 1 thuisdag. In die reeks van wedstrijden mag dan de huidige scheidsrechter maar 1x fluiten.
