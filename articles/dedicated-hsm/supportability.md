---
title: Ondersteuning - Azure toegewezen HSM | Microsoft Docs
description: Ondersteuningsopties en verantwoordelijkheidsgebieden ten opzichte van voor Azure toegewezen HSM in verschillende scenario 's
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881018"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure toegewezen HSM-ondersteuning

De Azure toegewezen HSM-Service biedt een fysiek apparaat voor gebruik door de enige klant met volledige controle en beheer van beheertaken. Het apparaat beschikbaar gesteld is een [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft heeft geen beheerderstoegang tot één keer worden ingericht door een klant, dan fysieke seriële poort bijlage als een controle rol.  Zonder toegang tot kan Microsoft zijn geen lopende software niveau onderhoud of het systeem beheer verantwoordelijk voor de. Als gevolg hiervan zijn klanten verantwoordelijk voor normale operationele activiteiten.
Klanten zijn zelf verantwoordelijk voor toepassingen die gebruikmaken van de HSM's en moeten werken met Gemalto voor ondersteuning of op basis van consulting assistentie. Vanwege de omvang van de klant eigendom van operationele hygiëne is het niet mogelijk voor Microsoft te bieden van welke aard dan ook van hoge beschikbaarheidsgarantie voor deze service. Het is de verantwoordelijkheid van de klant om te controleren of hun toepassingen correct zijn geconfigureerd voor het bereiken van hoge beschikbaarheid. Microsoft zal bewaken en onderhouden van apparaat status en de netwerkverbinding.

## <a name="getting-support"></a>Ondersteuning zoeken

Klanten ondersteuning voor toegewezen HSM is een gezamenlijke inspanning tussen micro soft en Gemalto. Eventuele hardwareproblemen of problemen met het netwerkpad worden door micro soft verholpen, en alles wat u kunt doen met de daad werkelijke HSM, zoals configuratie, software, firmware en toepassings ontwikkeling, wordt geadresseerd door Gemalto. Dit ondersteunings model zorgt voor een snelle route naar de meest efficiënte ondersteuning. Als u twijfelt met een bepaald probleem, verhoogt u een ondersteunings aanvraag met micro soft en zorgt u ervoor dat u op de juiste wijze omgaat. Micro soft blijft in de ondersteunings scenario's werken en streeft naar de beste ondersteunings ervaring voor onze klanten.

## <a name="gemalto-support"></a>Ondersteuning voor Gemalto

Klanten die gebruikmaken van de speciale HSM-service, komen in aanmerking voor ondersteuning van Gemalto conform hun plus ondersteunings plan. Hiervoor hebt u alleen een registratie proces nodig met behulp van de Gemalto-ondersteunings portal. Er wordt een klant-ID en instructies voor dit gegeven als onderdeel van de eerste betrokkenheid bij micro soft om toegang te krijgen tot de toegewezen HSM-service. Het mechanisme voor ondersteuning krijgen van Gemalto is via hun [ondersteuning klantportal](https://supportportal.gemalto.com/csm/).
Een belang rijk punt van de opmerking is dat Gemalto alle software en documentatie biedt die nodig zijn om de HSM (bijvoorbeeld Client Access software en Sdk's) te gebruiken via down load op de portal voor klant ondersteuning.

### <a name="software-components"></a>Software-onderdelen

Verschillende softwareonderdelen worden gebruikt in de configuratie van de HSM-apparaten:

* Clientsoftware
* SDK
* Hulpprogramma's

### <a name="guidance"></a>Richtlijnen

Gemalto maakt beschikbaar richtlijnen voor de beheer- en configuratie via de [ondersteuning klantportal](https://supportportal.gemalto.com/csm/). Nadat u aangemeld met een geldig vat de klant-ID, zijn deze documenten beschikbaar voor downloaden. Gemalto biedt ook een reeks integratiehandleidingen om klanten te helpen met verschillende scenario's en software-integraties. Zie voor meer informatie de [Gemalto partnersite voor Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Ondersteuning

Elk niveau softwareprobleem of vraag ten opzichte van de HSM's gebruiken als onderdeel van de toegewezen HSM-service moet worden opgelost op Gemalto rechtstreeks ondersteuning. Alle hierboven vermelde software-onderdelen en eventuele aangepaste HSM-configuratie die na inrichting is verholpen door Gemalto. Zie voor meer informatie de [Gemalto customer support portal](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Adviesservices

Voor elke hulp bij het ontwerp, ontwikkeling en implementatie van aangepaste toepassingen die gebruikmaken van de HSM, contact op met uw accountvertegenwoordiger Gemalto.

## <a name="microsoft-support"></a>Microsoft Ondersteuning

Micro soft zorgt ervoor dat fysieke HSM-apparaten toegankelijk zijn via het netwerk en in een operationele status voor exclusief gebruik van één klant. Klanten zijn verantwoordelijk voor de configuratie, het beheer en het beheer van het apparaat. De verantwoordelijkheden van Microsoft zijn onder andere:

* Ervoor dat het apparaat voeding heeft en koeling, zodat
* Onderhoud van de operationele status van de HSM (bijvoorbeeld ondersteuning voor probleemoplossing scenario's)
* Het apparaat is toegankelijk via het netwerk.

Problemen zoals het volgende moeten worden gerapporteerd aan Microsoft:

* Fouten met opslagcomponenten
* Fout met volledige
* Problemen met netwerktoegang
* Problemen met inrichten en het opheffen van inrichting.

Microsoft heeft een seriële poort fysieke toegang tot het apparaat via een controle rol (dat wil zeggen, niet met beheerdersrechten rol) waarmee basisbewaking van statussen telemetrie.  Hierdoor kan Microsoft voor proactieve meldingen van problemen voor de klant, tenzij de klant wil deze machtiging beperken. 

### <a name="provisioning-and-decommissioning"></a>Inrichting en buiten gebruik stellen

Nadat een klant een goedgekeurde registratie voor de toegewezen HSM-service heeft, wordt deze mogelijk zijn om HSM-resources (momenteel via PowerShell of opdrachtregelinterface en niet de Azure portal) te maken. De resource verloopt via een toegewezen proces dat is een fysiek apparaat in een bepaald gebied aan een klant vooraf gedefinieerde virtueel netwerk (VNet toegewezen). Zodra zichtbaar is op een VNet, kan de klant toegang tot het apparaat en verder te configureren volgens de vereisten. Klanten toegang krijgen tot hun toegewezen HSM's met behulp van de clientsoftware Gemalto en hulpprogramma's. Het proces voor het maken van resource wordt ondersteund door Microsoft. Aangepaste configuratie verwerken en meer worden ondersteund door Gemalto. (Zie Gemalto ondersteunen hierboven). Wanneer een klant is voltooid met behulp van een HSM, er moet opnieuw worden ingesteld (of zeroized) om te controleren of er is geen persistentie van gegevens. Het proces van het apparaat opnieuw instellen, verwijdert u alle aangepaste configuratie en gegevens. Microsoft maakt het apparaat de toewijzingen ongedaan en geeft dit terug aan de pool in een nieuw staat. Dit betekent dat wanneer het apparaat wordt geretourneerd aan de groep is er geen bewijs van vorige klantenactiviteit. 

### <a name="hardware-issues"></a>Hardwareproblemen

De HSM-apparaat heeft redundante en replaceable voedingen en fan-eenheden.  Het verwijderen van een ventilator eenheid heeft echter nog steeds een onrecht matige gebeurtenis. Wanneer een onderdeelfout optreedt, wordt Microsoft het meest geschikte proces gebruiken om het probleem met serveronderdeel niveau op een manier die ervoor zorgt minimale onderbreking en het laagste risico op de beschikbaarheid van onze klanten-services dat.
Een ernstige fout van het apparaat zal leiden tot dat apparaat wordt vervangen door een nieuwe naam van de groep met gratis. De klant bevat gewoon het nieuwe apparaat in het bestaande HA-paar voor het synchroniseren en gaat u terug naar volledige operationele status. Het mislukte apparaat heeft de gegevens die zijn voorzien van apparaten verwijderd en versnipperde op site in het datacenter. Alleen het chassis naar Gemalto geretourneerd voor hergebruik.


### <a name="networking-issues"></a>Netwerkproblemen

Als klanten netwerkproblemen toegang naar de HSM-apparaat ondervinden, moeten ze contact op met Microsoft ondersteuning. Een eenvoudige test voor toegang is het gebruik van SSH verbinding maken met de HSM-apparaat. Als dit mislukt, moet u contact op met Microsoft ondersteuning.

## <a name="service-level-expectations-for-support"></a>Service level verwachtingen voor ondersteuning

Voor serviceniveaus voor Microsoft-ondersteuning, raadpleegt u de [Azure-ondersteuningsplan](https://azure.microsoft.com/support/plans/).
Voor serviceniveaus Gemalto support, raadpleegt u de [Gemalto ondersteunen Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Volgende stappen

Het verdient aanbeveling dat belangrijkste concepten, zoals hoge beschikbaarheid en beveiliging voor apparaatinrichting en ontwerp van toepassingen of implementatie goed wordt begrepen.

* [Implementatie-architectuur](deployment-architecture.md)
* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)

