---
title: Sonarr
published: 2020-07-05
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Heb overkomt me nogal eens dat bij een package upgrade voor mijn Synology NAS, de package Sonarr uit gaat en niet meer op komt. Wanneer tv-series niet meer automagisch worden gedownload is dat voor mij en mijn vrouw allesbehalve een feestje, dus daar moeten we iets op verzinnen. Omdat ik voor mijn dagelijks werk een windows machine heb, wil ik graag op de hoogte zijn wanneer Sonarr uit de lucht is.

Ik ben lui dus daar moet ik iets voor kunnen verzinnen toch?

Sonarr heeft een [API](https://github.com/Sonarr/Sonarr/wiki/API) waar ik de status kan opvragen :

http://localhost:8989/api/system/status?apikey=YOUR\_API\_KEY

Maar laat ik eerst eens beginnen met een notificatie die als background job draait en me iedere 4 uur een update geeft of het IP en de poort van de Sonarr installatie nog wel is te benaderen :

$Destination = "http://192.168.178.200:8989/"
$ScriptBlock = {
    $TimesLooped = 0
    while ( $Duration -le $TimesLooped) {
        if ( Test-Connection -ComputerName $using:Destination -Count 1 -Quiet ) {
            New-BurntToastNotification -Text ($using:Destination + " is online"), ("Last checked :" + (Get-Date).ToString())    -UniqueIdentifier $using:Destination
        }
        else {
            New-BurntToastNotification -Text ($using:Destination + " is offline"), ("Last checked :" + (Get-Date).ToString())   -UniqueIdentifier $using:Destination
        }
        Start-Sleep -Hours 4
        $TimesLooped++
    }
}
Start-Job -Name $Destination -ScriptBlock $ScriptBlock

Inderdaad regelrecht gejat van [BurntToast](https://github.com/Windos/BurntToast/tree/master/Examples/Example07) 😁 Waarom moeilijk doen als het makkelijk kan?

Als dit niet bevalt ga ik wel andere oplossingen proberen. Voor nu werkt het.
