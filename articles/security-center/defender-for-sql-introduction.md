---
title: 'Azure Defender voor SQL: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301598"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Inleiding tot Azure Defender voor SQL

Azure Defender voor SQL bevat twee Azure Defender-abonnementen die het [gegevens beveiligings pakket](../azure-sql/database/advanced-data-security.md) van Azure Security Center uitbreiden om uw data bases en hun gegevens te beveiligen, waar ze zich ook bevinden. 

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|**Azure Defender voor Azure-SQL database servers** -algemeen beschikbaar (ga)<br>**Azure Defender voor SQL-servers op computers** -preview-versie|
|Koers|De twee plannen die **Azure Defender voor SQL** vormen, worden in rekening gebracht op [de pagina met prijzen](security-center-pricing.md)|
|Beveiligde SQL-versies:|Azure SQL Database <br>Azure SQL Managed Instance<br>Azure Synapse Analytics (voorheen SQL DW)<br>SQL Server (alle ondersteunde versies)|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China gov, andere gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Wat is Azure Defender voor SQL beveiligd?

**Azure Defender voor SQL** bestaat uit twee afzonderlijke Azure Defender-abonnementen:

- **Azure Defender voor azure SQL database-servers** beveiligt het volgende:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender voor SQL-servers op computers (preview)** breidt de beveiligingen voor uw Azure-systeem eigen SQL-servers uit om hybride omgevingen volledig te ondersteunen en SQL-servers (alle ondersteunde versies) te beveiligen die worden gehost in azure, andere Cloud omgevingen en zelfs on-premises machines


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Wat zijn de voor delen van Azure Defender voor SQL?

Deze twee plannen bevatten functionaliteit voor het identificeren en beperken van potentiële database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op bedreigingen voor uw data bases:

- [Beoordeling van beveiligings](../azure-sql/database/sql-vulnerability-assessment.md) problemen: de scan service om mogelijke beveiligings problemen met een Data Base op te sporen, op te sporen en te helpen oplossen. Evaluatie scans bieden een overzicht van de beveiligings status van uw SQL-machines en Details van eventuele beveiligings resultaten.

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) : de detectie service die CONTINU uw SQL-servers bewaakt voor bedreigingen, zoals SQL-injectie, brute-force aanvallen en misbruik van bevoegdheden. Deze service biedt actie gerichte beveiligings waarschuwingen in Azure Security Center met details van de verdachte activiteit, richt lijnen voor het oplossen van problemen met de bedreigingen en opties voor het voortzetten van uw onderzoeken met Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Wat voor soort waarschuwingen biedt Azure Defender voor SQL?

Beveiligings waarschuwingen worden geactiveerd wanneer zich:

- **Potentiële SQL-injectie aanvallen** , met inbegrip van beveiligings problemen die worden gedetecteerd bij het genereren van een mislukte SQL-instructie in de data base
- **Afwijkende database toegang en query patronen** : bijvoorbeeld een abnormaal groot aantal mislukte aanmeldings pogingen met andere referenties (een beveiligings poging voor een brute kracht)
- **Verdachte database activiteit** : bijvoorbeeld een wijziging in de opslag bestemming voor het exporteren van een SQL-import-en-export bewerking

Waarschuwingen bevatten details over het incident waarmee ze zijn geactiveerd, evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen.



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor SQL.

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- [Azure Defender voor SQL-servers op computers inschakelen](defender-for-sql-usage.md)
- [Azure Defender voor SQL database-servers inschakelen](../azure-sql/database/advanced-data-security.md)
- [De lijst met Azure Defender-waarschuwingen voor SQL](alerts-reference.md#alerts-sql-db-and-warehouse)