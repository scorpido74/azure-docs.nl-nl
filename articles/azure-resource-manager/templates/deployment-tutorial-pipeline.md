---
title: Continue integratie met Azure-pijplijnen
description: Meer informatie over het continu bouwen, testen en implementeren van Azure Resource Manager sjablonen.
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1c56ce913a1b63bab90f5dd5aaada382abbf493
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084325"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Zelf studie: doorlopende integratie van Azure Resource Manager sjablonen met Azure-pijp lijnen

In de [vorige zelf studie](./deployment-tutorial-linked-template.md)implementeert u een gekoppelde sjabloon.  In deze zelf studie leert u hoe u Azure-pijp lijnen kunt gebruiken om voortdurend Azure Resource Manager-sjabloon projecten te bouwen en implementeren.

Azure DevOps biedt ontwikkelaars Services om teams te ondersteunen bij het plannen van werk, het samen werken met code ontwikkeling en het bouwen en implementeren van toepassingen. Ontwikkel aars kunnen werken in de Cloud met behulp van Azure DevOps Services. Azure DevOps biedt een geïntegreerde set functies die u via uw webbrowser of IDE-client kunt openen. Azure-pijp lijn is een van deze functies. Azure-pijp lijnen is een volledig uitgeruste service voor continue integratie (CI) en continue levering (CD). Het werkt met uw favoriete Git-provider en kan worden geïmplementeerd in de meeste belang rijke Cloud Services. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

