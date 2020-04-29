---
title: Advanced Threat Protection-Azure Database for MySQL
description: Meer informatie over de geavanceerde beveiliging tegen bedreigingen, waarmee afwijkende database activiteiten worden gedetecteerd die duiden op mogelijke beveiligings dreigingen voor de data base.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 01ac6ccbc2789d2052bab07e2da51630b6dbf581
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537156"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL Advanced Threat Protection

Advanced Threat Protection for Azure Database for MySQL detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot databases of om deze aan te vallen.

> [!NOTE]
> Advanced Threat Protection bevindt zich in de open bare preview.

Advanced Threat Protection maakt deel uit van de Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com) of via [rest API](/rest/api/mysql/serversecurityalertpolicies). De functie is beschikbaar voor servers met Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US gov-Iowa, VS, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-Oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.


## <a name="what-is-advanced-threat-protection"></a>Wat is Advanced Threat Protection?

Advanced Threat Protection voor Azure Database for MySQL biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te bieden over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten en mogelijke beveiligings problemen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection voor Azure Database for MySQL integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging. Geavanceerde beveiliging tegen bedreigingen voor Azure Database for MySQL maakt het eenvoudig om mogelijke dreigingen naar de data base te verhelpen zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren. 

![Concept geavanceerde beveiliging tegen bedreigingen](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Geavanceerde beveiligings waarschuwingen 
Advanced Threat Protection voor Azure Database for MySQL detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases en de volgende waarschuwingen kunnen activeren:
- **Toegang vanaf ongebruikelijke locatie**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for mysql-server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de Azure database for mysql-server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang vanaf ongebruikelijk Azure Data Center**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for mysql-server, waarbij iemand zich bij de server heeft aangemeld vanuit een ongebruikelijk Azure Data Center dat tijdens de recente periode op deze server is gedetecteerd. In sommige gevallen detecteert de waarschuwing een legitieme actie (uw nieuwe toepassing in azure, Power BI, Azure Database for MySQL Query-Editor). In andere gevallen detecteert de waarschuwing een schadelijke actie vanuit een Azure resource/service (voormalig werknemer, externe aanvaller).
- **Toegang vanaf een niet-vertrouwde Principal**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure database for mysql-server, waarbij iemand zich heeft aangemeld bij de server met behulp van een ongewone principal (Azure database for MySQL gebruiker). In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval met behulp van gebruikelijk aanvalsprogramma’s.
- Beveiligings **verificatie Azure database for MySQL referenties**: deze waarschuwing wordt geactiveerd wanneer er sprake is van een abnormaal groot aantal mislukte aanmeldingen met andere referenties. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een Brute Force-aanval.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Zie de [pagina met prijzen voor Azure database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie over prijzen. 
* [Azure database for MySQL Advanced Threat Protection](howto-database-threat-protection-portal.md) configureren met behulp van de Azure Portal  
