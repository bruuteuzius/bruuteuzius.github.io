---
title: Mijn eigen DataWareHouse
published: 2022-08-04
tags: [homelab]
authors: 
    - name: "bruuteuzius"
      gitHubUserName: "bruuteuzius"
---

Een tijd geleden heb ik een homeassistant docker geinstalleerd op mijn NAS (een DS920+). Standaard draait homeassistant met een sqlite database maar omdat er nogal interessante data in staat heb ik deze geupgrade naar mysql. Voor homeassistant heb ik een P1 kabel gehaald en die op een raspberry pi aangesloten. Meer over dat project [hier](https://bruuteuziusbuilds.wordpress.com/2021/03/22/inzicht-in-stroom-en-gas-verbruik/).

Dus ik heb nu een mysql database met o.a. het verbruik van gas en electra. Heel mooi! Maar ik had de retentie op 7 dagen staan dus na een week was ik alles kwijt. Omdat ik graag het verbruik over bijvoorbeeld een jaar of per maand wou zien, had ik 2 opties : of de retentie ophogen of een soort ETL maken die de vluchtige data wat minder vluchtig maakt, zonder er last van te hebben in homeassistant.

Bij mn vorige werkgever heb ik miljonen SSIS packages gemaakt, maar ik draai natuurlijk geen Windoos meer en mijn NAS kan daar ook weinig mee. Dus ging ik op zoek naar een ETL alternatief. Na lang wikken en wegen kwam ik op [Airbyte](https://airbyte.com/) uit. Mooi idee, open source ook (nog) en werkt vrij intuitief. Je hebt een source, je hebt een destination, je kan templates maken hoe je data getransformeerd moet worden en er is een (vrij beperkte) maneer hoe je kan aangeven of je data geupsert moet worden of getrunsert. Juist je leest het goed : "getrunsert". Dat is mijn eigen variant op het werkwoord upserten wat "update or insert" betekent. Iets "trunserten" is niets meer dan "truncate and insert". Enniehoe supermooi dat Airbyte.

Tot ongeveer een week geleden toen ik er achter kwam dat mijn ETL al 3 dagen niets meer had gedaan vanwege een error. Hoe dan?! Hoe gaat iets stuk dat je niet aanraakt en maanden goed heeft gelopen 🤬

```
2022-04-12 14:17:04 ERROR i.a.w.DefaultReplicationWorker(run):141 - Sync worker failed.
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.RuntimeException: Source process exited with non-zero exit code 1
	at java.util.concurrent.CompletableFuture.reportGet(CompletableFuture.java:396) ~[?:?]
	at java.util.concurrent.CompletableFuture.get(CompletableFuture.java:2073) ~[?:?]
	at io.airbyte.workers.DefaultReplicationWorker.run(DefaultReplicationWorker.java:134) ~[io.airbyte-airbyte-workers-0.35.3-alpha.jar:?]
	at io.airbyte.workers.DefaultReplicationWorker.run(DefaultReplicationWorker.java:49) ~[io.airbyte-airbyte-workers-0.35.3-alpha.jar:?]
	at io.airbyte.workers.temporal.TemporalAttemptExecution.lambda$getWorkerThread$2(TemporalAttemptExecution.java:174) ~[io.airbyte-airbyte-workers-0.35.3-alpha.jar:?]
	at java.lang.Thread.run(Thread.java:833) [?:?]
```

Hier kan ik dus helemaal niets mee en airbyte is zo jong dat er ook miljonen github issues open staan en ik door de bomen het bos niet meer zie en er al weer helemaal klaar mee ben. Misschien ooit, maar nu is het plan : zelf een ETL maken! En waarin kan dat beter dan in python 😁 Beetje bij elkaar geraapt zooitje gecopy-paste maar het werkt en het is voor mezelf dus boeie.

```

import mysql.connector as connection
import psycopg2
import pandas as pd
import sys

file_path = '/volume1/backups/mysql_state_export.csv'

def mysql_export_table(file_path):
    '''
    This function exports data from a SQL to CSV
    '''
    try:
        # requirement: python-mysqldb

        db = connection.connect(user='<gebruikersnaam>', password='<wachtwoord>' ,port=3307, database='homeassistant', host='<IP adres NAS>')
        query = ("""
            select state, event_id, entity_id, last_changed 
            from homeassistant.states s 
            where s.domain = 'sensor' 
            and s.state != 'unknown' 
            and cast(s.created as date) = DATE_SUB(CURDATE(), INTERVAL 1 DAY)                
                 """) 

        result = pd.read_sql(query,db)

        result.to_csv(file_path, index=False)
        
    except Exception as ex:
        print("Error: {}".format(str(ex)))
        sys.exit(1)

def pg_upsert_table(file_path, table_name, dbname, host, port, user, pwd):
    '''
    This function upload csv to a target table
    '''
    try:
        conn = psycopg2.connect(dbname=dbname, host=host, port=port,\
         user=user, password=pwd)
        print("Connecting to Database")
        cur = conn.cursor()
        f = open(file_path, "r")
        # Truncate the table first
        # cur.execute("Truncate {} Cascade;".format(table_name))
        # print("Truncated {}".format(table_name))
        # Load table from the file with header
        cur.copy_expert("copy {} from STDIN CSV HEADER QUOTE '\"'".format(table_name), f)
        cur.execute("commit;")
        print("Loaded data into {}".format(table_name))
        conn.close()
        print("DB connection closed.")

    except Exception as e:
        print("Error: {}".format(str(e)))
        sys.exit(1)

mysql_export_table(file_path)

table_name = 'public.energy'
dbname = 'DWH'
host = '<IP adres NAS>'
port = '54321'
user = '<gebruikersnaam>'
pwd = '<wachtwoord>'

pg_upsert_table(file_path, table_name, dbname, host, port, user, pwd)

```

Voel je vrij om te jatten! En voor future-me : anders gooi je dit gewoon in een github gist de volgende keer ;)
