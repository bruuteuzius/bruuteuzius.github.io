---
title: Inzicht in stroom en gas verbruik
published: 2021-03-22
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Natuurlijk kan ik naar de meterkast lopen en het huidig verbruik aflezen maar dat zou te makkelijk zijn. Leuker (en moeilijker) is met een P1 kabel en wat docker en script werk. In een vorige post heb ik verteld over de upgrade naar InfluxDB 2.0 en de uitdagingen die dat met zich meebrengt. Het doel is om het stroom verbruik in een grafana dashboard te kunnen tonen. Aan de hand van onderstaand recept kunnen anderen dat ook voor elkaar krijen.

## Benodigdheden

- Een Iskra ME382 slimme meter
- [Deze](https://www.bol.com/nl/p/slimme-meter-kabel-p1-usb/9200000111535827/?s2a=) P1 usb-kabel van Cedel
- Een [Raspberry PI](https://www.raspberrypi.org/)
- Een Synology NAS met docker ondersteuning (ik heb een [DS920+](https://www.synology.com/en-us/products/DS920+))

## Voorbereiding

Toevallig had ik al een raspberry pi in de meterkast, waar ik de Unifi-controller op heb [geïnstalleerd](https://www.stevenkussmaul.com/2021/01/27/how-to-install-a-unifi-controller-on-your-raspberry-pi/), zodat ik mijn Ubiquiti AP's kan benaderen/beheren. Sluit de P1 kabel aan op de slimme meter en de usb in de raspberry pi.

Installeer nu ser2net op de pi :

```
sudo apt update
sudo apt upgrade -y
sudo apt install ser2net -y
```

Controleer waar de P1 kabel is aangesloten met : `dmesg | grep tty`

Nu gaan we de ser2net configuratie aanpassen : `sudo nano /etc/ser2net.conf`

Vul de volgende regel in als je ook een Iskra ME382 meter hebt. Anders moet je iets spelen met de baudrate enzo.

```
6001:raw:600:/dev/ttyUSB0:9600 EVEN 1STOPBIT 7DATABITS XONXOFF LOCAL -RTSCTS
```

Als het goed is kun je nu naar het IP adres van je raspberry pi met dat poortnummer en krijg je een stream van misschien onbegrijpelijke data te zien :

```
/ISk5\2ME382-1003

0-0:96.1.1(4B413650303035313332383430393133)
1-0:1.8.1(11212.230*kWh)
1-0:1.8.2(13588.261*kWh)
1-0:2.8.1(00000.086*kWh)
1-0:2.8.2(00000.728*kWh)
0-0:96.14.0(0001)
1-0:1.7.0(0000.85*kW)
1-0:2.7.0(0000.00*kW)
0-0:17.0.0(0999.00*kW)
0-0:96.3.10(1)
0-0:96.13.1()
0-0:96.13.0()
0-1:24.1.0(3)
0-1:96.1.0(3238303131303031333038353433373133)
0-1:24.3.0(210321190000)(00)(60)(1)(0-1:24.2.1)(m3)
(12168.209)
0-1:24.4.0(1)
!
```

Dit blokje data herhaald zich om de 8 seconden met andere waarden in hetzelfde formaat. Als je wil weten wat het precies betekent, is er bijvoorbeeld deze [handleiding](media/Mx382_User_manual_eng_V1.02_SMS.pdf) voor de Iskra ME382.

Home Assistant heeft de mogelijkheid dit endpoint uit te lezen. Zo ziet mijn configuratie er uit :

```
sensor:
  - platform: dsmr
    host: <IP raspbery pi>
    port: 6001
    dsmr_version: 2.2

group:
  meter_readings:
    name: Meter readings
    entities:
      - sensor.energy_consumption_tarif_1
      - sensor.energy_consumption_tarif_2
      - sensor.energy_production_tarif_1
      - sensor.energy_production_tarif_2
      - sensor.gas_consumption
```

In Home Assistant verschijnt nu een nieuwe integratie, die van Netbeheer Nederland : DSMR Slimme Meter. Home Assistant heeft standaard een SQLite database. Deze kunnen we niet uitlezen met InfluxDB, dus het plan is om MariaDB te gebruiken. Op de NAS staat deze al geinstalleerd. Maak daar een database en user aan (bijvoorbeeld : hass) en voeg deze configuratie toe aan configuration.yml van Home Assistant :

```
recorder:
  db_url: mysql://hass:<wachtwoord>@<IP Nas>:3307/homeassistant?charset=utf8
  purge_keep_days: 7
  purge_interval: 1
```

Toevallig draait mijn MariaDB op poort 3307 en heet de database homeassistant, maar kan ook wat anders. Herstart Home Assistant en controleer de database of er o.a. de tabellen "states", "events", "schema\_changes" en "recorder\_runs" bestaan.

## Bereidingswijze

Men neme nu de InfluxDB 2.0 docker installatie en navigeer naar de Data Explorer.

Hier kun je nu een Flux-query ingeven die de MariaDB database uitleest :

```
import "sql"

sql.from(
  driverName: "mysql",
  dataSourceName: "hass:<wachtwoord>@tcp(<IP NAS>:3307)/homeassistant",
  query: "SELECT state , last_changed FROM `states` WHERE domain = 'sensor' and entity_id = 'sensor.power_consumption' and state <> 'unknown' "
)
|> rename(columns: {"state": "_value" , "last_changed" : "_time"})
|> toFloat()
|> range(start: v.timeRangeStart, stop:v.timeRangeStop)
|> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
|> yield(name: "mean")
```

sql is een plugin die o.a. MS SQL Server, PostgresQL enzovoort kan uitlezen. Het rename attribuut zorgt ervoor dat de karakterreeks in de "state" kolom gelezen kan worden en aangegeven wordt als de waarde in deze timeseries data. De toFloat() functie is nodig om de \_value naar een decimaal om te kunnen zetten.

Zodra je voldoende aanpassingen hebt gedaan, kun je een panel toevoegen in je grafana dashboard om je stroomverbruik met behulp van de Flux-query in een grafiek te zetten :

![](media/afbeelding-1.png)

Nu kan ik nog wat spelen met de Y-as en kleuren en meerdere lijnen bij elkaar (Tarief 1 en tarief 2) enzovoort. Maar het begin is er!
