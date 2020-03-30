---
title: Back-ends en backendpools in Azure Front Door| Microsoft Documenten
description: In dit artikel wordt beschreven welke backends en backendpools zich in frontdoorconfiguratie bevinden.
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
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293472"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Back-ends en backend-zwembaden in Azure Front Door
In dit artikel worden concepten beschreven over het toewijzen van uw app-implementatie met Azure Front Door. Het verklaart ook de verschillende termen in Front Door configuratie rond app backends.

## <a name="backends"></a>Back-ends
Een back-end is gelijk aan de implementatie-instantie van een app in een regio. Front Door ondersteunt zowel Azure- als niet-Azure-backends, zodat de regio niet alleen beperkt is tot Azure-regio's. Het kan ook uw on-premises datacenter of een app-exemplaar in een andere cloud zijn.

Backends voor de voordeur verwijzen naar de hostnaam of het openbare IP-adres van uw app, die clientaanvragen kan dienen. Backends mogen niet worden verward met uw databaselaag, opslaglaag, enzovoort. Back-ends moeten worden gezien als het openbare eindpunt van uw app back-end. Wanneer u een backend toevoegt in een backendpool voor de voordeur, moet u ook het volgende toevoegen:

- **Backendhosttype**. Het type resource dat u wilt toevoegen. Front Door ondersteunt automatisch afspelen van uw app backends van app-service, cloudservice of opslag. Als u een andere bron in Azure of zelfs een niet-Azure-backend wilt, selecteert u **Aangepaste host**.

    >[!IMPORTANT]
    >Tijdens de configuratie valideren API's niet of de backend niet toegankelijk is vanuit frontdooromgevingen. Zorg ervoor dat de voordeur uw backend kan bereiken.

- **Naam van een abonnement en backendhost**. Als u aangepaste **host** niet hebt geselecteerd voor het type backendhost, selecteert u uw backend door het juiste abonnement en de bijbehorende backendhostnaam in de gebruikersinterface te kiezen.

