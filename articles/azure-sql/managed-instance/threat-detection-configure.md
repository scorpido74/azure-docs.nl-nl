---
title: Advanced Threat Protection configureren
titleSuffix: Azure SQL Managed Instance
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen aan de data base in Azure SQL Managed instance aanduiden.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 5cc5adf54b522f9209b386fa1fbb457ef6a3e8ff
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84322342"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Geavanceerde bedreigingen beveiliging configureren in Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md) voor een [Azure SQL Managed instance](sql-managed-instance-paas-overview.md) detecteert afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren Geavanceerde beveiliging tegen bedreigingen kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing**en **SQL-referenties** voor de beveiligings aanval: Bekijk meer informatie in de [waarschuwingen voor geavanceerde bedreigingen](../database/threat-detection-overview.md#alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) of [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](../database/threat-detection-overview.md) maakt deel uit van de [geavanceerde gegevens beveiliging](../database/advanced-data-security.md) , een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Advanced Threat Protection kan worden geopend en beheerd via de centrale SQL ADS-portal.

##  <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
2. Ga naar de configuratie pagina van het exemplaar van het SQL-beheerde exemplaar dat u wilt beveiligen. Selecteer op de pagina **instellingen** de optie **geavanceerde gegevens beveiliging**.
3. Op de pagina Geavanceerde gegevens beveiliging configuratie
   - Geavanceerde **gegevens** beveiliging inschakelen.
   - De **lijst met e-mail** berichten configureren voor het ontvangen van beveiligings waarschuwingen bij het detecteren van afwijkende database activiteiten.
   - Selecteer het **Azure-opslag account** waarin records voor afwijkende bedreigings controle worden opgeslagen.
   - Selecteer de **Geavanceerde beveiligings typen voor bedreigingen** die u wilt configureren. Meer informatie over [Geavanceerde beveiligings waarschuwingen voor bedreigingen](../database/threat-detection-overview.md).
4. Klik op **Opslaan** om het nieuwe of bijgewerkte geavanceerde gegevens beveiligings beleid op te slaan.

   ![Advanced Threat Protection](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](../database/threat-detection-overview.md).
- Zie [Wat is een Azure SQL Managed instance](sql-managed-instance-paas-overview.md)(Engelstalig) voor meer informatie over beheerde exemplaren.
- Meer informatie over [geavanceerde beveiliging tegen bedreigingen voor Azure SQL database](../database/threat-detection-configure.md).
- Meer informatie over [SQL Managed instance auditing](https://go.microsoft.com/fwlink/?linkid=869430).
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
