---
title: Hoe het uitvoeren van uw code met Azure Dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen voor het uitvoeren van uw code op Azure Kubernetes Service met Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241359"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Hoe het uitvoeren van uw code met Azure Dev Spaces werkt

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team op een AKS-cluster (Azure Kubernetes Service). Zodra uw [project is voorbereid om te worden uitgevoerd in een dev-ruimte,][how-it-works-prep]u Dev Spaces gebruiken om uw project in uw AKS-cluster te bouwen en uit te voeren.

In dit artikel wordt beschreven wat er gebeurt en voert u uw code uit in AKS met Dev Spaces.

## <a name="run-your-code"></a>Uw code uitvoeren

Als u uw code wilt uitvoeren `up` in een dev-ruimte, geeft u de opdracht uit in dezelfde map als uw `azds.yaml` bestand:

```cmd
azds up
```

De `up` opdracht uploadt uw toepassingsbronbestanden en andere artefacten die nodig zijn om uw project te bouwen en uit te voeren naar de dev-ruimte. Van daaruit, de controller in uw dev ruimte:

1. Hiermee maakt u de Kubernetes-objecten om uw toepassing te implementeren.
1. Hiermee bouwt u de container voor uw toepassing.
1. Implementeert uw toepassing naar de dev-ruimte.
1. Hiermee maakt u een openbaar toegankelijke DNS-naam voor het eindpunt van uw toepassing als deze is geconfigureerd.
1. Gebruikt *port forward* om toegang te bieden http://localhosttot het eindpunt van uw toepassing met behulp van .
1. Doorsturen stdout en stderr naar de client-side tooling.


## <a name="starting-a-service"></a>Een service starten

Wanneer u een service start in een dev-ruimte, werken de client-side tooling en controller in coördinatie om uw bronbestanden te synchroniseren, uw container- en Kubernetes-objecten te maken en uw toepassing uit te voeren.

Op een meer gedetailleerd niveau, hier `azds up`is wat er gebeurt als je uit te voeren:

1. [Bestanden worden gesynchroniseerd][sync-section] vanaf de computer van de gebruiker naar een Azure-bestandsopslag die uniek is voor het AKS-cluster van de gebruiker. De broncode, helmdiagram en configuratiebestanden worden geüpload.
1. De controller maakt een verzoek om een nieuwe sessie te starten. Deze aanvraag bevat verschillende eigenschappen, waaronder een unieke id, ruimtenaam, pad naar broncode en een foutopsporingsvlag.
1. De controller vervangt de tijdelijke aanduiding *$(tag)* in de Helm-grafiek door de unieke sessie-id en installeert de Helm-grafiek voor uw service. Als u een verwijzing naar de unieke sessie-id toevoegt aan de helmgrafiek, kan de container die is geïmplementeerd in het AKS-cluster worden gekoppeld aan deze specifieke sessie, worden gekoppeld aan de sessieaanvraag en bijbehorende informatie.
1. Tijdens de installatie van de Helm-grafiek voegt de Kubernetes webhook-toegangsserver extra containers toe aan de pod van uw toepassing voor instrumentatie en toegang tot de broncode van uw project. De devspaces-proxy- en devspaces-proxy-init-containers worden toegevoegd om HTTP-tracering en ruimteroutering te bieden. De devspaces-build container wordt toegevoegd om de pod toegang te geven tot de Docker-instantie en de projectbroncode voor het bouwen van de container van uw toepassing.
1. Wanneer de pod van de toepassing wordt gestart, worden de devspaces-build container en devspaces-proxy-init container gebruikt om de toepassingscontainer te bouwen. De toepassingscontainer- en devspaces-proxycontainers worden vervolgens gestart.
1. Nadat de toepassingscontainer is gestart, gebruikt de client-side functionaliteit de Kubernetes *port-forward functionaliteit* om HTTP-toegang tot uw toepassing te bieden. http://localhost Deze poort forwarding verbindt uw ontwikkelcomputer met de service in uw ontwikkelruimte.
1. Wanneer alle containers in de pod zijn gestart, wordt de service uitgevoerd. Op dit punt begint de client-side functionaliteit de HTTP-sporen, stdout en stderr te streamen. Deze informatie wordt weergegeven door de client-side functionaliteit voor de ontwikkelaar.

## <a name="updating-a-running-service"></a>Een lopende service bijwerken

Terwijl een service wordt uitgevoerd, heeft Azure Dev Spaces de mogelijkheid om die service bij te werken als een van de projectbronbestanden verandert. Dev Spaces verwerkt ook het bijwerken van de service anders, afhankelijk van het type bestand dat wordt gewijzigd. Er zijn drie manieren waarop Dev Spaces een hardloopservice kan bijwerken:

