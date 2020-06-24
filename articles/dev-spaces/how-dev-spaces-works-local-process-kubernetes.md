---
title: Hoe een lokaal proces met Kubernetes werkt
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Hierin worden de processen beschreven voor het gebruik van lokaal proces met Kubernetes om uw ontwikkel computer te verbinden met uw Kubernetes-cluster
keywords: Lokaal proces met Kubernetes, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: dd126fc55a86b1de115239a31e5adb7b1d264846
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974395"
---
# <a name="how-local-process-with-kubernetes-works"></a>Hoe een lokaal proces met Kubernetes werkt

Met het lokale proces met Kubernetes kunt u code op uw ontwikkel computer uitvoeren en fouten opsporen, terwijl u nog steeds met uw Kubernetes-cluster verbonden bent met de rest van uw toepassing of services. Als u bijvoorbeeld een grote micro Services-architectuur hebt met veel onderling afhankelijke services en data bases, kan het repliceren van die afhankelijkheden op uw ontwikkel computer lastig zijn. Daarnaast kan het maken en implementeren van code voor uw Kubernetes-cluster voor elke code wijziging tijdens de interne loop tijd traag, tijdrovend en moeilijk te gebruiken zijn met een fout opsporingsprogramma.

Lokaal proces met Kubernetes voor komt dat u uw code hoeft te bouwen en implementeren in uw cluster door in plaats daarvan rechtstreeks een verbinding te maken tussen uw ontwikkel computer en uw cluster. Wanneer u de ontwikkel computer met het cluster verbindt tijdens het opsporen van fouten, kunt u uw service snel testen en ontwikkelen in de context van de volledige toepassing zonder dat u een docker-of Kubernetes-configuratie hoeft te maken.

Lokaal proces met Kubernetes omleidt verkeer tussen uw verbonden Kubernetes-cluster en uw ontwikkel computer. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en-services die worden uitgevoerd in uw Kubernetes-cluster communiceren alsof ze zich in hetzelfde Kubernetes-cluster bevinden. Lokaal proces met Kubernetes biedt ook een manier om omgevings variabelen en gekoppelde volumes te repliceren die beschikbaar zijn voor een Peul in uw Kubernetes-cluster in uw ontwikkel computer. Door toegang te bieden tot omgevings variabelen en gekoppelde volumes op uw ontwikkel computer, kunt u snel aan uw code werken zonder dat u deze afhankelijkheden hand matig hoeft te repliceren.

## <a name="using-local-process-with-kubernetes"></a>Lokaal proces gebruiken met Kubernetes

Als u lokaal proces met Kubernetes wilt gebruiken, hebt u [Visual Studio code][vs-code] nodig met de [Azure dev Spaces][azds-vs-code] en de [Azure Kubernetes-service][az-aks-vs-code] -extensies geïnstalleerd en uitgevoerd op macOS of Windows 10, evenals de [Azure dev Space cli][azds-cli]die is geïnstalleerd. U kunt ook [Visual Studio 2019][visual-studio] uitvoeren op Windows 10 met de *ASP.net-en Web Development* -en *Azure Development* -workloads geïnstalleerd en de *AzureDevSpacesTools. LocalKubernetesDebugging* -preview-functie vlag ingeschakeld en de [geïnstalleerde Azure dev Spaces-cli][azds-cli]. Wanneer u lokaal proces met Kubernetes gebruikt om verbinding te maken met uw Kubernetes-cluster, hebt u de mogelijkheid om al het verkeer naar en van een bestaande pod in het cluster om te leiden naar uw ontwikkel computer.

> [!NOTE]
> Wanneer u lokaal proces met Kubernetes gebruikt, wordt u gevraagd om de naam van de service die u wilt omleiden naar uw ontwikkel computer. Deze optie is een handige manier om een pod voor omleiding te identificeren. Alle omleiding tussen uw Kubernetes-cluster en uw ontwikkel computer is voor een pod.

Wanneer het lokale proces met Kubernetes een verbinding met uw cluster tot stand brengt, wordt het volgende:

