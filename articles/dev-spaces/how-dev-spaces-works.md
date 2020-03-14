---
title: Hoe Azure dev Spaces werkt en is geconfigureerd
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Hierin worden de processen beschreven die Power Azure-ontwikkel ruimten en hoe ze worden geconfigureerd in het configuratie bestand azds. yaml.
keywords: azds. yaml, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259914"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Hoe Azure dev Spaces werkt en is geconfigureerd

Het ontwikkelen van een Kubernetes-toepassing kan lastig zijn. U hebt configuratie bestanden voor docker en Kubernetes nodig. U moet nagaan hoe u uw toepassing lokaal kunt testen en interactie met andere afhankelijke services. Mogelijk moet u ontwikkelen en testen op meerdere services tegelijk en met een team van ontwikkel aars afhandelen.

Met Azure dev Spaces kunt u Kubernetes-toepassingen rechtstreeks ontwikkelen, implementeren en fouten opsporen in azure Kubernetes service (AKS). Met Azure dev Spaces kan een team ook een dev-ruimte delen. Het delen van een dev-ruimte over een team stelt afzonderlijke team leden in staat om te ontwikkelen zonder dat afhankelijkheden of andere toepassingen in het cluster moeten worden gerepliceerd of gesimuleerd.

Met Azure dev Spaces maakt en gebruikt u een configuratie bestand voor het implementeren, uitvoeren en opsporen van fouten in uw Kubernetes-toepassingen in AKS. Dit configuratie bestand bevindt zich in de code van uw toepassing en kan worden toegevoegd aan uw versie beheersysteem.

In dit artikel worden de processen beschreven die Power Azure-ontwikkel ruimten en hoe deze processen worden geconfigureerd in het configuratie bestand voor Azure dev Spaces. Als u Azure dev Spaces snel wilt uitvoeren en dit in de praktijk wilt zien, kunt u een van de Quick starts volt ooien:

