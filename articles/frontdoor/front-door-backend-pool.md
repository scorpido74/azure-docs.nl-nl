---
title: Back-end-en back-end-Pools in de Azure front-deur service | Microsoft Docs
description: In dit artikel wordt beschreven welke back-end-en back-endservers zich in de voor deur bevinden.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229022"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Back-end-en back-end-Pools in de Azure front-deur service
In dit artikel worden de concepten beschreven voor het toewijzen van uw app-implementatie met de Azure front-deur service. Ook worden de verschillende termen in de voor deur configuratie van de back-end van apps uitgelegd.

## <a name="backends"></a>Back-ends
Een back-end is gelijk aan het implementatie-exemplaar van een app in een regio. De front-deur service ondersteunt zowel Azure-als niet-Azure-back-ends, zodat de regio niet alleen beperkt is tot Azure-regio's. Het kan ook uw on-premises Data Center of een app-exemplaar in een andere cloud zijn.

De back-end van de front-deur service verwijst naar de hostnaam of het open bare IP-adres van uw app, die client aanvragen kan verwerken. Back-ends mogen niet worden verward met uw data base-laag, opslaglaag, enzovoort. Back-ends moeten worden weer gegeven als het open bare eind punt van de back-end van uw app. Wanneer u een back-end in een back-end van een front-deur groep toevoegt, moet u ook het volgende toevoegen:

- **Type back-end**. Het type resource dat u wilt toevoegen. De front-deur service ondersteunt automatische detectie van uw app-back-ends vanuit app service, Cloud service of Storage. Als u een andere resource wilt in azure of zelfs een back-end die niet van Azure is, selecteert u **aangepaste host**.

    >[!IMPORTANT]
    >Tijdens de configuratie valideert Api's niet als de back-end niet toegankelijk is vanaf de front-deur omgevingen. Zorg ervoor dat de front-deur uw back-end kan bereiken.

- De hostnaam van het **abonnement en de back-end**. Als u geen **aangepaste host** voor het type back-end hebt geselecteerd, selecteert u uw back-end door het juiste abonnement en de bijbehorende back-end-hostnaam in de gebruikers interface te kiezen.

