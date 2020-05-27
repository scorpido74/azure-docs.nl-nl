---
title: Advanced Threat Protection
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die duiden op mogelijke beveiligings Risico's in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 0b231adafabff8414b1cf742e1441e938a3fa212
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848191"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection voor Azure Database

Advanced Threat Protection voor [Azure SQL database](sql-database-technical-overview.md) en [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecteert afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Advanced Threat Protection maakt deel uit van de aanbieding voor [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md) (ADS), een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Advanced Threat Protection kan worden geopend en beheerd via de centrale SQL ADS-portal.

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL Server en op zowel SQL Database als Azure Synapse die zijn gemaakt op de Azure SQL-Server. SQL Database wordt gebruikt bij het verwijzen naar SQL Database en Azure Synapse voor eenvoud.

## <a name="what-is-advanced-threat-protection"></a>Wat is Advanced Threat Protection?

 Advanced Threat Protection biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te geven over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten, potentiële kwetsbaar heden en SQL-injectie aanvallen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging. Geavanceerde beveiliging tegen bedreigingen maakt het eenvoudig om mogelijke bedreigingen te verhelpen voor de data base zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren.

Voor een volledige onderzoek is het raadzaam om [SQL database controle](sql-database-auditing.md)in te scha kelen, waarmee database gebeurtenissen worden geschreven naar een audit logboek in uw Azure Storage-account.  

## <a name="advanced-threat-protection-alerts"></a>Geavanceerde beveiligings waarschuwingen

Advanced Threat Protection voor Azure SQL Database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. Voor een lijst met waarschuwingen voor Azure SQL Database raadpleegt u de [waarschuwingen voor SQL database en SQL data warehouse in azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Afwijkende database activiteiten verkennen na detectie van een verdachte gebeurtenis

U ontvangt een e-mail melding wanneer er afwijkende database activiteiten worden gedetecteerd. Het e-mail bericht bevat informatie over de verdachte beveiligings gebeurtenis, inclusief de aard van de afwijkende activiteiten, de database naam, de server naam, de toepassings naam en de tijd van de gebeurtenis. Daarnaast bevat het e-mail bericht informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van de mogelijke bedreiging voor de data base.

![Rapport afwijkende activiteiten](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klik op de koppeling **recente SQL-waarschuwingen weer geven** in het e-mail bericht om de Azure portal te starten en de pagina Azure Security Center waarschuwingen weer te geven. Deze bevat een overzicht van actieve bedreigingen die zijn gedetecteerd op de SQL database.

   ![Bedreigingen voor activiteit](./media/sql-database-threat-detection/active_threats.png)

2. Klik op een specifieke waarschuwing voor aanvullende details en acties voor het onderzoeken van deze dreiging en het oplossen van toekomstige bedreigingen.

   SQL-injectie is bijvoorbeeld een van de meest voorkomende beveiligings problemen voor webtoepassingen op Internet die wordt gebruikt om gegevensgestuurde toepassingen aan te vallen. Aanvallers profiteren van toepassings lekken om schadelijke SQL-instructies in te voeren in velden voor toepassings invoer, de gegevens in de data base te schenden of te wijzigen. Voor SQL-injectie waarschuwingen bevat de details van de waarschuwing de kwets bare SQL-instructie die is misbruikt.

   ![Specifieke waarschuwing](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Geavanceerde beveiligings waarschuwingen voor bedreigingen voor uw data base verkennen in de Azure Portal

Geavanceerde bedreigings beveiliging integreert de waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-tegels in de data base en SQL ADS-blades in de Azure Portal de status van actieve bedreigingen bijhouden.

Klik op **Geavanceerde bedreigings beveiliging waarschuwing** om de pagina Azure Security Center waarschuwingen te starten en een overzicht te krijgen van actieve SQL-bedreigingen die zijn gedetecteerd op de data base.

   ![Waarschuwing geavanceerde bedreigings beveiliging](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde bedreigingen beveiliging in één en gegroepeerde Data bases](sql-database-threat-detection.md).
- Meer informatie over [geavanceerde bedreigingen beveiliging in het beheerde exemplaar](sql-database-managed-instance-threat-detection.md).
- Meer informatie over [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md).
- Meer informatie over het [controleren van Azure SQL database](sql-database-auditing.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen.  
