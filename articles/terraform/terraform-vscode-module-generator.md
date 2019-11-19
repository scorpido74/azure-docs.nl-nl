---
title: 'Zelf studie: een terraform-basis sjabloon maken in azure met behulp van Yeoman'
description: Leer hoe u een Terraform-basissjabloon maakt in Azure met behulp van Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159161"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Zelf studie: een terraform-basis sjabloon maken in azure met behulp van Yeoman

In deze zelf studie leert u hoe u de combi natie van [terraform](/azure/terraform/) en [Yeoman](https://yeoman.io/)gebruikt. Terraform is een hulp programma voor het maken van een infra structuur op Azure. Yeoman maakt het eenvoudig om terraform-modules te maken.

In dit artikel leert u hoe u de volgende taken kunt uitvoeren:
> [!div class="checklist"]
> * Maak een basis terraform-sjabloon met behulp van de Yeoman-module generator.
> * Test de terraform-sjabloon met twee verschillende methoden.
> * Voer de terraform-module uit met behulp van een docker-bestand.
> * Voer de terraform-module in een eigen naam uit Azure Cloud Shell.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Visual Studio code**: [down load Visual Studio code](https://code.visualstudio.com/download) voor uw platform.
- **Terraform**: [Installeer terraform](/azure/virtual-machines/linux/terraform-install-configure ) om de module die is gemaakt door Yeoman, uit te voeren.
- **Docker**: [Installeer docker](https://www.docker.com/get-started) om de module uit te voeren die door de Yeoman-Generator is gemaakt.
- **Go-programmeer taal**: [Installeer go](https://golang.org/) als door Yeoman gegenereerde test cases zijn code die de go-taal gebruikt.

>[!NOTE]
>De meeste procedures in deze zelf studie hebben betrekking op de opdracht regel interface. De stappen die worden beschreven, zijn van toepassing op alle besturings systemen en opdracht regel Programma's. Voor de voor beelden is Power shell gekozen voor de lokale omgeving en git bash voor de Cloud shell-omgeving.

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

Als u wilt controleren of Yeoman is geïnstalleerd, voert u de volgende opdracht uit:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Een map maken voor de Yeoman-gegenereerde module

De Yeoman-sjabloon genereert bestanden in de huidige map. Daarom moet u een map maken.

Deze lege map moet onder $GOPATH/src worden geplaatst. Zie het artikel [GOPATH instellen](https://github.com/golang/go/wiki/SettingGOPATH)voor meer informatie over dit pad.

1. Ga naar de bovenliggende map van waaruit u een nieuwe map wilt maken.

1. Voer de volgende opdracht uit om de tijdelijke aanduiding te vervangen. In dit voor beeld wordt een directory naam van `GeneratorDocSample` gebruikt.

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

    - **Terraform-module Project naam** : er wordt een waarde van `doc-sample-module` gebruikt voor het voor beeld.

        ![Projectnaam](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Wilt u het Docker-installatiekopiebestand opnemen?** -Voer `y`in. Als u `n`selecteert, wordt de gegenereerde module code alleen ondersteund in de native modus.

        ![Docker-installatiekopiebestand opnemen?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. De inhoud van de map weer geven om de resulterende bestanden te bekijken die worden gemaakt:

    ```bash
    ls
    ```

    ![Gemaakte bestanden weergeven](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>De gegenereerde modulecode controleren

1. Visual Studio Code starten

1. Selecteer **Bestand > Map openen** in de menubalk en selecteer de map die u hebt gemaakt.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

De volgende bestanden zijn gemaakt door de Yeoman-module generator. Zie [Terratest in terraform-modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)voor meer informatie over deze bestanden en hun gebruik.

- `main.tf`: definieert een module met de naam `random-shuffle`. De invoer is een `string_list`. De uitvoer is het aantal permutaties.
- `variables.tf`-definieert de invoer-en uitvoer variabelen die worden gebruikt door de module.
- `outputs.tf`-definieert wat de module-uitvoer is. Hier is de waarde die wordt geretourneerd door `random_shuffle`, een ingebouwde terraform-module.
- `Rakefile`-definieert de stappen voor het bouwen. Deze stappen omvatten:
    - `build`-de opmaak van het main.tf-bestand valideren.
    - `unit`-het gegenereerde module skelet bevat geen code voor een eenheids test. Als u een scenario voor een eenheidstest wilt opgeven, moet u hier code toevoegen.
    - `e2e`-een end-to-end-test van de module uitvoeren.
- `test`
    - Testcases worden geschreven in Go.
    - Alle codes in test zijn end-to-end-tests.
    - End-to-end-tests proberen alle items in te richten die zijn gedefinieerd onder `fixture`. De resultaten in het `template_output.go`-bestand worden vergeleken met de vooraf gedefinieerde verwachte waarden.
    - `Gopkg.lock` en `Gopkg.toml`: Hiermee worden de afhankelijkheden gedefinieerd. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>De nieuwe Terraform-module testen met behulp van een Docker-bestand

In deze sectie wordt uitgelegd hoe u een terraform-module kunt testen met behulp van een docker-bestand.

>[!NOTE]
>In dit voor beeld wordt de module lokaal uitgevoerd; niet in Azure.

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

1. Voer `docker image ls` vanaf de opdracht prompt in om de gemaakte module `terra-mod-example` weer te geven.

    ![Lijst met de nieuwe module](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Voer `docker run -it terra-mod-example /bin/sh` in. Nadat u de `docker run` opdracht hebt uitgevoerd, bevindt u zich in de docker-omgeving. Op dat moment kunt u het bestand detecteren met behulp van de `ls` opdracht.

    ![Lijst met bestanden in docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

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

1. Voer `exit` in om de test te volt ooien en de docker-omgeving af te sluiten.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Yeoman-generator gebruiken om een module te maken en te testen in Cloud Shell

In deze sectie wordt de Yeoman-generator gebruikt voor het maken en testen van een module in Cloud Shell. Door Cloud Shell te gebruiken in plaats van een Docker-bestand wordt het proces sterk vereenvoudigd. Op basis van Cloud Shell zijn de volgende producten vooraf geïnstalleerd:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Een Cloud Shell-sessie starten

1. Start een Azure Cloud Shell-sessie in de [Azure-portal](https://portal.azure.com/), op [shell.azure.com](https://shell.azure.com) of in de [mobiele Azure-app](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. De pagina **Welkom bij Azure Cloud Shell** wordt geopend. Selecteer **Bash (Linux)** .

    ![Welkom bij Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Als u nog geen Azure-opslagaccount hebt ingesteld, wordt het volgende scherm weergegeven. Selecteer **Opslag maken**.

    ![U hebt geen opslag gekoppeld](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell wordt in de geselecteerde shell geopend en er wordt informatie weergegeven voor de cloudschijf die zojuist voor u is gemaakt.

    ![Uw cloudschijf is gemaakt](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Een directory voorbereiden om uw terraform-module op te slaan

1. Bij dit punt aangekomen is GOPATH in Cloud Shell al voor u geconfigureerd in de omgevingsvariabelen. Voer `go env` in om het pad te zien.

1. Maak de $GOPATH Directory, als deze nog niet bestaat: Voer `mkdir ~/go`in.

1. Maak een map in de $GOPATH Directory. Deze map wordt gebruikt voor het opslaan van de verschillende project directory's die in dit voor beeld zijn gemaakt. 

    ```bash
    mkdir ~/go/src
    ```

1. Maak een map voor de terraform-module waarin de tijdelijke aanduiding wordt vervangen. In dit voor beeld wordt een directory naam van `my-module-name` gebruikt.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navigeer naar uw module directory: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>De Terraform-module maken en testen

1. Voer de volgende opdracht uit en volg de instructies. Wanneer u wordt gevraagd of u de docker-bestanden wilt maken, voert u `N`in.

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

    ![Test resultaten](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Installeer en gebruik de code-extensie van Azure terraform Visual Studio](/azure/terraform/terraform-vscode-extension).