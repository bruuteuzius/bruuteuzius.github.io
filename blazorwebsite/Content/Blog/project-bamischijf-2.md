---
title: Project bamischijf 2.0
published: 2025-09-09
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

## Project bamischijf 2.0
Mijn huidige NAS heet bamischijf en is een DS920+ van Synology. Maar de 4x4TB in SHR2 zit nu op 80% volheid en na
[deze](https://www.tomshardware.com/pc-components/nas/synology-requires-self-branded-drives-for-some-consumer-nas-systems-drops-full-functionality-and-support-for-third-party-hdds) 
stunt van Synology wil ik eigenlijk geen Synology meer maar mn eigen zelfbouw NAS.
De keuze is gevallen op een Jonsbo N2. Hier heb ik 3x16TB HDD's in, een Intel i5 13500 met 14 cores, 64GB aan geheugen en een 500GB SSD voor het OS.
Het gigabyte moederbord heeft 4 SATA stekkers wat voor nu genoeg is.

> **_Dikke tip :_**
> De fan die bij de jonsbo zit, niet aansluiten onder de molex stekkers maar op het moederbord, zodat je de snelheid kan reguleren.

## Het plan
Zoals [Jim Salter](https://jrs-s.net/2025/08/11/rustdesk-server-on-ubuntu-22-04/) het mooi zegt : _As usual, I’m self-documenting a project while I work on it_ Dat spreekt me wel enorm aan want ik ben niet zo goed in plannen. Ik doe liever.
Kortom, er is geen plan. Ik kan wel een lijstje maken met wat ik nu aan apps, scripts en functionaliteiten op de Synology heb en wat ik straks op 
mijn nieuwe server wil, of nodig ga hebben.

- DSM 7 als frontend om in te klikken
- SHR2 (een soort RAID5)
- Shared Folders (zoals backupgs, downloads, web, docker, homes)
- file services (AFP voor Mac, NFS, SMB)
- home dirs met daarin o.a. foto's
- apps (zoals synology drive client)
- docker containers
- media en backups
- scripts in de task scheduler
- firewall
- notificatie mogelijkheden
- verbinding met UPS
- fan control
- server monitoring

## Stap 1 : De server installeren
OpenMediaVault gaat mijn frontend worden ipv DSM 7. Het is ook mogelijk om DSM op een niet-Synology te installeren, maar dat voelt als cheaten en lijkt me niet wat.

> _21-09-2025 Zie struikelblok 1 verderop. Installeer dus debian 12!_

En OMV heeft debian nodig dus tsja. Ondertussen heb ik een usb stick met Debian 12 gemaakt. Ik gebruik op mijn laptop KDE Iso Writer. Die vraagt om een checksum.
Een checksum van je iso pakken doe je zo : 
```
sha256sum debian12.iso
```
Het resultaat daarvan plak je in de tool en hoppaaa usb stickie bakken. Dat is cheaten want je moet op de website de sha vinden, maar dat lukte dus niet :)

### SSH
Nu de server een debian install heeft, moet ik nog een openssh server draaien. 
```
sudo apt install openssh-server
```
en daarna starten met `sudo systemctl start ssh` en dan de service enablen zodat ie na een herstart `sudo systemctl enable ssh`.
Draait de ssh service? `sudo systemctl status ssh` Dan kan de server in de kast boven het voorraadhok.
Het enige dat de server nodig heeft is stroom en een UTP kabeltje. 

### Vast ip adres
Eerst maar eens een update doen. 
```
sudo apt update
```
bjdiedering is not in the sudoers file. Okeeeeej. Nouwja ook wel fijn dat niet alles aan staat en ik zelf ook wat moet doen.
`bjdiedering ALL=(ALL:ALL) ALL` toevoegen aan `/etc/sudoers` en klaar. 

```
sudo apt install net-tools dnsutils iproute2 nano
```
En dan een static ip adres in `/etc/network/interfaces` (eerst backup maken!)
```
auto eno1
allow-hotplug eno1
iface eno1 inet static
    address 192.168.178.200
    netmask 255.255.255.0
    gateway 192.168.178.1
    dns-nameservers 8.8.8.8 8.8.4.4
```
Misschien nog een keer niet-google dns-en, maar soit. 
Daar netwerk restarten:
```
sudo systemctl restart networking
```
### Firewall
Nu wil ik een firewall. Ik heb ergens gelezen dat OpenMediaVault ufw ondersteund, maar ik heb ook gelezen dat die niet lief
samen speelt met docker. Maar dat lijkt me een later zorg. 
Eerst maar eens `sudo apt install ufw`. Dan een backup maken van de basis met `sudo cp /etc/default/ufw /etc/default/ufw.backup` en `sudo cp -r /etc/ufw /etc/ufw.backup`
Ik wil nog wel kunnen ssh-en dus `sudo ufw add ssh` 
En een paar basis rules : 
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny forward
```

Aanzetten en we zijn safe `sudo ufw enable`

### Monitoring
Eerst maar eens snel de temps monitoren. 
```
sudo apt install lm-sensors
```
Daarna de sensors vragenlijst starten met `sudo sensors-detect` en daarna kun je met `sensors` zien wat o.a. de temps zijn.

![](media/nasischijftemp.png)

De nasischijf staat al een tijdje in de kast dus dit zijn goeie temps voor een ding dat niet zoveel doet :)

Maar om nu iedere keer te ssh-en en kijken wat o.a. de temperatuur is van alles, ga ik toch liever naar mn grafana dashboard.
Dus we gaan node-exporter installeren :
```
sudo apt install prometheus-node-exporter
```
Dan nog de node-exporter poort 9100 toevoegen aan ufw en klaar is Bert `sudo ufw allow 9100/tcp`

![](media/node-exporter.png)

Hierna heb ik de prometheus.yml op mn oude NAS (bamischijf 1.0) aangepast, `docker restart prometheus` en ik kan de metric toevoegen:
![](media/grafanatemp.png)

Misschien is 25,6 graden niet helemaal zuiver de gemiddelde temperatuur, maar alleszins de temp van teminste 1 CPU core. Mooi woord : alleszins.

### Struikelblok 1
Ik dacht : ik installeer debian 13 want die is de nieuwste. Kom ik er net op dit punt achter dat OpenMediaVault nog niet wordt ondersteund
door [debian 13](https://docs.openmediavault.org/en/latest/releases.html)... Dus ik ga denk ik maar weer opnieuw een installatie doen, 
maar dan met OpenMediaVault 7 met debian12. Zodra OpenMediaVault 8 klaar is, kan ik upgraden naar debian13.

### OpenMediaVault
De installatiehandleiding kan ik niet veel meer van maken dan wat [hier](https://docs.openmediavault.org/en/latest/installation/on_debian.html) staat.
Niet vergeten om na de installatie van OMW mezelf toe te voegen als iemand die mag ssh-en: `sudo usermod -a -G _ssh bjdiedering`
Er staat namelijk duidelijk in de installatie handleiding dat door het installeren van OMV de user die tijdens het installeren
van debian is aangemaakt, niet perse kan ssh-en. 

Na een reboot, naar http://192.168.178.200 en we krijgen een mooi loginscherm te zien : 

![](media/myfirstomv.png)

Let the fun begin! Voor nu deze post committen naar mn github repo, zodat de blogpost wordt bijgewerkt.
