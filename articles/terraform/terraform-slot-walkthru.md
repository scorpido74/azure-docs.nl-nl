---
title: Zelf studie-een infra structuur inrichten met Azure-implementatie sites met behulp van terraform
description: Zelfstudie over het gebruik van Terraform met Azure-implementatiesites voor providers
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 68c790b4fad442d94e6ac82d1a545b8554d2dd4f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159179"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Zelf studie: infra structuur inrichten met Azure-implementatie sites met behulp van terraform

U kunt [Azure-implementatiesites](/azure/app-service/deploy-staging-slots) gebruiken om te wisselen tussen verschillende versies van uw app. Deze mogelijkheid helpt u om de impact van afgebroken implementaties te minimaliseren. 

In dit artikel ziet u een voorbeeld van het gebruik van implementatiesites waarbij stapsgewijs twee apps worden geïmplementeerd via GitHub en Azure. Eén app wordt gehost op een productiesite. De tweede app wordt gehost op een staging-site. (De namen ' productie ' en ' staging ' zijn wille keurig. Ze zijn ook geschikt voor uw scenario.) Nadat u uw implementatie sleuven hebt geconfigureerd, gebruikt u terraform om naar behoefte te scha kelen tussen de twee sleuven.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **GitHub-account**: u hebt een [GitHub](https://www.github.com)-account nodig om te splitsen en de GitHub-testopslagplaats te gebruiken.

## <a name="create-and-apply-the-terraform-plan"></a>Het Terraform-plan maken en toepassen

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een map met de naam `deploy`.

    ```bash
    mkdir deploy
    ```

1. Maak een map met de naam `swap`.

    ```bash
    mkdir swap
    ```

1. Gebruik de Bash-opdracht `ls` om te controleren of u beide mappen hebt gemaakt.

    ![Cloud Shell na het maken van mappen](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Ga naar de map `deploy`.

    ```bash
    cd deploy
    ```

1. Maak in Cloud Shell een bestand met de naam `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

1. Nu u het bestand hebt gemaakt, controleert u de inhoud ervan.

    ```bash
    cat deploy.tf
    ```

1. Initialiseer Terraform.

    ```bash
    terraform init
    ```

1. Maak het Terraform-plan.

    ```bash
    terraform plan
    ```

1. Richt de resources in die zijn gedefinieerd in het configuratiebestand `deploy.tf`. (Bevestig de actie door `yes` in te voeren bij de opdrachtprompt.)

    ```bash
    terraform apply
    ```

1. Sluit het Cloud Shell-venster.

1. In het hoofdmenu van Azure Portal selecteert u **Resourcegroepen**.

    ![Selectie van 'Resourcegroepen' in de portal](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Op het tabblad **Resourcegroepen** selecteert u **slotDemoResourceGroup**.

    ![Resourcegroep gemaakt door Terraform](./media/terraform-slot-walkthru/resource-group.png)

U ziet nu alle resources die door Terraform zijn gemaakt.

![Resources gemaakt door Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Het testproject splitsen

Voordat u de creatie en het wisselen naar en van de implementatiesites kunt testen, moet u het testproject splitsen vanuit GitHub.

1. Blader naar de [opslagplaats awesome-terraform in GitHub](https://github.com/Azure/awesome-terraform).

1. Splits de opslagplaats **awesome-terraform**.

    ![De GitHub-opslagplaats awesome-terraform splitsen](./media/terraform-slot-walkthru/fork-repo.png)

1. Volg de aanwijzingen op het scherm om uw omgeving te splitsen.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Vanuit GitHub implementeren op uw implementatiesites

Nadat u uw testprojectopslagplaats hebt gesplitst, configureert u de implementatiesites met de volgende stappen:

1. In het hoofdmenu van Azure Portal selecteert u **Resourcegroepen**.

1. Selecteer **slotDemoResourceGroup**.

1. Selecteer **slotAppService**.

1. Selecteer **Implementatieopties**.

    ![Implementatieopties voor een App Service-resource](./media/terraform-slot-walkthru/deployment-options.png)

1. Op het tabblad **Implementatieoptie** selecteert u **Bron kiezen** en selecteert u vervolgens **GitHub**.

    ![Implementatiebron selecteren](./media/terraform-slot-walkthru/select-source.png)

1. Als via Azure verbinding is gemaakt en alle opties worden weergegeven, selecteert u **Autorisatie**.

1. Op het tabblad **Autorisatie** selecteert u **Autoriseren** en voert u de referenties in die Azure nodig heeft voor toegang tot uw GitHub-account. 

1. Nadat uw GitHub-referenties zijn gevalideerd in Azure, wordt het bericht weergegeven dat het autorisatieproces is voltooid. Selecteer **OK** om het tabblad **Autorisatie** te sluiten.

1. Selecteer **Kies uw organisatie** en selecteer uw organisatie.

1. Selecteer **Kies project**.

1. Op het tabblad **Kies project** selecteert u het project **awesome-terraform**.

    ![Het project awesome-terraform kiezen](./media/terraform-slot-walkthru/choose-project.png)

1. Selecteer **Kies branch**.

1. Op het tabblad **Kies branch** selecteert u **master**.

    ![De master branch kiezen](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Op het tabblad **Implementatieoptie** selecteert u **OK**.

U hebt op dit moment de productie site geïmplementeerd. Voer de volgende stappen uit om de faserings sleuf te implementeren:

- Selecteer in stap 3 de resource **slotAppServiceSlotOne**.

- Selecteer in stap 13 de working branch in plaats van de master branch.

    ![De working branch kiezen](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>De app-implementaties testen

In de vorige secties hebt u twee sites ingesteld, **slotAppService** en **slotAppServiceSlotOne** voor implementatie vanuit verschillende branches in GitHub. Laten we een voorbeeld van de web-apps weergeven om te controleren of deze zijn geïmplementeerd.

1. In het hoofdmenu van Azure Portal selecteert u **Resourcegroepen**.

1. Selecteer **slotDemoResourceGroup**.

1. Selecteer **slotAppService** of **slotAppServiceSlotOne**.

1. Selecteer **URL** op de pagina Overzicht.

    ![De URL op het tabblad Overzicht selecteren om de app weer te geven](./media/terraform-slot-walkthru/resource-url.png)

1. Afhankelijk van de geselecteerde app ziet u de volgende resultaten:
    - **slotAppService** web-app-blauwe pagina met een pagina titel van de **sleuf demo-app 1**. 
    - **slotAppServiceSlotOne** web-app-groene pagina met een pagina titel van de **sleuf demo-app 2**.

    ![Een voorbeeld van de apps weergeven om te testen of deze correct zijn geïmplementeerd](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Wisselen tussen de twee implementatiesites

Voer de volgende stappen uit om de twee implementatie sleuven te testen:
 
1. Schakel naar het browsertabblad waarop **slotAppService** (de app met de blauwe pagina) wordt uitgevoerd. 

1. Ga terug naar Azure Portal op een afzonderlijk tabblad.

1. Open Cloud Shell.

1. Ga naar de map **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Maak in Cloud Shell een bestand met de naam `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Sla het bestand op ( **&lt;Ctrl > S**) en sluit de editor af ( **&lt;CTRL > Q**).

1. Initialiseer Terraform.

    ```bash
    terraform init
    ```

1. Maak het Terraform-plan.

    ```bash
    terraform plan
    ```

1. Richt de resources in die zijn gedefinieerd in het configuratiebestand `swap.tf`. (Bevestig de actie door `yes` in te voeren bij de opdrachtprompt.)

    ```bash
    terraform apply
    ```

1. Nadat terraform de sleuven heeft gewisseld, keert u terug naar de browser. Vernieuw de pagina. 

De web-app op uw staging-site **slotAppServiceSlotOne** is verwisseld met de productiesite en wordt nu groen weergegeven. 

![De implementatiesites zijn gewisseld](./media/terraform-slot-walkthru/slots-swapped.png)

Als u wilt terugkeren naar de oorspronkelijke productieversie van de app, past u het Terraform-plan dat u hebt gemaakt vanuit het configuratiebestand `swap.tf` opnieuw toe.

```bash
terraform apply
```

Na de app-wisseling ziet u de oorspronkelijke configuratie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)