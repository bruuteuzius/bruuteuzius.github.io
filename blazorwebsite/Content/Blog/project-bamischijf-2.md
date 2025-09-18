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

- [ ] DSM 7 als frontend om in te klikken
- [ ] SHR2 (een soort RAID5)
- [ ] Shared Folders (zoals backupgs, downloads, web, docker, homes)
- [ ] file services (AFP voor Mac, NFS, SMB)
- [ ] home dirs met daarin o.a. foto's
- [ ] apps (zoals synology drive client)
- [ ] docker containers
- [ ] media en backups
- [ ] scripts in de task scheduler
- [ ] firewall
- [ ] notificatie mogelijkheden
- [ ] verbinding met UPS
- [ ] fan control
- [ ] server monitoring

## Stap 1 : De server installeren
OpenMediaVault gaat mijn frontend worden ipv DSM 7. Het is ook mogelijk om DSM op een niet-Synology te installeren, maar dat voelt als cheaten en lijkt me niet wat.
En OMV heeft debian nodig dus tsja. Ondertussen heb ik een usb stick met Debian 13 gemaakt. Ik gebruik op mijn laptop KDE Iso Writer. Die vraagt om een checksum.
Een checksum van je iso pakken doe je zo : 
```
sha256sum debian13.iso
```
Het resultaat daarvan plak je in de tool en hoppaaa usb stickie bakken. Dat is cheaten want je moet op de website de sha vinden, maar dat lukte dus niet :)

Nu de server een debian install heeft en ssh staat aan (had ik aangevinkt) kan ik de server boven de voorraadkast plaatsen. 
Het enige dat de server nodig heeft is stroom en een UTP kabeltje. 

### Vast ip adres
Eerst maar eens een update doen. 
```
sudo apt update
```
bjdiedering is not in the sudoers file. Okeeeeej. Nouwja ook wel fijn dat niet alles aan staat en ik zelf ook wat moet doen.
`bjdiedering ALL=(ALL:ALL) ALL` toevoegen aan `/etc/sudoers` en klaar. 

```
sudo apt install net-tools dnsutils iproute2 nano` 
```
En dan een static ip adres in `/etc/network/interfaces` (eerst backup maken!)
```
# Static IP configuration for primary interface
auto eno1
allow-hotplug eno1
iface eno1 inet static
    address 192.168.178.200
    netmask 255.255.255.0
    gateway 192.168.178.1
    dns-nameservers 8.8.8.8 8.8.4.4
```
Misschien nog een keer niet-google dns-en, maar soit. 
Daar netwerk restarten :
```
sudo systemctl restart networking
```

### Monitoring
Eerst maar eens snel de temps monitoren. 
```
sudo apt install lm-sensors
```
Daarna de sensors vragenlijst starten met `sudo sensors-detect` en daarna kun je met `sensors` zien wat o.a. de temps zijn.

![](/media/bamischijf2temps.png)

Bamischijf2 staat al een tijdje in de kast dus dit zijn goeie temps voor een ding dat niet zoveel doet :)