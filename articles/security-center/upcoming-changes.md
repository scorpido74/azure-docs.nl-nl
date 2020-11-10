---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026714"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Aanbevelingen met betrekking tot het toe te voegen Azure Security Benchmark (preview)

| Aspect | Details |
|---------|---------|
|Aankondigingsdatum | 26 oktober 2020  |
|Datum voor deze wijziging  |  November 2020 |
|Impact     | Mogelijk, meer aanbevelingen om te controleren.<br>Geen directe invloed op beveiligde score - Aanbevelingen van de preview hebben geen invloed op uw beveiligde score.<br>Geen directe gevolgen voor de integriteitsstatus van uw resources - Aanbevelingen van de preview zorgen er niet voor dat een resource als ‘Beschadigd’ wordt weergegeven.|
|  |  |

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

De volgende 18 nieuwe aanbevelingen worden toegevoegd aan Security Center om de dekking van de benchmark te vergroten.

Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

- Azure Backup moet zijn ingeschakeld voor virtuele machines
- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers
- SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL
- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL
- Java moet worden bijgewerkt naar de nieuwste versie van uw API-app
- Java moet worden bijgewerkt naar de nieuwste versie van uw functie-app
- Java moet worden bijgewerkt naar de nieuwste versie van uw web-app
- PHP moet worden bijgewerkt naar de nieuwste versie van uw API-app
- PHP moet worden bijgewerkt naar de nieuwste versie van uw web-app
- Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers
- Het privé-eindpunt moet worden ingeschakeld voor MySQL-servers
- Het privé-eindpunt moet worden ingeschakeld voor PostgreSQL-servers
- Python moet worden bijgewerkt naar de nieuwste versie van uw API-app
- Python moet worden bijgewerkt naar de nieuwste versie van uw functie-app
- Python moet worden bijgewerkt naar de nieuwste versie van uw web-app
- Web-apps moeten een SSL-certificaat aanvragen voor alle inkomende aanvragen

Gerelateerde links:

- [Meer informatie over Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Meer informatie over Azure API-apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Meer informatie over Azure functie-apps](../azure-functions/functions-overview.md)
- [Meer informatie over Azure web-apps](../app-service/overview.md)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.