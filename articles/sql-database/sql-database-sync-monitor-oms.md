---
title: SQL Data Sync controleren met Azure Monitor-logboeken
description: Meer informatie over het bewaken van Azure SQL Data Sync met behulp van Azure Monitor-logboeken
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383691"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>SQL Data Sync controleren met Azure Monitor-logboeken 

Als u het SQL Data Sync-activiteiten logboek wilt controleren en fouten en waarschuwingen wilt detecteren, moet u SQL Data Sync eerder in de Azure Portal controleren of Power shell of de REST API gebruiken. Volg de stappen in dit artikel voor het configureren van een aangepaste oplossing voor het verbeteren van de bewakings ervaring voor gegevens synchronisatie. U kunt deze oplossing aanpassen aan uw scenario.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Bewakings dashboard voor al uw synchronisatie groepen 

Het is niet meer nodig om de logboeken van elke synchronisatie groep afzonderlijk te bekijken om problemen op te sporen. U kunt al uw synchronisatie groepen vanuit uw abonnementen op één locatie bewaken met behulp van een aangepaste Azure Monitor weer gave. In deze weer gave worden de informatie die betrekking heeft op SQL Data Sync klanten, geoppereerd.

![Bewakings dashboard voor gegevens synchronisatie](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatische e-mail meldingen

U hoeft het logboek niet meer hand matig te controleren in de Azure Portal of via Power shell of de REST API. Met [Azure monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)kunt u waarschuwingen maken die rechtstreeks naar de e-mail adressen van de mensen gaan die ze moeten zien wanneer er een fout optreedt.

![E-mail meldingen voor gegevens synchronisatie](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hoe stelt u deze bewakings functies in? 

Voer de volgende stappen uit om in minder dan een uur een bewakings oplossing voor aangepaste Azure Monitor logboeken voor SQL Data Sync te implementeren:

U moet drie onderdelen configureren:

-   Een Power shell-runbook om SQL Data Sync logboek gegevens naar Azure Monitor-logboeken te feeden.

-   Een Azure Monitor waarschuwing voor e-mail meldingen.

-   Een Azure Monitor weergave voor bewaking.

### <a name="samples-to-download"></a>Voor beelden om te downloaden

De volgende twee voor beelden downloaden:

-   [Data Sync-logboek Power shell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Weer gave gegevens synchronisatie Azure Monitor](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende zaken hebt ingesteld:

-   Een Azure Automation-account

-   Log Analytics-werkruimte

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Power shell-Runbook om SQL Data Sync-logboek op te halen 

Gebruik een Power shell-runbook dat wordt gehost in Azure Automation om de SQL Data Sync-logboek gegevens op te halen en te verzenden naar Azure Monitor Logboeken. Er is een voorbeeld script opgenomen. Als vereiste moet u een Azure Automation-account hebben. Vervolgens moet u een runbook maken en dit plannen om uit te voeren. 

### <a name="create-a-runbook"></a>Een runbook maken

Zie [mijn eerste Power shell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)voor meer informatie over het maken van een runbook.

1.  Onder uw Azure Automation-account selecteert u het tabblad **Runbooks** onder proces automatisering.

2.  Selecteer **een Runbook toevoegen** in de linkerbovenhoek van de pagina Runbooks.

3.  Selecteer **een bestaand Runbook importeren**.

4.  Gebruik het opgegeven `DataSyncLogPowerShellRunbook` bestand onder **Runbook file**. Stel het **type Runbook** in `PowerShell`op als. Geef een naam op voor het runbook.

5.  Selecteer **Maken**. U hebt nu een runbook.

6.  Onder uw Azure Automation-account selecteert u het tabblad **variabelen** onder gedeelde resources.

7.  Selecteer **een variabele toevoegen** op de pagina variabelen. Maak een variabele om de laatste uitvoerings tijd voor het runbook op te slaan. Als u meerdere runbooks hebt, hebt u één variabele nodig voor elk runbook.

8.  Stel de naam van de `DataSyncLogLastUpdatedTime` variabele in en stel het type in op DateTime.

9.  Selecteer het runbook en klik op de knop bewerken boven aan de pagina.

10. Breng de vereiste wijzigingen aan voor uw account en uw SQL Data Sync configuratie. (Zie het voorbeeld script voor meer gedetailleerde informatie.)

    1.  Azure-gegevens.

    2.  Groeps gegevens synchroniseren.

    3.  Azure Monitor logboek gegevens. Deze informatie zoeken in azure Portal | Instellingen | Verbonden bronnen. Zie voor meer informatie over het verzenden van gegevens naar Azure Monitor [-Logboeken gegevens verzenden naar Azure monitor logboeken met de http data collector API (preview)](../azure-monitor/platform/data-collector-api.md).

11. Voer het runbook uit in het test venster. Controleer of het is gelukt.

    Als u fouten hebt, zorg er dan voor dat de meest recente Power shell-module is geïnstalleerd. U kunt de meest recente Power shell-module installeren in de **Galerie met modules** in uw Automation-account.

12. Klik op **publiceren**

### <a name="schedule-the-runbook"></a>Het runbook plannen

Het runbook plannen:

1.  Selecteer onder het runbook het tabblad **schema's** onder resources.

2.  Selecteer **een planning toevoegen** op de pagina planningen.

3.  Selecteer **een planning koppelen aan uw runbook**.

4.  Selecteer **een nieuw schema maken.**

5.  Stel **terugkeer patronen** in op terugkerend en stel het gewenste interval in. Gebruik hetzelfde interval, in het script en in Azure Monitor Logboeken.

6.  Selecteer **Maken**.

### <a name="check-the-automation"></a>De automatisering controleren

Als u wilt controleren of uw automatisering op de verwachte manier wordt uitgevoerd, gaat u naar de weer gave **taak statistieken** onder **bewaking**onder **overzicht** van uw Automation-account. Deze weer gave vastmaken aan uw dash board voor een eenvoudige weer gave. Geslaagde uitvoeringen van het runbook worden weer gegeven als voltooid en mislukte uitvoeringen weer geven als ' mislukt '.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Een waarschuwing voor een Azure Monitor lezer maken voor e-mail meldingen

Ga als volgt te werk om een waarschuwing te maken die gebruikmaakt van Azure Monitor Logboeken. Als vereiste moet u Azure Monitor-logboeken die zijn gekoppeld aan een Log Analytics-werk ruimte.

1.  Selecteer in het Azure Portal **logboek zoeken**.

2.  Maak een query om de fouten en waarschuwingen te selecteren op basis van de synchronisatie groep binnen het interval dat u hebt geselecteerd. Bijvoorbeeld:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Nadat de query is uitgevoerd, selecteert u de Bell met de **melding waarschuwing**.

4.  Selecteer onder **waarschuwing genereren op basis van**de optie **metrische maat eenheid**.

    1.  Stel de cumulatieve waarde in op **groter dan**.

    2.  Voer na **groter dan**de drempel waarde in die moet verstrijken voordat u meldingen ontvangt. Er worden tijdelijke fouten verwacht in de gegevens synchronisatie. Stel de drempel waarde in op 5 om ruis te verminderen.

5.  Stel onder **acties** **e-mail melding** in op Ja. Voer de ontvangers van het gewenste e-mail adres in.

6.  Klik op **Opslaan**. De opgegeven ontvangers ontvangen nu e-mail meldingen wanneer er fouten optreden.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Een Azure Monitor weer gave voor bewaking maken

Met deze stap maakt u een Azure Monitor weer gave voor het visueel bewaken van alle opgegeven synchronisatie groepen. De weer gave bevat verschillende onderdelen:

-   Een overzichts tegel, die laat zien hoeveel fouten, successen en waarschuwingen alle synchronisatie groepen hebben.

-   Een tegel voor alle synchronisatie groepen, waarin het aantal fouten en waarschuwingen per synchronisatie groep wordt weer gegeven. Groepen zonder problemen worden niet weer gegeven op deze tegel.

-   Een tegel voor elke synchronisatie groep, waarin het aantal fouten, geslaagde en waarschuwingen en de recente fout berichten worden weer gegeven.

Ga als volgt te werk om de weer gave Azure Monitor te configureren:

1.  Selecteer op de start pagina van de Log Analytics werkruimte de plus aan de linkerkant om de **weergave ontwerper**te openen.

2.  Selecteer **importeren** in de bovenste balk van de weer gave Designer. Selecteer vervolgens het voorbeeld bestand ' DataSyncLogOMSView '.

3.  De voorbeeld weergave is voor het beheren van twee synchronisatie groepen. Bewerk deze weer gave zodat deze overeenkomt met uw scenario. Klik op **bewerken** en breng de volgende wijzigingen aan:

    1.  Maak indien nodig nieuwe objecten van de galerie & lijst.

    2.  Werk in elke tegel de query's bij met uw gegevens.

        1.  Wijzig op elke tegel het TimeStamp_t interval naar wens.

        2.  Werk de namen van de synchronisatie groepen bij op de tegels voor elke synchronisatie groep.

    3.  Werk op elke tegel de titel zo nodig bij.

4.  Klik op Opslaan om de weer gave gereed te **houden** .

## <a name="cost-of-this-solution"></a>Kosten van deze oplossing

In de meeste gevallen is deze oplossing gratis.

**Azure Automation:** Er zijn mogelijk kosten verbonden aan het Azure Automation-account, afhankelijk van uw gebruik. De eerste 500 minuten van de taak uitvoerings tijd per maand zijn gratis. In de meeste gevallen wordt deze oplossing waarschijnlijk minder dan 500 minuten per maand gebruikt. Als u kosten wilt voor komen, moet u het runbook plannen om te worden uitgevoerd met een interval van twee uur of langer. Zie [prijzen van Automation](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie.

**Azure monitor logboeken:** Er zijn mogelijk kosten verbonden aan Azure Monitor logboeken, afhankelijk van uw gebruik. De laag gratis bevat 500 MB aan opgenomen gegevens per dag. In de meeste gevallen wordt deze oplossing naar verwachting kleiner dan 500 MB per dag. Als u het gebruik wilt verkleinen, gebruikt u het filter voor fout filters dat is opgenomen in het runbook. Als u meer dan 500 MB per dag gebruikt, moet u upgraden naar de betaalde laag om te voor komen dat het risico van analyse wordt gestopt wanneer de beperking wordt bereikt. Zie de [prijzen van Azure monitor-logboeken](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie.

## <a name="code-samples"></a>Codevoorbeelden

Down load de code voorbeelden die in dit artikel worden beschreven van de volgende locaties:

-   [Data Sync-logboek Power shell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Weer gave gegevens synchronisatie Azure Monitor](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

-   [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
-   [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
