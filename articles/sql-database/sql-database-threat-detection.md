---
title: Advanced Threat Protection configureren
description: Advanced Threat Protection detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen voor de database aangeven in één database of elastische groep.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822510"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Geavanceerde bedreigingsbeveiliging van Azure SQL Database voor afzonderlijke of samengevoegde databases

[Geavanceerde bedreigingsbeveiliging](sql-database-threat-detection-overview.md) voor afzonderlijke en gepoolde databases detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om toegang te krijgen tot databases of deze te exploiteren. Advanced Threat Protection kan **potentiële SQL-injectie**, **Toegang vanaf ongebruikelijke locatie of datacenter**identificeren, Toegang vanaf onbekende **hoofd- of potentieel schadelijke toepassing**en Brute force **SQL-referenties** - zie meer details in [Advanced Threat Protection-waarschuwingen](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

U meldingen ontvangen over de gedetecteerde bedreigingen via [e-mailmeldingen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) of [Azure-portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) maakt deel uit van het [advanced data security](sql-database-advanced-data-security.md) (ADS) aanbod, dat is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Advanced Threat Protection kan worden geopend en beheerd via de centrale SQL ADS-portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde bedreigingsbeveiliging instellen in de Azure-portal

1. Start de Azure-portal op [https://portal.azure.com](https://portal.azure.com).
2. Navigeer naar de configuratiepagina van de Azure SQL Database-server die u wilt beveiligen. Selecteer **geavanceerde gegevensbeveiliging**in de beveiligingsinstellingen .
3. Ga als een op de configuratiepagina **voor geavanceerde gegevensbeveiliging:**

   - Geavanceerde gegevensbeveiliging op de server inschakelen.
   - Geef in **Geavanceerde instellingen voor bedreigingsbeveiliging**in het vak **Waarschuwingen verzenden naar** tekst de lijst met e-mails om beveiligingswaarschuwingen te ontvangen bij detectie van afwijkende databaseactiviteiten.
  
   ![Geavanceerde bescherming voor bedreigingen instellen](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Prijzen in screenshots weerspiegelt niet altijd de huidige prijs, en zijn een voorbeeld.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Advanced Threat Protection instellen met behulp van PowerShell

Zie Controle en [geavanceerde bedreigingsbeveiliging configureren met PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)voor een voorbeeld van een script.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Geavanceerde bedreigingsbeveiliging](sql-database-threat-detection-overview.md).
- Meer informatie over [Geavanceerde bedreigingsbeveiliging in beheerde instantie](sql-database-managed-instance-threat-detection.md).  
- Meer informatie over [geavanceerde gegevensbeveiliging](sql-database-advanced-data-security.md).
- Meer informatie over [auditing](sql-database-auditing.md)
- Meer informatie over [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Zie de [prijspagina van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen  
