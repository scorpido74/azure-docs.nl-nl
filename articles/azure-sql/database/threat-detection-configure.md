---
title: Advanced Threat Protection configureren
description: Geavanceerde bedreigings beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen aan de data base in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321541"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Geavanceerde bedreigings beveiliging voor Azure SQL Database configureren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) voor Azure SQL database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. Geavanceerde beveiliging tegen bedreigingen kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing**en **SQL-referenties** voor de beveiligings aanval: Bekijk meer informatie in de [waarschuwingen voor geavanceerde bedreigingen](threat-detection-overview.md#alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](threat-detection-overview.md#explore-detection-of-a-suspicious-event) of [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) maakt deel uit van de [geavanceerde gegevens beveiliging](advanced-data-security.md) , een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale SQL Advanced Data Security-Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde beveiliging tegen bedreigingen instellen in de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar de configuratie pagina van de server die u wilt beveiligen. Selecteer **geavanceerde gegevens beveiliging**in de beveiligings instellingen.
3. Op de pagina Geavanceerde configuratie van **gegevens beveiliging** :

   - Geavanceerde gegevens beveiliging op de server inschakelen.
   - Geef in het tekstvak **waarschuwingen verzenden naar** een lijst met e-mail berichten op voor het ontvangen van beveiligings waarschuwingen bij de detectie van afwijkende database activiteiten in de **instellingen voor geavanceerde beveiliging tegen bedreigingen**.
  
   ![Geavanceerde bedreigings beveiliging instellen](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Advanced Threat Protection instellen met behulp van PowerShell

Zie voor een script voorbeeld [controle configureren en geavanceerde bedreigingen beveiliging met behulp van Power shell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](threat-detection-overview.md).
- Meer informatie over [Advanced Threat Protection in SQL Managed instance](../managed-instance/threat-detection-configure.md).  
- Meer informatie over [geavanceerde gegevens beveiliging](advanced-data-security.md).
- Meer informatie over [auditing](../../azure-sql/database/auditing-overview.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen.  