* Vraagt u om de service te configureren voor vervanging op uw cluster, de poort op de ontwikkel computer die u wilt gebruiken voor uw code en de start taak voor uw code als eenmalige actie.
* Vervangt de container in het pod op het cluster met een externe agent container die verkeer omleidt naar uw ontwikkel computer.
* Voert [kubectl-poort-vooruit][kubectl-port-forward] uit op uw ontwikkel computer om verkeer door te sturen van uw ontwikkel computer naar de externe agent die in uw cluster wordt uitgevoerd.
* Hiermee worden omgevings gegevens uit uw cluster verzameld met de externe agent. Deze omgevings informatie omvat omgevings variabelen, zicht bare Services, volume koppelingen en geheime koppels.
* Hiermee stelt u de omgeving in Visual Studio of Visual Studio code in, zodat de service op uw ontwikkel computer toegang heeft tot dezelfde variabelen als wanneer deze op het cluster wordt uitgevoerd.  
* Werkt uw hosts-bestand bij om services in uw cluster toe te wijzen aan lokale IP-adressen op de ontwikkel computer. Met deze hosts file entries kan code die wordt uitgevoerd op uw ontwikkel computer, aanvragen indienen bij andere services die in uw cluster worden uitgevoerd. Voor het bijwerken van uw hosts-bestand vraagt lokaal proces met Kubernetes beheerders toegang op uw ontwikkel computer wanneer er verbinding wordt gemaakt met uw cluster.
* Start de uitvoering en fout opsporing van uw code op uw ontwikkel computer. Als dat nodig is, kan lokaal proces met Kubernetes de vereiste poorten op uw ontwikkel computer gratis door de services of processen te stoppen die momenteel gebruikmaken van die poorten.

Nadat u een verbinding met uw cluster tot stand hebt gebracht, kunt u de code op uw computer en zonder container opslag uitvoeren en fouten opsporen. de code kan rechtstreeks communiceren met de rest van het cluster. Elk netwerk verkeer dat door de externe agent wordt ontvangen, wordt omgeleid naar de lokale poort die tijdens de verbinding is opgegeven, zodat uw systeem eigen code die verkeer kan accepteren en verwerken. De omgevings variabelen, volumes en geheimen van uw cluster worden beschikbaar gesteld aan code die wordt uitgevoerd op uw ontwikkel computer. Als gevolg van de hosts bestands vermeldingen en poort door sturen worden toegevoegd aan uw ontwikkelaars computer door lokaal proces met Kubernetes, kan uw code netwerk verkeer verzenden naar services die worden uitgevoerd op uw cluster met behulp van de service namen van uw cluster en dat verkeer wordt doorgestuurd naar de services die in uw cluster worden uitgevoerd. Verkeer wordt gerouteerd tussen uw ontwikkel computer en uw cluster op het hele moment dat u verbonden bent.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

Wanneer u lokaal proces met Kubernetes gebruikt om verbinding te maken met uw cluster, worden Diagnostische logboeken van uw cluster geregistreerd in de [tijdelijke map][azds-tmp-dir]van uw ontwikkel computer. Met Visual Studio code kunt u ook de opdracht *Diagnostische gegevens weer geven* gebruiken om de huidige omgevings variabelen en DNS-vermeldingen uit uw cluster af te drukken.

## <a name="limitations"></a>Beperkingen

Lokaal proces met Kubernetes heeft de volgende beperkingen:

* Lokaal proces met Kubernetes leidt het verkeer voor een enkele service om naar uw ontwikkel computer. U kunt geen lokaal proces met Kubernetes gebruiken om meerdere services tegelijk om te leiden.
* Een service moet worden ondersteund door één pod om verbinding te kunnen maken met die service. U kunt geen verbinding maken met een service met meerdere peulen, zoals een service met replica's.
* Een pod kan slechts één container hebben die in dat pod wordt uitgevoerd voor lokaal proces met Kubernetes om verbinding te maken. Lokaal proces met Kubernetes kan geen verbinding maken met Services met een Peul dat extra containers heeft, zoals zijspan containers die zijn geïnjecteerd door service netten.
* Lokaal proces met Kubernetes heeft verhoogde machtigingen nodig om te worden uitgevoerd op uw ontwikkel computer om het hosts-bestand te kunnen bewerken.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het gebruik van een lokaal proces met Kubernetes om verbinding te maken met uw lokale ontwikkel computer met uw cluster, raadpleegt u [lokaal proces gebruiken met Kubernetes met Visual Studio code][local-process-kubernetes-vs-code] en [lokaal proces gebruiken met Kubernetes met Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download