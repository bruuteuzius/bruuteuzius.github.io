---
title: Temperatuur Raspberry Pi meten met telegraf op libreelec
published: 2021-06-04
tags: [grafana,libreelec,telegraf] 
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Supermakkelijk natuurlijk, want je hoeft alleen maar telegraf te installeren, aanzetten, laten wijzen naar je influxdb en hoppakee, klaar! Want daar zijn 100 miljoen miljard blogposts over geschreven toch? Nouwja natuurlijk weer niet op de manier dat ik wil. De pi in de woonkamer (en z'n logische naam woonkamerpi) wordt soms iets te warm er films worden afgespeeld op [libreelec (just enough OS for Kodi)](https://libreelec.tv/). Alleen het probleem met libreelec is, is dat het geen standaard debian afgeleide is, maar een dichtgespijkerde versie. Ik kan dus niet zomaar systemctl start telegraf doen. Vandaar deze handleiding voor dummies (mocht ik ook dom worden) dan komt het vast goed ;-)

Eerst maar eens ssh-en naar dat ding

`ssh` [`root@`](mailto:root@192.168.178.144)`woonkamerpi.local` ww libreelec

Dan telegraf downloaden als tar.gz van [hier](https://portal.influxdata.com/downloads/) en uitpakken met `tar xzf telegraf.1.18.linuxarm.tar.gz` .  
Dan de config aanpassen in telegraf/etc/telegraf.conf met :

\[\[inputs.exec\]\]
#   ## Commands array
commands = \[
"awk '{print $1/1000}' /sys/class/thermal/thermal\_zone0/temp"
\]
#   commands = \[
#     "/tmp/test.sh",
#     "/usr/bin/mycollector --foo=bar",
#     "/tmp/collect\_\*.sh"
#   \]
#
#   ## Timeout for each command to complete.
timeout = "5s"
#
#   ## measurement name suffix (for separating different commands)
name\_suffix = "\_tempC"
#
#   ## Data format to consume.
#   ## Each data format has its own unique set of configuration options, read
#   ## more about them here:
#   ## https://github.com/influxdata/telegraf/blob/master/docs/DATA\_FORMATS\_INPUT.md
data\_format = "value"
data\_type = "float"

Let op die data\_format en data\_type zijn belangrijk want `awk` geeft geen getal maar karakters terug. Natuurlijk zijn meer inputs dan alleen temperatuur ook gewenst en een connectie met je influxdb2 output :

\[\[outputs.influxdb\_v2\]\]
#   ## The URLs of the InfluxDB cluster nodes.
#   ##
#   ## Multiple URLs can be specified for a single cluster, only ONE of the
#   ## urls will be written to each interval.
#   ##   ex: urls = \["https://us-west-2-1.aws.cloud2.influxdata.com"\]
urls = \["http://192.168.178.202:8086"\]
#
#   ## Token for authentication.
token = "<hier token>"
#
#   ## Organization is the name of the organization you wish to write to; must exist.
organization = "Diedering"
#
#   ## Destination bucket to write into.
bucket = "raspberry"

Dan deze configuratie testen met :

`/storage/telegraf/usr/bin/telegraf --config /storage/telegraf/etc/telegraf/telegraf.conf --test`

Nu gaan we telegraf als service installeren, maar omdat libreelec /etc /var en /usr mappen o.a. op read-only heeft gezet, moeten we de locatie van telegraf.service aanwijzen in het command :

`systemctl enable /storage/telegraf/usr/lib/telegraf/scripts/telegraf.service`

En nu starten :

`systemctl start telegraf`

Maar je raadt het niet, maar je raadt het niet, zo doet de vogel... ow sorry nee dat is de schuld van mini. Enniehoe, dikke generieke error. Om de error messages te laten zien moest ik kijken wat ik gisteravond in journal had want toen ging ik meerdere malen hopeloos de fout in. De journal van gister bekijk je met :

`journalctl --since yesterday`

Goh, logisch commando ook :-) Beetje scrollen en dan krijg je dit soort vage meldingen als je `systemctl` probeert :

`Jun 03 21:37:58 woonkamerpi systemd[1]: Started The plugin-driven server agent for reporting metrics into InfluxDB.  
Jun 03 21:37:58 woonkamerpi systemd[1]: telegraf.service: Main process exited, code=exited, status=217/USER  
Jun 03 21:37:58 woonkamerpi systemd[1]: telegraf.service: Failed with result 'exit-code'.  
Jun 03 21:37:58 woonkamerpi systemd[1]: telegraf.service: Service RestartSec=100ms expired, scheduling restart.  
Jun 03 21:37:58 woonkamerpi systemd[1]: telegraf.service: Scheduled restart job, restart counter is at 2. 
Jun 03 21:37:58 woonkamerpi systemd[1]: Stopped The plugin-driven server agent for reporting metrics into InfluxDB.`

Maar ik ben gekke henkie niet dus niet voor 1 gat te vangen. Gisteren eerder op de avond zag ik ergens een `init.sh` staan. Beetje googlen en dat schijnt dus een ouderwetse manier te zijn om services te installeren. Nou die maar eens proberen dan. Daar heb ik een boel aanpassingen in gemaakt, o.a. de locatie van telegraf van `/etc/telegraf/telegraf.conf` naar `/storage/telegraf/etc/telegraf.conf` gewijzigd. Dat komt omdat `/storage/` wel schrijfbaar is en de normale root (dus ook `/etc/` ) niet. Na wat directory aanpassingen in de init.sh kon ik starten met :

`/storage/telegraf/usr/lib/telegraf/scripts/init.sh start`

Hoppaaaa! En we hebben nu een bucket in influxdb2 die volloopt met informatie over onze raspberrypi met libreelec (Kodi) er op en bij een reboot start telegraf ook :)

![](media/woonkamerkoditemp.png)

Het is nog niet veel (klein ook) maar de bedoeling is om uiteindelijk alerts van Grafana te krijgen als de pi boven bepaalde thresholds gaat met de temperatuur.

_Edit : Mocht de service stoppen of niet meer opkomen, dan de pidfile verwijderen :_

`mv /storage/telegraf/var/run/telegraf/telegraf.pid /storage/telegraf/var/run/telegraf/telegraf.pid.old`

Na een herstart heb ik geen zin om zelf de pid te verplaatsen en de service opnieuw te starten. Omdat libreelec alleen /storage/ writable heeft gemaakt en de rest readonly, kan ik ook geen systemd service aanmaken voor telegraf. Daarom maak ik misbruik van de autostart.sh :

```
(
 sleep 20
 mv /storage/telegraf/var/run/telegraf/telegraf.pid /storage/telegraf/var/run/telegraf/telegraf.pid.old
 /storage/telegraf/usr/lib/telegraf/scripts/init.sh start
)
```
