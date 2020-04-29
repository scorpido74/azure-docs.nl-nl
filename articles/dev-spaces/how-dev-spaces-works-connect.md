---
title: Hoe verbinding maken met uw ontwikkel computer met uw AKS-cluster werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Hierin worden de processen beschreven die gebruikmaken van Azure dev Spaces om uw ontwikkel computer te verbinden met uw Azure Kubernetes service-cluster
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241710"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Hoe verbinding maken met uw ontwikkel computer met uw AKS-cluster werkt

Met Azure dev Spaces kunt u uw ontwikkel computer verbinden met uw AKS-cluster, zodat u code op uw ontwikkel computer uitvoert, alsof deze op het cluster wordt uitgevoerd. Azure dev Spaces omleidt verkeer tussen uw verbonden AKS-cluster door een pod op uw cluster uit te voeren dat fungeert als een externe agent om verkeer tussen uw ontwikkel computer en het cluster om te leiden. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en-services die worden uitgevoerd in uw AKS-cluster communiceren alsof ze zich in hetzelfde AKS-cluster bevonden. Met deze verbinding kunt u ook code uitvoeren en fouten opsporen met of zonder een container op uw ontwikkel computer. Het verbinden van uw ontwikkel computer met uw cluster helpt u snel uw toepassing te ontwikkelen en end-to-end tests uit te voeren.

## <a name="connecting-to-your-cluster"></a>Verbinding maken met uw cluster

U maakt verbinding met uw bestaande AKS-cluster met behulp van [Visual Studio code][vs-code] en de [Azure dev Spaces][azds-vs-code] -extensie die wordt uitgevoerd op MacOS of Windows 10. Wanneer u een verbinding tot stand brengt, hebt u de mogelijkheid om al het verkeer van en naar een nieuwe of bestaande pod in het cluster om te leiden naar uw ontwikkel computer.

> [!NOTE]
> Wanneer u Visual Studio code gebruikt om verbinding te maken met uw cluster, kunt u met de Azure dev Spaces-extensie een service omleiden naar uw ontwikkel computer. Deze optie is een handige manier om een pod voor omleiding te identificeren. Alle omleiding tussen uw AKS-cluster en uw ontwikkel computer is voor een pod.

Voor het maken van een verbinding met uw cluster hoeft u geen Azure dev Spaces in te scha kelen in uw cluster. Wanneer de Azure dev Spaces-extensie een verbinding met uw cluster tot stand brengt, wordt het volgende gebruikt:

* Vervangt de container in het pod op het AKS-cluster met een externe agent container die verkeer omleidt naar uw ontwikkel computer. Bij het omleiden van een nieuwe pod maakt Azure dev Spaces een nieuwe pod in uw AKS-cluster met de externe agent.
* Voert [kubectl-poort-vooruit][kubectl-port-forward] uit op uw ontwikkel computer om verkeer door te sturen van uw ontwikkel computer naar de externe agent die in uw cluster wordt uitgevoerd.
* Hiermee worden omgevings gegevens uit uw cluster verzameld met de externe agent. Deze omgevings informatie omvat omgevings variabelen, zicht bare Services, volume koppelingen en geheime koppels.
* Hiermee stelt u de omgeving in de Visual Studio code-terminal in, zodat de service op uw ontwikkel computer toegang heeft tot dezelfde variabelen als wanneer deze op het cluster wordt uitgevoerd.  
* Werkt uw hosts-bestand bij om services op uw AKS-cluster toe te wijzen aan lokale IP-adressen op de ontwikkel computer. Met deze hosts file entries kan code die wordt uitgevoerd op uw ontwikkel computer, aanvragen indienen bij andere services die in uw cluster worden uitgevoerd. Voor het bijwerken van uw hosts-bestand vraagt Azure dev Spaces om beheerders toegang op uw ontwikkel computer wanneer er verbinding wordt gemaakt met uw cluster.