* Een bestand rechtstreeks bijwerken
* Het proces van de toepassing opnieuw opbouwen en opnieuw starten in de container van de lopende toepassing
* De container van de toepassing opnieuw opbouwen en opnieuw implementeren

![Bestandssynchronisatie azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Bepaalde projectbestanden die statische elementen zijn, zoals html-, css- en cshtml-bestanden, kunnen rechtstreeks in de container van de toepassing worden bijgewerkt zonder iets opnieuw op te starten. Als een statische asset verandert, wordt het nieuwe bestand gesynchroniseerd met de dev-ruimte en vervolgens gebruikt door de lopende container.

Wijzigingen in bestanden zoals broncode of toepassingsconfiguratiebestanden kunnen worden toegepast door het proces van de toepassing opnieuw te starten in de lopende container. Zodra deze bestanden zijn gesynchroniseerd, wordt het proces van de toepassing opnieuw gestart in de lopende container met behulp *van het devhostagent-proces.* Bij het maken van de container van de toepassing vervangt de controller de opstartopdracht voor de toepassing door een ander proces genaamd *devhostagent.* Het werkelijke proces van de toepassing wordt vervolgens uitgevoerd als een onderliggend proces onder *devhostagent,* en de uitvoer ervan wordt uitgeleid met behulp van de uitvoer van *devhostagent.* Het *devhostagent-proces* maakt ook deel uit van Dev Spaces en kan opdrachten uitvoeren in de lopende container namens Dev Spaces. Bij het uitvoeren van een herstart, *devhostagent:*

* Hiermee stopt u het huidige proces of de bijbehorende processen die aan de toepassing zijn gekoppeld
* De toepassing opnieuw opbouwen
* Het proces of de processen die aan de toepassing zijn gekoppeld opnieuw starten

De manier waarop *de vhostagent* de voorgaande stappen uitvoert, is [geconfigureerd `azds.yaml`in ][azds-yaml-section].

Voor updates van projectbestanden zoals Dockerfiles, csproj-bestanden of een deel van de Helm-grafiek moet de container van de toepassing opnieuw worden opgebouwd en opnieuw worden geïmplementeerd. Wanneer een van deze bestanden is gesynchroniseerd met de dev-ruimte, voert de controller de opdracht [helmupgrade uit][helm-upgrade] en wordt de container van de toepassing opnieuw opgebouwd en opnieuw geïmplementeerd.

## <a name="file-synchronization"></a>Bestandssynchronisatie

De eerste keer dat een toepassing wordt gestart in een dev-ruimte, worden alle bronbestanden van de toepassing geüpload. Terwijl de toepassing wordt uitgevoerd en later opnieuw wordt opgestart, worden alleen de gewijzigde bestanden geüpload. Twee bestanden worden gebruikt om dit proces te coördineren: een client-side file en een controller-side file.

Het client-side bestand wordt opgeslagen in een tijdelijke map en wordt benoemd op basis van een hash van de projectmap die u uitvoert in Dev Spaces. Op Windows hebt u bijvoorbeeld een bestand zoals *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* voor uw project. Op Linux wordt het client-side bestand opgeslagen in de */tmp* directory. U de map op macOS vinden door de `echo $TMPDIR` opdracht uit te voeren.

Dit bestand is in JSON-indeling en bevat:

* Een vermelding voor elk projectbestand dat is gesynchroniseerd met de dev-ruimte
* Een synchronisatie-id
* De tijdstempel van de laatste synchronisatiebewerking

Elke projectbestandsinvoer bevat een pad naar het bestand en de tijdstempel.

Het controller-side bestand wordt opgeslagen in het AKS-cluster. Het bevat de synchronisatie-ID en de tijdstempel van de laatste synchronisatie.

Een synchronisatie gebeurt wanneer de synchronisatietijdstempels niet overeenkomen tussen de clientzijde en de bestanden aan de controllerzijde. Tijdens een synchronisatie wordt de tooling aan de clientzijde over de bestandsvermeldingen in het client-side-bestand gehesen. Als de tijdstempel van het bestand achter de synchronisatietijdstempel zit, wordt dat bestand gesynchroniseerd met de dev-ruimte. Zodra de synchronisatie is voltooid, worden de synchronisatietijdstempels bijgewerkt op zowel de client- als controller-side bestanden.

Alle projectbestanden worden gesynchroniseerd als het client-side bestand niet aanwezig is. Met dit gedrag u een volledige synchronisatie forceren door het client-side bestand te verwijderen.

## <a name="how-running-your-code-is-configured"></a>Hoe het uitvoeren van uw code is geconfigureerd

Azure Dev Spaces `azds.yaml` gebruikt het bestand om uw service te installeren en te configureren. De controller `install` gebruikt de `azds.yaml` eigenschap in het bestand om de Helm-grafiek te installeren en de Kubernetes-objecten te maken:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Standaard genereert `prep` de opdracht de grafiek Helm. Het stelt ook de eigenschap *install.chart in* op de map van de Helm-grafiek. Als u een Helm-diagram op een andere locatie wilt gebruiken, u deze eigenschap bijwerken om die locatie te gebruiken.

Bij het installeren van de Helm-diagrammen biedt Azure Dev Spaces een manier om waarden in de helmgrafiek te overschrijven. De standaardwaarden voor de `charts/APP_NAME/values.yaml`grafiek Helm bevinden zich in .

Met de eigenschap *install.values* u een of meer bestanden weergeven die waarden definiëren die u wilt vervangen in de grafiek Helm. Als u bijvoorbeeld een hostnaam of databaseconfiguratie specifiek wilt wanneer u uw toepassing in een dev-ruimte uitvoert, u deze override-functionaliteit gebruiken. U ook een *?* aan het einde van een van de bestandsnamen om het als optioneel in te stellen.

Met de eigenschap *install.set* u een of meer waarden configureren die u wilt vervangen in de helmgrafiek. Alle waarden die zijn geconfigureerd in *install.set* overschrijven waarden die zijn geconfigureerd in bestanden die worden vermeld in *install.values*. De eigenschappen onder *install.set* zijn afhankelijk van de waarden in de helmgrafiek en kunnen verschillen afhankelijk van de gegenereerde Helm-grafiek.

In het bovenstaande voorbeeld vertelt de eigenschap *install.set.replicaCount* de controller hoeveel exemplaren van uw toepassing in uw dev-ruimte moeten worden uitgevoerd. Afhankelijk van uw scenario u deze waarde verhogen, maar het heeft invloed op het koppelen van een foutopsporing aan de pod van uw toepassing. Zie het artikel [over probleemoplossing][troubleshooting]voor meer informatie.

In de gegenereerde Helm-grafiek wordt de containerafbeelding ingesteld op *{{ . Waarden.image.repository }}:{{ . Waarden.image.tag }}*. Het `azds.yaml` bestand definieert standaard de eigenschap *install.set.image.tag* als *$(tag),* die standaard wordt gebruikt als de waarde voor *{{ . Waarden.image.tag }}*. Door de eigenschap *install.set.image.tag* op deze manier in te stellen, kan de containerafbeelding voor uw toepassing op een afzonderlijke manier worden getagd wanneer azure dev-ruimten worden uitgevoerd. In dit specifieke geval wordt de afbeelding getagd als * \<waarde van image.repository>:$(tag)*. U moet de variabele *$(tag)* gebruiken als de waarde van *install.set.image.tag* om de container in het AKS-cluster te herkennen en te lokaliseren.

`azds.yaml` Definieert in het bovenstaande voorbeeld *install.set.ingress.hosts*. De eigenschap *install.set.ingress.hosts* definieert een hostnaamindeling voor openbare eindpunten. Deze eigenschap maakt ook gebruik van *$(spacePrefix),* *$(rootSpacePrefix)* en *$(hostSuffix),* die waarden zijn die door de controller worden verstrekt.

Het *$(spacePrefix)* is de naam van de onderliggende dev-ruimte, die de vorm aanneemt van *SPACENAME.s*. Het *$(rootSpacePrefix)* is de naam van de bovenliggende ruimte. Als *azureuser* bijvoorbeeld een *onderliggende*standaardruimte is, is de waarde voor *$(rootSpacePrefix)* *standaard* en is de waarde van *$(spacePrefix)* *azureuser.s.* Als de ruimte geen onderliggende ruimte is, is *$(spacePrefix)* leeg. Als de *standaardruimte* bijvoorbeeld geen bovenliggende ruimte heeft, is de waarde voor *$(rootSpacePrefix)* *standaard* en is de waarde van *$(spacePrefix)* leeg. Het *$(hostSuffix)* is een DNS-achtervoegsel dat verwijst naar de Azure Dev Spaces Ingress-controller die wordt uitgevoerd in uw AKS-cluster. Dit DNS-achtervoegsel komt overeen * \*met bijvoorbeeld een DNS-vermelding met jokertekens. RANDOM_VALUE.eus.azds.io*, die is gemaakt toen de Azure Dev Spaces-controller werd toegevoegd aan uw AKS-cluster.

In het `azds.yaml` bovenstaande bestand u *install.set.ingress.hosts* ook bijwerken om de hostnaam van uw toepassing te wijzigen. Als u bijvoorbeeld de hostnaam van uw toepassing wilt vereenvoudigen van *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* tot *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix).*

