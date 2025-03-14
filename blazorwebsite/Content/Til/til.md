## 2020
> 26-08-2020 - Als je een frontend model naar de backend stuurt, moet het model wel overeenkomen. Of json.stringify gebruiken 👌

> 26-08-2020 - [git reflog](https://github.blog/2015-06-08-how-to-undo-almost-anything-with-git/) !!!! Omdat ik vaak genoeg #oldpplsourcetree ben geweest, is het een fijn idee dat je fuckups ongedaan kan maken 😉

> 10-09-2020 - "git stash pop" popped de stash van stash af. "git stash apply" aplied de stash van stash, maar laat em staan in de stash.

> 15-09-2020 - Je kan beter 1x een 4TB zonder gegevens bescherming in [SHR](https://nascompares.com/guide/shr-synology-hybrid-raid/) hebben, dan 1x een 4TB en 1x een 2TB met gegevens bescherming in SHR.

> 23-09-2020 - het is mogelijk met css property [::before](https://www.w3schools.com/cssref/sel_before.asp) om iets voor een html-element te plaatsen.

> 25-09-2020 - het lijkt er op dat Datadog metrics die er niet meer zijn, nog 24 uur bewaard

> 20-10-2020 - deze tip had ik 10 jaar eerder moeten hebben 🤦‍♂️ : [RouteDebugger](https://haacked.com/archive/2008/03/13/url-routing-debugger.aspx/) door Phil Haack 😁

> 22-10-2020 - wanneer NHibernate begint te zeuren over "Could not instantiate abstract class" terwijl de betreffende class wel de annotatie "abstract = true" heeft, dikke kans dat de subclass mist in de hbm file.

> 02-11-2020 - ik vergeet steeds het uitpakken commando, maar als ik het nu eens met een Duits accent zeg : "tar eXtract Ze File bestand.tar.gz"

> 10-11-2020 - Met NHibernate kun je vrij goed joinen op basis van natuurlijke sleutels, maar stel nu dat ik tabellen zonder associatie wil joinen? Dat kan (eindelijk) met [JoinEntityAlias](https://nhibernate.info/doc/nh/en/index.html#queryqueryover_entityjoin) 😁

> 19-11-2020 - Git clonen van je eigen private repo zonder username en ww invullen met [deze](https://sysadmins.co.za/clone-a-private-github-repo-with-personal-access-token/) dikke tip!

> 26-11-2020 - je kan meerdere .env files naast meerdere docker-compose files hebben staan 😁

> 27-11-2020 - vimium is een extensie voor Chrome, Firefox en zelfs Edge, waarmee je toetsenbord snelkoppelingen kan gebruiken in de browser

> 27-11-2020 - een poort wijziging in een webapp project in VS2019 is natuurlijk nooit een goed idee, maar als het dan toch moet ook meteen de "override application root url" aanpassen naar de nieuwe poort en daarna weer dat vinkje uitzetten helpt.

## 2021
> 06-01-2021 - wow ik wist helemaal niet dat de stackoverflow/stackexchange-interwebs ook vragen zoals [deze](https://aviation.stackexchange.com/questions/83342/did-the-germans-ever-use-captured-allied-aircraft-against-the-allies) worden gesteld...

> 04-02-2021 - Azure Data Studio heeft gelukkig ook [snippets](https://docs.microsoft.com/en-us/sql/azure-data-studio/code-snippets?view=sql-server-ver15). Nooit aan gedacht omdat ik zo gewend was aan SSMS

> 16-02-2021 - Text wrap met CSS! Soms moet ik iets met frontend doen en ik wist helemaal niet dat [dit](https://www.w3schools.com/cssref/css3_pr_text-overflow.asp) kon.

> 09-03-2021 - Je kan getallen zoals 1000000 in C# ook leesbaar noteren met underscores zoals : 1\_000\_000 !!!

> 23-03-2021 - Conditional styling in Angular (of andere frameworks) is :vet:

> 29-04-2021 - `avahi-browse -aplr` is een betere nmap -sP <ipadres> :)

> 06-06-2021 - Met variables zoals `${__field.name}` kun je je legenda in grafana grafieken aanpassen

> 07-09-2021 - De debugger in bijvoorbeeld Chrome kan de value van een object undefined maken :')

> 16-09-2021 - Wanneer mn NAS een reboot heeft gehad kan Sonarqube niet starten vanwege memory, dus dan dit doen : `sudo sysctl -w vm.max_map_count=262144`

> 03-11-2021 - `[[inputs.internet_speed]]` in telegraf zorgt om de zoveel tijd voor een boel up en down verkeer. Een pingetje is net zo handig ;)

> 04-11-2021 - Ik ben grijs, maar ik heb vandaag pas ontdekt dat je in SQL ook een underscore \_ in pattern matching kan gebruiken

> 08-12-2021 - ngrok is een quick en dirty manier om ff snel een feature aan een klant te laten zien `ngrok http -host-header="localhost:58821" 58821`

> 15-12-2021 - een snapd applicatie kan ik alleen maar pinnen aan het panel als ik de .desktop file in de `/var/lib/snapd/desktop/applications` folder kopieer naar `~/.local/share/applications`

## 2022
> 12-01-2022 - een container met sqlserver op mijn Synology NAS draait niet uit zichzelf, soms moet je em helpen met een `chown 10001:0` op het volume waar sqlserver zit.

> 17-02-2022 - geen TIL maar ff een reminder voor mezelf. Mocht ik ooit (nog steeds) in Visual Studio 2022 ofzo zitten en ruzie hebben met assembly bindings : zet het vinkje "_enable just my code_" uit om excepties te gooien die betrekking hebben op loaderexceptions.

> 11-03-2022 - sinds versie 4.11.0 van Moq, kun je pas een geMockt object met IEquatable comparen.

> 17-03-2022 - image resizen met imagemagick : `mogrify -sampling-factor 4:2:0 -strip -quality 85 -resize 50% *.JPG`

> 06-04-2022 - omdat ik op Manjaro vscode nog wel eens update, kan het zijn dat vscode rsa kwijt is. Dat is te fixen door `yay -S openssh-askpass` te doen en daarna opnieuw de sync uit te voeren.

> 26-04-2022 - [fusion++](https://github.com/awaescher/Fusion) is een fancy pancy FUSLOGVW

> 04-05-2022 - Als bi-directional copy/paste in VirtualBox niet meer werkt : opnieuw Guest Editions installeren

> 21-07-2022 - Veel te warm om binnen te zitten op wordpress te klooien, maar ik moet gewoon niet vergeten dat `netsh http add urlacl url = http://+[poort]/ user=Everyone` best wel vet handig is in sommige situaties

> 21-07-2022 - Maarja als ik er toch zit voor future-me : VirtualBox met Linux als host en Windows als guest, kan die instabiel worden als o.a. de hypervisor is [geinstalleerd bij OptionalFeatures.exe](https://answers.microsoft.com/en-us/windows/forum/all/why-did-my-pc-restart-problem-windows-10-vm/cfcadcf1-d29d-4f99-9ee5-97e4cfdda350)

> 03-08-2022 - Dikke tip voor future-me : Als je je virtualbox vrij veel mem geeft (24gb bijvoorbeeld voor een windows VM) dan is het wel slim om de swappiness van 60 terug te brengen naar iets van 5 of 10 anders gaat je VM om de haverklap deaud 😉

> 22-08-2022 - Jenkins heeft een Replay functie bij een gefaalde build waardoor je de build opnieuw kan doen en zelfs groovy script kan aanpassen. Scheelt een hoop wachten op PRs!

> 03-11-2022 - OMG je kan zoveel met CSS, vandaag ben ik er per ongelijk achter gekomen dat er zoiets als [cubic-bezier](https://www.w3schools.com/CSSref/func_cubic-bezier.php) bestaat 🙃

> 10-11-2022 - VSCode heeft met de docker extensie de optie (CTRL+P) om `Add docker files to workspace` te doen

> 01-12-2022 - Future me : vervang die `.FutureValue<int>.Value > 0` nu eens gewoon door `.SingleOrDefault<int>() > 0`

> 12-12-2022 - Indien .local hostnames in het netwerk niet geresolved worden, ff checken of avahi wel draait : `systemctl list-unit-files | grep avahi` en dan evt aanzetten en starten.

> 14-12-2022 - Als git (in bijv. vscode) bij een zojuist ge-git-clonede repo bedenkt dat ik niet mag pushen, terwijl het mijn eigen repo is, dan even `git config credential.helper` doen.

## 2023
> 05-01-2023 - `arp-scan` is een mooie manier om IP addressen in een bepaalde netwerk range te displayen met hun MAC-adres 😀

> 07-03-2023 - Indien je pas nieuw gekochte ruitenwissers "stoekeren" dan is het misschien handig om eens te kijken in welke hoek de rubbers op de ruit staan.

> 21-04-2023 - Om een hangende jenkins-job te stoppen is het blijkbaar niet genoeg om op de stopknop te drukken. Je moet daarna in de console op een link drukken.

> 09-05-2023 - Indien tijdens een `npm run tsc:w` deze melding komt : `Assertion failed: new_time >= loop->time` dan gewoon Windows (VM) herstarten of Automatisch tijd synchronisatie herstarten.

> 06-06-2023 - `curl wttr.in/losser` is de aller vetste weer-app ooit

> 09-06-2023 - Sinds NLOG40 is er een probleem met teveel info logging. De final wordt niet gerespecteerd, dus de alle aparte loggers vallen in de laatste regel van \* en die minlevel is Info

> 08-09-2023 - Omdat ik nog wel eens verschillende werkplekken heb (thuis/kantoor) kan ik snel zien welke monitoren ik heb in i3 : `xrandr --query | grep '\bconnected\b'`

> 10-10-2023 - Op windows met hulp van powershell tellen hoeveel gewijzigde files je hebt in je lokale repo : `git status -s -uno | Measure-Object -Line`

> 12-10-2023 - Het is niet handig om globally een andere Angular CLI versie te installeren, dan de versie die in je project wordt genoemd in de tsconfig.json

> 27-10-2023 - Als ik nog eens een keer een veel te grote audiobook heb gevonden en die wil ik splitten : `mp3splt inputfile.mp3 -a -t 60.0 -o NaamVanBoek_@n`

> 29-10-2023 - Als de kobo langzaam wordt in het pagina's omslaan et cetera, dan is kepubify wel een handige tool om de boel sneller te maken.

> 06-11-2023 - Zogenaamde hot paths (warme paden) vinden in git : `git log --format=format: --name-only --since=12.month | egrep -v '^$' | sort | uniq -c | sort -nr | head -50`

> 29-11-2023 - Text editten in windows powershell of cmd : `& 'C:\Program Files\Git\usr\bin\nano.exe' textfilenaam`

> 12-12-2023 - de CONCAT functie kan in TSQL "maar" 254 parameters aan. Ask me how I know 😉

> 20-12-2023 - `export $(xargs <.env)` exporteert alles wat er in .env zit : awesomecakes! Hoe elegant en simpel eigenlijk 😎

> 28-12-2023 - Op de valreep : Jetbrains Rider in Linux snapt helemaal niets meer van een web-project als je een folder genaamd "C:\\Log" maakt 😂

## 2024
> 23-02-2024 - Mocht ik met mn stomme kop een keer de wifi uit hebben gezet (met de PrtScr) dan kan dat in terminal weer aan : `nmcli r wifi on` en daarna : `nmcli d wifi connect "<SSID>" password "<password>"`

> 26-02-2024 - Een backup van alle database maken in mariadb : `docker exec <gekke id> mysqldump -uroot -p<supergeheim> -h127.0.0.1 --protocol=tcp --all-databases > mariadb-dump-$(date +%F_%H-%M-%S).sql`

> 02-04-2024 - Accu info van mn laptop : `upower -i /org/freedesktop/UPower/devices/battery_BAT0`

> 16-04-2024 - Het is mogelijk in libreelec het ip adres static te maken : `connmanctl config "$(connmanctl services | awk '/^\*/ {print $3}')" --ipv4 manual 192.168.178.90 255.255.255.0 192.168.178.1`

> 13-05-2024 - Als je zo snugger bent geweest om het verkeerde ticket nummer in je branch te stoppen (ask me how I know) dan kun je dat amenden : `git commit --amend` en daarna (mits local en origin branch zelfde) `git push --force-with-lease`

> 02-06-2024 - Als ik het weer eens nodig vind om in Blazor een input veld zowel de binden als een onchange-event op te laten afgaan, dan kan dat bijvoorbeeld zo : `<input type="text" @bind-value:event="oninput" @onchange=@(args => OnChange(args, "maximum")) @bind-value=@voorkeur.Maximum />`

> 19-07-2024 - Mocht ik ooit nog ergens een keer directory.packages.props willen maken, er is een tool die dat genereert en het dus niet met de hand : [https://github.com/Webreaper/CentralisedPackageConverter](https://github.com/Webreaper/CentralisedPackageConverter)

> 26-09-2024 - Indien ooit nodig : consul fatsoenlijk in windows draaien : `c:\tools\consul\consul.exe agent -server -bootstrap -data-dir="C:\tools\consul\data" -client="127.0.0.1" -bind="127.0.0.1" -ui`

> 30-09-2024 - wat heb ik zelf op manjaro geinstalleerd : `comm -12 <(grep -Poe '[ALPM] installed \K\S*' /var/log/pacman.log | sort | uniq) <(pacman -Qeqm | sort)` of gewoon : `yay -Ps`

> 01-10-2024 - seamless muizen en clipboard functies in virt-manager : https://www.spice-space.org/download/windows/spice-guest-tools/spice-guest-tools-latest.exe

> 12-11-2024 - Voor future me als ik weer vergeten ben : `ssh-keygen -t rsa -b 4096 -C "emailadres@website.nl"`

> 17-11-2024 - Sinds Prometheus 3.0 is er een mogelijkheid dat bijvoorbeeld ping errors krijgt. De global setting `fallback_scrape_protocol` kun je dan op `PrometheusText0.0.4` zetten zodat het gedrag hetzelfde blijft.

## 2025
> 25-02-2025 - Als een (deb) package installen faalt omdat je zo nodig een google coral TPU aan de praat wil krijgen op je mini PC voor Frigate NVR, moet je soms zelf enge C debuilden : https://github.com/google/gasket-driver/issues/27

> 14-03-2025 - Laptop herstarten (ipv afloggen en aanloggen) als je je user in de `lock` en `uucp` groep hebt gezet nadat je Arduino Studio hebt geinstalleerd om je ESP32 te flashen. 