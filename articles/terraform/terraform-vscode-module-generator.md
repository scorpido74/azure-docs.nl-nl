---
title: Een Terraform-basissjabloon maken in Azure met behulp van Yeoman
description: Leer hoe u een Terraform-basissjabloon maakt in Azure met behulp van Yeoman.
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7a628eb02170346a826cab19498d6fdf40cebddd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173387"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Een Terraform-basissjabloon maken in Azure met behulp van Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) biedt een manier om eenvoudig een infrastructuur te maken in Azure. [Yeoman](https://yeoman.io/) vereenvoudigt de taak van de module-ontwikkelaar enorm wat betreft het maken van Terraform-modules, en biedt daarnaast een uitstekend *best practices*-framework.

In dit artikel leert u hoe u de modulegenerator van Yeoman gebruikt om een Terraform-basissjabloon te maken. Vervolgens leert u hoe u de nieuwe Terraform-sjabloon kunt testen met behulp van twee verschillende methoden:

- Voer de Terraform-module uit met behulp van een Docker-bestand dat u maakt in dit artikel.
- Voer de Terraform-module geïntegreerd uit in Azure Cloud Shell.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Visual Studio Code**: We gebruiken [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) om bestanden te onderzoeken die zijn gemaakt door de Yeoman-generator. U kunt echter ook een andere code-editor gebruiken.
- **Terraform**: U hebt een installatie van [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) nodig om de module uit te voeren die door Yeoman is gemaakt.
- **Docker**: We gebruiken [Docker](https://www.docker.com/get-started) om de module uit te voeren die door de Yeoman-generator is gemaakt. (Als u dat wilt, mag u ook Ruby in plaats van Docker gebruiken om de voorbeeldmodule uit te voeren.)
- **Go-programmeertaal**: U hebt een installatie van [Go](https://golang.org/) nodig omdat de testscenario's die door Yeoman zijn gegenereerd, zijn geschreven in Go.

>[!NOTE]
>De meeste procedures in deze zelfstudie hebben betrekking op opdrachtregelvermeldingen. De hier beschreven stappen zijn van toepassing op alle besturingssystemen en opdrachtregelprogramma's. In de voorbeelden hebben we ervoor gekozen om PowerShell te gebruiken voor de lokale omgeving en Git Bash voor de Cloud Shell-omgeving.

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

### <a name="install-nodejs"></a>Node.js installeren

Als u Terraform in Cloud Shell wilt gebruiken, moet u [Node.js](https://nodejs.org/en/download/) 6.0+ installeren.

>[!NOTE]
>Als u wilt controleren of Node.js is geïnstalleerd, opent u een terminalvenster en voert u `node --version` in.

### <a name="install-yeoman"></a>Yeoman installeren

Voer `npm install -g yo` in vanaf een opdrachtprompt

![Yeoman installeren](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>De Yeoman-sjabloon voor de Terraform-module installeren

Voer `npm install -g generator-az-terra-module` in vanaf een opdrachtprompt.

![Generator-az-terra-module installeren](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Als u wilt controleren of Yeoman is geïnstalleerd, voert u in een terminalvenster `yo --version` in.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Een lege map maken om de door Yeoman gegenereerde module in op te slaan

De Yeoman-sjabloon genereert bestanden in de **huidige map**. Daarom moet u een map maken.

>[!Note]
>Deze lege map moet onder $GOPATH/src worden geplaatst. De instructies om dit te doen, vindt u [hier](https://github.com/golang/go/wiki/SettingGOPATH).

Vanaf een opdrachtprompt:

1. Navigeer naar de bovenliggende map waaronder u de nieuwe, lege map wilt maken.
1. Voer `mkdir <new-directory-name>` in.

    > [!NOTE]
    > Vervang `<new-directory-name>` door de naam van de nieuwe map. In dit voorbeeld hebben we de nieuwe map `GeneratorDocSample` genoemd.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navigeer naar de nieuwe map door `cd <new directory's name>` te typen en op **Enter** te drukken.

    ![Naar de nieuwe map navigeren](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Als u wilt controleren of deze map is leeg, voert u `ls` in. Er mogen geen bestanden worden weergegeven in de resulterende uitvoer van deze opdracht.

## <a name="create-a-base-module-template"></a>Een basismodulesjabloon maken

Vanaf een opdrachtprompt:

1. Voer `yo az-terra-module` in.

1. Volg de aanwijzingen op het scherm om de volgende informatie op te geven:

    - *Projectnaam van Terraform-module*

        ![Projectnaam](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >In dit voorbeeld hebben we `doc-sample-module` ingevoerd.

    - *Wilt u het Docker-installatiekopiebestand opnemen?*

        ![Docker-installatiekopiebestand opnemen?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Voer `y` in. Als u **n** selecteert, biedt de gegenereerde modulecode alleen ondersteuning voor uitvoering in de systeemeigen modus.

3. Voer `ls` in om de resulterende bestanden weer te geven die zijn gemaakt.

    ![Gemaakte bestanden weergeven](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>De gegenereerde modulecode controleren

1. Visual Studio Code starten

1. Selecteer **Bestand > Map openen** in de menubalk en selecteer de map die u hebt gemaakt.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Nu gaan we enkele bestanden bekijken die door de Yeoman-modulegenerator zijn gemaakt.

>[!Note]
>In dit artikel gebruiken we de bestanden main.tf, variables.tf en outputs.tf die door de Yeoman-modulegenerator zijn gemaakt. Als u uw eigen modules maakt, moet u deze bestanden echter aanpassen aan de functionaliteit van uw Terraform-module. Zie [Terratest in Terraform-modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) voor een uitgebreidere bespreking van deze bestanden en hun gebruik.

### <a name="maintf"></a>main.tf

Definieert een module met de naam *random-shuffle*. De invoer is een *string_list*. De uitvoer is het aantal permutaties.

### <a name="variablestf"></a>variables.tf

Definieert de invoer- en uitvoervariabelen die door de module worden gebruikt.

### <a name="outputstf"></a>outputs.tf

Definieert het resultaat van de module. Hier is het de waarde die wordt geretourneerd door **random_shuffle**, een ingebouwde Terraform-module.

### <a name="rakefile"></a>Rakefile

Definieert de buildstappen. Deze stappen omvatten:

- **build**: Hiermee wordt de indeling van het bestand main.tf gevalideerd.
- **eenheid**: Het gegenereerde moduleraamwerk bevat geen code voor een eenheidstest. Als u een scenario voor een eenheidstest wilt opgeven, moet u hier code toevoegen.
- **e2e**: Hiermee wordt een end-to-end-test van de module uitgevoerd.

### <a name="test"></a>test

- Testcases worden geschreven in Go.
- Alle codes in test zijn end-to-end-tests.
- End-to-end-tests proberen Terraform te gebruiken om alle items in te richten die zijn gedefinieerd onder **aansluiting** en vergelijken vervolgens de uitvoer in de code **template_output.go** met de vooraf gedefinieerde verwachte waarden.
- **Gopkg.lock** en **Gopkg.toml**: Definieer uw afhankelijkheden. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>De nieuwe Terraform-module testen met behulp van een Docker-bestand

>[!NOTE]
>In ons voorbeeld voeren we de module uit als een lokale module, dus buiten Azure om.

### <a name="confirm-docker-is-installed-and-running"></a>Controleren of Docker is geïnstalleerd en wordt uitgevoerd

Voer `docker version` in vanaf een opdrachtprompt.

![Docker-versie](media/terraform-vscode-module-generator/ymg-docker-version.png)

De resulterende uitvoer bevestigt dat Docker is geïnstalleerd.

Als u wilt controleren of Docker werkelijk wordt uitgevoerd, typt u `docker info`.

![Docker-info](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Een Docker-container instellen

1. Voer vanaf een opdrachtprompt het volgende in:

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Het bericht **Gemaakt** wordt weergegeven.

    ![Gemaakt](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Voer vanaf de opdrachtregel `docker image ls` in.

    Nu wordt de zojuist gemaakte module *terra-mod-example* vermeld.

    ![Resultaten van de opslagplaats](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >De naam van de module, *terra-mod-example*, is opgegeven in de opdracht die u in stap 1 hierboven hebt ingevoerd.

1. Voer `docker run -it terra-mod-example /bin/sh` in.

    U werkt nu in Docker en u kunt het bestand weergeven door `ls` in te voeren.

    ![Docker-bestand weergeven](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>De module bouwen

1. Voer `bundle install` in.

    Wacht tot het bericht **Bundel voltooid** wordt weergegeven en ga dan verder met de volgende stap.

1. Voer `rake build` in.

    ![Rake-build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>De end-to-end-test uitvoeren

1. Voer `rake e2e` in.

1. Na enkele ogenblikken wordt het bericht **GESLAAGD** weergegeven.

    ![GESLAAGD](media/terraform-vscode-module-generator/ymg-pass.png)

1. Voer `exit` in om de end-to-end-test te voltooien en sluit de Docker-omgeving.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Yeoman-generator gebruiken om een module te maken en te testen in Cloud Shell

In de vorige sectie hebt u geleerd hoe u een Terraform-module kunt testen met behulp van een Docker-bestand. In deze sectie gebruikt u de Yeoman-generator om een module te maken en te testen in Cloud Shell.

Door Cloud Shell te gebruiken in plaats van een Docker-bestand wordt het proces sterk vereenvoudigd. Cloud Shell gebruiken:

- U hoeft Node.jsnniet te installeren
- U hoeft Yeoman niet te installeren
- U hoeft Terraform niet te installeren

Al deze items zijn vooraf geïnstalleerd in Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Een Cloud Shell-sessie starten

1. Start een Azure Cloud Shell-sessie in de [Azure-portal](https://portal.azure.com/), op [shell.azure.com](https://shell.azure.com) of in de [mobiele Azure-app](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. De pagina **Welkom bij Azure Cloud Shell** wordt geopend. Selecteer **Bash (Linux)** . (PowerShell wordt niet ondersteund.)

    ![Welkom bij Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >In dit voorbeeld is Bash (Linux) geselecteerd.

1. Als u nog geen Azure-opslagaccount hebt ingesteld, wordt het volgende scherm weergegeven. Selecteer **Opslag maken**.

    ![U hebt geen opslag gekoppeld](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell wordt in de geselecteerde shell geopend en er wordt informatie weergegeven voor de cloudschijf die zojuist voor u is gemaakt.

    ![Uw cloudschijf is gemaakt](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Een map voorbereiden om de Terraform-module in op te slaan

1. Bij dit punt aangekomen is GOPATH in Cloud Shell al voor u geconfigureerd in de omgevingsvariabelen. Voer `go env` in om het pad te zien.

1. Maak de map $GOPATH als deze nog niet bestaat: Voer `mkdir ~/go` in.

1. Maak een map in de map $GOPATH: Voer `mkdir ~/go/src` in. Deze map wordt gebruikt voor het bewaren en organiseren van verschillende project mappen die u kunt maken, zoals `<your-module-name>` de map die in de volgende stap wordt gemaakt.

1. Maak een map om de Terraform-module in op te slaan: Voer `mkdir ~/go/src/<your-module-name>` in.

    >[!NOTE]
    >In dit voorbeeld hebben we `my-module-name` gekozen als mapnaam.

1. Ga naar de modulemap: Voer `cd ~/go/src/<your-module-name>` in

### <a name="create-and-test-your-terraform-module"></a>De Terraform-module maken en testen

1. Voer `yo az-terra-module` in en volg de instructies in de wizard.

    >[!NOTE]
    >Wanneer u wordt gevraagd of u de Docker-bestanden wilt maken, kunt u `N` invoeren.

1. Voer `bundle install` in om de afhankelijkheden te installeren.

    Wacht tot het bericht **Bundel voltooid** wordt weergegeven en ga dan verder met de volgende stap.

1. Voer `rake build` in om de module te bouwen.

    ![Rake-build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Voer `rake e2e` in om de end-to-end-test uit te voeren.

1. Na enkele ogenblikken wordt het bericht **GESLAAGD** weergegeven.

    ![GESLAAGD](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De Azure Terraform Visual Studio Code-extensie installeren en gebruiken.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
