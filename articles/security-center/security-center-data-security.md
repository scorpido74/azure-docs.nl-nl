---
title: Gegevensbeveiliging in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe gegevens worden beheerd en beveiligd in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: dfa3f00e668488574abeb08964909a8972c8913f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772944"
---
# <a name="azure-security-center-data-security"></a>Gegevensbeveiliging in Azure Security Center
Om klanten te helpen bedreigingen te voor komen, te detecteren en erop te reageren, Azure Security Center gegevens verzamelen en verwerken, met inbegrip van configuratie-informatie, meta gegevens, gebeurtenis logboeken en meer. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service.

In dit artikel wordt uitgelegd hoe gegevens worden beheerd en beveiligd in Azure Security Center.

## <a name="data-sources"></a>Gegevensbronnen
Azure Security Center analyseert gegevens uit de volgende bronnen om inzicht in uw beveiligingsstatus te geven, beveiligingsproblemen te identificeren, oplossingen aan te raden en actieve bedreigingen te detecteren:

- Azure Services: gebruikt informatie over de configuratie van de Azure-services die u hebt geïmplementeerd door te communiceren met de resourceprovider van die service.
- Netwerkverkeer: gebruikt steekproefgewijs netwerkverkeermetagegevens uit de infrastructuur van Microsoft, zoals bron-/doel-IP/poort, pakketgrootte en netwerkprotocol.
- Oplossingen van partners: gebruikt beveiligingswaarschuwingen van geïntegreerde partneroplossingen, zoals firewalls en antimalwareoplossingen.
- Uw Virtual Machines en servers: gebruikt configuratie-informatie en informatie over beveiligings gebeurtenissen, zoals Windows-gebeurtenis-en audit logboeken, IIS-logboeken en syslog-berichten van uw virtuele machines. Bovendien kan Azure Security Center wanneer er een waarschuwing wordt gemaakt een momentopname maken van de beïnvloede VM-schijf en machine-artefacten gekoppeld aan de waarschuwing van de VM-schijf, zoals een registerbestand, extraheren voor onderzoeksdoeleinden.


## <a name="data-protection"></a>Gegevensbeveiliging
**Scheiding van gegevens**: gegevens worden op een logische manier apart van elkaar gehouden, in elk onderdeel van de service. Alle gegevens worden gemarkeerd per organisatie. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service.

**Gegevens toegang**: om aanbevelingen voor beveiliging te bieden en mogelijke beveiligings Risico's te onderzoeken, kunnen mede werkers van micro soft toegang hebben tot gegevens die worden verzameld of geanalyseerd door Azure-Services, waaronder het maken van proces gebeurtenissen, moment opnamen van virtuele machines en artefacten, wat per ongeluk klant gegevens of persoonlijke gegevens van uw virtuele machine kan bevatten. We voldoen aan de [voorwaarden voor Microsoft Online Services en de Privacyverklaring](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), die stellen dat Microsoft niet de klantgegevens gebruikt of gegevens ervan afleidt voor reclame- of vergelijkbare commerciële doeleinden. We gebruiken klantgegevens alleen indien nodig om u Azure-services te bieden, met inbegrip van doeleinden die compatibel zijn met het leveren van die services. U behoudt alle rechten op de klantgegevens.