Om de container voor uw toepassing te bouwen, `azds.yaml` gebruikt de controller de onderstaande secties van het configuratiebestand:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

De controller gebruikt een Dockerfile om uw toepassing te bouwen en uit te voeren.

De eigenschap *build.context* geeft de map weer waar de Dockerfiles bestaan. De eigenschap *build.dockerfile* definieert de naam van het Dockerfile voor het bouwen van de productieversie van de toepassing. De eigenschap *configurations.develop.build.dockerfile* configureert de naam van het Dockerfile voor de ontwikkelversie van de toepassing.

Met verschillende Dockerfiles voor ontwikkeling en productie u bepaalde dingen inschakelen tijdens de ontwikkeling en deze artikelen uitschakelen voor productie-implementaties. U bijvoorbeeld foutopsporing of meer verboselogboeking inschakelen tijdens de ontwikkeling en uitschakelen in een productieomgeving. U deze eigenschappen ook bijwerken als uw Dockerbestanden anders worden benoemd of zich op een andere locatie bevinden.

Om u te helpen snel te herhalen tijdens de ontwikkeling, synchroniseert Azure Dev Spaces wijzigingen van uw lokale project en werkt u uw toepassing stapsgewijs bij. Het onderstaande `azds.yaml` gedeelte in het configuratiebestand wordt gebruikt om de synchronisatie en update te configureren:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

