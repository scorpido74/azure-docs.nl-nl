---
title: SQL-gegevenssynchronisatie met Azure Monitor-logboeken bewaken
description: Meer informatie over het bewaken van Azure SQL Data Sync met Azure Monitor-logboeken
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383691"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>SQL-gegevenssynchronisatie met Azure Monitor-logboeken bewaken 

Als u het SQL Data Sync-activiteitenlogboek wilt controleren en fouten en waarschuwingen wilt detecteren, moest u sql-gegevenssynchronisatie eerder handmatig controleren in de Azure-portal of PowerShell of de REST-API gebruiken. Volg de stappen in dit artikel om een aangepaste oplossing te configureren die de monitoringervaring voor gegevenssynchronisatie verbetert. U deze oplossing aanpassen aan uw scenario.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Dashboard bewaken voor al uw synchronisatiegroepen 

U hoeft niet langer afzonderlijk door de logboeken van elke synchronisatiegroep te kijken om problemen te zoeken. U al uw synchronisatiegroepen vanaf een van uw abonnementen op één plaats controleren met behulp van een aangepaste Azure Monitor-weergave. Deze weergave geeft de informatie weer die belangrijk is voor SQL Data Sync-klanten.

![Monitoringdashboard gegevenssynchronisatie](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatische e-mailmeldingen

U hoeft het logboek niet langer handmatig te controleren in de Azure-portal of via PowerShell of de REST API. Met [Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)u waarschuwingen maken die rechtstreeks naar de e-mailadressen gaan van de mensen die ze moeten zien wanneer er een fout optreedt.

![E-mailmeldingen voor gegevenssynchronisatie](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hoe stelt u deze bewakingsfuncties in? 

Implementeer in minder dan een uur een aangepaste Azure Monitor-logboekenbewakingsoplossing voor SQL Data Sync door de volgende dingen te doen:

U moet drie componenten configureren:

-   Een PowerShell-runbook voor sql-gegevenssynchronisatielogboekgegevens naar Azure Monitor-logboeken.

-   Een Azure Monitor-waarschuwing voor e-mailmeldingen.

-   Een Azure Monitor-weergave voor bewaking.

### <a name="samples-to-download"></a>Voorbeelden die moeten worden gedownload

Download de volgende twee voorbeelden:

-   [PowerShell-runbook voor gegevenssynchronisatie](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure-monitorweergave voor gegevenssynchronisatie](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende dingen hebt ingesteld:

-   Een Azure Automation-account

-   Log Analytics-werkruimte

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell Runbook om SQL Data Sync Log te krijgen 

Gebruik een PowerShell-runbook die wordt gehost in Azure Automation om de SQL Data Sync-logboekgegevens op te halen en naar Azure Monitor-logboeken te verzenden. Een voorbeeldscript is inbegrepen. Als voorwaarde moet u een Azure Automation-account hebben. Vervolgens moet u een runbook maken en plannen om het uit te voeren. 

### <a name="create-a-runbook"></a>Een runbook maken

Zie [Mijn eerste PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)voor meer informatie over het maken van een runbook.

1.  Selecteer onder uw Azure Automation-account het tabblad **Runbooks** onder Procesautomatisering.

2.  Selecteer **Een runbook toevoegen** in de linkerbovenhoek van de pagina Runbooks.

3.  Selecteer **Een bestaand runbook importeren**.

4.  Gebruik **onder Runbook-bestand**het opgegeven `DataSyncLogPowerShellRunbook` bestand. Stel het type `PowerShell` **Runbook** in als . Geef het runbook een naam.

5.  Selecteer **Maken**. Je hebt nu een hardloopboek.

6.  Selecteer onder uw Azure Automation-account het tabblad **Variabelen** onder Gedeelde resources.

7.  Selecteer **Een variabele toevoegen** op de pagina Variabelen. Maak een variabele om de laatste uitvoeringstijd voor het runbook op te slaan. Als u meerdere runbooks hebt, hebt u één variabele nodig voor elk runbook.

8.  Stel de variabelenaam in als `DataSyncLogLastUpdatedTime` en stel het type in als DateTime.

9.  Selecteer het runbook en klik op de knop Bewerken boven aan de pagina.

10. Breng de wijzigingen aan die nodig zijn voor uw account en uw SQL Data Sync-configuratie. (Zie het voorbeeldscript voor meer gedetailleerde informatie.)

    1.  Azure-informatie.

    2.  Groepsinformatie synchroniseren.

    3.  Azure Monitor registreert gegevens. Deze informatie zoeken in Azure Portal | Instellingen | Verbonden bronnen. Zie [Gegevens verzenden naar Azure Monitor-logboeken met de HTTP Data Collector API (preview)](../azure-monitor/platform/data-collector-api.md)voor meer informatie over het verzenden van gegevens naar Azure Monitor-logboeken.

11. Voer het runbook uit in het deelvenster Test. Controleer of het gelukt is.

    Als u fouten hebt, moet u ervoor zorgen dat u de nieuwste PowerShell-module hebt geïnstalleerd. U de nieuwste PowerShell-module in de **modulesgalerie** installeren in uw automatiseringsaccount.

12. Klik **op Publiceren**

### <a name="schedule-the-runbook"></a>Het runbook plannen

Ga als u het loopboek in:

1.  Selecteer onder de runbook het tabblad **Schema's** onder Resources.

2.  Selecteer **Een planning toevoegen** op de pagina Schema's.

3.  Selecteer **Een planning koppelen aan uw runbook**.

4.  Selecteer **Een nieuw schema maken.**

5.  Stel **Herhaling** in op Terugkerend en stel het gewenste interval in. Gebruik hetzelfde interval hier, in het script en in Azure Monitor-logboeken.

6.  Selecteer **Maken**.

### <a name="check-the-automation"></a>Controleer de automatisering

Als u wilt controleren of uw automatisering werkt zoals verwacht, zoekt u onder **Overzicht** voor uw automatiseringsaccount de weergave **Taakstatistieken** onder **Monitoring**. Maak deze weergave vast aan uw dashboard voor een eenvoudige weergave. Succesvolle uitvoeringen van de runbook-show als 'Voltooid' en Mislukte uitvoeringen worden weergegeven als 'Mislukt'.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Een Azure-monitorlezerwaarschuwing maken voor e-mailmeldingen

Als u een waarschuwing wilt maken die azure monitorlogboeken gebruikt, doet u de volgende dingen. Als voorwaarde moet u Azure Monitor-logboeken hebben die zijn gekoppeld aan een Log Analytics-werkruimte.

1.  Selecteer **Logboekzoeken**in de Azure-portal .

2.  Maak een query om de fouten en waarschuwingen te selecteren op synchronisatiegroep binnen het geselecteerde interval. Bijvoorbeeld:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Nadat u de query hebt uitgevoerd, selecteert u de bel met de tekst **Alert**.

4.  Selecteer Metrische meting onder **Waarschuwing genereren op basis**van , selecteer Metrische **meting**.

    1.  Stel de totale waarde in **op Groter dan**.

    2.  Voer **na Groter dan**, de drempel in om te vervallen voordat u meldingen ontvangt. Tijdelijke fouten worden verwacht in Data Sync. Om ruis te verminderen, stelt u de drempelwaarde in op 5.

5.  Stel **onder Acties** **e-mailmelding** in op 'Ja'. Voer de gewenste e-mailontvangers in.

6.  Klik op **Opslaan**. De opgegeven ontvangers ontvangen nu e-mailmeldingen wanneer er fouten optreden.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Een Azure-monitorweergave maken voor bewaking

Met deze stap wordt een Azure Monitor-weergave om alle opgegeven synchronisatiegroepen visueel te controleren. De weergave bevat verschillende onderdelen:

-   Een overzichtstegel, die aangeeft hoeveel fouten, successen en waarschuwingen alle synchronisatiegroepen hebben.

-   Een tegel voor alle synchronisatiegroepen, die het aantal fouten en waarschuwingen per synchronisatiegroep weergeeft. Groepen zonder problemen worden niet op deze tegel weergegeven.

-   Een tegel voor elke synchronisatiegroep, die het aantal fouten, successen en waarschuwingen en de recente foutberichten weergeeft.

Ga als volgt te werk om de Azure Monitor-weergave te configureren:

1.  Selecteer op de startpagina van de Log Analytics-werkruimte het plusje aan de linkerkant om de weergaveontwerper te **openen.**

2.  Selecteer **Importeren** op de bovenste balk van de weergaveontwerper. Selecteer vervolgens het voorbeeldbestand "DataSyncLogOMSView".

3.  De voorbeeldweergave is voor het beheren van twee synchronisatiegroepen. Bewerk deze weergave om aan uw scenario te voldoen. Klik **op bewerken** en breng de volgende wijzigingen aan:

    1.  Maak indien nodig nieuwe objecten '&-lijst van donut& uit de galerie'.

    2.  Werk in elke tegel de query's bij met uw gegevens.

        1.  Wijzig op elke tegel het TimeStamp_t-interval zoals gewenst.

        2.  Werk op de tegels voor elke synchronisatiegroep de namen van de synchronisatiegroep bij.

    3.  Werk op elke tegel de titel zo nodig bij.

4.  Klik **op Opslaan** en de weergave is gereed.

## <a name="cost-of-this-solution"></a>Kosten van deze oplossing

In de meeste gevallen is deze oplossing gratis.

**Azure-automatisering:** Er kunnen kosten worden gemaakt met het Azure Automation-account, afhankelijk van uw gebruik. De eerste 500 minuten looptijd per maand zijn gratis. In de meeste gevallen zal deze oplossing naar verwachting minder dan 500 minuten per maand gebruiken. Om kosten te voorkomen, plant u het runbook te laten draaien op een interval van twee uur of meer. Zie [Automatiseringsprijzen](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie .

**Azure Monitor-logboeken:** Er kunnen kosten verbonden zijn aan Azure Monitor-logboeken, afhankelijk van uw gebruik. De gratis laag bevat 500 MB aan ingenomen gegevens per dag. In de meeste gevallen zal deze oplossing naar verwachting minder dan 500 MB per dag innemen. Als u het gebruik wilt verminderen, gebruikt u de filtering alleen voor fouten in het runbook. Als u meer dan 500 MB per dag gebruikt, u upgraden naar de betaalde laag om te voorkomen dat het risico bestaat dat analytics stopt wanneer de beperking is bereikt. Zie [Azure Monitor-logboekenprijzen voor](https://azure.microsoft.com/pricing/details/log-analytics/)meer informatie.

## <a name="code-samples"></a>Codevoorbeelden

Download de codevoorbeelden die in dit artikel worden beschreven vanaf de volgende locaties:

-   [PowerShell-runbook voor gegevenssynchronisatie](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure-monitorweergave voor gegevenssynchronisatie](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

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
