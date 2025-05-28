---
title: Synology DS920+ migreren naar een Jonsbo N2 
published: 2025-05-26
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

## Wat? Weg van Synology? Waarom?
Omdat het kan. Omdat mijn usage van mijn 4x4TB disks in SHR-2 nu op 80% vol zit. Maar ook omdat Synology een stunt pulled die ik slecht trek.
Ik bedoel waarom doen ze [deze](https://nasstore.eu/synologys-2025-drive-compatibility-policy-what-it-means-for-users/) dingen?
Enniehoe, ik wil dus dikke disks, liefst 16TB had ik me bedacht, maar ik ga niet 4x350 euro neerleggen voor dikke schijven. 
Ook wil ik wat meer flexibiliteit. Uitbreidingsmogelijkheden. En een fancy RAID hoef ik ook niet. 
Ik heb ooit 1x meegemaakt dat het mn bacon savede, maar die ene schijf redundantie is gewoon niet cool.
De oplossing die ik heb bedacht is: ik koop 2 dikke schijven van 16TB en stop die in een Jonsbo N2 (waar er 5 in kunnen) en dan zet ik de foto's
gewoon op beide schijven. Als er dan 1 uitvalt heb ik die andere nog. Mocht het zo zijn dat ovierge media en games en dingen kwijt raakt, 
dan kan ik dat opnieuw gaan verzamelen. Boeie. Yolo. Dat soort dingen.

## Hoe?
Nou! Mijn kale broeder Sebastian heeft een [lijst](https://tweakers.net/gallery/1161278/wenslijst/?wish_id=3736492) voor me gefabriekt met spulleboel dat goed bij elkaar past en voldoet aan mijn wensen.
Maar om te migreren moet ik een plan hebben. De WAF mag niet omlaag, er mag geen data verloren gaan, de overgang moet soepel verlopen.

1. Hardware kopen en in elkaar schroeven.
2. NAS software kiezen.
3. Vervanging vinden voor Synology Drive client.
   4. Foto storage net zoals op de iPhone?
   5. Thuis of remote bestanden kunnen syncen -> tailscale? welke app?
6. Alle docker containers verhuizen
   7. /volume/docker verhuizen want daar zit data in
   8. /volume/@docker ook verhuizen want er zijn compose files waar geen volume is opgegeven
   9. envs 
7. Data verhuizen
   8. per Shared Folder via rsync?
      9. Series en films en muziek en books
      10. home folders (foto's o.a.)
      11. backup folder
      12. Downloads folder
8. Scripts 
   9. om Home Assistant mariadb te importeren in postgresql DWH.
   10. om na een herstart Sonarqube te fixen
   11. om VPNTUN aan te zetten na een herstart
12. Toepassingen
    13. Media server (UPnP/DLNA)
    14. SMB, WS-service
    15. UPS instellen!
    
## Hardware
* Moederbord en CPU waren het goedkoopst bij NBB.com. 
* Jonsbo N2 kast en CPU cooler bij Proshop.nl gehaald
* 2x 16tb WD Reds bij Alternate gekocht.
* broeder kaal heeft nog MEM en voeding liggen.

## Software
Bij twijfel niet inhalen zegt de moeder. Dus ik twijfel of ik OpenMediaVault, TrueNAS scale of core, of 
gewoon een server installatie van Debian of Ubuntu of Arch? 

Eerst maar eens debian server installeren op een oude PC van debian server voorzien en daar OpenMediaVault op installeren.

#### OpenMediaVault
Pro's
* Updates komen met debian mee
* Plugins
  * Er is een UPS plugin! 
  * FileBrowser moet je apart installeren, vet raar.
* Klikerdieklik schijven kiezen, shared folders maken enz.
* Basic netwerk config enzo kan allemaal in de GUI
* SMB of NFS shares aanmaken geen probleem, volgens mij kan ook iSCSI
* kan gewoon ssh-en, Shared Folders die je bij mekaar hebt geklikt benaderen, enz.
* Er is niet een betaalde versie, pure open source onder een GPL licence.
* De GUI is er voor de basis, de dockertjes kan ik in de terminal ;)

Con's
* Als ik github zo bekijk zijn ze er wel steady mee bezig maar niet heel erg veel en hard.