**Gegevensgebruik**: Microsoft gebruikt informatie over patronen en bedreigingen die worden gezien tussen meerdere tenants voor het verbeteren van onze mogelijkheden voor voorkoming en detectie; wij doen dit in overeenstemming met de privacyverplichtingen beschreven in onze [Privacyverklaring](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Gegevenslocatie

**Uw werk ruimte (n)**: er is een werk ruimte opgegeven voor de volgende geografische gebieden en gegevens die zijn verzameld van uw virtuele Azure-machines, met inbegrip van bepaalde typen waarschuwings gegevens, worden opgeslagen in de dichtstbijzijnde werk ruimte.

| Geografisch gebied van virtuele machine                              | Geografisch gebied van werkruimte |
|-------------------------------------|---------------|
| Verenigde Staten, Brazilië, Zuid-Afrika | Verenigde Staten |
| Canada                              | Canada        |
| Europa (exclusief Verenigd Konink rijk)   | Europa        |
| Verenigd Koninkrijk                      | Verenigd Koninkrijk |
| Azië (exclusief India, Japan, Korea, China)   | Azië en Stille Oceaan  |
| Korea                              | Azië en Stille Oceaan  |
| India                               | India         |
| Japan                               | Japan         |
| China                               | China         |
| Australië                           | Australië     |


Momentopnamen van de VM-schijf worden opgeslagen in hetzelfde opslagaccount als de VM-schijf.

Voor virtuele machines en servers die in andere omgevingen worden uitgevoerd, bijvoorbeeld on-premises, kunt u de werkruimte en de regio opgeven waarin verzamelde gegevens worden opgeslagen.

**Azure Security Center-opslag**: informatie over beveiligingswaarschuwingen, met inbegrip van waarschuwingen van partners, wordt regionaal opgeslagen op basis van de locatie van de gerelateerde Azure-resource, terwijl informatie over de status van de beveiliging en aanbevelingen centraal wordt opgeslagen in de Verenigde Staten of in Europa, afhankelijk van de locatie van de klant. Machine-artefacten worden centraal opgeslagen in dezelfde regio als de virtuele machine.

## <a name="managing-data-collection-from-virtual-machines"></a>Gegevensverzameling van virtuele machines beheren

Wanneer u Security Center inschakelt in Azure, wordt gegevensverzameling ingeschakeld voor elk van uw Azure-abonnementen. U kunt gegevensverzameling voor uw abonnementen ook inschakelen in het gedeelte Beveiligingsbeleid van Azure Security Center. Wanneer het verzamelen van gegevens is ingeschakeld, wordt de Log Analytics agent door Azure Security Center ingericht op alle bestaande virtuele machines van Azure en nieuwe die worden gemaakt.
De Log Analytics-agent scant op verschillende aan beveiliging gerelateerde configuraties en legt deze vast in [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) -traceringen (etw). Bovendien worden door het besturingssysteem gebeurtenislogboekgebeurtenissen gegenereerd tijdens het uitvoeren van de machine. Voorbeelden van dergelijke gegevens zijn: besturingssysteemtype en -versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen, aangemelde gebruiker en tenant-ID. De Log Analytics-agent leest vermeldingen in gebeurtenis logboeken en ETW-traceringen en kopieert deze naar uw werk ruimte (n) voor analyse. De Log Analytics agent schakelt ook proces aanmaak gebeurtenissen en controle van de opdracht regel in.

Als u de gratis variant van Azure Security Center gebruikt, kunt u het verzamelen van gegevens van virtuele machines ook uitschakelen in het beveiligingsbeleid. Het verzamelen van gegevens is vereist voor abonnementen uit de prijscategorie Standard. De verzameling van momentopnamen en artefacten voor de VM-schijf is nog steeds ingeschakeld, zelfs als het verzamelen van gegevens is uitgeschakeld.

## <a name="data-consumption"></a>Gegevensverbruik

Klanten kunnen gegevens die verband houden met Security Center gebruiken uit verschillende gegevensstromen, zoals hieronder weergegeven:

* **Azure activity**: alle beveiligings waarschuwingen, goedgekeurd Security Center [just-in-time-](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) aanvragen en alle waarschuwingen die worden gegenereerd door [adaptieve toepassings besturings elementen](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Azure monitor-logboeken**: alle beveiligings waarschuwingen.


> [!NOTE]
> Beveiligingsaanbevelingen kunnen ook worden gebruikt via REST API. Lees [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) (REST API-naslaginformatie voor beveiligingsresourceprovider) voor meer informatie.

## <a name="see-also"></a>Zie ook
In dit document hebt u geleerd hoe gegevens worden beheerd en beveiligd in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Azure Security Center plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md) : informatie over het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) : informatie over het controleren van de status van uw Azure-resources
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md) : meer informatie over het beheren en reageren op beveiligings waarschuwingen
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/) : vind blog berichten over de beveiliging en naleving van Azure
