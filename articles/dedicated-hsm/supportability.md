---
title: Ondersteuning - Azure Dedicated HSM | Microsoft Documenten
description: Ondersteuningsopties en verantwoordelijkheden voor Azure Dedicated HSM in verschillende scenario's
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881018"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure Dedicated HSM-ondersteuning

De Azure Dedicated HSM-service biedt een fysiek apparaat voor uitsluitend gebruik door de klant met volledige beheercontrole en beheerverantwoordelijkheid. Het apparaat ter beschikking gesteld is een [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft heeft geen beheerderstoegang zodra deze door een klant is ingericht, naast fysieke seriële poortbijlage als controlerol.  Zonder toegang kan Microsoft geen doorlopend onderhoud op softwareniveau of verantwoordelijkheden voor systeembeheer hebben. Als gevolg hiervan zijn klanten verantwoordelijk voor typische operationele activiteiten.
Klanten zijn volledig verantwoordelijk voor toepassingen die gebruik maken van de HSM's en moeten samenwerken met Gemalto voor ondersteuning of consulting-gebaseerde hulp. Vanwege de mate van klantbezit van operationele hygiëne, is het niet mogelijk voor Microsoft om enige vorm van hoge beschikbaarheid garantie voor deze dienst te bieden. Het is de verantwoordelijkheid van de klant om ervoor te zorgen dat hun toepassingen correct zijn geconfigureerd om een hoge beschikbaarheid te bereiken. Microsoft controleert en onderhoudt de status van het apparaat en de netwerkconnectiviteit.

## <a name="getting-support"></a>Ondersteuning zoeken

Customer support voor Dedicated HSM is een gezamenlijke inspanning tussen Microsoft en Gemalto. Eventuele hardwareproblemen of netwerkpadproblemen worden door Microsoft aangepakt en alles wat met de werkelijke HSM te maken heeft, zoals configuratie, software, firmware en applicatieontwikkeling, wordt door Gemalto aangepakt. Dit ondersteuningsmodel zorgt voor de snelste route naar de meest effectieve ondersteuning. Als u twijfelt over een bepaald probleem, dient u een ondersteuningsverzoek in bij Microsoft en zorgen we ervoor dat u op de juiste manier wordt geleid. Microsoft blijft betrokken bij alle ondersteuningsscenario's en streeft naar de beste ondersteuningservaring voor onze klanten.

## <a name="gemalto-support"></a>Ondersteuning voor Gemalto

Klanten die de Dedicated HSM-service gebruiken, komen in aanmerking voor ondersteuning van Gemalto volgens hun Plus-ondersteuningsplan. Dit vereist alleen een registratieproces met behulp van de Gemalto support portal. Hiervoor worden een klant-id en instructies verstrekt als onderdeel van de eerste afspraak met Microsoft om toegang te krijgen tot de Dedicated HSM-service. Het mechanisme om ondersteuning te krijgen van Gemalto is via hun [customer support portal.](https://supportportal.gemalto.com/csm/)
Een belangrijk punt van opmerking is dat Gemalto alle software en documentatie zal leveren die nodig is om de HSM (bijvoorbeeld client access software en SDKs) te gebruiken via download op de customer support portal.

### <a name="software-components"></a>Softwarecomponenten

Verschillende softwarecomponenten worden gebruikt in de configuratie van HSM-apparaten:

* Clientsoftware
* SDK
* Hulpprogramma's

### <a name="guidance"></a>Richtlijnen

Gemalto stelt beheer- en configuratiebegeleiding beschikbaar via het [customer support portal.](https://supportportal.gemalto.com/csm/) Eenmaal aangemeld met een geldige klant-ID, zijn deze documenten beschikbaar om te downloaden. Gemalto biedt ook een reeks integratiegidsen om klanten te helpen met verschillende scenario's en software-integraties. Zie voor meer informatie de [gemalto-partnersite voor Microsoft.](https://safenet.gemalto.com/partners/microsoft/)

### <a name="support"></a>Ondersteuning

Elk probleem of vraag op softwareniveau met betrekking tot het gebruik van de HSM's als onderdeel van de Dedicated HSM-service, moet rechtstreeks worden gericht aan gemalto-ondersteuning. Alle hierboven genoemde softwarecomponenten en elke aangepaste HSM-configuratie die na de inprovisioning is, worden door Gemalto aangepakt. Zie voor meer informatie het [Gemalto customer support portal.](https://supportportal.gemalto.com/csm/)

### <a name="consulting-services"></a>Adviesservices

Neem contact op met uw Gemalto-accountvertegenwoordiger voor alle hulp bij het ontwerpen, ontwikkelen en implementeren van aangepaste toepassingen die gebruik maken van de HSM.

## <a name="microsoft-support"></a>Microsoft Ondersteuning

Microsoft zorgt ervoor dat fysieke HSM-apparaten netwerktoegankelijk zijn en in operationele staat zijn voor het exclusieve gebruik van één enkele klant. Klanten zijn verantwoordelijk voor de configuratie, het beheer en het beheer van het apparaat. De verantwoordelijkheden van Microsoft zijn onder andere:

* Ervoor zorgen dat het apparaat stroom en koeling heeft
* Een operationele status van de HSM behouden (bijvoorbeeld pauze-/fixscenario's)
* Het apparaat is toegankelijk via het netwerk.

Problemen zoals de volgende moeten worden gemeld aan Microsoft:

* Componentfouten
* Volledig apparaatfalen
* Problemen met netwerktoegang
* Problemen met het inrichten en deprovisioneren.

Microsoft heeft fysieke seriële poort toegang tot het apparaat via een controlerol (dat wil zeggen, niet administratieve rol) die basisgezondheid telemetrie mogelijk maakt.  Hierdoor kan Microsoft de klant proactief op de hoogte stellen van problemen, tenzij de klant ervoor kiest deze machtiging te beperken. 

### <a name="provisioning-and-decommissioning"></a>Inrichting en ontmanteling

Nadat een klant een goedgekeurde registratie heeft voor de Dedicated HSM-service, kunnen ze HSM-resources maken (momenteel via PowerShell- of command-line-interface en niet via de Azure-portal). De resource gaat door een toewijzingsproces dat een fysiek apparaat in een bepaald gebied in kaart brengt, naar het vooraf gedefinieerde virtuele netwerk (VNet) van een klant. Eenmaal zichtbaar op een VNet, kan de klant toegang krijgen tot het apparaat en het verder configureren volgens de vereisten. Klanten hebben toegang tot hun speciale HSM's met behulp van Gemalto-clientsoftware en -tools. Het proces voor het maken van resources wordt ondersteund door Microsoft. Aangepaste configuratieproces en daarbuiten worden ondersteund door Gemalto. (zie Gemalto ondersteuning hierboven). Wanneer een klant klaar is met het gebruik van een HSM, moet deze worden gereset (of gezeroiseerd) om ervoor te zorgen dat gegevens niet worden persistent. Het proces van het opnieuw instellen van het apparaat verwijdert alle aangepaste configuratie en gegevens. Microsoft dekent het apparaat af en retourneert het in een onberispelijke staat naar het zwembad. Dit betekent dat wanneer het apparaat wordt teruggestuurd naar de pool er geen bewijs is van eerdere activiteiten van de klant. 

### <a name="hardware-issues"></a>Hardwareproblemen

Het HSM-apparaat heeft redundante en vervangbare voedingen en ventilatorunits.  Het verwijderen van ventilatoreenheden zal echter nog steeds een sabotagegebeurtenis veroorzaken. Wanneer een componentfout optreedt, gebruikt Microsoft het meest geschikte proces om het probleem op componentniveau aan te pakken op een manier die een minimale onderbreking en het laagste risico voor de beschikbaarheid van onze klantenservice veroorzaakt.
Een ernstiger defect van het apparaat zal resulteren in dat apparaat wordt vervangen door een verse een van de vrije zwembad. De klant neemt eenvoudig het nieuwe apparaat in het bestaande HA-paar op om het te synchroniseren en terug te keren naar volledige operationele status. Het defecte apparaat laat zijn gegevensdragende apparaten ter plaatse in het datacenter verwijderen en versnipperen. Alleen het chassis wordt teruggegeven aan Gemalto voor recycling.


### <a name="networking-issues"></a>Netwerkproblemen

Als klanten problemen ondervinden met netwerktoegang tot het HSM-apparaat, moeten ze contact opnemen met de ondersteuning van Microsoft. Een eenvoudige test voor netwerktoegang is om SSH te gebruiken om verbinding te maken met het HSM-apparaat. Als dit niet lukt, neemt u contact op met de ondersteuning van Microsoft.

## <a name="service-level-expectations-for-support"></a>Verwachtingen op serviceniveau voor ondersteuning

Raadpleeg voor ondersteuningsserviceniveaus van Microsoft het [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/).
Voor gemalto ondersteuning service niveaus, verwijzen naar de [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen dat belangrijke concepten zoals hoge beschikbaarheid en beveiliging goed worden begrepen voordat apparaatinrichting en toepassingsontwerp of -implementatie worden geïmplementeerd.

* [Implementatiearchitectuur](deployment-architecture.md)
* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Networking](networking.md)

