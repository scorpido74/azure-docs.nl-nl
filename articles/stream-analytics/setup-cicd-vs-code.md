---
title: Een Azure Stream Analytics-taak implementeren met een CI/CD-npm-pakket
description: In dit artikel wordt beschreven hoe u het CI/CD-npm-pakket van Azure Stream Analytics gebruiken om een continu integratie- en implementatieproces in te stellen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962164"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Een Azure Stream Analytics-taak implementeren met een CI/CD-npm-pakket 

U het Ci/CD-npm-pakket azure stream Analytics gebruiken om een continu integratie- en implementatieproces voor uw Stream Analytics-taken in te stellen. In dit artikel wordt beschreven hoe u het npm-pakket in het algemeen gebruiken met een CI/CD-systeem, evenals specifieke instructies voor implementatie met Azure Pipelines.

Zie [Implementeren met een Resource Manager-sjabloonbestand en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)voor meer informatie over het implementeren met Powershell. U ook meer informatie krijgen over het [gebruik van een object als parameter in een resourcemanagersjabloon.](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

## <a name="build-the-vs-code-project"></a>Het VS Code-project bouwen

U continue integratie en implementatie inschakelen voor Azure Stream Analytics-taken met behulp van het **asa-streamanalytics-cicd npm-pakket.** Het npm-pakket biedt de hulpprogramma's voor het genereren van Azure Resource Manager-sjablonen van [Stream Analytics Visual Studio Code-projecten.](quick-create-vs-code.md) Het kan worden gebruikt op Windows, macOS en Linux zonder Visual Studio Code te installeren.

U [het pakket](https://www.npmjs.com/package/azure-streamanalytics-cicd) rechtstreeks downloaden of `npm install -g azure-streamanalytics-cicd` [wereldwijd](https://docs.npmjs.com/downloading-and-installing-packages-globally) installeren via de opdracht. Dit is de aanbevolen aanpak, die ook kan worden gebruikt in een PowerShell- of Azure CLI-scripttaak van een buildpijplijn in **Azure Pipelines.**

Zodra u het pakket hebt geïnstalleerd, gebruikt u de volgende opdracht om de Azure Resource Manager-sjablonen uit te voeren. Het argument **scriptPath** is het absolute pad naar het **asaql-bestand** in uw project. Zorg ervoor dat de asaproj.json- en JobConfig.json-bestanden zich in dezelfde map met het scriptbestand bevinden. Als het **uitvoerpad** niet is opgegeven, worden de sjablonen in de map **Implementeren** onder de **opslaglocatiemap** van het project geplaatst.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Voorbeeld (op macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Wanneer een Project voor Visual Studio Code van Stream Analytics succesvol wordt opgebouwd, worden de volgende twee Sjabloonbestanden van Azure Resource Manager gegenereerd onder de map Foutopsporing/Detailhandel/Detailhandel:When a Stream Analytics Visual Studio Code project builds successfully, it generates the following two Azure Resource Manager template files under the **bin/[Debug/Retail]/Deploy** folder: 

*  Resource Manager-sjabloonbestand

       [ProjectName].JobTemplate.json 

*  Bestand resourcebeheerparameters

       [ProjectName].JobTemplate.parameters.json   

De standaardparameters in het bestand parameters.json zijn afkomstig van de instellingen in uw Visual Studio Code-project. Als u wilt implementeren naar een andere omgeving, vervangt u de parameters dienovereenkomstig.

> [!NOTE]
> Voor alle referenties worden de standaardwaarden ingesteld op null. U **moet** de waarden instellen voordat u deze implementeert in de cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implementeren met Azure Pipelines

In dit gedeelte wordt beschreven hoe u Azure Pipelines [maakt voor het maken](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) en vrijgeven [van](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) pijplijnen met behulp van npm.

Open een webbrowser en navigeer naar uw Azure Stream Analytics Visual Studio Code-project.

1. Selecteer **Builds**onder **Pijplijnen** in het linkernavigatiemenu . Selecteer vervolgens **Nieuwe pijplijn**

   ![Nieuwe Azure-pijplijn maken](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecteer **De klassieke editor gebruiken** om een pijplijn te maken zonder YAML.

3. Selecteer uw brontype, teamproject en opslagplaats. Selecteer vervolgens **Doorgaan**.

   ![Azure Stream Analytics-project selecteren](./media/setup-cicd-vs-code/select-repo.png)

4. Selecteer op de pagina **Een sjabloon kiezen** de optie Taak **leegmaken**.

### <a name="add-npm-task"></a>NPM-taak toevoegen

1. Selecteer **op** de pagina Taken het plusteken naast **agenttaak 1**. Voer 'npm' in de taakzoekopdracht in en selecteer **npm**.

   ![NPM-taak selecteren](./media/setup-cicd-vs-code/search-npm.png)

2. Geef de taak een **weergavenaam**. Wijzig de optie **Opdracht** in *aangepast* en voer de volgende opdracht in **Opdracht en argumenten**in. Laat de resterende standaardopties achter.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Configuraties invoeren voor npm-taak](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Opdrachtregeltaak toevoegen

1. Selecteer **op** de pagina Taken het plusteken naast **agenttaak 1**. Zoeken naar **opdrachtregel**.

2. Geef de taak een **weergavenaam** en voer het volgende script in. Wijzig het script met de naam van uw opslagplaats en de projectnaam.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Configuraties voor opdrachtregeltaak invoeren](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Taak kopieerbestanden toevoegen

1. Selecteer **op** de pagina Taken het plusteken naast **agenttaak 1**. Zoeken naar **kopieerbestanden**. Voer vervolgens de volgende configuraties in.

   |Parameter|Invoer|
   |-|-|
   |Weergavenaam|Bestanden kopiëren naar: $(build.artifactstagingdirectory)|
   |Bronmap|`$(system.defaultworkingdirectory)`| 
   |Inhoud| `**\Deploy\**` |
   |Doelmap| `$(build.artifactstagingdirectory)`|

   ![Configuraties voor kopieertaak invoeren](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Taak Artefacten publiceren publiceren toevoegen

1. Selecteer **op** de pagina Taken het plusteken naast **agenttaak 1**. Zoek naar **artefacten publiceren en** selecteer de optie met het zwarte pijlpictogram. 

2. Wijzig geen standaardconfiguraties.

### <a name="save-and-run"></a>Opslaan en uitvoeren

Zodra u klaar bent met het toevoegen van de npm, opdrachtregel, het kopiëren van bestanden en het publiceren van taken voor het maken van artefacten, selecteert **u Opslaan & wachtrij**. Wanneer u wordt gevraagd, voert u een opmerking opslaan in en selecteert u **Opslaan en uitvoeren**.

## <a name="release-with-azure-pipelines"></a>Vrijgeven met Azure-pijplijnen

Open een webbrowser en navigeer naar uw Azure Stream Analytics Visual Studio Code-project.

1. Selecteer **Onder Pijplijnen** in het linkernavigatiemenu de optie **Releases**. Selecteer vervolgens **Nieuwe pijplijn**.

2. Selecteer **Begin met een lege taak**.

3. Selecteer in het vak **Artefacten** de optie **+ Een artefact toevoegen.** Selecteer **onder Bron**de buildpijplijn die u zojuist hebt gemaakt en selecteer **Toevoegen**.

   ![Artefact voor buildpijplijn invoeren](./media/setup-cicd-vs-code/build-artifact.png)

4. Wijzig de naam van **fase 1** in Taak implementeren **in testomgeving**.

5. Voeg een nieuw fase toe en geef deze naam **Taak implementeren in de productieomgeving**.

### <a name="add-tasks"></a>Taken toevoegen

1. Selecteer taak **implementeren naar testomgeving**in de vervolgkeuzelijst Taken . 

2. Selecteer **+** de volgende taak **voor Agent** en zoek naar *de implementatie van Azure-brongroepen*. Voer de volgende parameters in:

   |Instelling|Waarde|
   |-|-|
   |Weergavenaam| *MyASAJob implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Actie| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de testbrongroep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van uw testbrongroep.|
   |Sjabloonlocatie| *Gekoppeld artefact*|
   |Template| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Sjabloonparameters|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Sjabloonparameters overschrijven|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Implementatiemodus|Incrementeel|

3. Selecteer taak **implementeren naar productieomgeving**in de vervolgkeuzelijst Taken .

4. Selecteer **+** de volgende taak **voor Agent** en zoek naar *de implementatie van Azure-brongroepen*. Voer de volgende parameters in:

   |Instelling|Waarde|
   |-|-|
   |Weergavenaam| *MyASAJob implementeren*|
   |Azure-abonnement| Kies uw abonnement.|
   |Actie| *Resourcegroep maken of bijwerken*|
   |Resourcegroep| Kies een naam voor de productiebrongroep die uw Stream Analytics-taak bevat.|
   |Locatie|Kies de locatie van uw productiebrongroep.|
   |Sjabloonlocatie| *Gekoppeld artefact*|
   |Template| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Sjabloonparameters|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Sjabloonparameters overschrijven|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Implementatiemodus|Incrementeel|

### <a name="create-release"></a>Release maken

Als u een release wilt maken, selecteert u **Release maken** in de rechterbovenhoek.

![Een release maken met Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Aanvullende bronnen

Als u Managed Identity voor Azure Data Lake Store Gen1 wilt gebruiken als uitvoerzink, moet u toegang tot de serviceprincipal bieden met PowerShell voordat u wordt geïmplementeerd in Azure. Meer informatie over het [implementeren van ADLS Gen1 met de sjabloon Managed Identity met Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een Azure Stream Analytics-cloudtaak maken in Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Query's van TestStream Analytics lokaal met Visual Studio Code (Preview)](visual-studio-code-local-run.md)
* [Azure Stream Analytics verkennen met Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)
