---
title: Continue integratie met Azure-pijplijnen
description: Meer informatie over het continu bouwen, testen en implementeren van Azure Resource Manager-sjablonen.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6ce6f176a52a742a3216a5b761b34254027a1c5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255070"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Zelfstudie: Continue integratie van Azure Resource Manager-sjablonen met Azure Pipelines

In de [vorige zelfstudie](./deployment-tutorial-linked-template.md)implementeert u een gekoppelde sjabloon.  In deze zelfstudie leert u hoe u Azure Pipelines gebruikt om Azure Resource Manager-sjabloonprojecten continu te bouwen en te implementeren.

Azure DevOps biedt ontwikkelaarsservices om teams te ondersteunen bij het plannen van werk, samen te werken aan codeontwikkeling en toepassingen te bouwen en te implementeren. Ontwikkelaars kunnen in de cloud werken met Azure DevOps Services. Azure DevOps biedt een geïntegreerde set functies die u openen via uw webbrowser of IDE-client. Azure Pipeline is een van deze functies. Azure Pipelines is een volledig uitgeruste CI-service (continuous integration) en continuous delivery (CD). Het werkt met uw favoriete Git-provider en kan worden geïmplementeerd op de meeste grote cloudservices. Vervolgens u de build, het testen en de implementatie van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

> [!NOTE]
> Kies een projectnaam. Wanneer u de zelfstudie doorloopt, vervangt u een van de **AzureRmPipeline** door uw projectnaam.
> Deze projectnaam wordt gebruikt om resourcenamen te genereren.  Een van de bronnen is een opslagaccount. De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken. De naam moet uniek zijn. In de sjabloon is de naam van het opslagaccount de projectnaam met 'store' toegevoegd en moet de projectnaam tussen 3 en 11 tekens liggen. De projectnaam moet dus voldoen aan de vereisten voor de naam van het opslagaccount en heeft minder dan 11 tekens.

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

