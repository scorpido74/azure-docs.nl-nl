---
title: Azure Stream Analytics-taken implementeren met CI/CD en Azure DevOps
description: In dit artikel wordt beschreven hoe u een Stream Analytics-taak met CI/CD implementeert met behulp van Azure DevOps Services.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d9360ff64206cdce208f9643cf8ca86515aaeb7e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "75354438"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Zelfstudie: Een Azure Stream Analytics-taak met CI/CD implementeren met behulp van Azure Pipelines
This tutorial descriIn deze zelfstudie wordt beschreven hoe u continue integratie en implementatie instelt voor een Azure Stream Analytics-taak met behulp van Azure Pipelines. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-pijplijn in Azure Pipelines maken
> * Een release-pijplijn in Azure Pipelines maken
> * Automatisch een upgrade en een toepassing implementeren

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u het volgende hebt voordat u begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) en de workload **Azure-ontwikkeling** of **Gegevensopslag en verwerking**.
* Maak een [Stream Analytics-project in Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Maak een [Azure DevOps](https://visualstudio.microsoft.com/team-services/)-organisatie.

## <a name="configure-nuget-package-dependency"></a>Afhankelijkheid van NuGet-pakket configureren
Om automatisch een build te kunnen maken en automatisch te implementeren op een willekeurige computer, moet u het NuGet-pakket `Microsoft.Azure.StreamAnalytics.CICD` gebruiken. Dit bevat de tools voor MSBuild, lokaal uitvoeren en implementatie die ondersteuning bieden voor het continue integratie- en implementatieproces van Stream Analytics Visual Studio-projecten. Zie [Stream Analytics CI/CD-hulpprogramma's](stream-analytics-tools-for-visual-studio-cicd.md) voor meer informatie.

Voeg **packages.config** toe aan de projectmap.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Uw Visual Studio-oplossing delen met een nieuwe Git-opslagplaats van Azure Repos

Deel de bronbestanden van uw toepassing met een project in Azure DevOps zodat u builds kunt genereren.  

1. Maak een nieuwe Git-opslagplaats voor uw project door op de statusbalk in de linkeronderhoek van Visual Studio **Add to Source Control** en vervolgens **Git** te selecteren. 

2. Ga naar de **Synchronization**-weergave in **Team Explorer** en selecteer onder **Push to Azure DevOps Services** de knop **Publish Git Repo**.

   ![De knop Publish Git Repo onder Push to Azure DevOps Services](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Controleer uw e-mail en selecteer uw organisatie in de vervolgkeuzelijst **Azure DevOps Services Domain**. Voer de naam van de opslagplaats in en selecteer **Publish repository**.

   ![De knop Publish repository onder Push Git repo](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    Als u de opslagplaats pusht, wordt er een nieuw project voor uw organisatie gemaakt met dezelfde naam als de lokale opslagplaats. Als u de opslagplaats in een bestaand project wilt maken, klikt u naast de **naam van de opslagplaats** op **Advanced** en selecteert u een teamproject. U kunt uw code in de browser weergeven door **See it on the web** te selecteren.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Continue levering configureren met Azure DevOps
Een build-pijplijn van Azure Pipelines beschrijft een werkstroom die bestaat uit build-stappen die achtereenvolgens worden uitgevoerd. Meer informatie over [build-pijplijnen van Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Een release-pijplijn van Azure Pipelines beschrijft een werkstroom waarmee een toepassingspakket in een cluster wordt geïmplementeerd. Als de build- en release-pijplijnen samen worden gebruikt, wordt hiermee de hele werkstroom uitgevoerd, te beginnen met bronbestanden en te eindigen met het uitvoeren van een toepassing in uw cluster. Meer informatie over [release-pijplijnen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) van Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Een build-pijplijn maken
Open een webbrowser en navigeer naar het project dat u zojuist hebt gemaakt in [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Selecteer op het tabblad **Build & Release** de optie **Builds** en vervolgens **+New**.  Selecteer **Azure DevOps Services Git** en **Continue**.
    
    ![DevOps Git-bron selecteren in Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. Klik in **Select a template** op **Empty Process** om met een lege pijplijn te beginnen.
    
    ![Leeg proces in sjabloonopties selecteren in DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. Schakel onder **Triggers** continue integratie in door de triggerstatus **Enable continuous integration** in te stellen.  Selecteer **Save and queue** om handmatig een build te starten. 
    
    ![Triggerstatus voor continue integratie inschakelen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Builds worden ook getriggerd na pushen of inchecken. Als u de voortgang van de build wilt controleren, schakelt u over naar het tabblad **Builds**.  Als u hebt gecontroleerd dat de build correct wordt uitgevoerd, moet u een release-pijplijn definiëren waarmee uw toepassing in een cluster wordt geïmplementeerd. Klik met de rechtermuisknop op het beletselteken naast uw build-pijplijn en selecteer **Edit**.

5.  Voer in **Tasks** "Hosted" in als de **Agent queue**.
    
    ![Agentwachtrij selecteren in het taakmenu](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. Klik in **Phase 1** op **+** en voeg een **NuGet**-taak toe.
    
    ![Een NuGet-taak aan de agentwachtrij toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Vouw **Advanced** uit en voeg `$(Build.SourcesDirectory)\packages` toe aan **Destination directory**. Behoud de overige NuGet-standaardconfiguratiewaarden.

   ![NuGet-hersteltaak configureren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. Klik in **Phase 1** op **+** en voeg een **MSBuild**-taak toe.

   ![Een MSBuild-taak aan de agentwachtrij toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Wijzig de **MSBuild Arguments** in het volgende:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild-taak in DevOps configureren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. Klik in **Phase 1** op **+** en voeg een **Azure Resource Group Deployment**-taak toe. 
    
    ![Een Azure Resource Group Deployment-taak toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Vouw **Azure Details** uit en vul de configuratie met het volgende:
    
    |**Instelling**  |**Voorgestelde waarde**  |
    |---------|---------|
    |Abonnement  |  Kies uw abonnement.   |
    |Bewerking  |  Resourcegroep maken of bijwerken   |
    |Resourcegroep  |  Voer een resourcegroepnaam in.   |
    |Template  | [Uw oplossingspad] \bin\Debug\Deploy\\[Naam van uw project]. JobTemplate.json   |
    |Sjabloonparameters  | [Uw oplossingspad] \bin\Debug\Deploy\\[Naam van uw project].JobTemplate.parameters.json   |
    |Sjabloonparameters overschrijven  | Typ de sjabloonparameters die u wilt overschrijven in het tekstvak. Bijvoorbeeld: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Deze eigenschap is optioneel, maar er treden fouten op in uw build als belangrijke parameters worden niet overschreven.    |
    
    ![Implementatie-eigenschappen voor Azure-resourcegroep instellen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Klik op **Save & Queue** om de build-pijplijn te testen.
    
    ![Build opslaan en in de wachtrij plaatsen in DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Mislukt buildproces
Mogelijk ontvangt u fouten wegens implementatieparameters van null als u geen sjabloonparameters hebt overschreven in de **Azure Resource Group Deployment**-taak van uw build-pijplijn. Ga terug naar de build-pijplijn en overschrijf de parameters van null om de fout op te lossen.

   ![Mislukt DevOps Stream Analytics-buildproces](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Wijzigingen doorvoeren en pushen om een release te activeren
Controleer of de pijplijn voor continue integratie correct functioneert door enkele codewijzigingen aan te brengen in Azure DevOps.    

Terwijl u de code schrijft, worden de wijzigingen automatisch in Visual Studio bijgehouden. Voer wijzigingen door in de lokale Git-opslagplaats door het pictogram voor wijzigingen in behandeling te selecteren in de statusbalk rechtsonder.

1. Voeg aan de weergave **Changes** in Team Explorer een bericht toe waarin u de update beschrijft en voer de wijzigingen door.

    ![Wijzigingen in de opslagplaats doorvoeren vanuit Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Selecteer het pictogram voor de statusbalk met niet-gepubliceerde wijzigingen of de synchronisatieweergave in Team Explorer. Selecteer **Push** om de code bij te werken in Azure DevOps.

    ![Wijzigingen pushen vanuit Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Als u de wijzigingen naar Azure DevOps Services pusht, wordt er automatisch een build geactiveerd.  Als de build-pijplijn is voltooid, wordt er automatisch een release gemaakt en wordt gestart met het bijwerken van de taak in het cluster.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Klik in het menu aan de linkerkant in Azure Portal op **Resourcegroepen** en klik vervolgens op de resource die u hebt gemaakt.  
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van Azure Stream Analytics-hulpprogramma's voor Visual Studio voor het instellen van een continue integratie en implementatie, gaat u verder met artikelen over het instellen van de CI/CD-pijplijn:

> [!div class="nextstepaction"]
> [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)
