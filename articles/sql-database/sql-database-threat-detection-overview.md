---
title: Advanced Threat Protection
description: Advanced Threat Protection detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen in Azure SQL Database aangeven.
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
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124758"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection voor Azure Database

Geavanceerde bedreigingsbeveiliging voor [Azure SQL Database](sql-database-technical-overview.md) en Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om databases te openen of te exploiteren.

Advanced Threat Protection maakt deel uit van het Advanced [Data Security](sql-database-advanced-data-security.md) (ADS) aanbod, dat is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Advanced Threat Protection kan worden geopend en beheerd via de centrale SQL ADS-portal.

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL-server en op zowel SQL Database als Azure Synapse die zijn gemaakt op de Azure SQL-server. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse.

## <a name="what-is-advanced-threat-protection"></a>Wat is Geavanceerde bedreigingsbescherming

 Advanced Threat Protection biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop kunnen reageren wanneer ze zich voordoen door beveiligingswaarschuwingen te geven over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing over verdachte databaseactiviteiten, potentiële kwetsbaarheden en SQL-injectieaanvallen, evenals afwijkende databasetoegang en querypatronen. Advanced Threat Protection integreert waarschuwingen met [Azure Security Center,](https://azure.microsoft.com/services/security-center/)die details van verdachte activiteiten bevatten en aanbevelen actie te ondernemen om de dreiging te onderzoeken en te beperken. Advanced Threat Protection maakt het eenvoudig om potentiële bedreigingen voor de database aan te pakken zonder dat u beveiligingsexpert hoeft te zijn of geavanceerde beveiligingsbewakingssystemen hoeft te beheren.

Voor een volledige onderzoekservaring wordt aanbevolen [SQL Database Auditing](sql-database-auditing.md)in te schakelen, waarbij databasegebeurtenissen worden geschreven naar een controlelogboek in uw Azure-opslagaccount.  

## <a name="advanced-threat-protection-alerts"></a>Waarschuwingen voor geavanceerde bedreigingsbescherming

Advanced Threat Protection for Azure SQL Database detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen aangeven om databases te openen of te exploiteren en het kan de volgende waarschuwingen activeren:

- **Kwetsbaarheid voor SQL-injectie:** deze waarschuwing wordt geactiveerd wanneer een toepassing een defecte SQL-instructie in de database genereert. Deze waarschuwing kan duiden op een mogelijk beveiligingsprobleem met SQL-injectieaanvallen. Er zijn twee mogelijke redenen voor het genereren van een foutieve instructie:

  - Een fout in de toepassingscode die de foutieve SQL-instructie maakt
  - Toepassingscode of opgeslagen procedures schonen gebruikersinvoer niet op tijdens het construeren van de foutieve SQL-instructie, dit kan worden misbruikt voor SQL-injectie
- **Mogelijke SQL-injectie**: deze waarschuwing wordt geactiveerd wanneer een actieve aanval wordt uitgevoerd tegen een geïdentificeerd beveiligingslek voor SQL-injectie in een toepassing. Dit betekent dat de aanvaller schadelijke SQL-instructies probeert te injecteren met de kwetsbare toepassingscode of opgeslagen procedures.
- **Toegang vanaf ongebruikelijke locatie**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon tot de SQL-server, waarbij iemand zich vanuit een ongebruikelijke geografische locatie heeft aangemeld bij de SQL server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang vanaf ongebruikelijk Azure-datacentrum**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon tot de SQL-server, waarbij iemand zich heeft aangemeld bij de SQL server vanuit een Azure-datacentrum dat ongebruikelijk was op deze server in de recente periode. In sommige gevallen detecteert de waarschuwing een legitieme actie (uw nieuwe toepassing in Azure, Power BI, Azure SQL Query-Editor). In andere gevallen detecteert de waarschuwing een schadelijke actie vanuit een Azure resource/service (voormalig werknemer, externe aanvaller).
- **Toegang vanaf ongebruikelijke klant**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangspatroon tot de SQL-server, waarbij iemand zich vanuit een ongebruikelijke klant (SQL-gebruiker) heeft aangemeld bij de SQL-server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (voormalig werknemer, externe aanvaller).
- **Toegang tot een toepassing die mogelijk schadelijk is**: deze waarschuwing wordt geactiveerd wanneer een mogelijk schadelijke toepassing wordt gebruikt voor toegang tot de database. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval met behulp van gebruikelijk aanvalsprogramma’s.
- **Brute Force SQL-referenties**: deze waarschuwing wordt geactiveerd wanneer er een abnormaal groot aantal mislukte aanmeldingen met andere referenties is. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een Brute Force-aanval.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Ontdek afwijkende databaseactiviteiten bij detectie van een verdachte gebeurtenis

U ontvangt een e-mailmelding bij detectie van afwijkende databaseactiviteiten. De e-mail bevat informatie over de verdachte beveiligingsgebeurtenis, inclusief de aard van de afwijkende activiteiten, de naam van de database, de naam van de server, de naam van de toepassing en de gebeurtenistijd. Bovendien biedt de e-mail informatie over mogelijke oorzaken en aanbevolen acties om de potentiële bedreiging voor de database te onderzoeken en te beperken.

![Afwijkend activiteitenrapport](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klik op de koppeling **Recente SQL-waarschuwingen weergeven** in de e-mail om de Azure-portal te starten en de pagina Azure Security Center-waarschuwingen weer te geven, die een overzicht biedt van actieve bedreigingen die zijn gedetecteerd in de SQL-database.

   ![Activiteitsbedreigingen](./media/sql-database-threat-detection/active_threats.png)

2. Klik op een specifieke waarschuwing om aanvullende details en acties te krijgen voor het onderzoeken van deze bedreiging en het herstellen van toekomstige bedreigingen.

   SQL-injectie is bijvoorbeeld een van de meest voorkomende beveiligingsproblemen voor webtoepassingen op internet die worden gebruikt om gegevensgestuurde toepassingen aan te vallen. Aanvallers maken gebruik van kwetsbaarheden in toepassingen om kwaadaardige SQL-instructies te injecteren in toepassingsinvoervelden, gegevens in de database te schenden of te wijzigen. Voor SQL Injection-waarschuwingen bevatten de details van de waarschuwing de kwetsbare SQL-instructie die is misbruikt.

   ![Specifieke waarschuwing](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Advanced Threat Protection-waarschuwingen voor uw database verkennen in de Azure-portal

Advanced Threat Protection integreert de waarschuwingen met [Azure security center.](https://azure.microsoft.com/services/security-center/) Live SQL Advanced Threat Protection-tegels in de database en SQL ADS-blades in de Azure-portal volgen de status van actieve bedreigingen.

Klik **op De waarschuwing voor Geavanceerde bedreigingsbeveiliging** om de pagina Waarschuwingen van azure security center te starten en een overzicht te krijgen van actieve SQL-bedreigingen die in de database worden gedetecteerd.

   ![Waarschuwing voor geavanceerde bedreigingsbeveiliging](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Waarschuwing voor geavanceerde bedreigingsbeveiliging2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde bedreigingsbeveiliging in afzonderlijke en gepoolde databases](sql-database-threat-detection.md).
- Meer informatie over [Geavanceerde bedreigingsbeveiliging in beheerde instantie](sql-database-managed-instance-threat-detection.md).
- Meer informatie over [geavanceerde gegevensbeveiliging](sql-database-advanced-data-security.md).
- Meer informatie over [Azure SQL Database auditing](sql-database-auditing.md)
- Meer informatie over [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Zie de [prijspagina van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen  
