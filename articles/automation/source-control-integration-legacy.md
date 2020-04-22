---
title: Source Control-integratie in Azure Automation - Legacy
description: In dit artikel wordt de integratie van bronbeheer met GitHub in Azure Automation beschreven.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: dcadfcb4c2f8e6bc371b0a70b917c8c1e218fba9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679517"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Source control integration in Azure Automation - Legacy

> [!NOTE]
> Er is een nieuwe ervaring voor broncontrole. Zie [Bronbeheer (Voorbeeld)](source-control-integration.md)voor meer informatie over de nieuwe ervaring.

Met de integratie van Source Control u runbooks in uw Automation-account koppelen aan een GitHub-bronbeheeropslagplaats. Met bronbeheer u eenvoudig samenwerken met uw team, wijzigingen bijhouden en terugdraaien naar eerdere versies van uw runbooks. Met bronbeheer u bijvoorbeeld verschillende branches in bronbeheer synchroniseren met uw accounts voor ontwikkeling, test of productieautomatisering, waardoor het eenvoudig is om code die in uw ontwikkelomgeving is getest, te promoten bij uw productieautomatiseringsaccount.

Met Source Control u code van Azure Automation naar bronbeheer pushen of uw runbooks van bronbeheer naar Azure Automation trekken. In dit artikel wordt beschreven hoe u bronbeheer in stelt in uw Azure Automation-omgeving. We beginnen met het configureren van Azure Automation om toegang te krijgen tot uw GitHub-repository en verschillende bewerkingen te doorlopen die kunnen worden uitgevoerd met behulp van integratie van bronbeheer. 

