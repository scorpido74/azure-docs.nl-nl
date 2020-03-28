---
title: Continue integratie met Azure-pijplijnen
description: Meer informatie over het continu bouwen, testen en implementeren van Azure Resource Manager-sjablonen.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e7d6b23aa3f35c99cf03f855152b2b231a60a965
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75921625"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Zelfstudie: Continue integratie van Azure Resource Manager-sjablonen met Azure Pipelines

Meer informatie over het gebruik van Azure Pipelines om Azure Resource Manager-sjabloonprojecten continu te bouwen en te implementeren.

Azure DevOps biedt ontwikkelaarsservices om teams te ondersteunen bij het plannen van werk, samen te werken aan codeontwikkeling en toepassingen te bouwen en te implementeren. Ontwikkelaars kunnen in de cloud werken met Azure DevOps Services. Azure DevOps biedt een geïntegreerde set functies die u openen via uw webbrowser of IDE-client. Azure Pipeline is een van deze functies. Azure Pipelines is een volledig uitgeruste CI-service (continuous integration) en continuous delivery (CD). Het werkt met uw favoriete Git-provider en kan worden geïmplementeerd op de meeste grote cloudservices. Vervolgens u de build, het testen en de implementatie van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

Deze zelfstudie is ontworpen voor azure resource manager-sjabloonontwikkelaars die nieuwe Azure DevOps-services en Azure Pipelines zijn. Als u al bekend bent met GitHub en DevOps, u een [pijplijn maken.](#create-a-pipeline)

> [!NOTE]
> Kies een projectnaam. Wanneer u de zelfstudie doorloopt, vervangt u een van de **AzureRmPipeline** door uw projectnaam.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een GitHub-opslagplaats voorbereiden
> * Een Azure DevOps-project maken
> * Een Azure-pijplijn maken
> * De implementatie van de pijplijn verifiëren
> * De sjabloon bijwerken en opnieuw implementeren
> * Resources opschonen

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **Een GitHub-account,** waar u het gebruikt om een opslagplaats voor uw sjablonen te maken. Als u nog geen account hebt, kunt u er [gratis een maken](https://github.com). Zie [GitHub-repositories bouwen](/azure/devops/pipelines/repos/github)voor meer informatie over het gebruik van GitHub-repositories.
* **Installeer Git**. Deze tutorial instructie maakt gebruik van *Git Bash* of *Git Shell*. Zie [Git installeren voor]( https://www.atlassian.com/git/tutorials/install-git)instructies.
* **Een Azure DevOps-organisatie**. Als je er geen hebt, kun je er gratis een maken. Zie [Een organisatie of projectverzameling maken]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om Azure Resource Manager-sjablonen te maken.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden

GitHub wordt gebruikt om uw projectbroncode op te slaan, inclusief Resource Manager-sjablonen. Zie [repositories die worden ondersteund door Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)voor andere ondersteunde repositories.

### <a name="create-a-github-repository"></a>Een GitHub-repository maken

Zie [Voorwaarden als](#prerequisites)u geen GitHub-account hebt.

1. Meld u aan bij [GitHub](https://github.com).
2. Selecteer uw accountafbeelding in de rechterbovenhoek en selecteer **Uw opslagplaatsen**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines maken GitHub-repository](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecteer **Nieuw**, een groene knop.
1. Voer **in repository-naam**een naam van de opslagplaats in.  **AzureRmPipeline-repo**. Vergeet niet om een van **AzureRmPipeline** te vervangen door uw projectnaam. U **openbaar** of **privé** selecteren om deze zelfstudie te doorlopen. En selecteer vervolgens **Repository maken**.
1. Noteer de URL. De URL van de repository is de volgende indeling:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Deze repository wordt aangeduid als een *externe repository*. Elk van de ontwikkelaars van hetzelfde project kan zijn/haar eigen *lokale repository*klonen en de wijzigingen samenvoegen in de externe repository.

### <a name="clone-the-remote-repository"></a>Kloon de externe opslagplaats

1. Open Git Shell of Git Bash.  Zie [Voorwaarden](#prerequisites).
1. Controleer of uw huidige map **github**is.
1. Voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Vervang **[YourAccountName]** door de naam van je GitHub-account en vervang **[YourGitHubRepositoryName]** door je repository-naam die je in de vorige procedure hebt gemaakt.

    De volgende schermafbeelding toont een voorbeeld.

    ![Azure Resource Manager Azure DevOps Azure Pipelines maken GitHub-bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

De map **CreateAzureStorage** is de map waar de sjabloon is opgeslagen. De **pwd-opdracht** toont het mappad. Het pad is waar u de sjabloon opslaat in de volgende procedure.

### <a name="download-a-quickstart-template"></a>Een Quickstart-sjabloon downloaden

In plaats van een sjabloon te maken, u een [Quickstart-sjabloon]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)downloaden. Met deze sjabloon wordt een Azure Storage-account gemaakt.

1. Open Visual Studio-code. Zie [Voorwaarden](#prerequisites).
2. Open de sjabloon met de volgende URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Sla het bestand op als **azuredeploy.json** in de map **CreateAzureStorage.** Zowel de mapnaam als de bestandsnaam worden gebruikt zoals ze in de pijplijn zitten.  Als u deze namen wijzigt, moet u de namen bijwerken die in de pijplijn worden gebruikt.

### <a name="push-the-template-to-the-remote-repository"></a>De sjabloon naar de externe opslagplaats duwen

De azuredeploy.json is toegevoegd aan de lokale opslagplaats. Vervolgens upload je de sjabloon naar de externe opslagplaats.

1. Open *Git Shell* of *Git Bash,* als het niet wordt geopend.
1. Wijzig de map CreateAzureStorage in uw lokale opslagplaats.
1. Controleer of het bestand **azuredeploy.json** zich in de map bevindt.
1. Voer de volgende opdracht uit:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Je krijgt misschien een waarschuwing over LF. Je de waarschuwing negeren. **meester** is de meestertak.  U maakt meestal een vertakking voor elke update. Om de zelfstudie te vereenvoudigen, gebruikt u de hoofdbranch rechtstreeks.
1. Blader vanuit een browser naar je GitHub-repository.  De URL is ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. U ziet de map **CreateAzureStorage** en **Azuredeploy.json** in de map.

Tot nu toe hebt u een GitHub-repository gemaakt en een sjabloon naar de opslagplaats geüpload.

## <a name="create-a-devops-project"></a>Een DevOps-project maken

Een DevOps-organisatie is nodig voordat u naar de volgende procedure gaan.  Zie [Voorwaarden als](#prerequisites)je er geen hebt.

1. Meld u aan bij [Azure DevOps](https://dev.azure.com).
1. Selecteer een DevOps-organisatie aan de linkerkant.

    ![Azure Resource Manager Azure DevOps Azure Pipelines maken Azure DevOps-project](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecteer **Project maken**. Als u geen projecten hebt, wordt de projectpagina maken automatisch geopend.
1. Voer de volgende waarden in:

    * **Projectnaam:** voer een projectnaam in. U de projectnaam gebruiken die u aan het begin van de zelfstudie hebt gekozen.
    * **Versiebeheer**: Selecteer **Git**. Mogelijk moet u **Geavanceerd** uitbreiden om versiebeheer te **zien.**

    Gebruik de standaardwaarde voor de andere eigenschappen.
1. Selecteer **Project maken**.

Maak een serviceverbinding die wordt gebruikt om projecten naar Azure te implementeren.

1. Selecteer **Project-instellingen** onder aan het linkermenu.
1. Selecteer **Serviceverbindingen** onder **Pijplijnen**.
1. Selecteer **Nieuwe serviceverbinding**en selecteer **Vervolgens AzureResourceManager**.
1. Voer de volgende waarden in:

    * **Verbindingsnaam:** voer een verbindingsnaam in. **AzureRmPipeline-conn**. Noteer deze naam, je hebt de naam nodig wanneer je je pijplijn maakt.
    * **Bereiksniveau**: selecteer **Abonnement**.
    * **Abonnement**: selecteer uw abonnement.
    * **Resourcegroep:** Laat het leeg.
    * **Sta alle pijplijnen toe deze verbinding te gebruiken.** (geselecteerd)
1. Selecteer **OK**.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Tot nu toe hebt u de volgende taken voltooid.  Als u de vorige secties overslaat omdat u bekend bent met GitHub en DevOps, moet u de taken voltooien voordat u verdergaat.

- Maak een GitHub-opslagplaats en sla [deze sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) op in de map **CreateAzureStorage** in de repository.
- Maak een DevOps-project en maak een Azure Resource Manager-serviceverbinding.

Ga als lid van het volgende over een pijplijn met een stap om een sjabloon te implementeren:

1. Selecteer **Pijplijnen** in het linkermenu.
1. Selecteer **Nieuwe pijplijn**.
1. Op het tabblad **Verbinding maken** selecteert u **GitHub**. Voer desgevraagd uw GitHub-referenties in en volgt de instructies. Als u het volgende scherm ziet, selecteert u **Alleen opslagplaatsen selecteren**en controleert u of uw opslagplaats zich in de lijst bevindt voordat u **& installeren goedkeurt**selecteert.

    ![Azure Resource Manager Azure DevOps Azure Pipelines selecteert alleen repositories](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Selecteer op het tabblad **Selecteren** uw opslagplaats.  De standaardnaam is **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Selecteer op het tabblad **Configureren** de optie **Aanmaakpijplijn**. Het toont het **azure-pipelines.yml-pijplijnbestand** met twee scriptstappen.
1. Vervang de **sectie stappen** door de volgende YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Het zal eruit zien als:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Breng de volgende wijzigingen aan:

    * **deploymentScope**: Selecteer het implementatiebereik `Management Group`uit `Subscription` `Resource Group`de opties: , en . Gebruik **Resourcegroep** in deze zelfstudie. Zie Implementatiescopes voor meer informatie over de [scopes.](deploy-rest.md#deployment-scope)
    * **ConnectedServiceName:** geef de naam van de serviceverbinding op die u eerder hebt gemaakt.
    * **AbonnementNaam:** geef de doelabonnements-id op.
    * **actie:** de actie **Resourcegroep maken of bijwerken** doet 2 acties - 1. een resourcegroep maken als er een nieuwe naam van de resourcegroep wordt opgegeven; 2. de opgegeven sjabloon implementeren.
    * **resourceGroupName:** geef een nieuwe naam van de resourcegroep op. **AzureRmPipeline-rg.**
    * **locatie:** geef de locatie voor de resourcegroep op.
    * **TemplateLocatie:** wanneer **gekoppeldartefact** is opgegeven, zoekt de taak rechtstreeks vanuit de verbonden opslagplaats naar het sjabloonbestand.
    * **csmFile** is het pad naar het sjabloonbestand. U hoeft geen sjabloonparametersbestand op te geven omdat alle parameters die in de sjabloon zijn gedefinieerd, standaardwaarden hebben.

    Zie Azure Resource Group [Deployment-taak](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)en [Azure Resource Manager-sjabloonimplementatietaak](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) voor meer informatie over de taak.
1. Selecteer **Opslaan en uitvoeren**.
1. Selecteer Opslaan en opnieuw **uitvoeren.** Een kopie van het YAML-bestand wordt opgeslagen in de verbonden opslagplaats. Je het YAML-bestand bekijken door naar je repository te bladeren.
1. Controleer of de pijplijn is uitgevoerd.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Open de resourcegroep. De naam is wat u hebt opgegeven in het YAML-bestand van de pijplijn.  Er wordt één opslagaccount aangemaakt.  De naam van het opslagaccount begint met **het opslaan**.
1. Selecteer de naam van het opslagaccount om deze te openen.
1. Selecteer **Eigenschappen**. De replicatie is **lrs (Localredundant storage)** op de **hoogte.**

    ![Azure Resource Manager Azure DevOps Azure Pipelines-portalverificatie](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Bijwerken en opnieuw implementeren

Wanneer u de sjabloon bijwerkt en de wijzigingen naar de externe opslagplaats pusht, wordt de bronnen, het opslagaccount in dit geval, automatisch bijgewerkt.

1. Open **azuredeploy.json** vanuit uw lokale opslagplaats in Visual Studio Code.
1. Werk de **standaardwaarde** van **storageAccountType** bij naar **Standard_GRS**. Zie de volgende schermafbeelding:

    ![Azure Resource Manager Azure DevOps Azure Pipelines update yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Sla de wijzigingen op.
1. Duw de wijzigingen in de externe opslagplaats door de volgende opdrachten van Git Bash/Shell uit te voeren.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    De eerste opdracht synchroniseert de lokale opslagplaats met de externe opslagplaats. Vergeet niet de pijplijn YAML-bestand is toegevoegd aan de externe repository.

    Nu de hoofdtak van de externe opslagplaats is bijgewerkt, wordt de pijplijn opnieuw geactiveerd.

Als u de wijzigingen wilt verifiëren, u de eigenschap Replicatie van het opslagaccount controleren.  Zie [De implementatie verifiëren](#verify-the-deployment).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

U ook de GitHub-repository en het Azure DevOps-project verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie maakt u een Azure DevOps-pijplijn om een Azure Resource Manager-sjabloon te implementeren. Voor meer informatie over hoe u Azure-resources implementeert in meerdere regio's en hoe u veilige implementatiemethoden gebruikt, raadpleegt u

> [!div class="nextstepaction"]
> [Veilige implementatiemethoden gebruiken](./deployment-manager-tutorial.md)
