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
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 30f4358bea2670cfa8d825bf1ef75feeba7ea851
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202338"
---
# <a name="azure-security-center-data-security"></a>Gegevensbeveiliging in Azure Security Center
Om klanten te helpen bedreigingen te voorkomen, te detecteren en erop te reageren, verzamelt en verwerkt Azure Security Center gegevens over beveiliging, zoals configuratie-informatie, metagegevens, gebeurtenislogboeken, crashdumpbestanden en nog veel meer. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service.

In dit artikel wordt uitgelegd hoe gegevens worden beheerd en beveiligd in Azure Security Center.

## <a name="data-sources"></a>Gegevensbronnen
Azure Security Center analyseert gegevens uit de volgende bronnen om inzicht in uw beveiligingsstatus te geven, beveiligingsproblemen te identificeren, oplossingen aan te raden en actieve bedreigingen te detecteren:

- Azure-services: Maakt gebruik van informatie over de configuratie van Azure-Services die u hebt geïmplementeerd door te communiceren met de resource provider van die service.
- Netwerk verkeer: Maakt gebruik van voor beelden van meta gegevens van het netwerk verkeer van de infra structuur van micro soft, zoals bron/doel-IP/-poort, pakket grootte en netwerk protocol.
- Partner oplossingen: Maakt gebruik van beveiligings waarschuwingen van geïntegreerde partner oplossingen, zoals firewalls en antimalware-oplossingen.
- Uw Virtual Machines en servers: Maakt gebruik van configuratie-informatie en informatie over beveiligings gebeurtenissen, zoals Windows-gebeurtenis-en audit logboeken, IIS-logboeken, syslog-berichten en crash dump bestanden van uw virtuele machines. Bovendien kan Azure Security Center wanneer er een waarschuwing wordt gemaakt een momentopname maken van de beïnvloede VM-schijf en machine-artefacten gekoppeld aan de waarschuwing van de VM-schijf, zoals een registerbestand, extraheren voor onderzoeksdoeleinden.


## <a name="data-protection"></a>Gegevensbescherming
**Gegevens**scheiding: De gegevens worden in de gehele service logisch gescheiden gehouden van elk onderdeel. Alle gegevens worden gemarkeerd per organisatie. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service.

**Gegevenstoegang**: Om aanbevelingen voor beveiliging te bieden en mogelijke beveiligings Risico's te onderzoeken, kunnen mede werkers van micro soft toegang hebben tot gegevens die worden verzameld of geanalyseerd door Azure-Services, waaronder crash dump bestanden, gebeurtenissen voor het maken van processen, moment opnamen van virtuele schijven en artefacten, Dit kan onbedoeld klant gegevens of persoonlijke gegevens van uw virtuele machines bevatten. We voldoen aan de [voorwaarden voor Microsoft Online Services en de Privacyverklaring](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), die stellen dat Microsoft niet de klantgegevens gebruikt of gegevens ervan afleidt voor reclame- of vergelijkbare commerciële doeleinden. We gebruiken klantgegevens alleen indien nodig om u Azure-services te bieden, met inbegrip van doeleinden die compatibel zijn met het leveren van die services. U behoudt alle rechten op de klantgegevens.

