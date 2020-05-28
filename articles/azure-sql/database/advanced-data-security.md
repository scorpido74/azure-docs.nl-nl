---
title: Advanced Data Security
description: Meer informatie over de functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van uw database problemen en het opsporen van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw Azure SQL Database, Azure SQL Managed instance of Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: ed7d4b10219f4d4a3c437331bd1daf870495949d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047851"
---
# <a name="advanced-data-security"></a>Advanced Data Security
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Advanced Data Security (ADS) is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. ADS is beschikbaar voor voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het zichtbaar maken en inperken van potentiële beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="overview"></a>Overzicht

Advanced Data Security (ADS) biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder gegevens detectie & classificatie, evaluatie van beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.

- [Gegevens detectie & classificatie](data-discovery-and-classification-overview.md) biedt mogelijkheden die zijn ingebouwd in Azure SQL database, Azure SQL Managed instance en Azure Synapse voor het detecteren, classificeren, labelen & rapportage van de gevoelige gegevens in uw data bases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database.
- [Evaluatie van beveiligingsproblemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee u potentiële zwakke plekken in de beveiliging van de database kunt detecteren, volgen en verhelpen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Advanced Threat Protection](threat-detection-overview.md) detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op uw databases. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Meldingen van Advanced Threat Protection bevatten detailinformatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

Schakel SQL ADS eenmaal in om al deze opgenomen functies in te scha kelen. Met één klik kunt u ADS inschakelen voor alle data bases op uw [Server](logical-servers.md) in azure (die als host fungeert voor SQL database of Azure Synapse Analytics) of in een exemplaar van een Azure SQL Managed instance. Het inschakelen of beheren van ADS-instellingen vereist deel uitmaakt van de rol [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , SQL database BEHEERDERSROL of SQL Server-beheerdersrol.

Prijzen voor ADS worden uitgelijnd met Azure Security Center Standard-laag, waarbij elke beveiligde server of een beheerd exemplaar als één knoop punt wordt beschouwd. Nieuwe beveiligde resources komen in aanmerking voor een gratis proef versie van Security Center Standard-laag. Zie de pagina met prijzen voor [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

## <a name="getting-started-with-ads"></a>Aan de slag met ADS

Aan de hand van de volgende stappen kunt u aan de slag met ADS.

## <a name="1-enable-ads"></a>1. advertenties inschakelen

Schakel advertenties in door te navigeren naar **geavanceerde gegevens beveiliging** in de kop **beveiliging** voor uw server of het beheerde exemplaar.

> [!NOTE]
> Er wordt automatisch een opslag account gemaakt en geconfigureerd voor het opslaan van de scan resultaten voor de **evaluatie van beveiligings problemen** . Als u advertenties al hebt ingeschakeld voor een andere server in dezelfde resource groep en regio, wordt het bestaande opslag account gebruikt.

![ADVERTENTIES inschakelen](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> De kosten voor advertenties zijn afgestemd op Azure Security Center prijs van de Standard-laag per knoop punt, waarbij een knoop punt de volledige server of het hele beheerde exemplaar is. U betaalt dus slechts één keer voor het beveiligen van alle data bases op de server of het beheerde exemplaar met ADS. U kunt in eerste instantie een gratis proef versie van advertenties proberen.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. begin met het classificeren van gegevens, het volgen van beveiligings problemen en het onderzoeken van bedreigings waarschuwingen

Klik op de kaart **gegevens detectie & classificatie** voor een overzicht van de aanbevolen gevoelige kolommen voor het classificeren en classificeren van uw gegevens met persistente gevoeligheids labels. Klik op de kaart met de **evaluatie van beveiligings** problemen voor het weer geven en beheren van beveiligings problemen en rapporten en voor het bijhouden van uw beveiligings stature. Als er beveiligings waarschuwingen zijn ontvangen, klikt u op de **Advanced Threat Protection** -kaart om de details van de waarschuwingen weer te geven en een geconsolideerd rapport te bekijken over alle waarschuwingen in uw Azure-abonnement via de pagina Azure Security Center Security Alerts.

## <a name="3-manage-ads-settings"></a>3. ADS-instellingen beheren

Als u ADS-instellingen wilt weer geven en beheren, gaat u naar **geavanceerde gegevens beveiliging** onder de kop **beveiliging** voor uw server of het beheerde exemplaar. Op deze pagina kunt u advertenties in-of uitschakelen en de evaluatie van beveiligings problemen en geavanceerde instellingen voor bedreigingen wijzigen voor uw hele server of een beheerd exemplaar.

![Serverinstellingen](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. ADS-instellingen voor een SQL database beheren

Als u ADS-instellingen voor een bepaalde Data Base wilt overschrijven, schakelt u het selectie vakje **geavanceerde gegevens beveiliging inschakelen op database niveau** in. Gebruik deze optie alleen als u een bepaalde vereiste hebt voor het ontvangen van afzonderlijke geavanceerde beveiligings waarschuwingen of evaluatie resultaten van beveiligings problemen voor de afzonderlijke Data Base, in plaats van of naast de waarschuwingen en resultaten voor alle data bases op de server of het beheerde exemplaar.

Zodra het selectie vakje is ingeschakeld, kunt u de relevante instellingen voor deze data base configureren.

![Instellingen voor data bases en geavanceerde bedreigingen beveiliging](./media/advanced-data-security/database_threat_detection_settings.png)

Geavanceerde instellingen voor gegevens beveiliging voor uw server of een beheerd exemplaar kunnen ook worden bereikt via het deel venster ADS data base. Klik op **instellingen** in het hoofd venster Ads en klik vervolgens op **Geavanceerde instellingen voor gegevens beveiligings server weer geven**.

![Data base-instellingen](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [classificatie van gegevens detectie &](data-discovery-and-classification-overview.md)
- Meer informatie over de [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md)
- Meer informatie over [geavanceerde bedreigingen beveiliging](threat-detection-configure.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