- **Back-end-host-header**. De waarde van de host-header die wordt verzonden naar de back-end voor elke aanvraag. Zie voor meer informatie [back-end-host-header](#hostheader).

- **Prioriteit**. Wijs prioriteiten toe aan uw verschillende back-ends wanneer u een primaire service back-end voor al het verkeer wilt gebruiken. Geef ook back-ups op als de primaire of de back-end niet beschikbaar zijn. Zie [Priority](front-door-routing-methods.md#priority)voor meer informatie.

- **Gewicht**. Wijs gewichten toe aan uw verschillende back-ends voor het distribueren van verkeer over een reeks back-ends, hetzij gelijkmatig, hetzij op basis van gewichts coëfficiënten. Zie [gewichten](front-door-routing-methods.md#weighted)voor meer informatie.

### <a name = "hostheader"></a>Host-header van back-end

Aanvragen die door de voor deur naar een back-end worden doorgestuurd, bevatten een host-header veld dat door de back-end wordt gebruikt om de doel resource op te halen. De waarde voor dit veld is doorgaans afkomstig van de back-end-URI en heeft de host en poort.

Een aanvraag voor www\.contoso.com heeft bijvoorbeeld de host-header www\.contoso.com. Als u Azure Portal gebruikt voor het configureren van uw back-end, is de standaard waarde voor dit veld de hostnaam van de back-end. Als uw back-end contoso-westus.azurewebsites.net is, wordt in de Azure Portal de automatisch gevulde waarde voor de host-header van de back-end contoso-westus.azurewebsites.net. Als u echter Azure Resource Manager sjablonen of een andere methode gebruikt zonder dit veld expliciet in te stellen, stuurt de front-deur service de binnenkomende hostnaam als de waarde voor de host-header. Als de aanvraag voor www\.contoso.com is gemaakt en uw back-end contoso-westus.azurewebsites.net met een leeg header-veld heeft, wordt de host-header door de front-deur service ingesteld als www-\.contoso.com.

Voor de meeste app-back-ends (Azure Web Apps, Blob Storage en Cloud Services) moet de host-header overeenkomen met het domein van de back-end. De frontend-host die naar uw back-end stuurt, gebruikt echter een andere hostnaam, zoals www\.contoso.azurefd.net.

Als voor uw back-end de host-header moet overeenkomen met de naam van de back-end, moet u ervoor zorgen dat de back-endserver de hostnaam van de host bevat.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>De host-header van de back-end voor de back-end configureren

Het veld voor de **back-end-host-header** configureren voor een back-end in de back-endadresgroep-groep:

1. Open de resource van de voor deur en selecteer de back-end-pool met de te configureren back-end.

2. Voeg een back-end toe als u dit nog niet hebt gedaan, of bewerk een bestaande.

3. Stel het veld voor de back-end-host-header in op een aangepaste waarde of laat dit vak leeg. De hostnaam voor de binnenkomende aanvraag wordt gebruikt als de waarde voor de host-header.

## <a name="backend-pools"></a>Back-end-Pools
Een back-end-groep in de voor deur-service verwijst naar de set back-ends die soortgelijk verkeer voor hun app ontvangen. Met andere woorden, het is een logische groepering van uw app-instanties over de hele wereld die hetzelfde verkeer ontvangen en op de verwachte manier reageert. Deze back-ends worden geïmplementeerd in verschillende regio's of binnen dezelfde regio. Alle back-ends kunnen zich in de Active/Active-implementatie modus bevindt of wat is gedefinieerd als actieve/passieve configuratie.

Een back-end-pool definieert hoe de verschillende back-ends moeten worden geëvalueerd via status controles. Ook wordt gedefinieerd hoe taak verdeling tussen deze items wordt uitgevoerd.

### <a name="health-probes"></a>Statuscontroles
De voor deur-service verzendt periodiek HTTP/HTTPS-test aanvragen naar elk van de geconfigureerde back-endservers. Test aanvragen bepalen de nabijheid en status van elke back-end om uw aanvragen van uw eind gebruikers te verdelen. De status test instellingen voor een back-end-pool bepalen hoe we de status van app-back-ends controleren. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

- **Pad**. De URL die wordt gebruikt voor het testen van aanvragen voor alle back-ends in de back-end-pool. Als een van uw back-ends bijvoorbeeld contoso-westus.azurewebsites.net is en het pad is ingesteld op/probe/test.aspx, wordt de status van de voor deur-en test omgevingen, ervan uitgaande dat het protocol is ingesteld op HTTP, verzonden naar http\://contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocol**. Hiermee wordt bepaald of de Health probe-aanvragen van de front-deur service moeten worden verzonden naar uw back-end met het HTTP-of HTTPS-protocol.

- **Interval (seconden)** . Hiermee wordt de frequentie van status controles voor uw back-ends gedefinieerd, of de intervallen waarin elk van de voor deur omgevingen een sonde verzendt.

    >[!NOTE]
    >Voor snellere failovers stelt u het interval in op een lagere waarde. Hoe lager de waarde, hoe hoger het volume van de status test uw back-ends worden ontvangen. Als het interval bijvoorbeeld is ingesteld op 30 seconden met een front-deur omgeving van 90 of Pop's wereld wijd, ontvangt elke back-end ongeveer 3-5 test aanvragen per seconde.

Zie [status tests](front-door-health-probes.md)voor meer informatie.

### <a name="load-balancing-settings"></a>Instellingen voor taak verdeling
Instellingen voor taak verdeling voor de back-end-pool bepalen hoe de status tests worden geëvalueerd. Deze instellingen bepalen of de back-end in orde of beschadigd is. Ze controleren ook hoe de taak verdeling van verkeer tussen verschillende back-ends in de back-end-pool wordt gebalanceerd. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

- **Voorbeeld grootte**. Hiermee wordt aangegeven hoeveel voor beelden van status tests er nodig zijn om de status van de back-end te evalueren.

- De **voorbeeld grootte is voltooid**. Hiermee definieert u de voorbeeld grootte zoals eerder vermeld, het aantal geslaagde steek proeven dat nodig is voor het aanroepen van de back-end in orde. Stel dat het interval voor de status test voor de voor deur 30 seconden is, de voorbeeld grootte 5 is en een geslaagde voorbeeld grootte is ingesteld op 3. Telkens wanneer we de status controles voor uw back-end evalueren, bekijken we de laatste vijf steek proeven van meer dan 150 seconden (5 x 30). Ten minste drie geslaagde tests zijn vereist voor het declareren van de backend als in orde.

- **Latentie gevoeligheid (extra latentie)** . Hiermee definieert u of u de aanvraag wilt verzenden naar back-ends binnen het gevoeligheids bereik voor latentie meting of door sturen van de aanvraag naar de dichtstbijzijnde back-end.

Zie voor meer informatie [op minste latentie gebaseerde routerings methode](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Volgende stappen

- [Een voor deur profiel maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)