De bestanden en mappen die wijzigingen synchroniseren, worden weergegeven in de eigenschap *configurations.develop.container.sync.* Deze mappen worden in eerste instantie `up` gesynchroniseerd wanneer u de opdracht uitvoert en wanneer wijzigingen worden gedetecteerd. Als er extra of verschillende mappen zijn die u wilt synchroniseren met uw dev-ruimte, u deze eigenschap wijzigen.

De eigenschap *configurations.develop.container.iterate.buildCommands* geeft aan hoe u de toepassing in een ontwikkelingsscenario bouwen. De eigenschap *configurations.develop.container.command* biedt de opdracht voor het uitvoeren van de toepassing in een ontwikkelingsscenario. U een van deze eigenschappen bijwerken als er extra build- of runtimevlaggen of parameters zijn die u tijdens de ontwikkeling wilt gebruiken.

De *configurations.develop.container.iterate.processesToKill* geeft een overzicht van de processen die moeten worden gedood om de toepassing te stoppen. U deze eigenschap bijwerken als u het herstartgedrag van uw toepassing tijdens de ontwikkeling wilt wijzigen. Als u bijvoorbeeld de eigenschappen *configurations.develop.develop.iterate.build.buildCommands* of *configurations.develop.container.command* hebt bijgewerkt om de manier waarop de toepassing is gebouwd of gestart te wijzigen, moet u mogelijk wijzigen welke processen worden gestopt.

Wanneer u uw code `azds prep` voorbereidt met de opdracht, u de `--enable-ingress` vlag toevoegen. Als `--enable-ingress` u de vlag toevoegt, wordt een openbaar toegankelijke URL voor uw toepassing gemaakt. Als u deze vlag weglaat, is de toepassing alleen toegankelijk binnen het cluster of met behulp van de localhost-tunnel. Nadat u `azds prep` de opdracht hebt uitgevoerd, u deze instelling wijzigen waarbij de eigenschap *in -instelling wordt gewijzigd* in `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Volgende stappen

Zie Hoe [routering werkt met Azure Dev Spaces][how-it-works-routing]voor meer informatie over netwerken en hoe aanvragen worden doorgestuurd in Azure Dev Spaces.

Zie Hoe het aansluiten van [uw ontwikkelcomputer op uw dev-ruimte werkt][how-it-works-connect] en hoe [externe debuggen van uw code werkt voor][how-it-works-remote-debugging]het snel wissen en ontwikkelen van Azure Dev Spaces voor meer informatie over het gebruik van Azure Dev Spaces.

Zie de volgende snelstarts om aan de slag te gaan met Azure Dev Spaces om uw project uit te voeren:

* [Snel herhalen en debuggen met Visual Studio Code en Java][quickstart-java]
* [Snel herhalen en debuggen met Visual Studio Code en .NET][quickstart-netcore]
* [Snel herhalen en debuggen met Visual Studio Code en Node.js][quickstart-node]
* [Snel herhalen en debuggen met Visual Studio en .NET Core][quickstart-vs]
* [De CLI gebruiken om een toepassing op Kubernetes te ontwikkelen][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md