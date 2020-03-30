---
title: Handleiding voor het automatisch afstemmen van e-mailmeldingen
description: E-mailmeldingen inschakelen voor automatische queryafstemming in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b48c37a6e607d121416ebae4d74e58f39670b79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821928"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mailmeldingen voor automatisch afstemmen

SQL Database-tuningaanbevelingen worden gegenereerd door Azure SQL Database [Automatic tuning](sql-database-automatic-tuning.md). Deze oplossing bewaakt en analyseert continu workloads van SQL-databases met aangepaste tuningaanbevelingen voor elke afzonderlijke database met betrekking tot het maken van indexen, het verwijderen van indexen en het optimaliseren van queryuitvoeringsplannen.

SQL Database Automatische tuningaanbevelingen kunnen worden weergegeven in de [Azure-portal,](sql-database-advisor-portal.md)opgehaald met [REST API-aanroepen](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) of met Behulp van [T-SQL-](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) en [PowerShell-opdrachten.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Dit artikel is gebaseerd op het gebruik van een PowerShell-script om automatische tuningaanbevelingen op te halen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mailmeldingen automatiseren voor aanbevelingen voor automatisch afstemmen

De volgende oplossing automatiseert het verzenden van e-mailmeldingen met automatische tuningaanbevelingen. De beschreven oplossing bestaat uit het automatiseren van de uitvoering van een PowerShell-script voor het ophalen van tuningaanbevelingen met [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)en automatisering van het plannen van e-mailleveringstaken met [Microsoft Flow.](https://flow.microsoft.com)

## <a name="create-azure-automation-account"></a>Azure Automation-account maken

Als u Azure Automation wilt gebruiken, moet u een automatiseringsaccount maken en configureren met Azure-resources die moeten worden gebruikt voor de uitvoering van het PowerShell-script. Zie [Aan de slag met Azure-automatisering](https://docs.microsoft.com/azure/automation/automation-offering-get-started)voor meer informatie over Azure Automation en de bijbehorende mogelijkheden.

Volg deze stappen om Azure Automation Account te maken via de methode voor het selecteren en configureren van de automatiserings-app vanuit de Marketplace:

- Inloggen op de Azure-portal
- Klik op "**+ Maak een resource**" in de linkerbovenhoek
- Zoeken naar "**Automatisering**" (druk op enter)
- Klik op de automatiserings-app in de zoekresultaten

![Azure-automatisering toevoegen](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Eenmaal in het deelvenster 'Een automatiseringsaccount maken' klikt u op **'Maken'**
- Vul de vereiste gegevens in: voer een naam in voor dit automatiseringsaccount, selecteer uw Azure-abonnements-id en Azure-resources die moeten worden gebruikt voor de uitvoer van PowerShell-script
- Selecteer **Ja** om het type account te configureren waaronder PowerShell-script wordt uitgevoerd met behulp van Azure Automation voor de optie**Azure Run As-account**maken. Zie [Als-account uitvoeren](https://docs.microsoft.com/azure/automation/automation-create-runas-account) voor meer informatie over accounttypen
- Sluit het aanmaken van het automatiseringsaccount af door op **Maken te** klikken

> [!TIP]
> Neem uw Azure Automation-accountnaam, abonnements-ID en resources (zoals copy-paste naar een notitieblok) precies op zoals ingevoerd tijdens het maken van de automatiserings-app. Je hebt deze informatie later nodig.
>

Als u meerdere Azure-abonnementen hebt waarvoor u dezelfde automatisering wilt bouwen, moet u dit proces voor uw andere abonnementen herhalen.

## <a name="update-azure-automation-modules"></a>Azure Automation-modules bijwerken

Het PowerShell-script voor het ophalen van automatische tuningaanbeveling maakt gebruik van [Get-AzResource-](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) en [Get-AzSqlDatabaseRecommendedAction-opdrachten](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) waarvoor Azure Module-versie 4 en hoger vereist is.

- Zie [Az-moduleondersteuning in Azure Automation](../automation/az-modules.md)als u deze wilt bijwerken.

## <a name="create-azure-automation-runbook"></a>Azure Automation Runbook maken

De volgende stap is het maken van een Runbook in Azure Automation waarin het PowerShell-script voor het ophalen van tuningaanbevelingen zich bevindt.

Volg de volgende stappen om een nieuwe Azure Automation-runbook te maken:

- Toegang tot het Azure Automation-account dat u in de vorige stap hebt gemaakt
- Eenmaal in het deelvenster automatiseringsaccount klikt u op het menu-item**Runbooks**aan de linkerkant om een nieuwe Azure Automation-runbook te maken met het PowerShell-script. Zie [Een nieuw runbook](../automation/manage-runbooks.md#create-a-runbook)maken voor meer informatie over het maken van automatiseringsrunbooks.
- Als u een nieuw runbook wilt toevoegen, klikt u op de menuoptie **" +Een runbook toevoegen"** en klikt u vervolgens op de "**Snel maken - Maak een nieuw runbook**".
- Typ in het deelvenster Runbook de naam van uw runbook (voor dit voorbeeld wordt "**AutomaticTuningEmailAutomation**" gebruikt), selecteert u het type runbook als **PowerShell** en schrijf u een beschrijving van dit runbook om het doel ervan te beschrijven.
- Klik op de knop **Maken** om een nieuw runbook te maken

![Azure-automatiseringsrunbook toevoegen](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Volg de volgende stappen om een PowerShell-script te laden in de runbook die is gemaakt:

- Selecteer**in**het deelvenster "**PowerShell Runbook**bewerken " in de menustructuur en vouw de weergave uit totdat u de naam van uw runbook ziet (in dit voorbeeld "**AutomaticTuningEmailAutomation**"). Selecteer dit runbook.
- Kopieer de volgende PowerShell-scriptcode op de eerste regel van het "PowerShell-runbook bewerken" (te beginnen met het nummer 1). Dit PowerShell-script is beschikbaar om u op weg te helpen. Wijzig het script aan uw behoeften.

In de kop van het meegeleverde PowerShell-script moet u uw Azure-abonnements-ID vervangen. `<SUBSCRIPTION_ID_WITH_DATABASES>` Zie GUID ophalen van uw Azure-abonnement voor meer informatie over het ophalen van uw [Azure-abonnements-ID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

In het geval van meerdere abonnementen u deze toevoegen als komma's beperkt tot de eigenschap '$subscriptions' in de kop tekst van het script.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Klik op de knop**Opslaan**in de rechterbovenhoek om het script op te slaan. Wanneer u tevreden bent met het script, klikt u op de knop "**Publiceren**" om dit runbook te publiceren.

In het hoofddeelvenster van de runbook u ervoor kiezen om op de knop**Start**te klikken om het script te **testen.** Klik op de "**Output**" om de resultaten van het uitgevoerde script weer te geven. Deze output zal de inhoud van uw e-mail zijn. De voorbeelduitvoer van het script is te zien in de volgende schermafbeelding.

![Automatische tuningaanbevelingen weergeven met Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Zorg ervoor dat u de inhoud aanpast door het PowerShell-script aan te passen aan uw behoeften.

Met de bovenstaande stappen wordt het PowerShell-script voor het ophalen van automatische tuningaanbevelingen geladen in Azure Automation. De volgende stap is het automatiseren en plannen van de e-mailleveringtaak.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatiseer de e-mailtaken met Microsoft Flow

Als u de oplossing wilt voltooien, maakt u als laatste stap een automatiseringsstroom in Microsoft Flow die bestaat uit drie acties (taken):

1. "**Azure Automation - Job maken**" - wordt gebruikt om het PowerShell-script uit te voeren om automatische tuningaanbevelingen op te halen in het azure automation-runbook
2. "**Azure Automation - Get job output**" – gebruikt om uitvoer uit het uitgevoerde PowerShell-script op te halen
3. "**Office 365 Outlook – Stuur een e-mail**" – gebruikt om e-mail te verzenden. E-mails worden verzonden met behulp van het Office 365-account van de persoon die de stroom maakt.

Zie Aan de slag met [Microsoft Flow](https://docs.microsoft.com/flow/getting-started)voor meer informatie over Microsoft Flow-mogelijkheden.

Voorwaarde voor deze stap is om je aan te melden voor [het Microsoft Flow-account](https://flow.microsoft.com) en om in te loggen. Eenmaal binnen de oplossing, volg deze stappen om het opzetten van een **nieuwe stroom:**

- **Toegangsmenu-item Met mijn stromen**
- Selecteer in Mijn flows de link **" +Maken van leeg"** boven aan de pagina
- Klik op de link "**Zoek naar honderden connectoren en triggers**" aan de onderkant van de pagina
- Selecteer in het zoekveldtype "**recidief**" en selecteer "**Schema - Herhaling**" in de zoekresultaten om de taak voor e-maillevering te plannen.
- Selecteer in het deelvenster Herhaling in het veld Frequentie de planningsfrequentie die deze stroom moet uitvoeren, zoals het verzenden van geautomatiseerde e-mail elke minuut, uur, dag, week, enz.

De volgende stap is het toevoegen van drie taken (maken, uitvoeren en e-mail verzenden) naar de nieuw gecreëerde terugkerende stroom. Voer de volgende stappen uit om de vereiste taken aan de stroom toe te voegen:

1. Actie maken om PowerShell-script uit te voeren om tuningaanbevelingen op te halen

   - Selecteer "**+Nieuwe stap**", gevolgd door "**Een actie toevoegen**" in het deelvenster Herhalingsstroom
   - Selecteer in het zoekveldtype "**automatisering**" en selecteer "**Azure Automation – Create job**" in de zoekresultaten
   - Configureer in het deelvenster Taak maken de taakeigenschappen. Voor deze configuratie hebt u gegevens nodig over uw Azure-abonnements-ID, Resourcegroep- en Automatiseringsaccount **die eerder zijn geregistreerd** in het deelvenster **Automatiseringsaccount.** Zie [Azure Automation , Taak maken](https://docs.microsoft.com/connectors/azureautomation/#create-job)voor meer informatie over opties die beschikbaar zijn in deze sectie.
   - Voltooi het maken van deze actie door te klikken op "**Flow opslaan**"

2. Actie maken om uitvoer uit het uitgevoerde PowerShell-script op te halen

   - Selecteer "**+Nieuwe stap**", gevolgd door "**Een actie toevoegen**" in het deelvenster Herhalingsstroom
   - In het zoeken gedeponeerde type "**automatisering**" en selecteer "**Azure Automation – Get job output**" uit de zoekresultaten. Zie [Azure Automation – Taakuitvoer voor](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)meer informatie over de beschikbare opties in deze sectie.
   - Velden invullen die nodig zijn (vergelijkbaar met het maken van de vorige taak) - vul uw Azure-abonnements-ID, Resourcegroep en automatiseringsaccount in (zoals ingevoerd in het deelvenster Automatiseringsaccount)
   - Klik in het veld "**Job ID**" voor het menu "**Dynamische inhoud**" om te laten zien. Selecteer in dit menu de optie "**Job ID**".
   - Voltooi het maken van deze actie door te klikken op "**Flow opslaan**"

3. Actie maken om e-mail te verzenden met Office 365-integratie

   - Selecteer "**+Nieuwe stap**", gevolgd door "**Een actie toevoegen**" in het deelvenster Herhalingsstroom
   - Stuur in het verzonden type "**stuur een e-mail**" en selecteer "**Office 365 Outlook - Stuur een e-mail**" uit de zoekresultaten
   - Typ in het veld **" Aan"** het e-mailadres waarnaar u de meldingsmail moet verzenden
   - Typ in het veldtype **'Onderwerp'** in het onderwerp van uw e-mail, bijvoorbeeld 'Automatische tuning-aanbevelingen e-mailmelding'.
   - Klik in het veld "**Body**" voor het "**Dynamische inhoud**" menu om te verschijnen. Selecteer in dit menu onder "**Job output ophalen**", selecteer "**Inhoud**"
   - Voltooi het maken van deze actie door te klikken op "**Flow opslaan**"

> [!TIP]
> Als u geautomatiseerde e-mails naar verschillende ontvangers wilt verzenden, maakt u afzonderlijke stromen. Wijzig in deze extra stromen het e-mailadres van de geadresseerde in het veld Aan en de onderwerpregel voor e-mail in het veld Onderwerp. Het maken van nieuwe runbooks in Azure Automation met aangepaste PowerShell-scripts (zoals met wijziging van Azure-abonnements-ID) maakt verdere aanpassing van geautomatiseerde scenario's mogelijk, zoals het e-mailen van afzonderlijke ontvangers op geautomatiseerde tuning aanbevelingen voor afzonderlijke abonnementen.
>

De bovenstaande afsluitingen stappen die nodig zijn om de e-mail levering taak workflow configureren. De volledige stroom bestaande uit drie gebouwde acties wordt weergegeven in de volgende afbeelding.

![Automatische tuning-e-mailmeldingenstroom weergeven](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Om de stroom te testen, klikt u op "**Nu uitvoeren**" in de rechterbovenhoek in het stroomvenster.

Statistieken van het uitvoeren van de geautomatiseerde taken, waaruit blijkt succes van e-mailmeldingen verzonden, kan worden gezien in de Flow analytics venster.

![Loopstroom voor automatische afstemming e-mailmeldingen](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

De Flow-analyses zijn handig voor het bewaken van het succes van taakuitvoeringen en indien nodig voor het oplossen van problemen.  In het geval van probleemoplossing u ook het PowerShell-logboek voor de uitvoering van scripts onderzoeken dat toegankelijk is via de Azure Automation-app.

De uiteindelijke uitvoer van de geautomatiseerde e-mail lijkt op de volgende e-mail die u hebt ontvangen na het bouwen en uitvoeren van deze oplossing:

![Voorbeeld van e-mailuitvoer van automatische afstemming van e-mailmeldingen](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Door het PowerShell-script aan te passen, u de uitvoer en opmaak van de geautomatiseerde e-mail aanpassen aan uw behoeften.

U de oplossing verder aanpassen om e-mailmeldingen te maken op basis van een specifieke tuninggebeurtenis en voor meerdere ontvangers, voor meerdere abonnementen of databases, afhankelijk van uw aangepaste scenario's.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe automatisch afstemmen u kan helpen de databaseprestaties te verbeteren, zie [Automatische afstemming in Azure SQL Database](sql-database-automatic-tuning.md).
- Zie [Automatische](sql-database-automatic-tuning-enable.md)afstemming inschakelen als u automatische afstemming in Azure SQL Database wilt inschakelen om uw werkbelasting te beheren.
- Zie [Prestatieaanbevelingen zoeken en toepassen](sql-database-advisor-portal.md)als u aanbevelingen voor automatisch afstemmen handmatig wilt controleren en toepassen.
