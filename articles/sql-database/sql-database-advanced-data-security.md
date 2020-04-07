---
title: Advanced Data Security
description: Meer informatie over de functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van uw databasekwetsbaarheden en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging voor uw Azure SQL-database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677457"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Geavanceerde gegevensbeveiliging voor Azure SQL Database

Geavanceerde gegevensbeveiliging is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het zichtbaar maken en inperken van potentiële beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="overview"></a>Overzicht

Advanced data security (ADS) biedt een reeks geavanceerde SQL-beveiligingsmogelijkheden, waaronder gegevensdetectie & classificatie, kwetsbaarheidsbeoordeling en Geavanceerde bedreigingsbeveiliging.

- [Gegevensdetectie & classificatie](sql-database-data-discovery-and-classification.md) biedt mogelijkheden die zijn ingebouwd in Azure SQL Database voor het ontdekken, classificeren, labelen & het rapporteren van de gevoelige gegevens in uw databases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database.
- [Evaluatie van beveiligingsproblemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee u potentiële zwakke plekken in de beveiliging van de database kunt detecteren, volgen en verhelpen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op uw databases. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Meldingen van Advanced Threat Protection bevatten detailinformatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

Schakel SQL ADS één keer in om al deze meegeleverde functies in te schakelen. Met één klik u ADS inschakelen voor alle databases op uw SQL Database-server of beheerde instantie. Voor het inschakelen of beheren van ADS-instellingen moet u behoren tot de [SQL-beveiligingsbeheerrol,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) sql-databasebeheerrol of SQL-serverbeheerrol. 

Ads-prijzen zijn afgestemd op de standaardlaag azure security center, waarbij elke beveiligde SQL Database-server of beheerde instantie als één knooppunt wordt geteld. Nieuw beveiligde bronnen komen in aanmerking voor een gratis proefversie van de standaardlaag van het Beveiligingscentrum. Zie de [prijspagina azure security center voor](https://azure.microsoft.com/pricing/details/security-center/)meer informatie .

## <a name="getting-started-with-ads"></a>Aan de slag met ADS

Met de volgende stappen u aan de slag met ADS.

## <a name="1-enable-ads"></a>1. ADS inschakelen

Schakel ADS in door te navigeren naar **Geavanceerde gegevensbeveiliging** onder de **kop Beveiliging** voor uw SQL Database-server of beheerde instantie. Als u ADS wilt inschakelen voor alle databases op de databaseserver of beheerde instantie, klikt u **op Geavanceerde gegevensbeveiliging inschakelen op de server**.

> [!NOTE]
> Een opslagaccount wordt automatisch gemaakt en geconfigureerd om de scanresultaten van uw **kwetsbaarheidsbeoordeling** op te slaan. Als u ADS al hebt ingeschakeld voor een andere server in dezelfde resourcegroep en -regio, wordt het bestaande opslagaccount gebruikt.

![ADS inschakelen](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> De kosten van ADS zijn afgestemd op de standaardtariefprijzen van azure security center per knooppunt, waarbij een knooppunt de volledige SQL Database-server of beheerde instantie is. U betaalt dus slechts één keer voor het beveiligen van alle databases op de databaseserver of beheerde instantie met ADS. Je ADVERTENTIES in eerste instantie uitproberen met een gratis proefperiode.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Begin met het classificeren van gegevens, het bijhouden van kwetsbaarheden en het onderzoeken van dreigingswaarschuwingen

Klik op de **classificatiekaart gegevensdetectie &** om aanbevolen gevoelige kolommen te bekijken om uw gegevens te classificeren en te classificeren met permanente gevoeligheidslabels. Klik op de **kaart voor kwetsbaarheidsbeoordeling** om kwetsbaarheidsscans en -rapporten te bekijken en te beheren en om uw beveiligingsstatus bij te houden. Als er beveiligingswaarschuwingen zijn ontvangen, klikt u op de kaart **Geavanceerde bedreigingsbeveiliging** om de details van de waarschuwingen weer te geven en een geconsolideerd rapport te bekijken over alle waarschuwingen in uw Azure-abonnement via de pagina beveiligingswaarschuwingen van Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. ADS-instellingen beheren op uw SQL Database-server of beheerde instantie

Als u ADS-instellingen wilt weergeven en beheren, navigeert u naar **Geavanceerde gegevensbeveiliging** onder de kop **Beveiliging** voor uw SQL Database-server of beheerde instantie. Op deze pagina u ADS in- of uitschakelen en instellingen voor kwetsbaarheidsbeoordeling en Geavanceerde bedreigingsbeveiliging wijzigen voor uw hele SQL Database-server of beheerde instantie.

![Serverinstellingen](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. ADS-instellingen voor een SQL-database beheren

Als u ads-instellingen voor een bepaalde database wilt overschrijven, schakelt u het selectievakje **Geavanceerde gegevensbeveiliging inschakelen in het selectievakje databaseniveau** in. Gebruik deze optie alleen als u een bepaalde vereiste hebt om afzonderlijke advanced threat protection-waarschuwingen of resultaten voor de beoordeling van kwetsbaarheden voor de afzonderlijke database te ontvangen, in plaats van of in plaats van de waarschuwingen en resultaten die zijn ontvangen voor alle databases op de databaseserver of beheerde instantie.

Zodra het selectievakje is ingeschakeld, u de relevante instellingen voor deze database configureren.
 
![Database- en geavanceerde instellingen voor bedreigingsbeveiliging](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Geavanceerde instellingen voor gegevensbeveiliging voor uw databaseserver of beheerde instantie kunnen ook worden bereikt vanuit het ads-databasevenster. Klik op **Instellingen** in het hoofddeelvenster van ADS en klik vervolgens op **Instellingen voor geavanceerde gegevensbeveiliging weergeven**. 

![Database-instellingen](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Volgende stappen 

- Meer informatie over [&-classificatie voor gegevensdetectie](sql-database-data-discovery-and-classification.md) 
- Meer informatie over [kwetsbaarheidsbeoordeling](sql-vulnerability-assessment.md) 
- Meer informatie over [Geavanceerde bedreigingsbeveiliging](sql-database-threat-detection.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