**Gebruik van gegevens**: Micro soft gebruikt patronen en bedreigings informatie over meerdere tenants om onze preventie-en detectie mogelijkheden te verbeteren. We doen dit in overeenstemming met de privacy-verplichtingen die zijn beschreven in de [privacyverklaring](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Locatie van gegevens

**Uw werk ruimte (n)** : Een werk ruimte is opgegeven voor de volgende geografische gebieden en gegevens die zijn verzameld van uw virtuele Azure-machines, met inbegrip van crash dumps en bepaalde typen waarschuwings gegevens, worden opgeslagen in de dichtstbijzijnde werk ruimte.

| Geografisch gebied van virtuele machine                              | Geografisch gebied van werkruimte |
|-------------------------------------|---------------|
| Verenigde Staten, Brazilië, Zuid-Afrika | Verenigde Staten |
| Canada                              | Canada        |
| Europa (exclusief Verenigd Konink rijk)   | Europa        |
| Verenigd Koninkrijk                      | Verenigd Koninkrijk |
| Azië (exclusief India, Japan, Korea, China)   | Azië en Stille Oceaan  |
| Zuid-Korea                              | Azië en Stille Oceaan  |
| India                               | India         |
| Japan                               | Japan         |
| China                               | China         |
| Australië                           | Australië     |


Momentopnamen van de VM-schijf worden opgeslagen in hetzelfde opslagaccount als de VM-schijf.

Voor virtuele machines en servers die in andere omgevingen worden uitgevoerd, bijvoorbeeld on-premises, kunt u de werkruimte en de regio opgeven waarin verzamelde gegevens worden opgeslagen.

**Opslag Azure Security Center**: Informatie over beveiligings waarschuwingen, met inbegrip van partner waarschuwingen, wordt regionaal opgeslagen op basis van de locatie van de gerelateerde Azure-resource, terwijl informatie over de status van de beveiligings status en aanbeveling centraal wordt opgeslagen in de Verenigde Staten of Europa op basis van de locatie van de klant.
Azure Security Center verzamelt tijdelijke kopieën van uw crashdumpbestanden en analyseert deze op bewijs van pogingen tot misbruik en geslaagde aanvallen. Azure Security Center voert deze analyse uit binnen hetzelfde geografische gebied als de werkruimte en verwijdert de tijdelijke kopieën wanneer de analyse is voltooid.

Machine-artefacten worden centraal opgeslagen in dezelfde regio als de virtuele machine.


## <a name="managing-data-collection-from-virtual-machines"></a>Gegevensverzameling van virtuele machines beheren

Wanneer u Security Center inschakelt in Azure, wordt gegevensverzameling ingeschakeld voor elk van uw Azure-abonnementen. U kunt gegevensverzameling voor uw abonnementen ook inschakelen in het gedeelte Beveiligingsbeleid van Azure Security Center. Wanneer gegevensverzameling is ingeschakeld, levert Azure Security Center de Microsoft Monitoring Agent op alle bestaande, ondersteunde virtuele machines in Azure en op nieuwe virtuele machines die worden gemaakt.
De Microsoft Monitoring Agent scant op verschillende aan beveiliging gerelateerde configuraties en legt gebeurtenissen vast in [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)-traceringen (ETW). Bovendien worden door het besturingssysteem gebeurtenislogboekgebeurtenissen gegenereerd tijdens het uitvoeren van de machine. Voorbeelden van dergelijke gegevens zijn: besturingssysteemtype en -versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen, aangemelde gebruiker en tenant-ID. De Microsoft Monitoring Agent leest vermeldingen in gebeurtenislogboeken en ETW-traceringen en kopieert deze voor analyse naar uw werkruimte(n). De Microsoft Monitoring Agent kopieert ook crashdumpbestanden naar uw werkruimte(n) en maakt procesgebeurtenissen en controle van de opdrachtregel mogelijk.

Als u de gratis variant van Azure Security Center gebruikt, kunt u het verzamelen van gegevens van virtuele machines ook uitschakelen in het beveiligingsbeleid. Het verzamelen van gegevens is vereist voor abonnementen uit de prijscategorie Standard. De verzameling van momentopnamen en artefacten voor de VM-schijf is nog steeds ingeschakeld, zelfs als het verzamelen van gegevens is uitgeschakeld.

## <a name="data-consumption"></a>Gegevensverbruik

Klanten kunnen gegevens die verband houden met Security Center gebruiken uit verschillende gegevensstromen, zoals hieronder weergegeven:

* **Azure-activiteit**: alle beveiligingswaarschuwingen, goedgekeurde [Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)-aanvragen van Security Center en alle waarschuwingen die zijn gegenereerd door [besturingselementen voor adaptieve toepassingen](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Azure monitor-logboeken**: alle beveiligings waarschuwingen.


> [!NOTE]
> Beveiligingsaanbevelingen kunnen ook worden gebruikt via REST API. Lees [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) (REST API-naslaginformatie voor beveiligingsresourceprovider) voor meer informatie.

## <a name="see-also"></a>Zie ook
In dit document hebt u geleerd hoe gegevens worden beheerd en beveiligd in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Plannings- en bedieningsgids voor het Azure Beveiligingscentrum](security-center-planning-and-operations-guide.md): leer de ontwerpoverwegingen kennen en plan hiervoor bij de overstap naar Azure Security Center.
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) (Beveiligingsstatus controleren in Azure Security Center): meer informatie over het controleren van de status van uw Azure-resources
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure
