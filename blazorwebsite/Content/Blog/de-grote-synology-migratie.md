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
Enniehoe, ik wil dus dikke disks in een Jonsbo N2, liefst disks van 16TB had ik me bedacht, en dan begin ik met 3 stuks in RAID5. Dat is 32GB aan opslagruimte, 1 disk redundantie. 
Dan heb ik nog 2 slots over want ik wil wat meer flexibiliteit. Uitbreidingsmogelijkheden.  

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
* 3x 16tb WD Reds bij Alternate gekocht.
* broeder kaal heeft nog MEM en voeding liggen.
* De connector voor de harddisks hebben stroom nodig van 2 molex aansluitingen. Deze heb ik besteld en aangesloten op de 2 sata stroomkabels die in de PSU zitten.
* Er moeten 4 SATA kabels naar de harddisk connector printplaat toe. Die heb ik nu los en genummerd.

## NAS Software
Bij twijfel niet inhalen zegt de moeder. Dus ik heb lang getwijfeld of ik OpenMediaVault, TrueNAS, of 
gewoon een server installatie van Debian of Ubuntu of Arch. Het is OpenMediaVault geworden want die leunt op de updates van Debian.
In principe heb ik ook alleen een UI nodig voor acties met disks enzo. Misschien een filebrowser.
Eerst maar eens Debian server installeren op een oude PC en daar OpenMediaVault op installeren.

#### OpenMediaVault
Pro's
* Updates komen met debian mee
* Plugins
  * Er is een UPS plugin! 
  * FileBrowser moet je apart installeren, vet raar.
* Klikerdieklik schijven kiezen, shared folders maken enz.
* Basic netwerk config enzo kan allemaal in de GUI
* SMB of NFS shares aanmaken geen probleem, volgens mij kan ook iSCSI
* Kan gewoon ssh-en, Shared Folders die je bij mekaar hebt geklikt benaderen, enz.
* Er is niet een betaalde versie, pure open source onder een GPL licence.
* De GUI is er voor de basis, de dockertjes kan ik in de terminal ;)

Con's
* Als ik github zo bekijk zijn ze er wel steady mee bezig maar niet heel erg veel en hard.

#### TrueNAS
Pro's
* Meer opties meer beter dan OpenMediaVault
* Bestaat al lang en komt van FreeNAS
* ZFS lijkt me interessant om eens uit te zoeken

Con's
* ZFS is misschien te moeilijk voor wat ik doe. Ook overkill lijkt me.
* Naast de Community-versie is er een betaalde variant. Het is een bedrijf met een winstoogmerk.

#### Kale servert
Pro's
* Enorm leerzaam alles via de terminal
* Maximale controle
* Er zijn wel webapps die een filebrowser bieden.

Con's
* Gaat veel tijd in zitten.
* Er is geen standaard setup mogelijkheid voor disks, partitioneren, users, enz.

### Synology apps
Ik heb dus een vervanging nodig voor de Drive Client om foto's en home folders te syncen. 
Syncting is een optie. Maar voor foto's sowieso Immich. Die heb ik al uitgeprobeerd en werkt prima.

### Docker containers 

### Data verhuizen
Als ik data over de lijn kopieer, ben ik volgens mij eeuwen bezig. Er is nu 10TB aan data. Ik heb een test gedaan met rsync copy tussen mn Synology en mn Frigate server.
Beide servers hebben een 10GBit Ethernet aansluiting, ik heb daar CAT5E tussen liggen en dat is 1Gbps.
Maar op een of andere manier heb ik een kopieersnelheid van 11 MBps... Uitgerekend gaat dat ongeveer 10 dagen duren dat kopiëren.
Waar de F ligt dat aan? Zelfs 1 GB kopiëren van mijn laptop naar de Synology gaat met 20MB per seconde. 
Mijn vermoeden is dat de ZIGGO router de bottleneck is. Of de lengte van de kabels. Daarom heb ik bedacht dat ik misschien een
een switch met 1GBit poorten moet halen, bamischijf 1 en 2 beide met een korte kabel daarop aan moet sluiten en dan de boel kopieren.
Want theoretisch gezien zou ik met mn CAT5E kabels en mn 1GBps poorten toch 125Mb/s moeten halen ipv 11MB/s.

Een andere optie is data via USB-schijf kopiëren van bamischijf 1 naar 2. Maar Synology heeft het gebruik van USB
uitgeschakeld sinds DSM 7. 


