---
title: Advanced Data Security
description: Meer informatie over de functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van uw data base-beveiligings problemen en het opsporen van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base in Azure SQL Database, Azure SQL Managed instance of Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 7efcc4a9ec7da2bdd4005c124d5d0e8dd03c4363
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669695"
---
# <a name="advanced-data-security"></a>Advanced Data Security
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Advanced Data Security (ADS) is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. ADS is beschikbaar voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het zichtbaar maken en inperken van potentiële beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="overview"></a>Overzicht

ADS biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder gegevens detectie & classificatie, evaluatie van SQL-beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.
- [Gegevens detectie & classificatie](data-discovery-and-classification-overview.md) biedt mogelijkheden die zijn ingebouwd in Azure SQL database, Azure SQL Managed instance en Azure Synapse voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw data bases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database.
- De [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service die u kunt gebruiken om potentiële database problemen op te lossen, op te sporen en te verhelpen. Het biedt inzicht in uw beveiligings status en bevat stappen die kunnen worden uitgevoerd om beveiligings problemen op te lossen en uw data base-Fortifications te verbeteren.
- [Advanced Threat Protection](threat-detection-overview.md) detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op uw databases. Het controleert uw data base voortdurend op verdachte activiteiten en biedt onmiddellijke beveiligings waarschuwingen voor mogelijke beveiligings problemen, Azure SQL-injectie aanvallen en afwijkende database toegangs patronen. Meldingen van Advanced Threat Protection bevatten detailinformatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

Geavanceerde gegevens beveiliging één keer inschakelen om al deze opgenomen functies in te scha kelen. Met één klik kunt u ADS inschakelen voor alle data bases op uw [Server](logical-servers.md) in azure of in uw door SQL beheerde exemplaar. Het inschakelen of beheren van ADS-instellingen vereist deel uitmaakt van de rol [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) of een van de data base-of Server beheerders rollen.

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
