---
title: Geavanceerde beveiliging tegen bedreigingen configureren-beheerd exemplaar
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen aan de data base in een beheerd exemplaar aangeven.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822554"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Geavanceerde beveiliging tegen bedreigingen configureren in Azure SQL Database beheerde instantie

[Geavanceerde bedreigings beveiliging](sql-database-threat-detection-overview.md) voor een [beheerd exemplaar](sql-database-managed-instance-index.yml) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren. Geavanceerde bedreigings beveiliging kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing**en **SQL-referenties** voor de beveiligings aanval-Zie meer informatie over de [Geavanceerde beveiligings waarschuwingen voor bedreigingen](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) of [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) maakt deel uit van de aanbieding voor [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md) (ADS), een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale SQL ADS-Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde beveiliging tegen bedreigingen instellen in de Azure Portal

1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Ga naar de configuratie pagina van het beheerde exemplaar dat u wilt beveiligen. Selecteer op de pagina **instellingen** de optie **geavanceerde gegevens beveiliging**.
3. Op de pagina Geavanceerde gegevens beveiliging configuratie
   - Geavanceerde **gegevens** beveiliging inschakelen.
   - De **lijst met e-mail** berichten configureren voor het ontvangen van beveiligings waarschuwingen bij het detecteren van afwijkende database activiteiten.
   - Selecteer het **Azure-opslag account** waarin records voor afwijkende bedreigings controle worden opgeslagen.
   - Selecteer de **Geavanceerde beveiligings typen voor bedreigingen** die u wilt configureren. Meer informatie over [Geavanceerde beveiligings waarschuwingen voor bedreigingen](sql-database-threat-detection-overview.md).
4. Klik op **Opslaan** om het nieuwe of bijgewerkte geavanceerde gegevens beveiligings beleid op te slaan.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Prijzen in scherm afbeeldingen geven niet altijd de huidige prijs weer en vormen een voor beeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](sql-database-threat-detection-overview.md).
- Zie [Wat is een beheerd exemplaar](sql-database-managed-instance.md)? voor meer informatie over beheerde exemplaren.
- Meer informatie over [geavanceerde beveiliging tegen bedreigingen voor één data base](sql-database-threat-detection.md).
- Meer informatie over [controle van beheerde exemplaren](https://go.microsoft.com/fwlink/?linkid=869430).
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
