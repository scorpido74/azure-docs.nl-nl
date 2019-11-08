---
title: Geavanceerde bedreigingen beveiliging configureren
description: Geavanceerde bedreigings beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen voor de data base in één data base of elastische pool aangeven.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822510"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Geavanceerde beveiliging tegen bedreigingen Azure SQL Database voor één of gegroepeerde Data bases

[Geavanceerde bedreigings beveiliging](sql-database-threat-detection-overview.md) voor afzonderlijke en gegroepeerde Data bases detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te gebruiken Geavanceerde bedreigings beveiliging kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing**en **SQL-referenties** voor de beveiligings aanval-Zie meer informatie over de [Geavanceerde beveiligings waarschuwingen voor bedreigingen](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) of [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) maakt deel uit van de aanbieding voor [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md) (ADS), een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale SQL ADS-Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde beveiliging tegen bedreigingen instellen in de Azure Portal

1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Ga naar de configuratie pagina van de Azure SQL Database-Server die u wilt beveiligen. Selecteer **geavanceerde gegevens beveiliging**in de beveiligings instellingen.
3. Op de pagina Geavanceerde configuratie van **gegevens beveiliging** :

   - Geavanceerde gegevens beveiliging op de server inschakelen.
   - Geef in het tekstvak **waarschuwingen verzenden naar** een lijst met e-mail berichten op voor het ontvangen van beveiligings waarschuwingen bij de detectie van afwijkende database activiteiten in de **instellingen voor geavanceerde beveiliging tegen bedreigingen**.
  
   ![Geavanceerde bedreigings beveiliging instellen](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Prijzen in scherm afbeeldingen geven niet altijd de huidige prijs weer en vormen een voor beeld.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Geavanceerde bedreigingen beveiliging instellen met behulp van Power shell

Zie voor een script voorbeeld [controle configureren en geavanceerde bedreigingen beveiliging met behulp van Power shell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](sql-database-threat-detection-overview.md).
- Meer informatie over [geavanceerde bedreigingen beveiliging in het beheerde exemplaar](sql-database-managed-instance-threat-detection.md).  
- Meer informatie over [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md).
- Meer informatie over [auditing](sql-database-auditing.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen.  