* **Een GitHub-account,** waar u het gebruikt om een opslagplaats voor uw sjablonen te maken. Als u er geen hebt, u [er gratis een maken.](https://github.com) Zie [GitHub-repositories bouwen](/azure/devops/pipelines/repos/github)voor meer informatie over het gebruik van GitHub-repositories.
* **Installeer Git**. Deze tutorial instructie maakt gebruik van *Git Bash* of *Git Shell*. Zie [Git installeren voor]( https://www.atlassian.com/git/tutorials/install-git)instructies.
* **Een Azure DevOps-organisatie**. Als je er geen hebt, kun je er gratis een maken. Zie [Een organisatie of projectverzameling maken]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* (facultatief) **Visual Studio Code met Resource Manager Tools extensie**. Zie [Visual Studio Code gebruiken om Azure Resource Manager-sjablonen te maken.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden

GitHub wordt gebruikt om uw projectbroncode op te slaan, inclusief Resource Manager-sjablonen. Zie [repositories die worden ondersteund door Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops)voor andere ondersteunde repositories.

### <a name="create-a-github-repository"></a>Een GitHub-repository maken

Zie [Voorwaarden als](#prerequisites)u geen GitHub-account hebt.

1. Meld u aan bij [GitHub](https://github.com).
1. Selecteer uw accountafbeelding in de rechterbovenhoek en selecteer **Uw opslagplaatsen**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines maken GitHub-repository](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecteer **Nieuw**, een groene knop.
1. Voer **in repository-naam**een naam van de opslagplaats in.  **AzureRmPipeline-repo**. Vergeet niet om een van **AzureRmPipeline** te vervangen door uw projectnaam. U **openbaar** of **privé** selecteren om deze zelfstudie te doorlopen. En selecteer vervolgens **Repository maken**.
1. Noteer de URL. De URL van de repository is de volgende indeling:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Deze repository wordt aangeduid als een *externe repository*. Elk van de ontwikkelaars van hetzelfde project kan zijn/haar eigen *lokale repository*klonen en de wijzigingen samenvoegen in de externe repository.

### <a name="clone-the-remote-repository"></a>Kloon de externe opslagplaats

1. Open Git Shell of Git Bash.  Zie [Voorwaarden](#prerequisites).
1. Controleer of uw huidige map **GitHub**is.
1. Voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Vervang **[YourAccountName]** door de naam van je GitHub-account en vervang **[YourGitHubRepositoryName]** door je repository-naam die je in de vorige procedure hebt gemaakt.

De map **CreateWebApp** is de map waar de sjabloon is opgeslagen. De **pwd-opdracht** toont het mappad. Het pad is waar u de sjabloon opslaat in de volgende procedure.

### <a name="download-a-quickstart-template"></a>Een Quickstart-sjabloon downloaden

In plaats van de sjablonen te maken, u de sjablonen downloaden en opslaan in de **map CreateWebApp.**

* De hoofdsjabloon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* De gekoppelde sjabloon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Zowel de mapnaam als de bestandsnamen worden gebruikt zoals ze in de pijplijn zitten.  Als u deze namen wijzigt, moet u de namen bijwerken die in de pijplijn worden gebruikt.

### <a name="push-the-template-to-the-remote-repository"></a>De sjabloon naar de externe opslagplaats duwen

De azuredeploy.json is toegevoegd aan de lokale opslagplaats. Vervolgens upload je de sjabloon naar de externe opslagplaats.

1. Open *Git Shell* of *Git Bash,* als het niet wordt geopend.
1. Wijzig de map CreateWebApp in uw lokale opslagplaats.
1. Controleer of het bestand **azuredeploy.json** zich in de map bevindt.
1. Voer de volgende opdracht uit:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Je krijgt misschien een waarschuwing over LF. Je de waarschuwing negeren. **meester** is de meestertak.  U maakt meestal een vertakking voor elke update. Om de zelfstudie te vereenvoudigen, gebruikt u de hoofdbranch rechtstreeks.
1. Blader vanuit een browser naar je GitHub-repository.  De URL is ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. U ziet de **map CreateWebApp** en de drie bestanden in de map.
1. Selecteer **linkedStorageAccount.json** om de sjabloon te openen.
1. Selecteer de knop **Raw.** De URL wordt gestart met **raw.githubusercontent.com**.
1. Maak een kopie van de URL.  U moet deze waarde opgeven wanneer u de pijplijn later in de zelfstudie configureert.

Tot nu toe hebt u een GitHub-repository gemaakt en de sjablonen naar de opslagplaats geüpload.

## <a name="create-a-devops-project"></a>Een DevOps-project maken

Een DevOps-organisatie is nodig voordat u naar de volgende procedure gaan.  Zie [Voorwaarden als](#prerequisites)je er geen hebt.

1. Meld u aan bij [Azure DevOps](https://dev.azure.com).
1. Selecteer een DevOps-organisatie aan de linkerkant.

    ![Azure Resource Manager Azure DevOps Azure Pipelines maken Azure DevOps-project](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecteer **Nieuw project**. Als u geen projecten hebt, wordt de projectpagina maken automatisch geopend.
1. Voer de volgende waarden in:

    * **Projectnaam:** voer een projectnaam in. U de projectnaam gebruiken die u aan het begin van de zelfstudie hebt gekozen.
    * **Versiebeheer**: Selecteer **Git**. Mogelijk moet u **Geavanceerd** uitbreiden om versiebeheer te **zien.**

    Gebruik de standaardwaarde voor de andere eigenschappen.
1. Selecteer **Maken**.

Maak een serviceverbinding die wordt gebruikt om projecten naar Azure te implementeren.

1. Selecteer **Project-instellingen** onder aan het linkermenu.
1. Selecteer **Serviceverbindingen** onder **Pijplijnen**.
1. Selecteer **Nieuwe serviceverbinding,** selecteer **Azure Resource Manager**en selecteer **Volgende**.
1. Selecteer **Serviceprincipal**en selecteer **Volgende**.
1. Voer de volgende waarden in:

    * **Bereiksniveau**: selecteer **Abonnement**.
    * **Abonnement**: selecteer uw abonnement.
    * **Resourcegroep:** Laat het leeg.
    * **Verbindingsnaam:** voer een verbindingsnaam in. **AzureRmPipeline-conn**. Noteer deze naam, je hebt de naam nodig wanneer je je pijplijn maakt.
    * **Toegangstoestemming verlenen voor alle pijplijnen**. (geselecteerd)
1. Selecteer **Opslaan**.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Tot nu toe hebt u de volgende taken voltooid.  Als u de vorige secties overslaat omdat u bekend bent met GitHub en DevOps, moet u de taken voltooien voordat u verdergaat.

* Maak een GitHub-opslagplaats en sla de sjablonen op in de **map CreateWebApp** in de repository.
* Maak een DevOps-project en maak een Azure Resource Manager-serviceverbinding.

Ga als lid van het volgende over een pijplijn met een stap om een sjabloon te implementeren:

1. Selecteer **Pijplijnen** in het linkermenu.
1. Selecteer **Nieuwe pijplijn**.
1. Op het tabblad **Verbinding maken** selecteert u **GitHub**. Voer desgevraagd uw GitHub-referenties in en volgt de instructies. Als u het volgende scherm ziet, selecteert u **Alleen opslagplaatsen selecteren**en controleert u of uw opslagplaats zich in de lijst bevindt voordat u **& installeren goedkeurt**selecteert.

    ![Azure Resource Manager Azure DevOps Azure Pipelines selecteert alleen repositories](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Selecteer op het tabblad **Selecteren** uw opslagplaats.  De standaardnaam is **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Selecteer op het tabblad **Configureren** de optie **Aanmaakpijplijn**. Het toont het **azure-pipelines.yml-pijplijnbestand** met twee scriptstappen.
1. Verwijder de twee scriptstappen uit het yml-bestand.
1. Verplaats de cursor naar de regel na **stappen:**.
1. Selecteer **Assistent weergeven** aan de rechterkant van het scherm om het deelvenster **Taken** te openen.
1. Selecteer **DE implementatie van ARM-sjabloon**.
1. Voer de volgende waarden in:

    * **deploymentScope:** Selecteer **Resourcegroep**.. Zie Implementatiescopes voor meer informatie over de [scopes.](deploy-rest.md#deployment-scope)
    * **Azure Resource Manager-verbinding**: selecteer de naam van de serviceverbinding die u eerder hebt gemaakt.
    * **Abonnement:** Geef de doelabonnements-id op.
    * **Actie:** Selecteer de actie **Resourcegroep maken of bijwerken** doet 2 acties - 1. een resourcegroep maken als er een nieuwe naam van de resourcegroep wordt opgegeven; 2. de opgegeven sjabloon implementeren.
    * **Resourcegroep:** voer een nieuwe naam van de resourcegroep in. **AzureRmPipeline-rg.**
    * **Locatie:** selecteer een locatie voor de resourcegroep, bijvoorbeeld **Centraal VS**.
    * **Sjabloonlocatie:** Selecteer **Gekoppelde artefact,** wat betekent dat de taak rechtstreeks vanuit de verbonden opslagplaats naar het sjabloonbestand zoekt.
    * **Sjabloon:** Enter **CreateWebApp/azuredeploy.json**. Als u de naam van de map en de bestandsnaam hebt gewijzigd, moet u deze waarde wijzigen.
    * **Sjabloonparameters:** Laat dit veld leeg. U geeft de parameterwaarden op in de sjabloonparameters **Overschrijven.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Vervang de projectnaam en de url van de gekoppelde sjabloon. De url van de gekoppelde sjabloon is wat u hebt opgeschreven aan het einde van [Een GitHub-opslagplaats maken.](#create-a-github-repository)
    * **Implementatiemodus:** **Incrementeel selecteren**.
    * **Implementatienaam:** **DeployPipelineTemplate invoeren**. Selecteer **Geavanceerd** voordat u **de naam Implementatie**zien.

    ![Azure Resource Manager Azure DevOps Azure Pipelines stap](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selecteer **Toevoegen**.

    Zie Azure Resource Group [Deployment-taak](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)en [Azure Resource Manager-sjabloonimplementatietaak](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) voor meer informatie over de taak.

    Het yml-bestand moet vergelijkbaar zijn met:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selecteer **Opslaan en uitvoeren**.
1. Selecteer Opslaan en opnieuw **uitvoeren** in het deelvenster **Opslaan en uitvoeren.** Een kopie van het YAML-bestand wordt opgeslagen in de verbonden opslagplaats. Je het YAML-bestand bekijken door naar je repository te bladeren.
1. Controleer of de pijplijn is uitgevoerd.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resourcegroep. De naam is wat u hebt opgegeven in het YAML-bestand van de pijplijn.  Er wordt één opslagaccount aangemaakt.  De naam van het opslagaccount begint met **het opslaan**.
1. Selecteer de naam van het opslagaccount om deze te openen.
1. Selecteer **Eigenschappen**. De replicatie is **lrs (Localredundant storage)** op de **hoogte.**

## <a name="update-and-redeploy"></a>Bijwerken en opnieuw implementeren

Wanneer u de sjabloon bijwerkt en de wijzigingen naar de externe opslagplaats pusht, wordt de bronnen, het opslagaccount in dit geval, automatisch bijgewerkt.

1. Open **linkedStorageAccount.json** vanuit uw lokale opslagplaats in Visual Studio Code of een teksteditor.
1. Werk de **standaardwaarde** van **storageAccountType** bij naar **Standard_GRS**. Zie de volgende schermafbeelding:

    ![Azure Resource Manager Azure DevOps Azure Pipelines update yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Sla de wijzigingen op.
1. Duw de wijzigingen in de externe opslagplaats door de volgende opdrachten van Git Bash/Shell uit te voeren.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    De eerste opdracht (pull) synchroniseert de lokale repository met de externe repository. Het YAML-bestand voor pijplijnen is alleen toegevoegd aan de externe opslagplaats. Als u de opdracht pull uitvoert, wordt een kopie van het YAML-bestand naar de lokale branch gedownload.

    De vierde opdracht (push) uploadt het herziene linkedStorageAccount.json-bestand naar de externe opslagplaats. Nu de hoofdtak van de externe opslagplaats is bijgewerkt, wordt de pijplijn opnieuw geactiveerd.

Als u de wijzigingen wilt verifiëren, u de eigenschap Replicatie van het opslagaccount controleren.  Zie [De implementatie verifiëren](#verify-the-deployment).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

U ook de GitHub-repository en het Azure DevOps-project verwijderen.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u bent klaar met de zelfstudie voor het implementeren van resourcebeheersjablonen. Laat het ons weten als je opmerkingen en suggesties hebt in de feedbacksectie. Bedankt!
U bent klaar om te springen in meer geavanceerde concepten over sjablonen. De volgende zelfstudie gaat in meer detail over het gebruik van sjabloonreferentiedocumentatie om te helpen bij het definiëren van resources die moeten worden geïmplementeerd.

> [!div class="nextstepaction"]
> [Sjabloonverwijzing gebruiken](./template-tutorial-use-template-reference.md)
