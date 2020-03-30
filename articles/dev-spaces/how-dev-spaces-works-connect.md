---
title: Hoe het aansluiten van uw ontwikkelcomputer op uw AKS-cluster werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen voor het gebruik van Azure Dev Spaces om uw ontwikkelcomputer te verbinden met uw Azure Kubernetes Service-cluster
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241710"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Hoe het aansluiten van uw ontwikkelcomputer op uw AKS-cluster werkt

Met Azure Dev Spaces u uw ontwikkelcomputer verbinden met uw AKS-cluster, zodat u code op uw ontwikkelcomputer uitvoeren en debuggen alsof deze op het cluster wordt uitgevoerd. Azure Dev Spaces leidt het verkeer tussen uw verbonden AKS-cluster om door een pod op uw cluster uit te voeren die fungeert als een externe agent om verkeer tussen uw ontwikkelingsmachine en het cluster om te leiden. Met deze verkeersomleiding kunnen code op uw ontwikkelingscomputer en services die in uw AKS-cluster worden uitgevoerd, communiceren alsof ze zich in hetzelfde AKS-cluster bevinden. Met deze verbinding u ook code uitvoeren en debuggen met of zonder een container op uw ontwikkelingscomputer. Door uw ontwikkelcomputer aan te sluiten op uw cluster u uw toepassing snel ontwikkelen en end-to-end testen uitvoeren.

## <a name="connecting-to-your-cluster"></a>Verbinding maken met uw cluster

U maakt verbinding met uw bestaande AKS-cluster met [Behulp van Visual Studio Code][vs-code] met de Azure [Dev Spaces-extensie][azds-vs-code] die is geïnstalleerd op MacOS of Windows 10. Wanneer u een verbinding tot stand wilt brengen, u al het verkeer van en naar een nieuwe of bestaande pod in het cluster omleiden naar uw ontwikkelingscomputer.

> [!NOTE]
> Wanneer u Visual Studio Code gebruikt om verbinding te maken met uw cluster, biedt de azure dev spaces-extensie u de mogelijkheid om een service om te leiden naar uw ontwikkelcomputer. Deze optie is een handige manier om een pod te identificeren voor omleiding. Alle omleiding tussen uw AKS-cluster en uw ontwikkelingscomputer is voor een pod.

Als u verbinding maakt met uw cluster, hoeft u Azure Dev Spaces niet in uw cluster te hebben ingeschakeld. In plaats daarvan, wanneer de Azure Dev Spaces-extensie een verbinding met uw cluster tot leggen, gaat het als het gaat om:

* Hiermee vervangt u de container in de pod op het AKS-cluster door een externe agentcontainer die verkeer doorverwijst naar uw ontwikkelingscomputer. Wanneer azure Dev Spaces een nieuwe pod omleidt, maakt het een nieuwe pod in uw AKS-cluster met de externe agent.
* Met [wordt kubectl port-forward uitgevoerd][kubectl-port-forward] op uw ontwikkelcomputer om verkeer van uw ontwikkelingscomputer door te sturen naar de externe agent die in uw cluster wordt uitgevoerd.
* Verzamelt omgevingsinformatie van uw cluster met behulp van de externe agent. Deze informatie over de omgeving omvat omgevingsvariabelen, zichtbare services, volumemounts en geheime bevestigingen.
* Hiermee stelt u de omgeving in de Visual Studio Code-terminal in, zodat de service op uw ontwikkelingscomputer toegang heeft tot dezelfde variabelen als wanneer deze op het cluster wordt uitgevoerd.  
* Werkt uw hosts-bestand bij om services op uw AKS-cluster toe te stellen aan lokale IP-adressen op uw ontwikkelingscomputer. Met deze bestandsvermeldingen kunnen code die op uw ontwikkelingscomputer wordt uitgevoerd, aanvragen indienen bij andere services die in uw cluster worden uitgevoerd. Als u uw hosts-bestand wilt bijwerken, vraagt Azure Dev Spaces om beheerderstoegang op uw ontwikkelcomputer wanneer u verbinding maakt met uw cluster.

