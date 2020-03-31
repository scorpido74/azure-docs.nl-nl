---
title: Bescherming van klantgegevens in Azure
description: In dit artikel wordt ingegaan op de manier waarop Azure klantgegevens beschermt.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726684"
---
# <a name="azure-customer-data-protection"></a>Azure-klantgegevensbeveiliging   
Toegang tot klantgegevens door Microsoft-operations en ondersteuningspersoneel wordt standaard geweigerd. Wanneer toegang tot klantgegevens wordt verleend, is goedkeuring van leiderschap vereist en wordt de toegang zorgvuldig beheerd en geregistreerd. De vereisten voor toegangscontrole worden vastgesteld door het volgende Azure-beveiligingsbeleid:

- Standaard geen toegang tot klantgegevens.
- Geen gebruikers- of beheerdersaccounts op virtuele machines van klanten (VM's).
- Geef de minste bevoegdheid die nodig is om taken uit te voeren; audit- en logtoegangsverzoeken.

Azure-ondersteuningspersoneel krijgt unieke bedrijfsActive Directory-accounts van Microsoft toegewezen. Azure vertrouwt op Microsoft corporate Active Directory, beheerd door Microsoft Information Technology (MSIT), om de toegang tot belangrijke informatiesystemen te beheren. Multi-factor authenticatie is vereist, en toegang wordt alleen verleend van beveiligde consoles.

Alle toegangspogingen worden gecontroleerd en kunnen worden weergegeven via een basisset rapporten.

## <a name="data-protection"></a>Gegevensbeveiliging
Azure biedt klanten een sterke gegevensbeveiliging, zowel standaard als als klantopties.

**Gegevensscheiding**: Azure is een multi-tenantservice, wat betekent dat meerdere klantimplementaties en VM's op dezelfde fysieke hardware worden opgeslagen. Azure gebruikt logische isolatie om de gegevens van elke klant te scheiden van de gegevens van anderen. Segregatie biedt de schaal en economische voordelen van multi-tenant services en voorkomt tegelijkertijd dat klanten toegang krijgen tot elkaars gegevens.

**Gegevensbescherming ten rust:** klanten zijn verantwoordelijk voor het waarborgen dat gegevens die in Azure zijn opgeslagen, worden versleuteld in overeenstemming met hun normen. Azure biedt een breed scala aan versleutelingsmogelijkheden, waardoor klanten de flexibiliteit hebben om de oplossing te kiezen die het beste aan hun behoeften voldoet. Azure Key Vault helpt klanten eenvoudig de controle te behouden over sleutels die worden gebruikt door cloudtoepassingen en -services om gegevens te versleutelen. Azure Disk Encryption stelt klanten in staat om VM's te versleutelen. Azure Storage Service Encryption maakt het mogelijk om alle gegevens te versleutelen die in het opslagaccount van een klant zijn geplaatst.

**Gegevensbescherming tijdens het transport:** klanten kunnen versleuteling inschakelen voor verkeer tussen hun eigen VM's en eindgebruikers. Azure beschermt gegevens die onderweg zijn naar of van externe componenten en gegevens die intern worden vervoerd, zoals tussen twee virtuele netwerken. Azure gebruikt het industriestandaard TLS(1.2) of hoger protocol (Transport Layer Security) met 2.048-bits RSA/SHA256-coderingssleutels, zoals aanbevolen door CESG/NCSC, om communicatie tussen:

- De klant en de cloud.
- Intern tussen Azure-systemen en datacenters.

**Versleuteling**: Versleuteling van gegevens in opslag en onderweg kan door klanten worden geïmplementeerd als een best practice om de vertrouwelijkheid en integriteit van gegevens te waarborgen. Het is eenvoudig voor klanten om hun Azure-cloudservices te configureren om SSL te gebruiken om communicatie te beschermen tegen internet en zelfs tussen hun door Azure gehoste VM's.

**Gegevensredundantie**: Microsoft helpt ervoor te zorgen dat gegevens worden beschermd als er een cyberaanval of fysieke schade aan een datacenter is. Klanten kunnen kiezen voor:

- In-country/in-region opslag voor naleving of latentie overwegingen.
- Out-of-country/out-of-region storage voor veiligheids- of disaster recovery doeleinden.

Gegevens kunnen worden gerepliceerd binnen een geselecteerd geografisch gebied voor redundantie, maar kunnen niet worden verzonden buiten het. Klanten hebben meerdere opties voor het repliceren van gegevens, waaronder het aantal kopieën en het aantal en de locatie van replicatiedatacenters.

Wanneer u uw opslagaccount maakt, selecteert u een van de volgende replicatieopties:

- **Lokaal redundante opslag (LRS)**: Lokaal redundante opslag onderhoudt drie kopieën van uw gegevens. LRS wordt binnen één faciliteit in één regio driemaal gerepliceerd. LRS beschermt uw gegevens tegen normale hardwarefouten, maar niet tegen een storing van één faciliteit.
- **Zoneredundante opslag (ZRS)**: Zoneredundante opslag bevat drie kopieën van uw gegevens. ZRS wordt drie keer gerepliceerd in twee tot drie faciliteiten om een hogere duurzaamheid te bieden dan LRS. Replicatie vindt plaats binnen één regio of in twee regio's. ZRS zorgt ervoor dat uw gegevens binnen één regio duurzaam zijn.
- **Georedundante opslag (GRS):** Georedundant-opslag is standaard ingeschakeld voor uw opslagaccount wanneer u deze maakt. GRS onderhoudt zes kopieën van uw gegevens. Met GRS worden uw gegevens drie keer gerepliceerd binnen de primaire regio. Uw gegevens worden ook drie keer gerepliceerd in een secundaire regio honderden kilometers afstand van de primaire regio, waardoor het hoogste niveau van duurzaamheid. In het geval van een storing in het primaire gebied, mislukt Azure Storage naar het secundaire gebied. GRS zorgt ervoor dat uw gegevens in twee verschillende regio's duurzaam zijn.

**Gegevensvernietiging:** Wanneer klanten gegevens verwijderen of Azure verlaten, volgt Microsoft strenge normen voor het overschrijven van opslagbronnen voordat ze opnieuw worden gebruikt, evenals de fysieke vernietiging van buiten bedrijf genomen hardware. Microsoft voert een volledige verwijdering van gegevens uit op verzoek van de klant en bij beëindiging van het contract.

## <a name="customer-data-ownership"></a>Eigendom van klantgegevens
Microsoft inspecteert, keurt of controleert geen toepassingen die klanten implementeren in Azure. Bovendien weet Microsoft niet wat voor soort gegevens klanten kiezen om op te slaan in Azure. Microsoft claimt geen eigendom van gegevens over de klantgegevens die in Azure zijn ingevoerd.

## <a name="records-management"></a>Recordsbeheer
Azure heeft interne vereisten voor het bewaren van records voor back-endgegevens vastgesteld. Klanten zijn verantwoordelijk voor het identificeren van hun eigen vereisten voor het bewaren van gegevens. Voor records die zijn opgeslagen in Azure, zijn klanten verantwoordelijk voor het extraheren van hun gegevens en het bewaren van hun inhoud buiten Azure voor een door de klant opgegeven bewaarperiode.

Azure stelt klanten in staat om gegevens en auditrapporten van het product te exporteren. De export wordt lokaal opgeslagen om de informatie te bewaren voor een door de klant gedefinieerde bewaarperiode.

## <a name="electronic-discovery-e-discovery"></a>Elektronische ontdekking (e-discovery)
Azure-klanten zijn verantwoordelijk voor het voldoen aan de vereisten voor e-detectie bij hun gebruik van Azure-services. Als Azure-klanten hun klantgegevens moeten bewaren, kunnen ze de gegevens lokaal exporteren en opslaan. Daarnaast kunnen klanten de uitvoer van hun gegevens aanvragen bij de azure customer support-afdeling. Azure stelt klanten niet alleen in staat om hun gegevens te exporteren, maar voert ook uitgebreide logboekregistratie en monitoring intern uit.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
