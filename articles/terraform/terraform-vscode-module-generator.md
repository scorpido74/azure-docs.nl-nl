---
title: Zelfstudie - Een terraformbasissjabloon maken in Azure met Yeoman
description: Leer hoe u een Terraform-basissjabloon maakt in Azure met behulp van Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472142"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Zelfstudie: Een terraformbasissjabloon maken in Azure met Yeoman

In deze tutorial leer je hoe je de combinatie van [Terraform](/azure/terraform/) en [Yeoman](https://yeoman.io/)gebruiken. Terraform is een hulpmiddel voor het maken van infrastructuur op Azure. Yeoman maakt het eenvoudig om Terraform modules te maken.

In dit artikel leert u hoe u de volgende taken uitvoert:
> [!div class="checklist"]
> * Maak een basis Sjabloon Terraform met behulp van de Yeoman-modulegenerator.
> * Test de Terraform-sjabloon met twee verschillende methoden.
> * Voer de Terraform-module uit met een Docker-bestand.
> * Voer de Terraform-module native uit in Azure Cloud Shell.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Visual Studio Code**: [Download Visual Studio Code](https://code.visualstudio.com/download) voor uw platform.
- **Terraform**: [Installeer Terraform](terraform-install-configure.md) om de module gemaakt door Yeoman uit te voeren.
- **Docker**: [Installeer Docker](https://www.docker.com/get-started) om de module die door de Yeoman-generator is gemaakt, uit te voeren.
- **Ga programmeertaal**: [Installeer Go](https://golang.org/) als Yeoman-gegenereerde testcases zijn code met behulp van de Go-taal.

>[!NOTE]
>De meeste procedures in deze zelfstudie hebben betrekking op de interface van de opdrachtregel. De beschreven stappen zijn van toepassing op alle besturingssystemen en opdrachtregelgereedschappen. Voor de voorbeelden is PowerShell gekozen voor de lokale omgeving en Git Bash voor de cloud shell omgeving.

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

### <a name="install-nodejs"></a>Node.js installeren

Als u Terraform in de Cloud Shell wilt gebruiken, moet u [Node.js](https://nodejs.org/en/download/) 6.0+ installeren.

>[!NOTE]
>Als u wilt controleren of Node.js is geïnstalleerd, opent u een terminalvenster en voert u `node --version` in.

### <a name="install-yeoman"></a>Yeoman installeren

Voer de volgende opdracht uit:

```bash
npm install -g yo
```

![Yeoman installeren](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>De Yeoman-sjabloon voor de Terraform-module installeren

Voer de volgende opdracht uit:

```bash
npm install -g generator-az-terra-module
```

![Generator-az-terra-module installeren](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Voer de volgende opdracht uit om te controleren of Yeoman is geïnstalleerd:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Een map maken voor de door Yeoman gegenereerde module

De Yeoman-sjabloon genereert bestanden in de huidige map. Daarom moet u een map maken.

Deze lege map moet onder $GOPATH/src worden geplaatst. Zie het artikel [GOPATH instellen](https://github.com/golang/go/wiki/SettingGOPATH)voor meer informatie over dit pad.

1. Navigeer naar de bovenliggende map van waaruit u een nieuwe map wilt maken.

1. Voer de volgende opdracht uit ter vervanging van de tijdelijke aanduiding. In dit voorbeeld `GeneratorDocSample` wordt een mapnaam gebruikt.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navigeer naar de nieuwe map:

    ```bash
    cd <new-directory-name>
    ```

    ![Naar de nieuwe map navigeren](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Een basismodulesjabloon maken

1. Voer de volgende opdracht uit:

    ```bash
    yo az-terra-module
    ```

1. Volg de aanwijzingen op het scherm om de volgende informatie op te geven:

    - **Terraform module project Naam** `doc-sample-module` - Een waarde van wordt gebruikt voor het voorbeeld.

        ![Projectnaam](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Wilt u het Docker-installatiekopiebestand opnemen?** - `y`Voer . Als u `n`de gegenereerde modulecode selecteert, wordt deze alleen in de oorspronkelijke modus uitgevoerd.

        ![Docker-installatiekopiebestand opnemen?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. De inhoud van de map weergeven om de resulterende bestanden weer te geven die zijn gemaakt:

    ```bash
    ls
    ```

    ![Gemaakte bestanden weergeven](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>De gegenereerde modulecode controleren

1. Visual Studio Code starten

1. Selecteer **Bestand > Map openen** in de menubalk en selecteer de map die u hebt gemaakt.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

De volgende bestanden zijn gemaakt door de Yeoman module generator. Zie [Terratest in Terraform Modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)voor meer informatie over deze bestanden en het gebruik ervan.

- `main.tf`- Definieert `random-shuffle`een module genaamd . De input `string_list`is een . De uitvoer is het aantal permutaties.
- `variables.tf`- Definieert de invoer- en uitvoervariabelen die door de module worden gebruikt.
- `outputs.tf`- Hiermee bepaalt u wat de module uitlevert. Hier is het de waarde `random_shuffle`die wordt geretourneerd door , dat is een ingebouwde, Terraform module.
- `Rakefile`- Definieert de bouwstappen. Deze stappen omvatten:
    - `build`- Valideert de opmaak van het main.tf bestand.
    - `unit`- Het gegenereerde moduleskelet bevat geen code voor een eenheidstest. Als u een scenario voor een eenheidstest wilt opgeven, moet u hier code toevoegen.
    - `e2e`- Voert een end-to-end test van de module uit.
- `test`
    - Testcases worden geschreven in Go.
    - Alle codes in test zijn end-to-end-tests.
    - End-to-end tests proberen alle onder `fixture`. De resultaten `template_output.go` in het bestand worden vergeleken met de vooraf gedefinieerde verwachte waarden.
    - `Gopkg.lock`en `Gopkg.toml`: Definieert de afhankelijkheden. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>De nieuwe Terraform-module testen met behulp van een Docker-bestand

In deze sectie ziet u hoe u een Terraform-module testen met behulp van een Docker-bestand.

>[!NOTE]
>In dit voorbeeld wordt de module lokaal uitgevoerd; niet op Azure.

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

    ![Bericht dat een geslaagde build aangeeft](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Voer in de `docker image ls` opdrachtprompt de `terra-mod-example` lijst in om de gemaakte module te bekijken.

    ![Lijst met de nieuwe module](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Voer `docker run -it terra-mod-example /bin/sh` in. Nadat u `docker run` de opdracht hebt uitgevoerd, bevindt u zich in de Docker-omgeving. Op dat moment u het `ls` bestand ontdekken met behulp van de opdracht.

    ![Bestandslijst in Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>De module bouwen

1. Voer de volgende opdracht uit:

    ```bash
    bundle install
    ```

1. Voer de volgende opdracht uit:

    ```bash
    rake build
    ```

    ![Rake-build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>De end-to-end-test uitvoeren

1. Voer de volgende opdracht uit:

    ```bash
    rake e2e
    ```

1. Na enkele ogenblikken wordt het bericht **GESLAAGD** weergegeven.

    ![GESLAAGD](media/terraform-vscode-module-generator/ymg-pass.png)

1. Voer `exit` in om de test te voltooien en de Docker-omgeving te verlaten.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Yeoman-generator gebruiken om een module te maken en te testen in Cloud Shell

In deze sectie wordt de Yeoman-generator gebruikt om een module in Cloud Shell te maken en te testen. Door Cloud Shell te gebruiken in plaats van een Docker-bestand wordt het proces sterk vereenvoudigd. Met Cloud Shell zijn de volgende producten allemaal vooraf geïnstalleerd:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Een Cloud Shell-sessie starten

1. Start een Azure Cloud Shell-sessie in de [Azure-portal](https://portal.azure.com/), op [shell.azure.com](https://shell.azure.com) of in de [mobiele Azure-app](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **De** pagina Welkom bij Azure Cloud Shell wordt geopend. Selecteer **Bash (Linux)**.

    ![Welkom bij Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Als u nog geen Azure-opslagaccount hebt ingesteld, wordt het volgende scherm weergegeven. Selecteer **Opslag maken**.

    ![U hebt geen opslag gekoppeld](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell wordt in de geselecteerde shell geopend en er wordt informatie weergegeven voor de cloudschijf die zojuist voor u is gemaakt.

    ![Uw cloudschijf is gemaakt](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Een map voorbereiden om uw Terraform-module vast te houden

1. Bij dit punt aangekomen is GOPATH in Cloud Shell al voor u geconfigureerd in de omgevingsvariabelen. Voer `go env` in om het pad te zien.

1. De $GOPATH-map maken als deze nog `mkdir ~/go`niet bestaat: Enter .

1. Maak een map in de $GOPATH map. Deze map wordt gebruikt om de verschillende projectmappen die in dit voorbeeld zijn gemaakt, vast te houden. 

    ```bash
    mkdir ~/go/src
    ```

1. Maak een map om uw Terraform-module te houden ter vervanging van de tijdelijke aanduiding. In dit voorbeeld `my-module-name` wordt een mapnaam gebruikt.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navigeer naar uw modulemap: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>De Terraform-module maken en testen

1. Voer de volgende opdracht uit en volg de instructies. Wanneer u wordt gevraagd of u de `N`Docker-bestanden wilt maken, voert u .

    ```bash
    yo az-terra-module
    ```

1. Voer de volgende opdracht uit om de afhankelijkheden te installeren:

    ```bash
    bundle install
    ```

1. Voer de volgende opdracht uit om de module te bouwen:

    ```bash
    rake build
    ```

    ![Rake-build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Voer de volgende opdracht uit om de tekst uit te voeren:

    ```bash
    rake e2e
    ```

    ![Test-pass resultaten](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Installeer en gebruik de Azure Terraform Visual Studio Code-extensie](/azure/terraform/terraform-vscode-extension).