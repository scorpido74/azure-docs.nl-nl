---
title: Geavanceerde beveiligings concepten voor bedreigingen-Azure Database for PostgreSQL-één server
description: Meer informatie over het gebruik van Advanced Threat Protection voor het detecteren van afwijkende database activiteiten die duiden op mogelijke beveiligings Risico's voor de data base.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 61c0e666458018d4e03d5560e417d28d0efadb27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950082"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Geavanceerde beveiliging tegen bedreigingen in Azure Database for PostgreSQL-één server

Advanced Threat Protection for Azure Database for PostgreSQL detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot databases of om deze aan te vallen.

> [!NOTE]
> Advanced Threat Protection bevindt zich in de open bare preview.

Bedreigings beveiliging maakt deel uit van het aanbod voor Advanced Threat Protection (ATP). Dit is een uniform pakket voor geavanceerde beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de [Azure Portal](https://portal.azure.com) of via [rest API](/rest/api/postgresql/serversecurityalertpolicies). De functie is beschikbaar voor servers met Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure Government-en soevereine Cloud regio's: US Gov-Texas, US Gov-Arizona, US Gov-Iowa, US, gov Virginia, US DoD-oost, US DoD-centraal, Duitsland-centraal, Duitsland-noord, China-oost, China-oost 2. Ga naar beschik [bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor de beschik baarheid van algemene producten.

## <a name="what-is-advanced-threat-protection"></a>Wat is Advanced Threat Protection?

Advanced Threat Protection voor Azure Database for PostgreSQL biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te bieden over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten en mogelijke beveiligings problemen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection voor Azure Database for PostgreSQL integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging. Geavanceerde beveiliging tegen bedreigingen voor Azure Database for PostgreSQL maakt het eenvoudig om mogelijke dreigingen naar de data base te verhelpen zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren. 

![Concept geavanceerde beveiliging tegen bedreigingen](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection-waarschuwingen 
Advanced Threat Protection voor Azure Database for PostgreSQL detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases en de volgende waarschuwingen kunnen activeren:
- **Toegang vanaf ongebruikelijke locatie**: Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure Database for PostgreSQL-server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de Azure Database for PostgreSQL-server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang vanaf ongebruikelijk Azure Data Center**: Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure Database for PostgreSQL-server, waarbij iemand zich heeft aangemeld bij de server vanaf een ongebruikelijk Azure Data Center dat tijdens de laatste periode is gedetecteerd op deze server. In sommige gevallen detecteert de waarschuwing een legitieme actie (uw nieuwe toepassing in azure, Power BI, Azure Database for PostgreSQL Query-Editor). In andere gevallen detecteert de waarschuwing een schadelijke actie vanuit een Azure resource/service (voormalig werknemer, externe aanvaller).
- **Toegang vanaf onbekende Principal**: Deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor de Azure Database for PostgreSQL-server, waarbij iemand zich heeft aangemeld bij de server met behulp van een ongewone principal (Azure Database for PostgreSQL gebruiker). In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang via een mogelijk schadelijke toepassing**: Deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de data base. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval met behulp van gebruikelijk aanvalsprogramma’s.
- **Azure database for PostgreSQL referenties voor brute kracht**: Deze waarschuwing wordt geactiveerd wanneer er sprake is van een abnormaal groot aantal mislukte aanmeldingen met andere referenties. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een Brute Force-aanval.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Zie de [pagina met prijzen voor Azure database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie over prijzen. 
* [Azure database for PostgreSQL Advanced Threat Protection](howto-database-threat-protection-portal.md) configureren met behulp van de Azure Portal  
