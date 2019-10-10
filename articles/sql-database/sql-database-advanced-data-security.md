---
title: Geavanceerde gegevens beveiliging-Azure SQL Database | Microsoft Docs
description: Meer informatie over de functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van uw database problemen en het opsporen van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw Azure-SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: v-mohabe
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: b8fc82ca8f4b42adf595680541042ef38efe1470
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569536"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Geavanceerde gegevens beveiliging voor Azure SQL Database

Geavanceerde gegevens beveiliging is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het halen en het beperken van potentiële database problemen en de detectie van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="overview"></a>Overzicht

Advanced Data Security (ADS) biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder gegevens detectie & classificatie, evaluatie van beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.

- [Classificatie van gegevens detectie &](sql-database-data-discovery-and-classification.md) (momenteel in Preview) biedt mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen & beveiligen van gevoelige gegevens in uw data bases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database.
- [Evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee u mogelijke beveiligings problemen met een Data Base kunt detecteren, bijhouden en helpen oplossen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Geavanceerde bedreigingen beveiliging](sql-database-threat-detection-overview.md) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw data base of deze te exploiteren. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Geavanceerde beveiligings waarschuwingen bieden Details over de verdachte activiteit en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.

Schakel SQL ADS eenmaal in om al deze opgenomen functies in te scha kelen. Met één klik kunt u ADS inschakelen voor alle data bases op uw SQL Database Server of een beheerd exemplaar. Het inschakelen of beheren van ADS-instellingen vereist deel uitmaakt van de rol [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , SQL database BEHEERDERSROL of SQL Server-beheerdersrol. 

Prijzen voor ADS worden uitgelijnd met Azure Security Center Standard-laag, waarbij elke beveiligde SQL Database Server of een beheerd exemplaar als één knoop punt wordt beschouwd. Nieuwe beveiligde resources komen in aanmerking voor een gratis proef versie van Security Center Standard-laag. Zie de pagina met prijzen voor [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

## <a name="getting-started-with-ads"></a>Aan de slag met ADS

Aan de hand van de volgende stappen kunt u aan de slag met ADS.

## <a name="1-enable-ads"></a>1. ADVERTENTIES inschakelen

Activeer advertenties door te navigeren naar **geavanceerde gegevens beveiliging** in de kop **beveiliging** voor uw SQL database server of beheerd-exemplaar. Als u ADS wilt inschakelen voor alle data bases op de database server of het beheerde exemplaar, klikt u op **geavanceerde gegevens beveiliging inschakelen op de server**.

> [!NOTE]
> Er wordt automatisch een opslag account gemaakt en geconfigureerd voor het opslaan van de scan resultaten voor de **evaluatie van beveiligings problemen** . Als u advertenties al hebt ingeschakeld voor een andere server in dezelfde resource groep en regio, wordt het bestaande opslag account gebruikt.

![ADVERTENTIES inschakelen](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> De kosten voor advertenties zijn afgestemd op Azure Security Center prijs van de Standard-laag per knoop punt, waarbij een knoop punt de volledige SQL Database Server of het beheerde exemplaar is. U betaalt dus slechts één keer voor de beveiliging van alle data bases op de database server of het beheerde exemplaar met ADS. U kunt in eerste instantie een gratis proef versie van advertenties proberen.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Begin met het classificeren van gegevens, het volgen van beveiligings problemen en het onderzoeken van bedreigings waarschuwingen

Klik op de kaart **gegevens detectie & classificatie** voor een overzicht van de aanbevolen gevoelige kolommen voor het classificeren en classificeren van uw gegevens met persistente gevoeligheids labels. Klik op de kaart met de **evaluatie van beveiligings** problemen voor het weer geven en beheren van beveiligings problemen en rapporten en voor het bijhouden van uw beveiligings stature. Als er beveiligings waarschuwingen zijn ontvangen, klikt u op de **Advanced Threat Protection** -kaart om de details van de waarschuwingen weer te geven en een geconsolideerd rapport te bekijken over alle waarschuwingen in uw Azure-abonnement via de pagina Azure Security Center Security Alerts.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. ADS-instellingen op uw SQL Database Server of een beheerd exemplaar beheren

Als u ADS-instellingen wilt weer geven en beheren, gaat u naar **geavanceerde gegevens beveiliging** onder de kop **beveiliging** voor uw SQL database server of een beheerd exemplaar. Op deze pagina kunt u advertenties in-of uitschakelen en de evaluatie van beveiligings problemen en geavanceerde instellingen voor bedreigingen wijzigen voor uw hele SQL Database Server of een beheerd exemplaar.

![Serverinstellingen](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. ADS-instellingen voor een SQL database beheren

Als u ADS-instellingen voor een bepaalde Data Base wilt overschrijven, schakelt u het selectie vakje **geavanceerde gegevens beveiliging inschakelen op database niveau** in. Gebruik deze optie alleen als u een bepaalde vereiste hebt voor het ontvangen van afzonderlijke geavanceerde bedreigingen voor dreigingen of voor de afzonderlijke Data Base, in plaats van of naast de waarschuwingen en resultaten die voor alle data bases zijn ontvangen op de database server of een beheerd exemplaar.

Zodra het selectie vakje is ingeschakeld, kunt u de relevante instellingen voor deze data base configureren.
 
![Instellingen voor data bases en geavanceerde bedreigingen beveiliging](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Geavanceerde instellingen voor gegevens beveiliging voor de database server of het beheerde exemplaar kunnen ook worden bereikt via het deel venster ADS data base. Klik op **instellingen** in het hoofd venster Ads en klik vervolgens op **Geavanceerde instellingen voor gegevens beveiligings server weer geven**. 

![Database-instellingen](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Volgende stappen 

- Meer informatie over de [classificatie van gegevens detectie &](sql-database-data-discovery-and-classification.md) 
- Meer informatie over de [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) 
- Meer informatie over [geavanceerde bedreigingen beveiliging](sql-database-threat-detection.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
