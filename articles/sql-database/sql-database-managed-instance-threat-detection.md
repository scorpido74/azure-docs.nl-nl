---
title: Geavanceerde bedreigingsbeveiliging configureren - beheerde instantie
description: Advanced Threat Protection detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen voor de database in een beheerde instantie aangeven.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822554"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Geavanceerde bedreigingsbeveiliging configureren in azure SQL-databasebeheer, beheerde instantie

[Geavanceerde bedreigingsbeveiliging](sql-database-threat-detection-overview.md) voor een [beheerde instantie](sql-database-managed-instance-index.yml) detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om toegang te krijgen tot databases of deze te exploiteren. Advanced Threat Protection kan **potentiële SQL-injectie**, **Toegang vanaf ongebruikelijke locatie of datacenter**identificeren, Toegang vanaf onbekende **hoofd- of potentieel schadelijke toepassing**en Brute force **SQL-referenties** - zie meer details in [Advanced Threat Protection-waarschuwingen](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

U meldingen ontvangen over de gedetecteerde bedreigingen via [e-mailmeldingen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) of [Azure-portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) maakt deel uit van het [advanced data security](sql-database-advanced-data-security.md) (ADS) aanbod, dat is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Advanced Threat Protection kan worden geopend en beheerd via de centrale SQL ADS-portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde bedreigingsbeveiliging instellen in de Azure-portal

1. Start de Azure-portal op [https://portal.azure.com](https://portal.azure.com).
2. Navigeer naar de configuratiepagina van het beheerde exemplaar dat u wilt beveiligen. Selecteer **op** de pagina Instellingen de optie **Geavanceerde gegevensbeveiliging**.
3. Op de configuratiepagina geavanceerde gegevensbeveiliging
   - Schakel geavanceerde gegevensbeveiliging **in.**
   - Configureer de **lijst met e-mails** om beveiligingswaarschuwingen te ontvangen bij detectie van afwijkende databaseactiviteiten.
   - Selecteer het **Azure-opslagaccount** waar afwijkende bedreigingscontrolerecords worden opgeslagen.
   - Selecteer de **typen Advanced Threat Protection** die u wilt configureren. Meer informatie over [waarschuwingen voor geavanceerde bedreigingsbeveiliging](sql-database-threat-detection-overview.md).
4. Klik **op Opslaan** om het nieuwe of bijgewerkte geavanceerde gegevensbeveiligingsbeleid op te slaan.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Prijzen in screenshots weerspiegelt niet altijd de huidige prijs, en zijn een voorbeeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Geavanceerde bedreigingsbeveiliging](sql-database-threat-detection-overview.md).
- Meer informatie over beheerde instanties, zie [Wat is een beheerde instantie](sql-database-managed-instance.md).
- Meer informatie over [Geavanceerde bedreigingsbeveiliging voor één database](sql-database-threat-detection.md).
- Meer informatie over [beheerde instantiecontrole](https://go.microsoft.com/fwlink/?linkid=869430).
- Meer informatie over [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