* [Java met CLI en Visual Studio code](quickstart-java.md)
* [.NET core met CLI en Visual Studio code](quickstart-netcore.md)
* [.NET core met Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js met CLI en Visual Studio code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Hoe Azure Dev Spaces werkt

Azure dev Spaces heeft twee afzonderlijke onderdelen waarmee u communiceert: de controller en het hulp programma aan de client zijde.

![Azure dev Space-onderdelen](media/how-dev-spaces-works/components.svg)

De controller voert de volgende acties uit:

* Beheert de ontwikkelings ruimte die wordt gemaakt en geselecteerd.
* Hiermee wordt het helm-diagram van uw toepassing geïnstalleerd en worden Kubernetes-objecten gemaakt.
* Hiermee bouwt u de container installatie kopie van uw toepassing.
* Hiermee implementeert u uw toepassing in AKS.
* Hiermee worden incrementele builds gemaakt en wordt de computer opnieuw opgestart wanneer de bron code wordt gewijzigd.
* Hiermee beheert u Logboeken en HTTP-traceringen.
* Stdout en stderr worden doorgestuurd naar het hulp programma aan de client zijde.
* Hiermee kunnen team leden onderliggende ontwikkel ruimten maken die zijn afgeleid van een bovenliggende ontwikkel ruimte.
* Hiermee configureert u route ring voor toepassingen binnen een ruimte en over de bovenliggende en onderliggende spaties.

De controller bevindt zich buiten AKS. Hiermee worden het gedrag en de communicatie tussen het programma aan de client zijde en het AKS-cluster gestimuleerd. De controller wordt ingeschakeld met behulp van de Azure CLI wanneer u uw cluster voorbereidt op het gebruik van Azure dev Spaces. Als deze functie is ingeschakeld, kunt u hiermee communiceren met behulp van het hulp programma aan de client zijde.

Met het hulp programma aan de client zijde kan de gebruiker het volgende doen:
* Genereer een configuratie bestand voor Dockerfile, helm en Azure dev Spaces voor de toepassing.
* Maak bovenliggende en onderliggende ontwikkel ruimten.
* Vertel de controller om uw toepassing te bouwen en te starten.

Terwijl uw toepassing wordt uitgevoerd, wordt het hulp programma voor de client ook gebruikt:
* Hiermee worden stdout en stderr ontvangen en weer gegeven vanuit uw toepassing die wordt uitgevoerd in AKS.
* Maakt gebruik van [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) om webtoegang toe te staan voor uw toepassing via http:\//localhost.
* Hiermee koppelt u een fout opsporingsprogramma aan uw actieve toepassing in AKS.
* Synchroniseert de bron code naar uw ontwikkel ruimte wanneer een wijziging wordt gedetecteerd voor incrementele builds, waardoor snelle herhaling mogelijk is.

U kunt het hulp programma aan de client zijde vanaf de opdracht regel gebruiken als onderdeel van de `azds` opdracht. U kunt ook het hulp programma aan de client zijde gebruiken met:

* Visual Studio code met behulp van de [Azure dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio met [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools).

Dit is de basis stroom voor het instellen en gebruiken van Azure dev Spaces:
1. Uw AKS-cluster voorbereiden voor Azure dev Spaces
1. Bereid uw code voor op het uitvoeren van Azure-ontwikkel ruimten
1. Uw code uitvoeren op een dev-ruimte
1. Fouten opsporen in uw code op een dev-ruimte
1. Een ontwikkelings ruimte delen

Hier vindt u meer informatie over hoe Azure-ontwikkel ruimten werken in elk van de onderstaande gedeelten.

## <a name="prepare-your-aks-cluster"></a>Uw AKS-cluster voorbereiden

Voor het voorbereiden van uw AKS-cluster moeten:
* Het verifiëren van uw AKS-cluster bevindt zich in een regio [die wordt ondersteund door Azure dev Spaces][supported-regions].
* Controleer of Kubernetes 1.10.3 of hoger wordt uitgevoerd.
* Azure dev-ruimten in uw cluster inschakelen met behulp van `az aks use-dev-spaces`

Zie een van de aan de slag-hand leidingen voor meer informatie over het maken en configureren van een AKS-cluster voor Azure-ontwikkel ruimten:
* [Aan de slag met Azure dev Spaces met Java](get-started-java.md)
* [Aan de slag met Azure-ontwikkel ruimten met .NET core en Visual Studio](get-started-netcore-visualstudio.md)
* [Aan de slag met Azure dev Spaces met .NET core](get-started-netcore.md)
* [Aan de slag met Azure-ontwikkel ruimten met node. js](get-started-nodejs.md)

Wanneer Azure dev Spaces is ingeschakeld op uw AKS-cluster, wordt de controller voor het cluster geïnstalleerd. De controller is een afzonderlijke Azure-resource buiten uw cluster en doet het volgende voor resources in het cluster:

* Hiermee wordt een Kubernetes-naam ruimte gemaakt of toegewezen die moet worden gebruikt als een dev-ruimte.
* Hiermee verwijdert u een Kubernetes-naam ruimte met de naam *azds*, als deze bestaat en maakt u een nieuwe.
* Hiermee implementeert u een Kubernetes-webhook-configuratie.
* Hiermee implementeert u een toegangs server voor webhooks.
    

Ook wordt dezelfde service-principal gebruikt die door uw AKS-cluster wordt gebruikt om service aanroepen naar andere Azure dev Space-onderdelen te maken.

![Azure dev Spaces voorbereiden cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Als u Azure dev Spaces wilt gebruiken, moet er ten minste één dev-ruimte zijn. Azure dev Spaces maakt gebruik van Kubernetes-naam ruimten in uw AKS-cluster voor dev Spaces. Wanneer een controller wordt geïnstalleerd, wordt u gevraagd een nieuwe Kubernetes-naam ruimte te maken of een bestaande naam ruimte te kiezen die moet worden gebruikt als uw eerste dev-ruimte. Wanneer een naam ruimte wordt aangewezen als een dev-ruimte, voegt de controller het label *azds.io/Space=True* toe aan die naam ruimte om het te identificeren als een dev-ruimte. De eerste ontwikkel ruimte die u maakt of aanwijst, is standaard geselecteerd nadat u het cluster hebt voor bereid. Wanneer er een ruimte is geselecteerd, wordt deze door Azure-ontwikkel ruimten gebruikt voor het maken van nieuwe werk belastingen.

Standaard maakt de controller een ontwikkel ruimte met de naam *standaard* door de bestaande *standaard* Kubernetes-naam ruimte bij te werken. U kunt het hulp programma aan de client zijde gebruiken om nieuwe ontwikkel ruimten te maken en bestaande ontwikkel ruimten te verwijderen. Als gevolg van een beperking in Kubernetes kan de *standaard* -ontwikkel ruimte niet worden verwijderd. De controller verwijdert ook alle bestaande Kubernetes-naam ruimten met de naam *azds* om te voor komen dat er conflicten ontstaan met de `azds` opdracht die wordt gebruikt door het hulp programma aan de client zijde.

De Kubernetes-webhook-toegangs server wordt gebruikt voor het injecteren van Peul met drie containers tijdens de implementatie voor instrumentatie: een devspaces-proxy container, een devspaces-proxy-init-container en een devspaces-build-container. **Alle drie deze containers worden uitgevoerd met toegang tot het hoofd niveau van uw AKS-cluster.** Ze gebruiken ook dezelfde service-principal die uw AKS-cluster gebruikt om service aanroepen naar andere Azure dev Space-onderdelen te maken.

![Azure dev Spaces Kubernetes webhook-toegangs server](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

De devspaces-proxy container is een container voor samen werken die alle TCP-verkeer naar en van de toepassings container verwerkt en de route ring helpt. De devspaces-proxy container stuurt HTTP-berichten opnieuw als bepaalde ruimten worden gebruikt. Het kan bijvoorbeeld leiden tot het routeren van HTTP-berichten tussen toepassingen in bovenliggende en onderliggende ruimten. Alle niet-HTTP-verkeer passeren via devspaces-proxy ongewijzigd. De devspaces-proxy container registreert ook alle inkomende en uitgaande HTTP-berichten en verzendt deze naar het hulp programma aan de client zijde als traceringen. Deze traceringen kunnen vervolgens door de ontwikkelaar worden weer gegeven om het gedrag van de toepassing te controleren.

De devspaces-proxy-init-container is een [init-container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) waarmee extra routerings regels worden toegevoegd op basis van de ruimte hiërarchie naar de container van uw toepassing. Hiermee worden routerings regels toegevoegd door het */etc/resolv.conf* -bestand van de toepassings container en de iptables-configuratie bij te werken voordat deze wordt gestart. Met de updates voor */etc/resolv.conf* wordt de DNS-omzetting van services in bovenliggende ruimten toegestaan. De iptables-configuratie-updates zorgen ervoor dat alle TCP-verkeer naar en van de container van de toepassing worden gerouteerd, hoewel devspaces-proxy. Alle updates van devspaces-proxy-init worden uitgevoerd naast de regels die Kubernetes toevoegt.

De devspaces-build-container is een init-container en de project bron code en docker-socket zijn gekoppeld. De bron code van het project en de toegang tot docker maken het mogelijk om de toepassings container rechtstreeks op te bouwen met de pod.

> [!NOTE]
> Azure dev Spaces gebruiken hetzelfde knoop punt om de container van uw toepassing te bouwen en uit te voeren. Als gevolg hiervan heeft Azure dev Spaces geen extern container register nodig om uw toepassing te bouwen en uit te voeren.

De Kubernetes-webhook-toegangs server luistert naar een nieuwe pod die is gemaakt in het AKS-cluster. Als dat pod wordt geïmplementeerd in een naam ruimte met het label *azds.io/Space=True* , wordt dat pod met de extra containers injecteerd. De devspaces-build-container wordt alleen geïnjecteerd als de container van de toepassing wordt uitgevoerd met behulp van het programma aan de client zijde.

Wanneer u uw AKS-cluster hebt voor bereid, kunt u het hulp programma aan de client zijde gebruiken om uw code voor te bereiden en uit te voeren in uw dev-ruimte.

## <a name="prepare-your-code"></a>Uw code voorbereiden

Als u uw toepassing wilt uitvoeren in een dev-ruimte, moet deze worden containerd en moet u definiëren hoe deze moet worden geïmplementeerd in Kubernetes. Als u uw toepassing wilt container plaatsen, hebt u een Dockerfile nodig. Als u wilt definiëren hoe uw toepassing wordt geïmplementeerd naar Kubernetes, hebt u een [helm-grafiek](https://docs.helm.sh/)nodig. Om u te helpen bij het maken van de Dockerfile-en helm-grafiek voor uw toepassing, biedt de client-side hulp middelen de `prep` opdracht:

```cmd
azds prep --public
```

De `prep`-opdracht bekijkt de bestanden in uw project en probeert de Dockerfile-en helm-grafiek te maken voor het uitvoeren van uw toepassing in Kubernetes. Op dit moment wordt met de opdracht `prep` een Dockerfile-en helm-grafiek gegenereerd met de volgende talen:

* Java
* Node.js
* .NET Core

U *moet* de `prep`-opdracht uitvoeren vanuit een map met de bron code. Als u de `prep` opdracht uit de juiste Directory uitvoert, kan het programma aan de client zijde de taal identificeren en een geschikte Dockerfile maken om uw toepassing te container plaatsen. U kunt ook de `prep` opdracht uitvoeren vanuit een map die een *pom. XML-* bestand voor Java-projecten bevat.

Als u de opdracht `prep` uitvoert vanuit een map die geen bron code bevat, wordt door het programma voor client-side geen Dockerfile gegenereerd. Er wordt ook een fout weer gegeven met de melding: *Dockerfile kan niet worden gegenereerd als gevolg*van een niet-ondersteunde taal. Deze fout treedt ook op als het project type niet wordt herkend door het programma aan de client zijde.

Wanneer u de `prep` opdracht uitvoert, hebt u de optie om de `--public`-vlag op te geven. Met deze markering wordt aan de controller aangegeven dat er een eind punt voor Internet toegang moet worden gemaakt voor deze service. Als u deze vlag niet opgeeft, is de service alleen toegankelijk vanuit het cluster of met behulp van de localhost-tunnel die is gemaakt door het programma aan de client zijde. U kunt dit gedrag in-of uitschakelen nadat u de `prep`-opdracht hebt uitgevoerd door de gegenereerde helm-grafiek bij te werken.

Met de `prep` opdracht worden geen bestaande Dockerfiles-of helm-grafieken vervangen in uw project. Als een bestaande Dockerfile-of helm-grafiek dezelfde naam conventie gebruikt als de bestanden die worden gegenereerd door de `prep` opdracht, worden deze bestanden door de `prep` opdracht overs Laan. Anders wordt met de `prep`-opdracht een eigen Dockerfile-of helm-grafiek gegenereerd naast de bestaande bestanden.

Met de opdracht `prep` wordt ook een `azds.yaml` bestand in de hoofdmap van het project gegenereerd. Azure dev Spaces gebruiken dit bestand om uw toepassing te bouwen, te installeren, te configureren en uit te voeren. Dit configuratie bestand bevat een lijst met de locatie van uw Dockerfile-en helm-grafiek en biedt ook aanvullende configuratie boven deze artefacten.

Hier volgt een voor beeld van een azds. yaml-bestand dat is gemaakt met behulp van [.net core-voorbeeld toepassing](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
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
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
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
```

Het `azds.yaml` bestand dat door de `prep`-opdracht wordt gegenereerd, moet goed werken voor een eenvoudig scenario voor het ontwikkelen van één project. Als uw specifieke project meer complexiteit heeft, moet u dit bestand mogelijk bijwerken nadat u de `prep`-opdracht hebt uitgevoerd. Het is bijvoorbeeld mogelijk dat uw project een verfijning van uw bouw-of start proces vereist op basis van uw ontwikkelings-of probleem oplossing. Het is ook mogelijk dat er meerdere toepassingen in uw project zijn, waarvoor meerdere bouw processen of een andere build-inhoud zijn vereist.

## <a name="run-your-code"></a>Uw code uitvoeren

Als u uw code in een dev-ruimte wilt uitvoeren, geeft u de `up` opdracht uit in dezelfde map als uw `azds.yaml`-bestand:

```cmd
azds up
```

Met de `up` opdracht worden de bron bestanden van de toepassing en andere artefacten geüpload die nodig zijn om uw project te bouwen en uit te voeren op de dev-ruimte. Van daaruit, de controller in uw deel ruimte voor ontwikkel aars:

1. Hiermee maakt u de Kubernetes-objecten om uw toepassing te implementeren.
1. Bouwt de container voor uw toepassing.
1. Implementeert uw toepassing naar de ontwikkel ruimte.
1. Hiermee maakt u een openbaar toegankelijke DNS-naam voor het eind punt van uw toepassing, indien geconfigureerd.
1. Maakt gebruik van *port-forward* om toegang te bieden tot het eind punt van de toepassing met behulp van http://localhost.
1. Stdout en stderr worden doorgestuurd naar het hulp programma aan de client zijde.


### <a name="starting-a-service"></a>Een service starten

Wanneer u een service in een dev-ruimte start, werken het hulp programma en de controller aan de client zijde samen om uw bron bestanden te synchroniseren, uw container-en Kubernetes-objecten te maken en uw toepassing uit te voeren.

Op een meer gedetailleerd niveau ziet u hier wat er gebeurt wanneer u `azds up`uitvoert:

1. Bestanden worden gesynchroniseerd van de computer van de gebruiker naar een Azure-bestands opslag die uniek is voor het AKS-cluster van de gebruiker. De bron code, het helm-diagram en de configuratie bestanden worden geüpload. Meer informatie over het synchronisatie proces vindt u in de volgende sectie.
1. De controller maakt een aanvraag om een nieuwe sessie te starten. Deze aanvraag bevat verschillende eigenschappen, met inbegrip van een unieke ID, naam van de ruimte, het pad naar de bron code en een vlag voor fout opsporing.
1. De controller vervangt de tijdelijke aanduiding *$ (tag)* in het helm-diagram met de unieke sessie-id en installeert de helm-grafiek voor uw service. Door een verwijzing naar de unieke sessie-ID toe te voegen aan de helm-grafiek, kan de container die is geïmplementeerd op het AKS-cluster voor deze specifieke sessie worden gekoppeld aan de sessie aanvraag en de bijbehorende gegevens.
1. Tijdens de installatie van de helm-grafiek voegt de Kubernetes-webhook-Admission server extra containers toe aan de pod van uw toepassing voor instrumentatie en toegang tot de bron code van uw project. De containers devspaces-proxy en devspaces-proxy-init worden toegevoegd om HTTP-tracering en ruimte routering te bieden. De devspaces-build-container wordt toegevoegd om de pod toegang te geven tot het docker-exemplaar en de project bron code voor het bouwen van de container van uw toepassing.
1. Wanneer de pod van de toepassing is gestart, worden de container devspaces-build en de container devspaces-proxy-init gebruikt voor het bouwen van de toepassings container. Vervolgens worden de toepassings container-en devspaces-proxy containers gestart.
1. Nadat de toepassings container is gestart, gebruikt de client-side-functionaliteit de functionaliteit voor Kubernetes *-poorten* om via http://localhosthttp-toegang tot uw toepassing te bieden. Door deze poort door te sturen verbindt u uw ontwikkelings machine met de service in uw dev-ruimte.
1. Wanneer alle containers in het Pod zijn gestart, wordt de service uitgevoerd. Op dit punt begint de client-side-functionaliteit de HTTP-traceringen, stdout en stderr te streamen. Deze informatie wordt weer gegeven door de client-side-functionaliteit voor de ontwikkelaar.

### <a name="updating-a-running-service"></a>Een actieve service bijwerken

Terwijl een service wordt uitgevoerd, kunnen Azure dev Spaces de service bijwerken als een van de project bron bestanden wordt gewijzigd. Dev Spaces zorgt er ook voor dat de service anders wordt bijgewerkt, afhankelijk van het type bestand dat wordt gewijzigd. Er zijn drie manieren om een actieve service bij te werken:

* Een bestand rechtstreeks bijwerken
* Het proces van de toepassing in de container van de actieve toepassing opnieuw samen stellen en opnieuw starten
* De container van de toepassing opnieuw samen stellen en implementeren

![Bestands synchronisatie voor Azure dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Bepaalde project bestanden met statische activa, zoals HTML-, CSS-en cshtml-bestanden, kunnen rechtstreeks in de container van de toepassing worden bijgewerkt zonder dat er iets opnieuw hoeft te worden opgestart. Als een statisch activum wordt gewijzigd, wordt het nieuwe bestand gesynchroniseerd met de ontwikkel ruimte en vervolgens gebruikt door de container die wordt uitgevoerd.

Wijzigingen in bestanden zoals bron code-of toepassings configuratie bestanden kunnen worden toegepast door het proces van de toepassing opnieuw te starten binnen de container die wordt uitgevoerd. Zodra deze bestanden zijn gesynchroniseerd, wordt het proces van de toepassing opnieuw gestart binnen de container die wordt uitgevoerd met behulp van het *devhostagent* -proces. Bij het maken van de container van de toepassing vervangt de controller de opstart opdracht voor de toepassing met een ander proces met de naam *devhostagent*. Het daad werkelijke proces van de toepassing wordt vervolgens als een onderliggend proces uitgevoerd onder *devhostagent*en de uitvoer ervan wordt door gegeven met behulp van de uitvoer van *devhostagent*. Het *devhostagent* -proces maakt ook deel uit van dev Spaces en kan opdrachten uitvoeren in de container die wordt uitgevoerd namens ontwikkel ruimten. Bij het opnieuw opstarten *devhostagent*:

* Stopt het huidige proces of de processen die zijn gekoppeld aan de toepassing
* De toepassing opnieuw samen stellen
* Hiermee worden de processen die zijn gekoppeld aan de toepassing opnieuw gestart

De manier waarop *devhostagent* de voor gaande stappen uitvoert, wordt geconfigureerd in het configuratie bestand van `azds.yaml`. Deze configuratie wordt beschreven in een latere sectie.

Voor updates voor Project bestanden zoals Dockerfiles, csproj-bestanden of een deel van de helm-grafiek moet de container van de toepassing opnieuw worden opgebouwd en geïmplementeerd. Wanneer een van deze bestanden wordt gesynchroniseerd met de dev-ruimte, voert de controller de [helm-upgrade opdracht uit](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) en wordt de container van de toepassing opnieuw opgebouwd en geïmplementeerd.

### <a name="file-synchronization"></a>Bestands synchronisatie

De eerste keer dat een toepassing wordt gestart in een dev-ruimte, worden alle bron bestanden van de toepassing geüpload. Terwijl de toepassing wordt uitgevoerd en later opnieuw wordt opgestart, worden alleen de gewijzigde bestanden geüpload. Er worden twee bestanden gebruikt om dit proces te coördineren: een bestand aan de client zijde en een bestand aan de spel besturing.

Het bestand aan de client zijde wordt opgeslagen in een tijdelijke map en krijgt de naam op basis van een hash van de projectmap die u uitvoert in dev Spaces. In Windows hebt u bijvoorbeeld een bestand zoals *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* voor uw project. In Linux wordt het bestand aan de client zijde opgeslagen in de *map/tmp* -map. U kunt de directory op macOS vinden door de `echo $TMPDIR` opdracht uit te voeren.

Dit bestand bevindt zich in JSON-indeling en bevat:

* Een vermelding voor elk project bestand dat wordt gesynchroniseerd met de ontwikkelaars ruimte
* Een synchronisatie-ID
* De tijds tempel van de laatste synchronisatie bewerking

Elke project bestands vermelding bevat een pad naar het bestand en de tijds tempel.

Het bestand aan de spel besturing wordt opgeslagen op het AKS-cluster. Het bevat de synchronisatie-ID en de tijds tempel van de laatste synchronisatie.

Er treedt een synchronisatie op wanneer de tijds tempels van de synchronisatie niet overeenkomen tussen de bestanden aan de client zijde en de controller. Tijdens een synchronisatie worden de bestands ingangen in het client-side-bestand herhaald. Als de tijds tempel van het bestand na de synchronisatie-tijds tempel ligt, wordt dat bestand gesynchroniseerd met de dev-ruimte. Zodra de synchronisatie is voltooid, worden de synchronisatie-tijds tempels bijgewerkt op zowel de bestanden aan de client zijde als aan de controller zijde.

Alle project bestanden worden gesynchroniseerd als het bestand aan de client zijde niet aanwezig is. Met dit gedrag kunt u een volledige synchronisatie afdwingen door het client-side-bestand te verwijderen.

### <a name="how-routing-works"></a>Hoe werkt route ring?

Een dev-ruimte is gebaseerd op AKS en maakt gebruik van dezelfde [netwerk concepten](../aks/concepts-network.md). Azure dev Spaces heeft ook een gecentraliseerde *ingressmanager* -service en implementeert zijn eigen ingangs controller naar het AKS-cluster. De *ingressmanager* -Service bewaakt AKS-clusters met ontwikkel ruimten en breidt de Azure dev Spaces-controller uit in het cluster met ingangs objecten voor route ring naar toepassing. De devspaces-proxy container in elke pod voegt een `azds-route-as` HTTP-header voor HTTP-verkeer toe aan een ontwikkel ruimte op basis van de URL. Zo kan een aanvraag naar de URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* een http-header ontvangen met `azds-route-as: azureuser`. Als een devspaces-proxy container al aanwezig is, wordt er geen `azds-route-as` header toegevoegd.

Wanneer een HTTP-aanvraag wordt gedaan bij een service van buiten het cluster, gaat de aanvraag naar de ingangs controller. De ingangs controller stuurt de aanvraag rechtstreeks naar de juiste pod op basis van de inkomende objecten en regels. De devspaces-proxy container in de Pod ontvangt de aanvraag, voegt de `azds-route-as` header toe op basis van de URL en stuurt de aanvraag vervolgens door naar de toepassings container.

Wanneer een HTTP-aanvraag wordt gedaan bij een service van een andere service binnen het cluster, loopt de aanvraag eerst door de devspaces-proxy container van de aanroepende service. De devspaces-proxy container zoekt naar de HTTP-aanvraag en controleert de `azds-route-as`-header. Op basis van de header zoekt de devspaces-proxy container het IP-adres van de service die is gekoppeld aan de waarde van de header. Als er een IP-adres wordt gevonden, wordt de aanvraag door de devspaces-proxy container omgeleid naar dat IP-adres. Als er geen IP-adres wordt gevonden, stuurt de devspaces-proxy container de aanvraag door naar de bovenliggende toepassings container.

De toepassingen *servicea* en *serviceB* worden bijvoorbeeld geïmplementeerd naar een bovenliggende ontwikkel ruimte met de naam *standaard*. *servicea* is afhankelijk van *serviceB* en maakt http-aanroepen. Azure-gebruiker maakt een onderliggende ontwikkel ruimte op basis van de *standaard* ruimte met de naam *azureuser*. Azure-gebruiker implementeert ook hun eigen versie van *servicea* op hun onderliggende ruimte. Wanneer een aanvraag wordt ingediend bij *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Route ring van Azure dev Spaces](media/how-dev-spaces-works/routing.svg)

1. De ingangs controller zoekt naar het IP-adres voor de pod die is gekoppeld aan de URL. Dit is *servicea. azureuser*.
1. De ingangs controller vindt het IP-adres voor de pod in de ontwikkel ruimte van Azure User en stuurt de aanvraag door naar de *servicea. azureuser* pod.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de aanvraag en voegt `azds-route-as: azureuser` toe als een http-header.
1. De devspaces-proxy container in de *servicea. azureuser* pod stuurt de aanvraag door naar de *servicea* -toepassings container in de *servicea. azureuser* pod.
1. De *servicea* -toepassing in de *servicea. azureuser* pod maakt een aanroep van *serviceB*. De *servicea* -toepassing bevat ook code voor het behouden van de bestaande `azds-route-as`-header, in dit geval `azds-route-as: azureuser`.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de aanvraag en zoekt het IP-adres van *serviceB* op basis van de waarde van de `azds-route-as` koptekst.
1. De devspaces-proxy container in de *servicea. azureuser* Pod heeft geen IP-adres voor *serviceB. azureuser*.
1. De devspaces-proxy container in de *servicea. azureuser* pod zoekt het IP-adres voor *serviceB* in de bovenliggende ruimte. Dit is *serviceB. default*.
1. De devspaces-proxy container in de *servicea. azureuser* pod vindt het IP-adres voor *serviceB. default* en stuurt de aanvraag door naar de *serviceB. default* pod.
1. De devspaces-proxy container in de *serviceB. default* Pod ontvangt de aanvraag en stuurt de aanvraag door naar de container *serviceB* -toepassing in de *serviceB. standaard* pod.
1. De *serviceB* -toepassing in de *serviceB. default* pod retourneert een antwoord op de *servicea. azureuser* pod.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt het antwoord en stuurt het antwoord naar de container *servicea* -toepassing in de *servicea. azureuser* pod.
1. De *servicea* -toepassing ontvangt het antwoord en retourneert vervolgens een eigen antwoord.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de reactie van de *servicea* -toepassings container en stuurt het antwoord naar de oorspronkelijke aanroeper buiten het cluster.

Alle andere TCP-verkeer dat niet HTTP wordt door gegeven via de ingangs controller en devspaces-proxy containers, worden niet gewijzigd.

### <a name="how-running-your-code-is-configured"></a>Hoe het uitvoeren van uw code is geconfigureerd

Azure dev Spaces maakt gebruik van het `azds.yaml`-bestand om uw service te installeren en te configureren. De controller gebruikt de eigenschap `install` in het `azds.yaml` bestand om het helm-diagram te installeren en de Kubernetes-objecten te maken:

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

Met de `prep`-opdracht wordt standaard het helm-diagram gegenereerd. Ook de eigenschap *install. Chart* wordt ingesteld op de map van het helm-diagram. Als u een helm-diagram wilt gebruiken op een andere locatie, kunt u deze eigenschap bijwerken om die locatie te gebruiken.

Bij de installatie van de helm-grafieken biedt Azure dev Spaces een manier om waarden in de helm-grafiek te overschrijven. De standaard waarden voor het helm-diagram bevinden zich in `charts/APP_NAME/values.yaml`.

Met de eigenschap *install. values* kunt u een of meer bestanden weer geven waarmee waarden worden gedefinieerd die u wilt vervangen in het helm-diagram. Als u bijvoorbeeld een hostnaam of database configuratie specifiek wilt gebruiken voor het uitvoeren van uw toepassing in een dev-ruimte, kunt u deze onderdrukkings functionaliteit. U kunt ook een *?* toevoegen aan het einde van de bestands namen om deze in te stellen als optioneel.

Met de eigenschap *install. set* kunt u een of meer waarden configureren die u wilt vervangen in het helm-diagram. Alle waarden die zijn geconfigureerd in *install. set* overschrijven de waarden die zijn geconfigureerd in de bestanden die worden vermeld in *install. values*. De eigenschappen onder *install. set* zijn afhankelijk van de waarden in het helm-diagram en kunnen verschillen, afhankelijk van het gegenereerde helm-diagram.

In het bovenstaande voor beeld vertelt de eigenschap *install. set. replicaCount* aan de controller hoeveel exemplaren van uw toepassing moeten worden uitgevoerd in uw dev-ruimte. Afhankelijk van uw scenario kunt u deze waarde verg Roten, maar dit heeft gevolgen voor het koppelen van een fout opsporingsprogramma aan de pod van uw toepassing. Zie het [artikel over probleem oplossing](troubleshooting.md)voor meer informatie.

In de gegenereerde helm-grafiek is de container installatie kopie ingesteld op *{{. Values. image. repository}}: {{. Values. image. tag}}* . In het `azds.yaml` bestand wordt de eigenschap *install. set. image. tag* gedefinieerd als *$ (tag)* standaard, die wordt gebruikt als de waarde voor *{{. Values. image. tag}}* . Door de eigenschap *install. set. image. tag* op deze manier in te stellen, kan de container installatie kopie voor uw toepassing op een unieke manier worden gelabeld wanneer Azure-ontwikkel ruimten worden uitgevoerd. In dit specifieke geval wordt de afbeelding gelabeld als *\<waarde van de afbeelding. repository >: $ (tag)* . U moet de variabele *$ (tag)* gebruiken als de waarde *install. set. image. tag* , zodat ontwikkel ruimten de container herkennen en vinden in het AKS-cluster.

In het bovenstaande voor beeld definieert `azds.yaml` *install. set. ingress. hosts*. De eigenschap *install. set. ingress. hosts* definieert een indeling voor de hostnaam voor open bare eind punten. Deze eigenschap gebruikt ook *$ (spacePrefix)* , *$ (rootSpacePrefix)* en *$ (hostSuffix)* . Dit zijn de waarden die door de controller worden verschaft. 

*$ (SpacePrefix)* is de naam van de onderliggende dev-ruimte, waarbij de notatie Space naam *. s*wordt gebruikt. *$ (RootSpacePrefix)* is de naam van de bovenliggende ruimte. Als *azureuser* bijvoorbeeld een onderliggend gebied *is, is*de waarde voor *$ (rootSpacePrefix)* *standaard* en is de waarde van *$ (spacePrefix)* *azureuser. s*. Als de ruimte geen onderliggende ruimte is, *$ (spacePrefix)* is leeg. Als de *standaard* ruimte bijvoorbeeld geen bovenliggende ruimte heeft, is de waarde voor *$ (rootSpacePrefix)* *standaard* en is de waarde van *$ (spacePrefix)* leeg. *$ (HostSuffix)* is een DNS-achtervoegsel dat verwijst naar de Azure dev Spaces-controller die wordt uitgevoerd in uw AKS-cluster. Dit DNS-achtervoegsel komt overeen met een DNS-vermelding met Joker tekens, bijvoorbeeld *\*. RANDOM_VALUE. Eus. azds. io*, dat is gemaakt toen de Azure dev Spaces-controller werd toegevoegd aan uw AKS-cluster.

In het bovenstaande `azds.yaml`-bestand kunt u ook *install. set. ingress. hosts* bijwerken om de hostnaam van uw toepassing te wijzigen. Bijvoorbeeld, als u de hostnaam van uw toepassing wilt vereenvoudigen van *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* naar $ ( *spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Voor het bouwen van de container voor uw toepassing, gebruikt de controller de onderstaande secties van het `azds.yaml` configuratie bestand:

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

De eigenschap *Build. context* bevat een lijst met de map waarin de Dockerfiles bestaat. De eigenschap *Build. dockerfile* definieert de naam van de dockerfile voor het bouwen van de productie versie van de toepassing. De eigenschap *configurations. develope. build. dockerfile* configureert de naam van de dockerfile voor de ontwikkelings versie van de toepassing.

Met verschillende Dockerfiles voor ontwikkeling en productie kunt u bepaalde dingen tijdens de ontwikkeling inschakelen en deze items uitschakelen voor productie-implementaties. U kunt bijvoorbeeld fout opsporing of uitgebreide logboek registratie inschakelen tijdens de ontwikkeling en uitschakelen in een productie omgeving. U kunt deze eigenschappen ook bijwerken als uw Dockerfiles een andere naam hebben of zich op een andere locatie bevinden.

Om u te helpen snel tijdens het ontwikkelen te herhalen, zullen Azure dev Spaces wijzigingen van uw lokale project synchroniseren en uw toepassing incrementeel bijwerken. De onderstaande sectie in het configuratie bestand van `azds.yaml` wordt gebruikt voor het configureren van de synchronisatie en de update:

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

De bestanden en mappen waarmee wijzigingen worden gesynchroniseerd, worden weer gegeven in de eigenschap *configuraties. ontwikkeling. container. Sync* . Deze directory's worden in eerste instantie gesynchroniseerd wanneer u de `up` opdracht uitvoert en wanneer er wijzigingen worden gedetecteerd. Als er extra of andere directory's zijn die u wilt synchroniseren met uw dev-ruimte, kunt u deze eigenschap wijzigen.

De eigenschap *configurations. develope. container. ITER. buildCommands* geeft aan hoe de toepassing in een ontwikkelings scenario moet worden gebouwd. De eigenschap *configurations. Development. container. Command* biedt de opdracht voor het uitvoeren van de toepassing in een ontwikkelings scenario. Het is raadzaam om een van deze eigenschappen bij te werken als er aanvullende build-of runtime-vlaggen of-para meters zijn die u tijdens de ontwikkeling wilt gebruiken.

De *configuraties. develope. container. ITER. processesToKill* geeft een lijst van de processen die u wilt beëindigen om de toepassing te stoppen. U kunt deze eigenschap bijwerken als u tijdens de ontwikkeling het gedrag voor opnieuw opstarten van uw toepassing wilt wijzigen. Als u bijvoorbeeld de configuraties hebt bijgewerkt *. ontwikkelen. container. ITER. buildCommands* of configurations. de eigenschappen van de *opdracht develope. container. Command* om te wijzigen hoe de toepassing wordt gebouwd of gestart, moet u mogelijk wijzigen welke processen worden gestopt.

Wanneer u de code voorbereidt met behulp van de `azds prep` opdracht, kunt u de `--public` markering toevoegen. Als u de vlag `--public` toevoegt, maakt u een openbaar toegankelijke URL voor uw toepassing. Als u deze vlag weglaat, is de toepassing alleen toegankelijk binnen het cluster of met behulp van de localhost-tunnel. Nadat u de `azds prep` opdracht hebt uitgevoerd, kunt u deze instelling wijzigen van de eigenschap *ingangs. enabled* in `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Fouten opsporen in uw code

Voor Java-, .NET-en node. js-toepassingen kunt u fouten opsporen in uw toepassing die rechtstreeks wordt uitgevoerd in uw ontwikkel ruimte met Visual Studio code of Visual Studio. Visual Studio code en Visual Studio bieden hulp middelen om verbinding te maken met uw ontwikkel ruimte, uw toepassing te starten en een fout opsporingsprogramma te koppelen. Nadat `azds prep`is uitgevoerd, kunt u het project openen in Visual Studio code of Visual Studio. Visual Studio code of Visual Studio genereert hun eigen configuratie bestanden om verbinding te maken. Dit is niet het uitvoeren van `azds prep`. Vanuit Visual Studio code of Visual Studio kunt u onderbrekings punten instellen en uw toepassing starten op uw dev-ruimte.

![Fout opsporing voor uw code](media/get-started-node/debug-configuration-nodejs2.png)

Wanneer u uw toepassing start met Visual Studio code of Visual Studio voor het opsporen van fouten, worden de start-en verbinding maken met uw ontwikkel ruimte op dezelfde manier uitgevoerd als `azds up`. De hulpprogram ma's aan de client zijde in Visual Studio code en Visual Studio bieden ook een extra para meter met specifieke informatie voor fout opsporing. De para meter bevat de naam van de afbeelding van het fout opsporingsprogramma, de locatie van het fout opsporingsprogramma in de installatie kopie van de fout opsporing en de doel locatie binnen de container van de toepassing om de map met fout opsporing te koppelen.

De afbeelding van het fout opsporingsprogramma wordt automatisch bepaald door het hulp programma aan de client zijde. Er wordt gebruikgemaakt van een methode die vergelijkbaar is met die in de Dockerfile-en helm-grafiek wordt gegenereerd wanneer `azds prep`wordt uitgevoerd. Nadat de debugger is gekoppeld in de installatie kopie van de toepassing, wordt deze uitgevoerd met `azds exec`.

## <a name="sharing-a-dev-space"></a>Een ontwikkelings ruimte delen

Wanneer u met een team werkt, kunt u [een dev-ruimte delen binnen een heel team](how-to/share-dev-spaces.md) en afgeleide ontwikkel ruimten maken. Een ontwikkel ruimte kan worden gebruikt door iedereen met Inzender toegang tot de resource groep van de ontwikkelings ruimte.

U kunt ook een nieuwe ontwikkel ruimte maken die is afgeleid van een andere dev-ruimte. Wanneer u een afgeleide dev-ruimte maakt, wordt het label *azds.io/Parent-Space=Parent-Space-name* toegevoegd aan de naam ruimte van de afgeleide dev-ruimte. Daarnaast worden alle toepassingen van de bovenliggende ontwikkel ruimte gedeeld met de afgeleide dev-ruimte. Als u een bijgewerkte versie van een toepassing implementeert in de afgeleide dev-ruimte, bestaat deze alleen in de afgeleide dev-ruimte en blijft de bovenliggende ontwikkel ruimte ongewijzigd. U kunt Maxi maal drie niveaus afgeleide dev-ruimten of groot *ouders* hebben.

Met de afgeleide ontwikkel ruimte worden aanvragen ook intelligent gerouteerd tussen de eigen toepassingen en de toepassingen die worden gedeeld vanuit het bovenliggende knoop punt. De route ring werkt door het routeren van aanvragen naar een toepassing in de afgeleide dev-ruimte en terug te vallen op de gedeelde toepassing vanuit de bovenliggende ontwikkel ruimte. De route ring gaat terug naar de gedeelde toepassing in de grootst-ruimte als de toepassing zich niet in de bovenliggende ruimte bevindt.

Bijvoorbeeld:
* De ontwikkel ruimte *standaard* heeft toepassingen *servicea* en *serviceB* .
* De *azureuser* voor de ontwikkelings ruimte wordt afgeleid van de *standaard waarde*.
* Er is een bijgewerkte versie van *servicea* geïmplementeerd op *azureuser*.

Wanneer *azureuser*wordt gebruikt, worden alle aanvragen naar *servicea* doorgestuurd naar de bijgewerkte versie in *azureuser*. Er wordt eerst een aanvraag naar *serviceB* verzonden naar de *azureuser* -versie van *serviceB*. Omdat deze niet bestaat, wordt deze doorgestuurd naar de *standaard* versie van *serviceB*. Als de *azureuser* -versie van *servicea* wordt verwijderd *, worden alle aanvragen van servicea* teruggestuurd naar het gebruik van de *standaard* versie van *servicea*.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met Azure dev Spaces, raadpleegt u de volgende Quick starts:

* [Java met CLI en Visual Studio code](quickstart-java.md)
* [.NET core met CLI en Visual Studio code](quickstart-netcore.md)
* [.NET core met Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js met CLI en Visual Studio code](quickstart-nodejs.md)

Raadpleeg de volgende artikelen met procedures om aan de slag te gaan met team ontwikkeling:

* [Team ontwikkeling-Java met CLI en Visual Studio code](team-development-java.md)
* [Team ontwikkeling-.NET core met CLI en Visual Studio code](team-development-netcore.md)
* [Team ontwikkeling-.NET core met Visual Studio](team-development-netcore-visualstudio.md)
* [Team ontwikkeling: node. js met CLI en Visual Studio code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