> [!NOTE]
> Source Control ondersteunt het trekken en pushen van [PowerShell Workflow runbooks](automation-runbook-types.md#powershell-workflow-runbooks) en [PowerShell runbooks.](automation-runbook-types.md#powershell-runbooks) [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) worden nog niet ondersteund.

## <a name="configuring-source-control"></a>Bronbeheer configureren

Er zijn twee eenvoudige stappen nodig om bronbeheer voor uw Automation-account te configureren en slechts één als u al een GitHub-account hebt. 

### <a name="create-a-github-repository"></a>Een GitHub-repository maken

Als u al een GitHub-account en een opslagplaats hebt die u wilt koppelen aan Azure Automation, meldt u zich aan bij uw bestaande account en begint u vanaf stap 2 hieronder. Anders navigeer je naar [GitHub,](https://github.com/)meld je je aan voor een nieuw account en [maak je een nieuwe opslagplaats.](https://help.github.com/articles/create-a-repo/)

### <a name="set-up-source-control"></a>Bronbeheer instellen

1. Klik op de pagina Automatiseringsaccount in de Azure-portal onder **Accountinstellingen**op **Bronbeheer.**

2. De pagina Bronbeheer wordt geopend, waar u uw GitHub-accountgegevens configureren. Hieronder volgt de lijst met te configureren parameters:  

   | **Parameter** | **Beschrijving** |
   |:--- |:--- |
   | Bron kiezen |Selecteer de bron. Momenteel wordt alleen **GitHub** ondersteund. |
   | Autorisatie |Klik op de knop **Autoriseren** om Azure Automation toegang te verlenen tot uw GitHub-repository. Als u al bent ingelogd op uw GitHub-account in een ander venster, worden de referenties van dat account gebruikt. Zodra de autorisatie is gelukt, wordt op de pagina uw GitHub-gebruikersnaam weergegeven onder **Autorisatieeigenschap**. |
   | Repository kiezen |Selecteer een GitHub-repository in de lijst met beschikbare opslagplaatsen. |
   | Branch kiezen |Selecteer een vertakking in de lijst met beschikbare branches. Alleen **master** de hoofdvertakking wordt weergegeven als u geen branches hebt gemaakt. |
   | Pad runbook-map |Het mappad runbook geeft het pad op in de GitHub-opslagplaats waaruit u uw code wilt pushen of trekken. Deze moet worden ingevoerd in de indeling **/mapnaam/submapnaam**. Alleen runbooks in het pad met de runbookmap worden gesynchroniseerd met uw Automatiseringsaccount. Runbooks in de submappen van het pad met de runbookmapworden **NIET** gesynchroniseerd. Met **/** gebruiken om alle runbooks onder de repository te synchroniseren. |
3. Als u bijvoorbeeld een opslagplaats met de naam **PowerShellScripts** hebt met de naam **RootFolder**, die een map met de naam **Submap**bevat. U de volgende tekenreeksen gebruiken om elk mapniveau te synchroniseren:

   1. Als u runbooks vanuit **de opslagplaats** **/** wilt synchroniseren, is runbookmapmappad .
   2. Als u runbooks vanuit **RootFolder**wilt synchroniseren, is runbook-mappad **/RootFolder**.
   3. Als u runbooks vanuit **Submap**wilt synchroniseren, is runbookmapmappad **/RootFolder/SubMap**.
4. Nadat u de parameters hebt geconfigureerd, worden ze weergegeven op de pagina Bronbeheer instellen.  

    ![De pagina Bronbesturingselement met de instellingen](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Zodra u op **OK**klikt, is de integratie van bronbeheer nu geconfigureerd voor uw Automatiseringsaccount en moet deze worden bijgewerkt met uw GitHub-gegevens. U nu op dit onderdeel klikken om al uw taakgeschiedenis voor het synchroniseren van bronbeheer weer te geven.  

    ![Waarden voor de huidige geconfigureerde bronbeheerconfiguratie](media/source-control-integration-legacy/automation-RepoValues.png)
6. Nadat u bronbeheer hebt ingesteld, worden er twee [variabele elementen](automation-variables.md) gemaakt in uw Automatiseringsaccount. Daarnaast wordt een geautoriseerde toepassing toegevoegd aan uw GitHub-account.

   * De variabele **Microsoft.Azure.Automation.SourceControl.Connection** bevat de waarden van de verbindingstekenreeks, zoals hieronder wordt weergegeven.  

     | **Parameter** | **Waarde** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Tekenreeks |
     | `Value` |{"Branch":\<*uw filiaalnaam*>,'RunbookFolderPath':\<*runbook map path*>'ProviderType':\<heeft een waarde 1 voor*GitHub*>,'Repository':\<Naam van uw*repository*>,Gebruikersnaam':\<uw*GitHub-gebruikersnaam*>} |

   * De variabele **Microsoft.Azure.Automation.SourceControl.OAuthToken**, bevat de beveiligde versleutelde waarde van uw OAuthToken.  

     |**Parameter**            |**Waarde** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Een venster met variabelen voor bronbesturingselementen](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation Source Control** wordt als geautoriseerde toepassing toegevoegd aan uw GitHub-account. Als u de toepassing wilt bekijken, navigeert u vanaf de startpagina van GitHub naar**toepassingen****voor profielinstellingen.** >  **profile** >  Met deze toepassing kan Azure Automation uw GitHub-repository synchroniseren met een Automation-account.  

     ![Toepassingsinstellingen in GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Bronbeheer gebruiken in Automatisering

Met het inchecken van runbook u de wijzigingen die u hebt aangebracht in een runbook in Azure Automation naar uw bronbeheeropslagplaats pushen. Hieronder vindt u de stappen om een runbook in te checken:

1. Maak vanuit uw Automatiseringsaccount [een nieuw tekstueel runbook](automation-first-runbook-textual.md)of [bewerk een bestaand tekstueel runbook](automation-edit-textual-runbook.md). Deze runbook kan een PowerShell-werkstroom of een PowerShell-scriptrunbook zijn.  
2. Nadat u uw runbook hebt bewerkt, slaat u het op en klikt u op **Inchecken** op de bewerkingspagina.  

    ![Een venster met de knop inchecken op GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Inchecken vanuit Azure Automation overschrijft de code die momenteel in uw bronbeheer bestaat. De Git equivalent command-line instructie om in te checken is **git add + git commit + git push**  

3. Wanneer u op **inchecken**klikt, wordt u gevraagd een bevestigingsbericht te ontvangen, klikt u op **Ja** om door te gaan.  

    ![Een dialoogvenster waarin het incheckbesturingselement wordt bevestigd aan het bronbesturingselement](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Inchecken start de runbook voor bronbeheer: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Deze runbook maakt verbinding met GitHub en pusht wijzigingen van Azure Automation naar uw repository. Als u de ingecheckte taakgeschiedenis wilt weergeven, gaat u terug naar het tabblad **Integratie van bronbeheer** en klikt u om de pagina Repository Synchronisatie te openen. Op deze pagina ziet u al uw bronbeheertaken. Selecteer de taak die u wilt weergeven en klik om de details weer te geven.  

    ![Een venster met de resultaten van een synchronisatietaak](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Runbooks voor bronbeheer zijn speciale runbooks voor automatisering die u niet bekijken of bewerken. Hoewel ze niet worden weergegeven in uw runbook-lijst, ziet u synchronisatietaken in uw takenlijst.

5. De naam van het gewijzigde runbook wordt verzonden als invoerparameter voor het aangevinkte runbook. U [de taakgegevens bekijken](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) door runbook uit te vouwen op de pagina Repository Synchronization.  

    ![Een venster met de invoer voor een synchronisatietaak](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Vernieuw uw GitHub-opslagplaats zodra de taak is voltooid om de wijzigingen te bekijken.  Er moet een commit in je repository zijn met een commit bericht: **Updated *Runbook Name* in Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbooks synchroniseren van bronbeheer naar Azure Automation

Met de synchronisatieknop op de pagina Repository Synchronization u alle runbooks in het loopboekmappad van uw opslagplaats naar uw Automatiseringsaccount trekken. Dezelfde repository kan worden gesynchroniseerd met meer dan één Automatiseringsaccount. Hieronder vindt u de stappen om een runbook te synchroniseren:

1. Open op het automatiseringsaccount waar u bronbeheer instelt de pagina Source Control Integration/Repository Synchronization en klik op **Synchroniseren.**  U wordt gevraagd een bevestigingsbericht te ontvangen, klik op **Ja** om door te gaan.  

    ![Knop Synchroniseren met bericht waarin wordt bevestigd dat alle runbooks worden gesynchroniseerd](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Sync start de **Runbook Sync-MicrosoftAzureAutomationAccountFromGitHubV1,** die verbinding maakt met GitHub en de wijzigingen van uw repository naar Azure Automation haalt. U ziet een nieuwe taak op de pagina Repository Synchronisatie voor deze actie. Als u details over de synchronisatietaak wilt weergeven, klikt u om de pagina taakgegevens te openen.  

    ![Een venster met de synchronisatieresultaten van een synchronisatietaak in een GitHub-opslagplaats](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Een synchronisatie van bronbeheer overschrijft de conceptversie van de runbooks die momenteel in uw Automatiseringsaccount aanwezig zijn voor **ALLE** runbooks die momenteel in bronbeheer zijn. De Git equivalent command-line instructie om te synchroniseren is **git pull**

![Een venster met alle logboeken van een gesynchroniseerde taak voor opgeschorte bronbeheer](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Bronbeheer loskoppelen

Als u de verbinding met uw GitHub-account wilt verbreken, opent u de pagina Repository Synchronization en klikt u op **Verbreken**. Zodra u het bronbeheer loskoppelt, blijven runbooks die eerder zijn gesynchroniseerd nog steeds in uw Automatiseringsaccount, maar de pagina Repository Synchronisatie is niet ingeschakeld.  

  ![Een venster met de knop Verbinding verbreken om het bronbesturingselement los te koppelen](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over de integratie van bronbeheer:  

* [Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Azure Automation: Runbook Source Control integreren met Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
