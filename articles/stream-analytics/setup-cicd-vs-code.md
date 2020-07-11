---
title: Een Azure Stream Analytics-taak implementeren met CI/CD NPM-pakket
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics CI/CD NPM-pakket gebruikt om een doorlopend integratie-en implementatie proces in te stellen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 75db20bdb746e7d15bef56ce7ac0a064993d3f3a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187758"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Een Azure Stream Analytics-taak implementeren met CI/CD NPM-pakket 

U kunt het Azure Stream Analytics CI/CD NPM-pakket gebruiken om een doorlopend integratie-en implementatie proces voor uw Stream Analytics-taken in te stellen. In dit artikel wordt beschreven hoe u het NPM-pakket gebruikt in het algemeen met een CI/CD-systeem, evenals specifieke instructies voor implementatie met Azure-pijp lijnen.

Zie [Deploy with a Resource Manager Temp late File and Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)voor meer informatie over het implementeren met Power shell. U kunt ook meer te weten komen over het [gebruik van een object als een para meter in een resource manager-sjabloon](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Het VS code-project bouwen

U kunt continue integratie en implementatie voor Azure Stream Analytics-taken inschakelen met het NPM **-pakket ASA-streamanalytics-cicd** . Het NPM-pakket bevat de hulpprogram ma's voor het genereren van Azure Resource Manager sjablonen van [Stream Analytics Visual Studio-code projecten](quick-create-vs-code.md). Het kan worden gebruikt in Windows, macOS en Linux zonder Visual Studio code te installeren.

U kunt [het pakket rechtstreeks downloaden](https://www.npmjs.com/package/azure-streamanalytics-cicd) of het [wereld wijd](https://docs.npmjs.com/downloading-and-installing-packages-globally) installeren via de `npm install -g azure-streamanalytics-cicd` opdracht. Dit is de aanbevolen benadering, die ook kan worden gebruikt in een Power shell-of Azure CLI-script taak van een build-pijp lijn in **Azure-pijp lijnen**.

Nadat u het pakket hebt geïnstalleerd, gebruikt u de volgende opdracht om de Azure Resource Manager-sjablonen uit te voeren. Het argument **ScriptPath** is het absolute pad naar het **asaql** -bestand in uw project. Zorg ervoor dat de asaproj.jsop en JobConfig.jsop bestanden zich in dezelfde map bevinden als het script bestand. Als de **outputPath** niet is opgegeven, worden de sjablonen geplaatst in de map **Deploy** onder de **bin** -map van het project.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Voor beeld (op macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Wanneer een Stream Analytics Visual Studio code-project is gebouwd, worden de volgende twee Azure Resource Manager sjabloon bestanden gegenereerd in de map **bin/[debug/Retail]/Deploy** : 

* Resource Manager-sjabloon bestand

   `[ProjectName].JobTemplate.json`

* Resource Manager-parameter bestand

   `[ProjectName].JobTemplate.parameters.json`   

De standaard parameters in de parameters.jsin het bestand zijn afkomstig uit de instellingen in uw Visual Studio code-project. Als u wilt implementeren in een andere omgeving, vervangt u de para meters dienovereenkomstig.

> [!NOTE]
> Voor alle referenties worden de standaard waarden ingesteld op null. U **moet** de waarden instellen voordat u naar de Cloud implementeert.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implementeren met Azure Pipelines

In deze sectie vindt u informatie over het maken van pijp lijnen voor het [bouwen](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) en [vrijgeven](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) van Azure-pipelines met behulp van NPM.

Open een webbrowser en navigeer naar uw Azure Stream Analytics Visual Studio code-project.

1. Selecteer **builds**onder **pijp lijnen** in het navigatie menu aan de linkerkant. Selecteer vervolgens **nieuwe pijp lijn**

   ![Nieuwe Azure-pijp lijn maken](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecteer **de klassieke editor gebruiken** om een pijp lijn zonder yaml te maken.

3. Selecteer het bron type, het team project en de opslag plaats. Selecteer vervolgens **Doorgaan**.

   ![Azure Stream Analytics project selecteren](./media/setup-cicd-vs-code/select-repo.png)

4. Selecteer op de pagina **een sjabloon kiezen** de optie **lege taak**.

### <a name="add-npm-task"></a>NPM-taak toevoegen

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Geef ' NPM ' op in de zoek opdracht van de taak en selecteer **NPM**.

   ![NPM-taak selecteren](./media/setup-cicd-vs-code/search-npm.png)

2. Geef de taak een **weergave naam**. Wijzig de **opdracht** optie in *aangepast* en voer de volgende opdracht in: **opdracht en argumenten**. Wijzig de overige standaard opties.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Configuraties voor NPM-taak invoeren](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Opdracht regel taak toevoegen

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoeken naar **opdracht regel**.

2. Geef de taak een **weergave naam** en voer het volgende script in. Wijzig het script met de naam van uw opslag plaats en de project naam.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Configuraties voor opdracht regel taak invoeren](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Taak voor het kopiëren van bestanden toevoegen

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoeken naar **Kopieer bestanden**. Voer vervolgens de volgende configuraties in.

   |Parameter|Invoer|
   |-|-|
   |Weergavenaam|Bestanden kopiëren naar: $ (build. artifactstagingdirectory)|
   |Bronmap|`$(system.defaultworkingdirectory)`| 
   |Inhoud| `**\Deploy\**` |
   |Doelmap| `$(build.artifactstagingdirectory)`|

   ![Configuraties voor kopieer taak invoeren](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Taak voor het maken van opbouw van publicatie-indexen toevoegen

1. Selecteer op de pagina **taken** het plus teken naast **Agent taak 1**. Zoek naar **Build-artefacten publiceren** en selecteer de optie met het pictogram met de zwarte pijl. 

2. Wijzig geen van de standaard configuraties.

### <a name="save-and-run"></a>Opslaan en uitvoeren

Wanneer u klaar bent met het toevoegen van de taken NPM, opdracht regel, copy files en Publish builds-artefacten, selecteert u **& wachtrij opslaan**. Wanneer u hierom wordt gevraagd, voert u een opmerking opslaan in en selecteert u **opslaan en uitvoeren**.

## <a name="release-with-azure-pipelines"></a>Release met Azure-pijp lijnen

Open een webbrowser en navigeer naar uw Azure Stream Analytics Visual Studio code-project.

1. Selecteer onder **pijp lijnen** in het navigatie menu aan de linkerkant de optie **releases**. Selecteer vervolgens **nieuwe pijp lijn**.

2. Selecteer **beginnen met een lege taak**.

3. Selecteer in het vak **artefacten** **+ een artefact toevoegen**. Selecteer onder **bron**de build-pijp lijn die u zojuist hebt gemaakt en selecteer **toevoegen**.

   ![Bouw pijplijn artefact invoeren](./media/setup-cicd-vs-code/build-artifact.png)

4. Wijzig de naam van **fase 1** in de implementatie van de **taak in de test omgeving**.

5. Voeg een nieuwe fase toe en geef deze de naam **implementatie taak aan de productie omgeving**.

### <a name="add-tasks"></a>Taken toevoegen

1. Selecteer in de vervolg keuzelijst taken de optie **taak implementeren naar test omgeving**. 

2. Selecteer de **+** **taak** volgende bij agent en zoek naar *implementatie van Azure-resource groep*. Voer de volgende para meters in:

   |Instelling|Waarde|
   |-|-|
   |Weergavenaam| *MyASAJob implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Actie| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de test resource groep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van de test resource groep.|
   |Sjabloonlocatie| *Gekoppeld artefact*|
   |Sjabloon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.jsop |
   |Sjabloonparameters|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.jsop|
   |Sjabloonparameters overschrijven|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Implementatie modus|Incrementeel|

3. Selecteer in de vervolg keuzelijst taken de optie **taak op productie omgeving implementeren**.

4. Selecteer de **+** **taak** volgende bij agent en zoek naar *implementatie van Azure-resource groep*. Voer de volgende para meters in:

   |Instelling|Waarde|
   |-|-|
   |Weergavenaam| *MyASAJob implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Actie| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de productie resource groep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van de productie resource groep.|
   |Sjabloonlocatie| *Gekoppeld artefact*|
   |Sjabloon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.jsop |
   |Sjabloonparameters|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.jsop|
   |Sjabloonparameters overschrijven|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Implementatie modus|Incrementeel|

### <a name="create-release"></a>Release maken

Als u een release wilt maken, selecteert u **vrijgave maken** in de rechter bovenhoek.

![Een release maken met behulp van Azure-pijp lijnen](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Aanvullende bronnen

Als u beheerde identiteit voor Azure Data Lake Store gen1 als uitvoer Sink wilt gebruiken, moet u toegang geven tot de service-principal met behulp van Power shell voordat u implementeert in Azure. Meer informatie over het [implementeren van ADLS gen1 met beheerde identiteit met een resource manager-sjabloon](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Volgende stappen

* [Quick Start: een Azure Stream Analytics Cloud taak maken in Visual Studio code (preview)](quick-create-vs-code.md)
* [Stream Analytics query's lokaal testen met Visual Studio code (preview)](visual-studio-code-local-run.md)
* [Azure Stream Analytics verkennen met Visual Studio code (preview)](visual-studio-code-explore-jobs.md)