Als Azure dev Spaces wel zijn ingeschakeld op uw cluster, hebt u ook de mogelijkheid om de [omleiding van verkeer te gebruiken die wordt aangeboden door Azure dev Spaces][how-it-works-routing]. Met de omleiding van het verkeer dat wordt aangeboden door Azure dev Spaces, kunt u verbinding maken met een kopie van uw service die wordt uitgevoerd in een onderliggende ontwikkel ruimte. Door gebruik te maken van een onderliggende ontwikkel ruimte helpt u te voor komen dat anderen werken in de bovenliggende ontwikkel ruimte omdat u alleen het verkeer omleidt dat is gericht op het exemplaar van de onderliggende ruimte van uw service, waardoor het bovenliggende Space-exemplaar van de service niet meer kan worden gewijzigd.

Zodra u verbinding hebt gemaakt met uw cluster, wordt het verkeer doorgestuurd naar uw ontwikkel computer, ongeacht of u uw service op uw ontwikkel computer uitvoert.

## <a name="running-code-on-your-development-computer"></a>Code uitvoeren op uw ontwikkel computer

Nadat u een verbinding met uw AKS-cluster tot stand hebt gebracht, kunt u wille keurige code uitvoeren op uw computer, zonder container opslag. Elk netwerk verkeer dat door de externe agent wordt ontvangen, wordt omgeleid naar de lokale poort die tijdens de verbinding is opgegeven, zodat uw systeem eigen code die verkeer kan accepteren en verwerken. De omgevings variabelen, volumes en geheimen van uw cluster worden beschikbaar gesteld aan code die wordt uitgevoerd op uw ontwikkel computer. Als gevolg van de hosts bestands vermeldingen en poort door sturen toegevoegd aan uw ontwikkelaars computer door Azure dev Spaces, kan uw code netwerk verkeer verzenden naar services die worden uitgevoerd op uw cluster met behulp van de service namen van het cluster en dat verkeer wordt doorgestuurd naar de services die in uw cluster worden uitgevoerd.

Omdat uw code wordt uitgevoerd op uw ontwikkel computer, hebt u de flexibiliteit om een hulp programma te gebruiken dat u normaal gesp roken gebruikt voor ontwikkeling om uw code uit te voeren en fouten op te sporen. Verkeer wordt gerouteerd tussen uw ontwikkel computer en uw cluster op het hele moment dat u verbonden bent. Met deze permanente verbinding kunt u de code zo veel mogelijk starten, stoppen en opnieuw starten zonder dat u opnieuw een verbinding hoeft te maken.

Daarnaast biedt Azure dev Spaces een manier om omgevings variabelen en gekoppelde bestanden te repliceren die beschikbaar zijn voor een Peul in uw AKS-cluster in uw ontwikkel computer via het bestand *azds-local. env* . U kunt dit bestand ook gebruiken om nieuwe omgevings variabelen en volume koppelingen te maken.

## <a name="additional-configuration-with-azds-localenv"></a>Aanvullende configuratie met azds-local. env

Met het bestand *azds-local. env* kunt u omgevings variabelen en gekoppelde bestanden die beschikbaar zijn voor uw Peul, repliceren in uw AKS-cluster. U kunt de volgende acties opgeven in een *azds-local. env* -bestand:

* Down load een volume en stel het pad naar dat volume in als een omgevings variabele.
* Down load een afzonderlijk bestand of een set bestanden van een volume en koppel deze op uw ontwikkel computer.
* Een service beschikbaar maken, ongeacht het cluster waarmee u bent verbonden.

Hier volgt een voor beeld van een *azds-local. env* -bestand:

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

Een standaard *azds-local. env* -bestand wordt niet automatisch gemaakt, dus u moet het bestand hand matig maken in de hoofdmap van het project.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

Wanneer u bent verbonden met uw AKS-cluster, worden de diagnostische logboeken van uw cluster vastgelegd in de [tijdelijke map][azds-tmp-dir]van uw ontwikkel computer. Met Visual Studio code kunt u ook de opdracht *Diagnostische gegevens weer geven* gebruiken om de huidige omgevings variabelen en DNS-vermeldingen uit uw AKS-cluster af te drukken.

## <a name="next-steps"></a>Volgende stappen

Zie [uw ontwikkel computer verbinden met een AKS-cluster][connect]om aan de slag te gaan met het verbinden van uw lokale ontwikkel computer met uw AKS-cluster.

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
