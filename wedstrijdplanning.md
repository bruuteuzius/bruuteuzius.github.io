---
title: Wedstrijdplanning
published: 2020-06-27
tags: [planningalgoritme,volleybal]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Al eerder had ik het over een webapp die ik voor onze volleybalvereniging heb gemaakt om thuiswedstrijden in te kunnen plannen met scheidsrechters, bardiensten en zaaldienst. In deze blogpost probeer ik uit te leggen hoe ik dat heb gedaan.

Iedere wedstrijd heeft een scheidsrechter nodig. In ons geval is dat een team of een verenigingslid zonder team die graag fluit. Iedere scheidsrechter krijgt een score per wedstrijd. Die score bepaald uiteindelijk of die scheidsrechter die wedstrijd gaat fluiten of niet.

Scores worden bepaald door regels. Bijvoorbeeld : kan deze scheidsrechter dit niveau aan? Zo ja : score + 1. Zo nee : score = 0.

Zo kwam ik dankzij al die regels uit op een 2D array met op de y-as de wedstrijden en op de x-as de scheidsrechters (en teams). Op de index staat dan de score die de scheidsrechter voor die wedstrijd krijgt.

Een 2D array met scores kan er dan zo uit zien :

![](media/scorearray.jpg)

Hier heb ik vervolgens de [CP-SAT](https://developers.google.com/optimization/cp/cp_solver) solver van [Google OR tools](https://developers.google.com/optimization) op los gelaten. Daar heb ik een aantal constraints aan toegevoegd zoals :  
  
Een wedstrijd mag maar 1 scheidsrechter krijgen.  
Een scheidsrechter moet minimaal gemiddeld aantal wedstrijden fluiten.  
...enzovoort.

Uiteindelijk kom ik uit op een bitmap die verdacht veel lijkt op de wedstrijd planning :

![](media/bitmap.jpg)

Het algoritme samen met de CP-SAT solver, heeft dus de maximalen binnen de totale verzameling van scores bepaald in de kolommen, rekening houdende met de constraints die ik heb toegevoegd.

Wedstrijd 1 wordt dus door team 4 gefloten, wedstrijd 2 door team 3, enzovoort.

Rondom dat planningsalgoritme heb ik zoals gezegd een webapp omheen gemaakt. Hiermee knutsel ik met nieuwe technieken, die ik dan weer kan gebruiken in mijn werk. Het is prettig om een side-project te hebben :)

Momenteel heb ik writers-block betreft de webapp. Ik zie door de bomen het bos niet meer en ben toe aan wat refactorings.
