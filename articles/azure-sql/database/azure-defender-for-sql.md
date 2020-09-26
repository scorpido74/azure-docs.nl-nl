---
title: Azure Defender voor SQL
description: Meer informatie over de functionaliteit voor het beheren van uw database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base in Azure SQL Database, Azure SQL Managed instance of Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d567876e0210c025fa34c5b82791eafe4cdff561
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372150"
---
# <a name="azure-defender-for-sql"></a>Azure Defender voor SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender voor SQL is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Azure Defender is beschikbaar voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het zichtbaar maken en inperken van potentiële beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="overview"></a>Overzicht

Azure Defender biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder evaluatie van SQL-beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.
- De [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service die u kunt gebruiken om potentiële database problemen op te lossen, op te sporen en te verhelpen. Het biedt inzicht in uw beveiligings status en bevat stappen die kunnen worden uitgevoerd om beveiligings problemen op te lossen en uw data base-Fortifications te verbeteren.
- [Advanced Threat Protection](threat-detection-overview.md) detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op uw databases. Het controleert uw data base voortdurend op verdachte activiteiten en biedt onmiddellijke beveiligings waarschuwingen voor mogelijke beveiligings problemen, Azure SQL-injectie aanvallen en afwijkende database toegangs patronen. Meldingen van Advanced Threat Protection bevatten detailinformatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

Schakel Azure Defender voor SQL één keer in om al deze opgenomen functies in te scha kelen. Met één klik kunt u Azure Defender inschakelen voor alle data bases op uw [Server](logical-servers.md) in azure of in uw door SQL beheerde exemplaar. Het inschakelen of beheren van Azure Defender-instellingen vereist deel uitmaken van de rol [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) of een van de data base-of Server beheerders rollen.

Zie de [pagina met prijzen voor Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie over de prijzen van Azure Defender voor SQL.

## <a name="getting-started-with-azure-defender"></a>Aan de slag met Azure Defender

Aan de hand van de volgende stappen kunt u aan de slag met Azure Defender.

## <a name="enable-azure-defender"></a>Azure Defender inschakelen

Azure Defender kan worden geopend via de [Azure Portal](https://portal.azure.com). Schakel Azure Defender in door naar **Security Center** te navigeren in de kop **Security** voor uw server of het beheerde exemplaar.

> [!NOTE]
> Er wordt automatisch een opslag account gemaakt en geconfigureerd voor het opslaan van de scan resultaten voor de **evaluatie van beveiligings problemen** . Als u Azure Defender al hebt ingeschakeld voor een andere server in dezelfde resource groep en regio, wordt het bestaande opslag account gebruikt.
>
> De kosten van Azure Defender zijn afgestemd op Azure Security Center prijs van de Standard-laag per knoop punt, waarbij een knoop punt de volledige server of het hele beheerde exemplaar is. U betaalt dus slechts één keer voor het beveiligen van alle data bases op de server of het beheerde exemplaar met Azure Defender. U kunt Azure Defender in eerste instantie uitproberen met een gratis proef versie.

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Het bijhouden van beveiligings problemen en het onderzoeken van bedreigings waarschuwingen

Klik op de kaart met de **evaluatie van beveiligings** problemen voor het weer geven en beheren van beveiligings problemen en rapporten en voor het bijhouden van uw beveiligings stature. Als er beveiligings waarschuwingen zijn ontvangen, klikt u op de **Advanced Threat Protection** -kaart om de details van de waarschuwingen weer te geven en een geconsolideerd rapport te bekijken over alle waarschuwingen in uw Azure-abonnement via de pagina Azure Security Center Security Alerts.

## <a name="manage-azure-defender-settings"></a>Azure Defender-instellingen beheren

Als u Azure Defender-instellingen wilt weer geven en beheren, gaat u naar **Security Center** onder de kop **Security** voor uw server of het beheerde exemplaar. Op deze pagina kunt u Azure Defender in-of uitschakelen en de evaluatie van beveiligings problemen en geavanceerde instellingen voor bedreigingen wijzigen voor uw hele server of een beheerd exemplaar.

## <a name="manage-azure-defender-settings-for-a-database"></a>Azure Defender-instellingen voor een Data Base beheren

Als u de Azure Defender-instellingen voor een bepaalde Data Base wilt onderdrukken, schakelt u het selectie vakje **Azure Defender voor SQL inschakelen op database niveau** in. Gebruik deze optie alleen als u een bepaalde vereiste hebt voor het ontvangen van afzonderlijke geavanceerde beveiligings waarschuwingen of evaluatie resultaten van beveiligings problemen voor de afzonderlijke Data Base, in plaats van of naast de waarschuwingen en resultaten voor alle data bases op de server of het beheerde exemplaar.

Zodra het selectie vakje is ingeschakeld, kunt u de relevante instellingen voor deze data base configureren.

Azure Defender voor SQL-instellingen voor uw server of het beheerde exemplaar kan ook worden bereikt via het deel venster Azure Defender data base. Klik op **instellingen** in het hoofd venster van Azure Defender en klik vervolgens op **Azure Defender voor SQL Server-instellingen weer geven**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md)
- Meer informatie over [geavanceerde bedreigingen beveiliging](threat-detection-configure.md)
- Meer informatie over [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
