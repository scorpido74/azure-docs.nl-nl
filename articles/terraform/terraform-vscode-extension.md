---
title: Zelf studie-de Azure terraform Visual Studio code extension configureren
description: Lees hoe u de Azure Terraform-extensie in Visual Studio Code installeert en gebruikt.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: fcb47024fd26f061ca4475b01d00f1ae13303b61
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472278"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Zelf studie: de Azure terraform Visual Studio code extension configureren

Met de code-extensie van Azure terraform Visual Studio kunt u met terraform werken vanuit de editor. Met deze extensie kunt u terraform-configuraties ontwerpen, testen en uitvoeren. De uitbrei ding biedt ook ondersteuning voor de visualisatie van resource grafieken.

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Het inrichten van Azure-Services automatiseren met terraform
> * Installeer en gebruik de terraform Visual Studio code-extensie voor Azure-Services.
> * Gebruik Visual Studio code om terraform-abonnementen te schrijven, te plannen en uit te voeren.

## <a name="prerequisites"></a>Vereisten
- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform**: [installeer en configureer Terraform](terraform-install-configure.md).

- **Visual Studio Code**: installeer de versie van [Visual Studio Code](https://code.visualstudio.com/download) die bij uw omgeving past.

## <a name="prepare-your-dev-environment"></a>Uw ontwikkelaarsomgeving voorbereiden

### <a name="install-git"></a>Git installeren

U moet [Git installeren](https://git-scm.com/) om de oefeningen in het artikel te voltooien.

### <a name="install-hashicorp-terraform"></a>HashiCorp Terraform installeren

Volg de instructies op de HashiCorp-webpagina [Terraform installeren](https://www.terraform.io/intro/getting-started/install.html). Deze behandelen:

- Het downloaden van Terraform
- Het installeren van Terraform
- Het controleren of Terraform goed is geïnstalleerd

>[!Tip]
>Volg de instructies met betrekking tot het instellen van uw PATH-systeemvariabele.

### <a name="install-nodejs"></a>Node.js installeren

Als u Terraform in Cloud Shell wilt gebruiken, moet u [Node.js](https://nodejs.org/) 6.0+ installeren.

>[!NOTE]
>U kunt controleren of Node.js is geïnstalleerd door een terminalvenster te openen en `node -v` in te voeren.

### <a name="install-graphviz"></a>GraphViz installeren

Als u de visualisatiefunctie van Terraform wilt gebruiken, moet u [GraphViz installeren](https://graphviz.org/).

>[!NOTE]
>U kunt controleren of GraphViz is geïnstalleerd door een terminalvenster te openen en `dot -V` in te voeren.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>De Azure Terraform Visual Studio Code-extensie installeren

1. Start Visual Studio Code.

1. Selecteer **Extensies**.

    ![De knop Extensies](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Gebruik het tekstvak **Extensies zoeken in Marketplace** om de Azure Terraform-extensie te zoeken:

    ![Visual Studio Code-extensies zoeken in Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Selecteer **Installeren**.

    >[!NOTE]
    >Wanneer u **Installeren** selecteert als Azure Terraform-extensie, wordt de Azure Account-extensie automatisch door Visual Studio Code geïnstalleerd. Azure Account is een afhankelijkheidsbestand voor de Azure Terraform-extensie, die wordt gebruikt om verificaties van Azure-abonnementen en aan Azure gerelateerde code-extensies uit te voeren.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Controleren of de Terraform-extensie in Visual Studio Code is geïnstalleerd

1. Selecteer **Extensies**.

1. Voer `@installed` in het zoekvak in.

    ![Geïnstalleerde extensies](media/terraform-vscode-extension/tf-installed-extensions.png)

De Azure Terraform-extensie wordt in de lijst met geïnstalleerde extensies weergegeven.

![Geïnstalleerde Terraform-extensies](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

U kunt nu alle ondersteunde Terraform-opdrachten in uw Cloud Shell-omgeving uitvoeren vanuit Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Oefening 1: overzicht van Terraform-basisopdrachten

In deze oefening gaat u een basisconfiguratiebestand van Terraform maken en uitvoeren waarmee een nieuwe Azure-resourcegroep wordt ingericht.

### <a name="prepare-a-test-plan-file"></a>Een testplanbestand voorbereiden

1. Selecteer **Bestand > Nieuw bestand** in de menubalk in Visual Studio Code.

1. Ga in uw browser naar de pagina [Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) en kopieer de code in het codeblok **Gebruiksvoorbeeld**:

    ![Gebruiksvoorbeeld](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Plak de gekopieerde code in het nieuwe bestand dat u in Visual Studio Code hebt gemaakt.

    ![Code uit Example Usage plakken](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >U kunt de waarde **name** van de resourcegroep wijzigen, maar dit moet wel een unieke naam voor uw Azure-abonnement zijn.

1. Selecteer **Bestand > Opslaan als** in de menubalk.

1. Navigeer in het dialoogvenster **Opslaan als** naar de gewenste locatie en selecteer vervolgens **Nieuwe map**. (Wijzig de naam van de nieuwe map in een naam die meer beschrijft dan alleen *Nieuwe map*.)

    >[!NOTE]
    >In dit voorbeeld noemen we de map TERRAFORM-TEST-PLAN.

1. Controleer of uw nieuwe map is gemarkeerd (geselecteerd) en selecteer **Openen**.

1. Wijzig de standaardnaam van het bestand in het dialoogvenster **Opslaan als** in *main.tf*.

    ![Opslaan als main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Selecteer **Opslaan**.
1. In de menubalk selecteert u **Bestand > Map openen**. Navigeer naar de nieuwe map die u hebt gemaakt en selecteer deze map.

### <a name="run-terraform-init-command"></a>De Terraform-opdracht *init* uitvoeren

1. Start Visual Studio Code.

1. Ga naar de menubalk van Visual Studio Code, selecteer **Bestand > Map openen...** en zoek en selecteer het bestand *main.tf*.

    ![Het bestand main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Selecteer **Weergeven > Opdrachtenpalet... > Azure Terraform: init** in de menubalk.

1. Zodra de bevestiging wordt weergegeven, selecteert u **OK**.

    ![Wilt u Cloud Shell openen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. De eerste keer dat u Cloud Shell vanuit een nieuwe map Start, wordt u gevraagd een webtoepassing te maken. Selecteer **Openen**.

    ![Eerste start van Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. De pagina Welkom bij Azure Cloud Shell wordt geopend. Selecteer Bash of PowerShell.

    ![Welkom bij Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >In dit voorbeeld is Bash (Linux) geselecteerd.

1. Als u nog geen Azure-opslagaccount hebt ingesteld, wordt het volgende scherm weergegeven. Selecteer **Opslag maken**.

    ![U hebt geen opslag gekoppeld](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell wordt in de geselecteerde shell geopend en er wordt informatie weergegeven voor de cloudschijf die zojuist voor u is gemaakt.

    ![Uw cloudschijf is gemaakt](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. U kunt Cloud Shell nu afsluiten

1. Selecteer **Weergeven** > **Opdrachtenpalet** > **Azure Terraform: init** in de menubalk.

    ![Terraform is geïnitialiseerd](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Het plan visualiseren

Eerder in deze zelfstudie hebt u GraphViz geïnstalleerd. Terraform kan GraphViz gebruiken om een visuele representatie te geven van ofwel een configuratie- of een uitvoeringsplan. Met de Azure Terraform Visual Studio Code-extensie wordt deze functie geïmplementeerd via de opdracht *visualize*.

- Selecteer **Weergeven > Opdrachtenpalet > Azure Terraform: Visualize** in de menubalk.

    ![Het plan visualiseren](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>De Terraform-opdracht *plan* uitvoeren

De Terraform-opdracht *plan* wordt gebruikt om te controleren of het uitvoeringsplan voor een reeks wijzigingen doet wat u ervan verwacht.

>[!NOTE]
>De Terraform-opdracht *plan* brengt geen wijzigingen aan in uw daadwerkelijke Azure-resources. Als u de wijzigingen in uw plan daadwerkelijk wilt doorvoeren, gebruiken we de Terraform-opdracht *apply*.

- Selecteer **Weergeven** > **Opdrachtenpalet** > **Azure Terraform: plan** in de menubalk.

    ![Terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>De Terraform-opdracht *apply* uitvoeren

Als u tevreden bent met de resultaten van de Terraform-opdracht *plan*, kunt u de opdracht *apply* uitvoeren.

1. Selecteer **Weergeven** > **Opdrachtenpalet** > **Azure Terraform: apply** in de menubalk.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Voer `yes` in.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Controleren of Terraform plan is uitgevoerd

U kunt als volgt zien of uw nieuwe Azure-resourcegroep is gemaakt:

1. Open Azure Portal.

1. Selecteer **Resourcegroepen** in het linkernavigatievenster.

    ![Uw nieuwe resource bevestigen](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Uw nieuwe resourcegroep moet worden vermeld in de kolom **NAAM**.

>[!NOTE]
>U kunt het Azure Portal-venster voorlopig geopend laten; dit gebruiken we in de volgende stap.

### <a name="run-terraform-destroy-command"></a>De Terraform-opdracht *destroy* uitvoeren

1. Selecteer **Weergeven** > **Opdrachtenpalet** > **Azure Terraform: destroy** in de menubalk.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Voer *yes* in.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Controleren of uw resourcegroep is vernietigd

U kunt als volgt bevestigen dat Terraform uw nieuwe resourcegroep heeft vernietigd:

1. Selecteer **Vernieuwen** op de pagina **Resourcegroepen** in Azure Portal.

1. Uw resourcegroep wordt niet langer vermeld.

    ![Controleren of uw resourcegroep is vernietigd](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Oefening 2: de Terraform-module *compute*

In deze oefening leert u hoe u de Terraform-module *compute* in de Visual Studio Code-omgeving laadt.

### <a name="clone-the-terraform-azurerm-compute-module"></a>De module terraform-azurerm-compute klonen

1. Gebruik [deze koppeling](https://github.com/Azure/terraform-azurerm-compute) voor toegang tot de module Terraform Azure Rm Compute in GitHub.

1. Selecteer **Klonen of downloaden**.

    ![Klonen of downloaden](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >In dit voorbeeld heeft onze map de naam *terraform-azurerm-compute* gekregen.

### <a name="open-the-folder-in-visual-studio-code"></a>De map openen in Visual Studio Code

1. Start Visual Studio Code.

1. Selecteer **Bestand > Map openen** in de menubalk en navigeer naar en selecteer de map die u hebt in de vorige stap hebt gemaakt.

    ![De map terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Terraform initialiseren

Voordat u de Terraform-opdrachten kunt gebruiken vanuit Visual Studio Code, moet u de invoegtoepassingen voor twee Azure-providers downloaden: random en azurerm.

1. Voer `terraform init` in het terminalvenster van de Visual Studio Code IDE in.

    ![De opdracht terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Voer `az login` in, druk op **<Invoeren** en volg de instructies op het scherm.

### <a name="module-test-lint"></a>Moduletest: *lint*

1. Selecteer **Weergeven > Opdrachtenpalet > Azure Terraform: Execute Test** in de menubalk.

1. Selecteer **lint** in de lijst met opties voor testtypen.

    ![Testtype selecteren](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Als de bevestiging verschijnt, selecteert u **OK**, en volgt u de aanwijzingen op het scherm.

    ![Wilt u CloudShell openen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Wanneer u de test **lint** of **end-to-end** uitvoert, wordt door Azure een containerservice gebruikt om een testmachine in te richten om de daadwerkelijke test uit te voeren. Hierdoor worden uw testresultaten doorgaans binnen enkele minuten geretourneerd.

Na enkele ogenblikken ziet u een lijst in het Terminal-deelvenster dat vergelijkbaar is met het volgende voorbeeld:

![Resulaten van lint-test](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>De module testen

1. Selecteer **Weergeven > Opdrachtenpalet > Azure Terraform: Execute Test** in de menubalk.

1. Selecteer **end-to-end** in de lijst met opties voor testtypen.

    ![Testtype selecteren](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Als de bevestiging verschijnt, selecteert u **OK**, en volgt u de aanwijzingen op het scherm.

    ![Wilt u CloudShell openen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Wanneer u de test **lint** of **end-to-end** uitvoert, wordt door Azure een containerservice gebruikt om een testmachine in te richten om de daadwerkelijke test uit te voeren. Hierdoor worden uw testresultaten doorgaans binnen enkele minuten geretourneerd.

Na enkele ogenblikken ziet u een lijst in het Terminal-deelvenster dat vergelijkbaar is met het volgende voorbeeld:

![Testresultaten](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Lijst met de Terraform-modules die beschikbaar zijn voor Azure (en andere ondersteunde providers)](https://registry.terraform.io/)