> [!NOTE]
> Kies een project naam. Wanneer u de zelf studie door lopen, vervangt u de **AzureRmPipeline** door de naam van uw project.
> Deze project naam wordt gebruikt voor het genereren van resource namen.  Een van de bronnen is een opslag account. Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. De naam moet uniek zijn. In de sjabloon is de naam van het opslag account de project naam ' Store ' toegevoegd en de project naam moet tussen de 3 en 11 tekens lang zijn. De project naam moet dus voldoen aan de naam vereisten voor het opslag account en mag uit minder dan 11 tekens bestaan.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een GitHub-opslagplaats voorbereiden
> * Een Azure DevOps-project maken
> * Een Azure-pijp lijn maken
> * De implementatie van de pijp lijn controleren
> * De sjabloon bijwerken en opnieuw implementeren
> * Resources opschonen

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **Een github-account**, waar u het gebruikt voor het maken van een opslag plaats voor uw sjablonen. Als u er nog geen hebt, kunt u [er gratis een maken](https://github.com). Zie [Build github-opslag](/azure/devops/pipelines/repos/github)plaatsen voor meer informatie over het gebruik van github-opslag plaatsen.
* **Installeer Git**. In deze zelfstudie instructie wordt *Git Bash* of *git shell*gebruikt. Zie [git installeren]( https://www.atlassian.com/git/tutorials/install-git)voor instructies.
* **Een Azure DevOps-organisatie**. Als u er nog geen hebt, kunt u er gratis een maken. Zie [een organisatie-of project verzameling maken]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Beschrijving **Visual Studio code met de extensie Resource Manager-Hulpprogram ma's**. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Een GitHub-opslagplaats voorbereiden

GitHub wordt gebruikt voor het opslaan van de project bron code, inclusief Resource Manager-sjablonen. Zie [opslag plaatsen die worden ondersteund door Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops)voor andere ondersteunde opslag plaatsen.

### <a name="create-a-github-repository"></a>Een GitHub-opslag plaats maken

Als u geen GitHub-account hebt, raadpleegt u [vereisten](#prerequisites).

1. Meld u aan bij [github](https://github.com).
1. Selecteer uw account afbeelding in de rechter bovenhoek en selecteer vervolgens **uw opslag**plaatsen.

    ![GitHub-opslag plaats Azure Resource Manager Azure DevOps Azure-pijp lijnen maken](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecteer **Nieuw**, een groene knop.
1. Voer in **naam van opslag plaats**een naam in voor de opslag plaats.  Bijvoorbeeld **AzureRmPipeline-opslag plaats**. Vergeet niet om een van de **AzureRmPipeline** te vervangen door de naam van uw project. U kunt **openbaar** of **privé** selecteren als u deze zelf studie wilt door lopen. En selecteer vervolgens **opslag plaats maken**.
1. Noteer de URL. De URL van de opslag plaats is de volgende indeling: ** https://github.com/[YourAccountName]/[YourRepositoryName]**.

Deze opslag plaats wordt een *externe opslag plaats*genoemd. Elk van de ontwikkel aars van hetzelfde project kan zijn/haar eigen *lokale opslag plaats*klonen en de wijzigingen in de externe opslag plaats samen voegen.

### <a name="clone-the-remote-repository"></a>De externe opslag plaats klonen

1. Open git-shell of git-bash.  Zie [vereisten](#prerequisites).
1. Controleer of de huidige map **github**is.
1. Voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Vervang **[YourAccountName]** door de naam van uw github-account en vervang **[YourGitHubRepositoryName]** door de naam van de opslag plaats die u in de vorige procedure hebt gemaakt.

De map **CreateWebApp** is de map waarin de sjabloon is opgeslagen. De opdracht **pwd** toont het mappad. In de volgende procedure slaat u de sjabloon op in het pad.

### <a name="download-a-quickstart-template"></a>Een Quick Start-sjabloon downloaden

In plaats van de sjablonen te maken, kunt u de sjablonen downloaden en opslaan in de map **CreateWebApp** .

* De hoofd sjabloon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* De gekoppelde sjabloon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Zowel de naam van de map als de bestands namen worden gebruikt als in de pijp lijn.  Als u deze namen wijzigt, moet u de namen bijwerken die worden gebruikt in de pijp lijn.

### <a name="push-the-template-to-the-remote-repository"></a>De sjabloon naar de externe opslag plaats pushen

De azuredeploy. json is toegevoegd aan de lokale opslag plaats. Vervolgens uploadt u de sjabloon naar de externe opslag plaats.

1. Open *git-shell* of *Git-Bash*als deze niet is geopend.
1. Wijzig de map in de map CreateWebApp in uw lokale opslag plaats.
1. Controleer of het bestand **azuredeploy. json** zich in de map bevindt.
1. Voer de volgende opdracht uit:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Er kan een waarschuwing over LF worden weer gegeven. U kunt de waarschuwing negeren. **Master** is de Master vertakking.  Doorgaans maakt u een vertakking voor elke update. Voor het vereenvoudigen van de zelf studie gebruikt u de hoofd vertakking rechtstreeks.
1. Blader vanuit een browser naar uw GitHub-opslag plaats.  De URL is ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. De map **CreateWebApp** en de drie bestanden in de map worden weer geven.
1. Selecteer **linkedStorageAccount. json** om de sjabloon te openen.
1. Selecteer de knop **RAW** . De URL wordt gestart met **RAW.githubusercontent.com**.
1. Maak een kopie van de URL.  U moet deze waarde opgeven wanneer u de pijp lijn later in de zelf studie configureert.

Tot nu toe hebt u een GitHub-opslag plaats gemaakt en de sjablonen geüpload naar de opslag plaats.

## <a name="create-a-devops-project"></a>Een DevOps-project maken

Er is een DevOps-organisatie vereist voordat u kunt door gaan met de volgende procedure.  Als u er nog geen hebt, raadpleegt u de [vereisten](#prerequisites).

1. Meld u aan bij [Azure DevOps](https://dev.azure.com).
1. Selecteer een DevOps-organisatie aan de linkerkant.

    ![Azure DevOps-project Azure Resource Manager Azure DevOps Azure-pijp lijnen maken](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecteer **Nieuw project**. Als u geen projecten hebt, wordt de pagina project maken automatisch geopend.
1. Voer de volgende waarden in:

    * **Project naam**: Voer een project naam in. U kunt de project naam gebruiken die u aan het begin van de zelf studie hebt gekozen.
    * **Versie beheer**: Selecteer **Git**. Mogelijk moet u **Geavanceerd** uitvouwen om **versie beheer**weer te geven.

    Gebruik de standaard waarde voor de andere eigenschappen.
1. Selecteer **Maken**.

Maak een service verbinding die wordt gebruikt voor het implementeren van projecten in Azure.

1. Selecteer **project instellingen** aan de onderkant van het linkermenu.
1. Selecteer **service verbindingen** onder **pijp lijnen**.
1. Selecteer **nieuwe service verbinding**, selecteer **Azure Resource Manager**en selecteer vervolgens **volgende**.
1. Selecteer **Service-Principal**en selecteer **volgende**.
1. Voer de volgende waarden in:

    * **Bereik niveau**: Selecteer **abonnement**.
    * **Abonnement**: Selecteer uw abonnement.
    * **Resource groep**: laat het veld leeg.
    * **Verbindings naam**: Voer een naam in voor de verbinding. Bijvoorbeeld **AzureRmPipeline-** verbindingen. Noteer deze naam, u hebt de naam nodig wanneer u de pijp lijn maakt.
    * **Verleen toegang tot alle pijp lijnen**. geselecteerde
1. Selecteer **Opslaan**.

## <a name="create-a-pipeline"></a>Een pijplijn maken

Tot nu toe hebt u de volgende taken voltooid.  Als u de vorige secties overs laat, omdat u bekend bent met GitHub en DevOps, moet u de taken volt ooien voordat u doorgaat.

* Maak een GitHub-opslag plaats en sla de sjablonen op in de map **CreateWebApp** in de opslag plaats.
* Maak een DevOps-project en maak een Azure Resource Manager service verbinding.

Een pijp lijn maken met een stap voor het implementeren van een sjabloon:

1. Selecteer **pijp lijnen** in het menu links.
1. Selecteer **nieuwe pijp lijn**.
1. Op het tabblad **Verbinding maken** selecteert u **GitHub**. Voer uw GitHub-referenties in als u daarom wordt gevraagd en volg de instructies. Als het volgende scherm wordt weer gegeven, selecteert u **alleen opslag**plaatsen selecteren en controleert u of uw opslag plaats in de lijst voor komt voordat u **& installatie goed keuren**selecteert.

    ![Azure Resource Manager Azure DevOps Azure-pijp lijnen alleen opslag plaatsen selecteren](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Selecteer uw opslag plaats op het tabblad **selecteren** .  De standaard naam is **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Selecteer op het tabblad **configureren** de optie **Start pijplijn**. Het bevat het pijplijn bestand **Azure-pipelines. yml** met twee script stappen.
1. Verwijder de twee script stappen uit het yml-bestand.
1. Verplaats de cursor naar de regel na de volgende **stappen:**.
1. Selecteer de optie **assistent weer geven** aan de rechter kant van het scherm om het deel venster **taken** te openen.
1. Selecteer **implementatie van arm-sjabloon**.
1. Voer de volgende waarden in:

    * **deploymentScope**: **resource groep**selecteren... Zie [implementatie bereiken](deploy-rest.md#deployment-scope)voor meer informatie over de scopes.
    * **Azure Resource Manager verbinding**: Selecteer de naam van de service verbinding die u eerder hebt gemaakt.
    * **Abonnement**: Geef de id van het doel abonnement op.
    * **Actie**: Selecteer de actie **resource groep maken of bijwerken** 2 acties-1. Maak een resource groep als er een nieuwe naam voor de resource groep wordt gegeven. twee. Implementeer de opgegeven sjabloon.
    * **Resource groep**: Voer een nieuwe naam voor de resource groep in. Bijvoorbeeld **AzureRmPipeline-RG**.
    * **Locatie**: Selecteer een locatie voor de resource groep, bijvoorbeeld VS- **Centraal**.
    * **Sjabloon locatie**: Selecteer **gekoppeld artefact**, wat betekent dat de taak het sjabloon bestand rechtstreeks vanuit de verbonden opslag plaats zoekt.
    * **Sjabloon**: Voer **CreateWebApp/azuredeploy. json**in. Als u de naam van de map en de bestands naam hebt gewijzigd, moet u deze waarde wijzigen.
    * **Sjabloon parameters**: laat dit veld leeg. U geeft de parameter waarden op in de para meters voor het overschrijven van de sjabloon.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName]-linkedTemplateUri [EnterTheLinkedTemplateURL]**. Vervang de project naam en de URL van de gekoppelde sjabloon. De URL van de gekoppelde sjabloon is wat u hebt geschreven aan het einde van [een github-opslag plaats maken](#create-a-github-repository).
    * **Implementatie modus**: Selecteer **Incrementeel**.
    * **Implementatie naam**: Voer **DeployPipelineTemplate**in. Selecteer **Geavanceerd** voordat u de **implementatie naam**kunt zien.

    ![Azure Resource Manager Azure DevOps Azure pipelines-stap](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selecteer **Toevoegen**.

    Zie [implementatie taak voor Azure-resource groep](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)en [Azure Resource Manager sjabloon implementatie taak](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) voor meer informatie over de taak

    Het yml-bestand moet er ongeveer als volgt uitzien:

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selecteer **Opslaan en uitvoeren**.
1. Selecteer in het deel venster **opslaan en uitvoeren** de optie **opslaan en voer** de bewerking opnieuw uit. Er wordt een kopie van het YAML-bestand opgeslagen in de verbonden opslag plaats. U kunt het YAML-bestand zien door naar uw opslag plaats te bladeren.
1. Controleer of de pijp lijn is uitgevoerd.

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resource groep. De naam is wat u hebt opgegeven in het YAML-bestand van de pijp lijn.  U ziet dat er één opslag account is gemaakt.  De naam van het opslag account begint met **Store**.
1. Selecteer de naam van het opslag account om deze te openen.
1. Selecteer **Eigenschappen**. Let op: de **replicatie** is **lokaal redundante opslag (LRS)**.

## <a name="update-and-redeploy"></a>Bijwerken en opnieuw implementeren

Wanneer u de sjabloon bijwerkt en de wijzigingen naar de externe opslag plaats pusht, worden de resources, het opslag account in dit geval automatisch bijgewerkt door de pijp lijn.

1. Open **linkedStorageAccount. json** vanuit uw lokale opslag plaats in Visual Studio code of een tekst editor.
1. Werk de **DefaultValue** van **storageAccountType** bij naar **Standard_GRS**. Zie de volgende schermafbeelding:

    ![Azure Resource Manager Azure DevOps Azure pipelines-update yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Sla de wijzigingen op.
1. Push de wijzigingen naar de externe opslag plaats door de volgende opdrachten uit Git Bash/shell uit te voeren.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    De eerste opdracht (pull) synchroniseert de lokale opslag plaats met de externe opslag plaats. Het YAML-bestand van de pijp lijn is alleen toegevoegd aan de externe opslag plaats. Als u de pull-opdracht uitvoert, wordt een kopie van het YAML-bestand naar de lokale vertakking gedownload.

    Met de vierde opdracht (push) wordt het herziene bestand linkedStorageAccount. json geüpload naar de externe opslag plaats. Als de hoofd vertakking van de externe opslag plaats is bijgewerkt, wordt de pijp lijn opnieuw geactiveerd.

U kunt de wijzigingen controleren door de replicatie-eigenschap van het opslag account te controleren.  Zie [de implementatie controleren](#verify-the-deployment).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

Misschien wilt u ook de GitHub-opslag plaats en het Azure DevOps-project verwijderen.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt de zelf studie voor de implementatie van Resource Manager-sjablonen voltooid. Laat het ons weten als u opmerkingen en suggesties hebt in het gedeelte feedback. Bedankt!
U kunt nu door gaan met meer geavanceerde concepten over sjablonen. De volgende zelf studie gaat meer details over het gebruik van sjabloon referentie documentatie voor hulp bij het definiëren van resources om te implementeren.

> [!div class="nextstepaction"]
> [Sjabloonverwijzing gebruiken](./template-tutorial-use-template-reference.md)
