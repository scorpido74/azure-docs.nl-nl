---
title: Geavanceerde bedreigingsbeveiliging - Azure-database voor MariaDB
description: Advanced Threat Protection detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen voor de database aangeven.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532175"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure-database voor MariaDB Advanced Threat Protection

Geavanceerde bedreigingsbeveiliging voor Azure Database voor MariaDB detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om databases te openen of te exploiteren.

> [!IMPORTANT]
> Advanced Threat Protection is in openbare preview.

Advanced Threat Protection maakt deel uit van het Advanced Data Security-aanbod, een uniform pakket voor geavanceerde beveiligingsmogelijkheden. Geavanceerde bedreigingsbeveiliging kan worden geopend en beheerd via de [Azure-portal.](https://portal.azure.com) De functie is beschikbaar voor servers voor algemeen gebruik en geheugengeoptimaliseerd.

> [!NOTE]
> De functie Advanced Threat Protection is **niet** beschikbaar in de volgende Azure-overheids- en soevereine cloudregio's: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Ga naar [producten die per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/) voor algemene beschikbaarheid van producten.


## <a name="what-is-advanced-threat-protection"></a>Wat is Advanced Threat Protection?

Advanced Threat Protection for Azure Database for MariaDB biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop kunnen reageren wanneer deze zich voordoen door beveiligingswaarschuwingen te verstrekken voor afwijkende activiteiten. Gebruikers ontvangen een waarschuwing over verdachte databaseactiviteiten en mogelijke kwetsbaarheden, evenals afwijkende databasetoegang en querypatronen. Advanced Threat Protection for Azure Database for MariaDB integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), dat details van verdachte activiteiten bevat en adviseert actie om de dreiging te onderzoeken en te beperken. Geavanceerde bedreigingsbeveiliging voor Azure Database voor MariaDB maakt het eenvoudig om potentiële bedreigingen voor de database aan te pakken zonder dat u beveiligingsexpert hoeft te zijn of geavanceerde beveiligingsbewakingssystemen hoeft te beheren. 

![Geavanceerd concept voor bedreigingsbescherming](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Waarschuwingen voor geavanceerde bedreigingsbescherming 
Advanced Threat Protection for Azure Database for MariaDB detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen aangeven om databases te openen of te exploiteren en kan de volgende waarschuwingen activeren:
- **Toegang vanaf ongebruikelijke locatie:** deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon naar de Azure Database voor MariaDB-server, waarbij iemand zich heeft aangemeld bij de Azure Database voor MariaDB-server vanaf een ongebruikelijke geografische locatie. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang vanuit een ongebruikelijk Azure-datacenter:** deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon in de Azure-database voor MariaDB-server, waarbij iemand zich heeft aangemeld bij de server vanuit een ongebruikelijk Azure-datacenter dat in de afgelopen periode op deze server is gezien. In sommige gevallen detecteert de waarschuwing een legitieme actie (uw nieuwe toepassing in Azure, Power BI). In andere gevallen detecteert de waarschuwing een schadelijke actie vanuit een Azure resource/service (voormalig werknemer, externe aanvaller).
- **Toegang vanaf onbekende principal:** deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon naar de Azure Database voor MariaDB-server, waarbij iemand zich heeft aangemeld bij de server met behulp van een ongebruikelijke principal (Azure Database voor MariaDB-gebruiker). In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval met behulp van gebruikelijk aanvalsprogramma’s.
- **Brute force Azure Database voor MariaDB-referenties:** deze waarschuwing wordt geactiveerd wanneer er een abnormaal hoog aantal mislukte aanmeldingen met verschillende referenties is. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een Brute Force-aanval.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Zie de pagina Azure [Database for MariaDB-prijzen voor](https://azure.microsoft.com/pricing/details/mariadb/) meer informatie over prijzen 
* Azure Database configureren [voor MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) met de Azure-portal  