Als azure dev-ruimten in uw cluster zijn ingeschakeld, u ook de verkeersomleiding gebruiken [die wordt aangeboden door Azure Dev Spaces.][how-it-works-routing] Met de verkeersomleiding die azure dev spaces aanbiedt, u verbinding maken met een kopie van uw service die wordt uitgevoerd in een onderliggende dev-ruimte. Als u een onderliggende dev-ruimte gebruikt, u voorkomen dat anderen die in de bovenliggende dev-ruimte werken, worden verstoord, omdat u alleen het verkeer dat zich richt op het exemplaar van uw service voor de onderliggende ruimte omleidt, waardoor de instantie van de bovenliggende ruimte van de service ongewijzigd blijft.

Zodra u verbinding maakt met uw cluster, wordt het verkeer doorgestuurd naar uw ontwikkelingscomputer, ongeacht of u uw service op uw ontwikkelingscomputer hebt uitgevoerd.

## <a name="running-code-on-your-development-computer"></a>Code uitvoeren op uw ontwikkelingscomputer

Nadat u een verbinding met uw AKS-cluster hebt gemaakt, u elke code native op uw computer uitvoeren, zonder containerisatie. Elk netwerkverkeer dat de externe agent ontvangt, wordt doorgestuurd naar de lokale poort die tijdens de verbinding is opgegeven, zodat uw native running-code dat verkeer kan accepteren en verwerken. De omgevingsvariabelen, volumes en geheimen van uw cluster worden beschikbaar gesteld voor code die op uw ontwikkelingscomputer wordt uitgevoerd. Als gevolg van de bestandsvermeldingen en poortdoorsturen die door Azure Dev Spaces aan uw ontwikkelaarscomputer zijn toegevoegd, kan uw code netwerkverkeer verzenden naar services die op uw cluster worden uitgevoerd met behulp van de servicenamen van uw cluster en dat verkeer wordt doorgestuurd naar de services die in uw cluster worden uitgevoerd.

Aangezien uw code wordt uitgevoerd op uw ontwikkelingscomputer, hebt u de flexibiliteit om elke tool die u normaal gebruikt voor de ontwikkeling te gebruiken om uw code uit te voeren en te debuggen. Het verkeer wordt de hele tijd dat u verbonden bent omgeleid tussen uw ontwikkelingscomputer en uw cluster. Met deze permanente verbinding u uw code zoveel mogelijk starten, stoppen en opnieuw starten zonder dat u een verbinding opnieuw hoeft te maken.

Bovendien biedt Azure Dev Spaces een manier om omgevingsvariabelen en gemonteerde bestanden die beschikbaar zijn voor pods in uw AKS-cluster in uw ontwikkelcomputer te repliceren via het *bestand azds-local.env.* U dit bestand ook gebruiken om nieuwe omgevingsvariabelen en volumemounts te maken.

## <a name="additional-configuration-with-azds-localenv"></a>Aanvullende configuratie met azds-local.env

Met *het azds-local.env-bestand* u omgevingsvariabelen en bevestigde bestanden repliceren die beschikbaar zijn voor uw pods in uw AKS-cluster. U de volgende acties opgeven in een *bestand azds-local.env:*

* Download een volume en stel het pad naar dat volume in als omgevingsvariabele.
* Download een individueel bestand of een set bestanden van een volume en monteer het op uw ontwikkelingscomputer.
* Maak een service beschikbaar, ongeacht het cluster met wie u bent verbonden.

Hier is een voorbeeld *azds-local.env* bestand:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Een standaard *bestand azds-local.env* wordt niet automatisch gemaakt, dus u moet het bestand handmatig maken aan de basis van uw project.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

Wanneer u verbinding maakt met uw AKS-cluster, worden diagnostische logboeken van uw cluster aangemeld bij de tijdelijke map van uw [ontwikkelingscomputer.][azds-tmp-dir] Met Visual Studio Code u ook de opdracht *Diagnostische gegevens weergeven* gebruiken om de huidige omgevingsvariabelen en DNS-vermeldingen uit uw AKS-cluster af te drukken.

## <a name="next-steps"></a>Volgende stappen

Zie [Uw ontwikkelcomputer verbinden met een AKS-cluster om][connect]aan de slag te gaan met het aansluiten van uw lokale ontwikkelingscomputer op uw AKS-cluster.

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