- **Koptekst voor backendhost**. De waarde van de hostheader die voor elk verzoek naar de backend wordt verzonden. Zie [Backend hostheader](#hostheader)voor meer informatie .

- **Prioriteit**. Wijs prioriteiten toe aan uw verschillende backends wanneer u een primaire servicebackend wilt gebruiken voor al het verkeer. Geef ook back-ups als de primaire of de back-upback-ups niet beschikbaar zijn. Zie [Prioriteit](front-door-routing-methods.md#priority)voor meer informatie.

- **Gewicht**. Wijs gewichten toe aan uw verschillende backends om het verkeer gelijkmatig of op basis van gewichtscoëfficiënten over een reeks backends te verdelen. Zie [Gewichten](front-door-routing-methods.md#weighted)voor meer informatie.

### <a name="backend-host-header"></a><a name = "hostheader"></a>Backendhostkop

Aanvragen die door Front Door naar een backend worden doorgestuurd, bevatten een hostheaderveld dat de backend gebruikt om de beoogde bron op te halen. De waarde voor dit veld komt meestal van de backend URI en heeft de host en poort.

Een aanvraag voor `www.contoso.com` een hostheader wordt bijvoorbeeld www.contoso.com. Als u Azure-portal gebruikt om uw back-end te configureren, is de standaardwaarde voor dit veld de hostnaam van de backend. Als uw backend contoso-westus.azurewebsites.net is, wordt in de Azure-portal de automatisch ingevulde waarde voor de backendhostkop contoso-westus.azurewebsites.net. Als u echter Azure Resource Manager-sjablonen of een andere methode gebruikt zonder dit veld expliciet in te stellen, stuurt Front Door de inkomende hostnaam als de waarde voor de hostkoptekst. Als het verzoek is\.ingediend voor www contoso.com en uw backend is contoso-westus.azurewebsites.net met een\.leeg koptekstveld, stelt De voordeur de hostheader in als contoso.com.

Voor de meeste back-ends voor apps (Azure Web Apps, Blob-opslag en Cloud Services) moet de hostkopovereenkomen met het domein van de backend. De frontendhost die naar uw backend leidt, gebruikt echter een andere hostnaam, zoals www.contoso.net.

Als voor uw backend de hostheader moet overeenkomen met de naam van de backendhost, moet u ervoor zorgen dat de backendhostkop de backend-backend bevat.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>De backend-hostkop voor de backend configureren

Ga als u het veld **backendhostheader** configureren voor een backend in de sectie backendpool:

1. Open uw bron voor de voordeur en selecteer de backendpool met de backend om te configureren.

2. Voeg een backend toe als u dit nog niet hebt gedaan of bewerk een bestaande backend.

3. Stel het veld backendhostheader in op een aangepaste waarde of laat het leeg. De hostnaam voor de binnenkomende aanvraag wordt gebruikt als de waarde van de hostheader.

## <a name="backend-pools"></a>Backend pools
Een backend pool in Front Door verwijst naar de set backends die vergelijkbaar verkeer voor hun app ontvangen. Met andere woorden, het is een logische groepering van uw app-exemplaren over de hele wereld die hetzelfde verkeer ontvangen en reageren met verwacht gedrag. Deze backends worden geïmplementeerd in verschillende regio's of binnen dezelfde regio. Alle backends kunnen in de active/actieve implementatiemodus of wat wordt gedefinieerd als actieve/passieve configuratie.

Een backend pool definieert hoe de verschillende backends moeten worden geëvalueerd via health probes. Het definieert ook hoe load balancing optreedt tussen hen.

### <a name="health-probes"></a>Statuscontroles
Front Door stuurt periodieke HTTP/HTTPS-sondeverzoeken naar elk van uw geconfigureerde backends. Sondeaanvragen bepalen de nabijheid en status van elke backend om het saldo van uw eindgebruikersaanvragen te laden. Instellingen voor statussen voor een backendpool definiëren hoe we de status van app-backends peilen. De volgende instellingen zijn beschikbaar voor de configuratie van de taakverdeling:

- **Pad:** de URL die wordt gebruikt voor sondeaanvragen voor alle backends in de backendpool. Als een van uw backends bijvoorbeeld is contoso-westus.azurewebsites.net en het pad is ingesteld op /probe/test.aspx, worden frontdoor-omgevingen, ervan uitgaande dat het protocol is ingesteld op HTTP, aanvragen voor statussonde naar http\:-contoso-westus.azurewebsites.net/probe/test.aspx verzonden.

- **Protocol:** hiermee bepaalt u of u de aanvragen voor statussonde sonde van Front Door naar uw backends wilt verzenden met HTTP- of HTTPS-protocol.

- **Methode**:De HTTP-methode die moet worden gebruikt voor het verzenden van statussondes. Opties zijn GET of HEAD (standaard).
    > [!NOTE]
    > Voor lagere belasting en kosten op uw backends, Front Door beveelt het gebruik van HEAD verzoeken voor de gezondheid sondes.

- **Interval (seconden):** hiermee definieert u de frequentie van statussondes naar uw backends of de intervallen waarin elk van de frontdooromgevingen een sonde verzendt.

    >[!NOTE]
    >Voor snellere failovers stelt u het interval in op een lagere waarde. Hoe lager de waarde, hoe hoger het volume van de health probe die uw backends ontvangen. Als het interval bijvoorbeeld is ingesteld op 30 seconden met bijvoorbeeld 100 pop-opsteken wereldwijd door de voordeur, ontvangt elke backend ongeveer 200 sondeaanvragen per minuut.

Zie [Gezondheidssondes voor](front-door-health-probes.md)meer informatie.

### <a name="load-balancing-settings"></a>Instellingen voor taakverdeling
Load-balancing-instellingen voor de backendpool definiëren hoe we statussondes evalueren. Deze instellingen bepalen of de backend gezond of ongezond is. Ze controleren ook hoe je het verkeer tussen verschillende backends in de backend pool inbalansbrengen. De volgende instellingen zijn beschikbaar voor de configuratie van de taakverdeling:

- **Steekproefgrootte**. Identificeert hoeveel monsters van de gezondheid sondes die we moeten overwegen voor backend gezondheid evaluatie.

- **Succesvolle steekproefgrootte**. Hiermee definieert u de steekproefgrootte zoals eerder vermeld, het aantal succesvolle voorbeelden dat nodig is om de backend gezond aan te roepen. Stel dat een interval voor de statussonde van de voordeur 30 seconden is, de steekproefgrootte 5 is en de winnende steekproefgrootte 3 is. Elke keer dat we de gezondheidssondes voor uw backend evalueren, kijken we naar de laatste vijf monsters over 150 seconden (5 x 30). Ten minste drie succesvolle sondes zijn nodig om de backend als gezond te verklaren.

- **Latentiegevoeligheid (extra latentie)**. Hiermee bepaalt u of u wilt dat Front Door het verzoek verzendt naar backends binnen het latentiemeetgevoeligheidsbereik of het verzoek doorstuurt naar de dichtstbijzijnde backend.

Zie [Op de minst latentie gebaseerde routeringsmethode](front-door-routing-methods.md#latency)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

- [Een voordeurprofiel maken](quickstart-create-front-door.md)
- [Hoe Voordeur werkt](front-door-routing-architecture.md)