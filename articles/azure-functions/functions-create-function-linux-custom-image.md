---
title: Azure Functions op Linux maken met behulp van een aangepaste installatie kopie
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: edb947f0748c186e146bce5f4dbe9d0b95a2568d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846481"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Een functie in Linux maken met behulp van een aangepaste container

In deze zelf studie maakt en implementeert u Python-code om Azure Functions als een aangepaste docker-container met behulp van een Linux-basis installatie kopie. Normaal gesp roken gebruikt u een aangepaste installatie kopie wanneer uw functies een specifieke taal versie vereisen of een specifieke afhankelijkheid of configuratie hebben die niet wordt opgegeven door de ingebouwde installatie kopie.

U kunt ook een standaard Azure App Service-container gebruiken zoals wordt beschreven in [uw eerste functie maken die wordt gehost op Linux](functions-create-first-azure-function-azure-cli-linux.md). Ondersteunde basis installatie kopieën voor Azure Functions vindt u in de [Azure functions basis installatie kopieën opslag plaats](https://hub.docker.com/_/microsoft-azure-functions-base).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een functie-app en Dockerfile met behulp van de Azure Functions Core Tools.
> * Een aangepaste installatiekopie met behulp van Docker bouwen.
> * Een aangepaste installatiekopie naar een containerregister publiceren.
> * Ondersteunende resources in azure maken voor de functie-app
> * Een functie-app vanuit Docker Hub implementeren.
> * Toepassingsinstellingen toevoegen aan de functie-app.
> * Continue implementatie inschakelen.
> * Schakel SSH-verbindingen met de container in.
> * Voeg een uitvoer binding voor de wachtrij opslag toe. 

U kunt deze zelf studie volgen op elke computer waarop Windows, Mac OS of Linux wordt uitgevoerd. Als u de zelf studie voltooit, worden er kosten in rekening gebracht voor een of meer Amerikaanse dollars in uw Azure-account.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- De [Azure functions core tools](./functions-run-local.md#v2) versie 2.7.1846 of hoger
- De [Azure cli](/cli/azure/install-azure-cli) -versie 2.0.77 of hoger
- De [Azure functions 2. x runtime](functions-versions.md)
- De volgende language runtime-onderdelen:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2. x of hoger](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3,6-64 bits](https://www.python.org/downloads/release/python-3610/) of [Python 3,7-64-bits](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Een [docker-id](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Controle van vereisten

1. Voer `func --version` in een Terminal-of opdracht venster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger is.
1. Voer `az --version` uit om te controleren of de versie van de Azure CLI 2.0.76 of hoger is.
1. Voer `az login` uit om u aan te melden bij Azure en een actief abonnement te verifiëren.
1. Voer `docker login` uit om u aan te melden bij docker. Deze opdracht mislukt als docker niet wordt uitgevoerd. in dat geval wordt docker gestart en voert de opdracht opnieuw uit.

## <a name="create-and-test-the-local-functions-project"></a>Het project met lokale functies maken en testen

1. Maak in een Terminal-of opdracht prompt een map voor deze zelf studie op een geschikte locatie en navigeer vervolgens naar die map.

1. Volg de instructies voor het [maken en activeren van een virtuele omgeving](functions-create-first-function-python.md#create-and-activate-a-virtual-environment) om een virtuele omgeving te maken voor gebruik met deze zelf studie.

1. Voer de volgende opdracht uit voor de taal die u hebt gekozen om een functie-app-project te maken in een map met de naam `LocalFunctionsProject`. Met de optie `--docker` wordt een `Dockerfile` voor het project gegenereerd, waarmee een geschikte aangepaste container wordt gedefinieerd voor gebruik met Azure Functions en de geselecteerde runtime.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Navigeer naar de projectmap:

    ```
    cd LocalFunctionsProject
    ```
    
1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie specificeert. `func new` een submap te maken die overeenkomt met de naam van de functie die geschikt is voor de gekozen taal van het project en een configuratie bestand met de naam *Function. json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Als u de functie lokaal wilt testen, start u de lokale Azure Functions runtime host in de map *LocalFunctionsProject* :
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Zodra het `HttpExample`-eind punt wordt weer gegeven in de uitvoer, gaat u naar `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als ' Hello, functions ' worden weer gegeven (wat iets anders is afhankelijk van de gekozen programmeer taal).

1. Gebruik **Ctrl**-**C** om de host te stoppen.

## <a name="build-the-container-image-and-test-locally"></a>De container installatie kopie bouwen en lokaal testen

1. Beschrijving Bekijk de * Dockerfile in de map *LocalFunctionsProj* . De Dockerfile beschrijft de vereiste omgeving voor het uitvoeren van de functie-app in Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > U vindt de volledige lijst met ondersteunde basis installatie kopieën voor Azure Functions op de [pagina basis installatie kopie van Azure functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Voer in de map *LocalFunctionsProject* de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) uit en geef een naam, `azurefunctionsimage`en label op `v1.0.0`. Vervang `<docker_id>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Wanneer de opdracht is voltooid, kunt u de nieuwe container lokaal uitvoeren.
    
1. Als u de build wilt testen, voert u de installatie kopie in een lokale container met behulp van de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) uit, vervangt u opnieuw `<docker_id>` met uw DOCKER-id en voegt u het argument poorten toe, `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Zodra de installatie kopie in een lokale container wordt uitgevoerd, opent u een browser om te `http://localhost:8080`, waarin de tijdelijke aanduiding voor de afbeelding moet worden weer gegeven. De afbeelding wordt op dit moment weer gegeven omdat uw functie wordt uitgevoerd in de lokale container, zoals in azure, wat betekent dat deze wordt beveiligd door een toegangs sleutel zoals gedefinieerd in *Function. json* met de eigenschap `"authLevel": "function"`. De container is nog niet gepubliceerd naar een functie-app in azure, maar de sleutel is nog niet beschikbaar. Als u lokaal wilt testen, sluit u docker, wijzigt u de autorisatie-eigenschap op `"authLevel": "anonymous"`, bouwt u de installatie kopie opnieuw en start u docker opnieuw op. Stel `"authLevel": "function"` in *Function. json*opnieuw in. Zie [autorisatie sleutels](functions-bindings-http-webhook.md#authorization-keys)voor meer informatie.

    ![Tijdelijke afbeelding die aangeeft dat de container lokaal wordt uitgevoerd](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Nadat u de functie-app in de container hebt geverifieerd, stopt u docker met **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>De installatie kopie pushen naar docker hub

Docker hub is een container register dat installatie kopieën host en installatie kopie-en container Services biedt. Als u uw installatie kopie wilt delen, met inbegrip van implementatie naar Azure, moet u deze naar een REGI ster pushen.

1. Als u zich nog niet hebt aangemeld bij docker, doet u dit met de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) , waarbij u `<docker_id>` vervangt door de id van uw docker. Met deze opdracht wordt u gevraagd om uw gebruikers naam en wacht woord. In het bericht ' Aanmelden is geslaagd ' wordt bevestigd dat u bent aangemeld.

    ```
    docker login
    ```
    
1. Nadat u zich hebt aangemeld, pusht u de installatie kopie naar docker hub met behulp van de opdracht [docker push](https://docs.docker.com/engine/reference/commandline/push/) . vervang opnieuw `<docker_id>` met uw DOCKER-id.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Afhankelijk van de snelheid van uw netwerk, kan het enkele minuten duren voordat de installatie kopie wordt gepusht (het pushen van de volgende wijzigingen is veel sneller). Terwijl u wacht, kunt u door gaan naar de volgende sectie en Azure-resources maken in een andere terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Als u uw functie code wilt implementeren in azure, moet u drie resources maken:

- Een resource groep, een logische container voor gerelateerde resources.
- Een Azure Storage account, waarmee de status en andere informatie over uw projecten worden bijgehouden.
- Een Azure functions-app, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Een functie-app wordt toegewezen aan uw lokale functie project en biedt u de mogelijkheid om functies te groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

U gebruikt Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

1. Meld u aan bij Azure met de opdracht [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam `AzureFunctionsContainers-rg` in de `westeurope` regio gemaakt. (In het algemeen maakt u de resource groep en-resources in een regio bij u in de buurt met behulp van een beschik bare regio van de `az account list-locations`-opdracht.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met de naam `AzureFunctionsContainers-rg` met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.
    
1. Maak een opslag account voor algemeen gebruik in uw resource groep en regio met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) . Vervang `<storage_name>` in het volgende voor beeld door een globaal unieke naam die voor u van toepassing is. Namen mogen alleen uit drie tot 24 tekens bestaan en mag alleen kleine letters bevatten. `Standard_LRS` geeft een typisch algemeen account op.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Het opslag account maakt voor deze zelf studie slechts een enkele USD cent.
    
1. Gebruik de opdracht voor het maken van een Premium-plan voor Azure Functions met de naam `myPremiumPlan` in de prijs categorie **elastisch Premium 1** (`--sku EP1`), in de Europa-West regio (`-location westeurope`, of gebruik een geschikte regio bij u in de buurt), en in een Linux-container (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux-hosting voor aangepaste functies-containers wordt ondersteund op [speciale (app service) plannen](functions-scale.md#app-service-plan) en [Premium-abonnementen](functions-premium-plan.md#features). We gebruiken hier het Premium-plan, dat kan worden geschaald naar behoefte. Zie [deze vergelijking van hostingabonnementen van Azure Functions](functions-scale.md) voor meer informatie over hosting. Zie de pagina met prijzen voor [functies](https://azure.microsoft.com/pricing/details/functions/)voor het berekenen van de kosten.

    Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht, waarmee u uw functie-app kunt bewaken en logboeken weer geven. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. Er worden geen kosten in rekening gebracht totdat u deze activeert.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Een functie-app in azure maken en configureren met de installatie kopie

Een functie-app in azure beheert de uitvoering van uw functies in uw hosting abonnement. In deze sectie gebruikt u de Azure-resources uit de vorige sectie om een functie-app te maken op basis van een installatie kopie op docker hub en deze te configureren met een connection string voor Azure Storage.

1. Maak de functions-app met de opdracht [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Vervang in het volgende voor beeld `<storage_name>` door de naam die u hebt gebruikt in de vorige sectie voor het opslag account. Vervang `<app_name>` ook door een globaal unieke naam die geschikt is voor u en `<docker_id>` met uw docker-ID.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Met de para meter *implementatie-container-image-name* wordt de afbeelding opgegeven die moet worden gebruikt voor de functie-app. U kunt de opdracht [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) gebruiken om informatie weer te geven over de installatie kopie die wordt gebruikt voor de implementatie. U kunt ook de opdracht [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) gebruiken om vanaf een andere installatie kopie te implementeren.

1. Haal de connection string op voor het opslag account dat u hebt gemaakt met behulp van de opdracht [AZ Storage account show-Connection-String](/cli/azure/storage/account) en wijs deze toe aan een shell-variabele `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. De functie kan nu deze connection string gebruiken om toegang te krijgen tot het opslag account.

> [!TIP]
> In bash kunt u een shell-variabele gebruiken om de connection string vast te leggen in plaats van het klem bord te gebruiken. Gebruik eerst de volgende opdracht om een variabele te maken met de connection string:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Ga vervolgens naar de variabele in de tweede opdracht:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Als u uw aangepaste installatie kopie naar een persoonlijk container account publiceert, moet u in plaats daarvan omgevings variabelen in de Dockerfile voor de connection string gebruiken. Zie de [env-instructie](https://docs.docker.com/engine/reference/builder/#env)voor meer informatie. U moet ook de variabelen `DOCKER_REGISTRY_SERVER_USERNAME` en `DOCKER_REGISTRY_SERVER_PASSWORD`instellen. Als u de waarden wilt gebruiken, moet u de installatie kopie opnieuw opbouwen, de installatie kopie naar het REGI ster pushen en vervolgens de functie-app opnieuw starten in Azure.

## <a name="verify-your-functions-on-azure"></a>Uw functies in azure controleren

Als de installatie kopie is geïmplementeerd in de functie-app in azure, kunt u de functie nu via HTTP-aanvragen aanroepen. Omdat de definitie van de *functie. json* de eigenschap `"authLevel": "function"`bevat, moet u eerst de toegangs sleutel (ook wel de functie sleutel) verkrijgen en deze opnemen als een URL-para meter in alle aanvragen voor het eind punt.

1. Haal de functie-URL op met de Access-sleutel (functie) met behulp van de Azure Portal of gebruik de Azure CLI met de `az rest`-opdracht.)

    # <a name="portaltabportal"></a>[Portal](#tab/portal)

    1. Meld u aan bij de Azure Portal en zoek vervolgens uw functie-app door de naam van uw functie-app in te voeren in het **zoekvak** boven aan de pagina. Selecteer in de resultaten de **app service** resource.

    1. Selecteer in het navigatie paneel aan de linkerkant onder **functies (alleen-lezen)** de naam van uw functie.

    1. Selecteer in het deel venster Details **</> functie-URL ophalen**:
    
        ![De opdracht functie-URL ophalen in het Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Selecteer in de pop-up de optie **standaard (functie toets)** en **Kopieer**vervolgens. De sleutel is de teken reeks die de volgende tekens bevat `?code=`.

        ![De functie-URL uit de Azure Portal kopiëren](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Omdat uw functie-app als een container is geïmplementeerd, kunt u geen wijzigingen aanbrengen in uw functie code in de portal. U moet in plaats daarvan het project bijwerken in de lokale installatie kopie, de installatie kopie naar het REGI ster pushen en vervolgens opnieuw implementeren naar Azure. U kunt in een later stadium doorlopende implementatie instellen.
    
    # <a name="azure-clitabazurecli"></a>[Azure-CLI](#tab/azurecli)

    1. Maak een URL-teken reeks in de volgende indeling, vervang `<subscription_id>`, `<resource_group>`en `<app_name>` met de ID van uw Azure-abonnement, de resource groep van de functie-app en de naam van uw functie-app, respectievelijk:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        De URL kan bijvoorbeeld het volgende adres zien:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Voor het gemak kunt u in plaats daarvan de URL toewijzen aan een omgevings variabele en deze gebruiken in de `az rest` opdracht.
    
    1. Voer de volgende `az rest` opdracht uit (beschikbaar in de Azure CLI-versie 2.0.77 en hoger), waarbij u `<uri>` vervangt door de URI-teken reeks uit de laatste stap, inclusief de aanhalings tekens:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. De uitvoer van de opdracht is de functie toets. De volledige functie-URL wordt vervolgens `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, waarbij `<app_name>`, `<function_name>`en `<key>` worden vervangen door uw specifieke waarden.
    
        > [!NOTE]
        > De sleutel die hier wordt opgehaald is de *host* -sleutel die werkt voor alle functies in de app functions. met de methode die wordt weer gegeven voor de portal, wordt de sleutel voor de ene functie opgehaald.

    ---

1. Plak de functie-URL in de adres balk van uw browser en voeg de para meter `&name=Azure` toe aan het einde van deze URL. Tekst als ' Hello Azure ' moet worden weer gegeven in de browser.

    ![Het antwoord van de functie in de browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. U kunt de autorisatie testen door de code = para meter uit de URL te verwijderen en te controleren of u geen reactie krijgt van de functie.


## <a name="enable-continuous-deployment-to-azure"></a>Continue implementatie naar Azure inschakelen

U kunt Azure Functions instellen dat uw implementatie van een installatie kopie automatisch wordt bijgewerkt wanneer u de installatie kopie in het REGI ster bijwerkt.

1. Continue implementatie inschakelen met behulp van [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) opdracht, vervangen `<app_name>` door de naam van uw functie-app:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Met deze opdracht wordt continue implementatie ingeschakeld en wordt de URL van de implementatie webhook geretourneerd. (U kunt deze URL later op elk gewenst moment ophalen met behulp van de opdracht [AZ functionapp Deployment container show-cd-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) .)

1. Kopieer de URL van de implementatie-webhook naar het klem bord.

1. Open [docker hub](https://hub.docker.com/), Meld u aan en selecteer **opslag** plaatsen op de navigatie balk. Zoek en selecteer afbeelding, selecteer het tabblad **webhooks** , geef een **naam**op voor de webhook, plak uw URL in de **webhook-URL**en selecteer vervolgens **maken**:

    ![De webhook in uw DockerHub opslag plaats toevoegen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Als de webhook is ingesteld, Azure Functions uw installatie kopie opnieuw implementeren wanneer u deze bijwerkt in docker hub.

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als SSH is ingeschakeld, kunt u verbinding maken met uw container met behulp van App Service geavanceerde hulp middelen (kudu). Azure Functions biedt een basis installatie kopie waarvoor SSH al is ingeschakeld, zodat u eenvoudig verbinding kunt maken met uw container via SSH. U hoeft alleen uw Dockerfile te bewerken en vervolgens de installatie kopie opnieuw te bouwen en opnieuw te implementeren. U kunt vervolgens verbinding maken met de container via de geavanceerde hulp middelen (kudu)

1. Voeg in uw Dockerfile de teken reeks `-appservice` toe aan de basis installatie kopie in uw `FROM` instructie:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    De verschillen tussen de basis installatie kopieën worden beschreven in de hand leiding voor de [app Services-aangepaste docker-installatie kopieën](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Bouw de installatie kopie opnieuw met behulp van de `docker build` opdracht opnieuw, waarbij u `<docker_id>` vervangt door uw docker-ID:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push de bijgewerkte installatie kopie naar docker hub. deze moet aanzienlijk minder tijd in beslag nemen dan de eerste push alleen de bijgewerkte segmenten van de afbeelding moeten worden geüpload.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions de installatie kopie automatisch opnieuw implementeert in uw functions-app; het proces vindt minder dan een minuut plaats.

1. Open in een browser `https://<app_name>.scm.azurewebsites.net/`en vervang `<app_name>` door uw unieke naam. Deze URL is het kudu-eind punt (Advanced tools) voor uw functie-app-container.

1. Meld u aan bij uw Azure-account en selecteer vervolgens de **SSH** om een verbinding met de container tot stand te brengen. Verbinding kan even duren als Azure nog steeds bezig is met het bijwerken van de container installatie kopie.

1. Nadat een verbinding tot stand is gebracht met uw container, voert u de `top` opdracht uit om de actieve processen weer te geven. 

    ![Bovenste Linux-opdracht die in een SSH-sessie wordt uitgevoerd](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Schrijven naar een Azure Storage wachtrij

Met Azure Functions kunt u uw functies verbinden met andere Azure-Services en-resources die uw eigen integratie code schrijven. Deze *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd in de functie definitie. Gegevens van bindingen worden aan de functie door gegeven als para meters. Een *trigger* is een speciaal type invoer binding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer-en uitvoer bindingen hebben. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In deze sectie wordt beschreven hoe u uw functie integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

## <a name="retrieve-the-azure-storage-connection-string"></a>De Azure Storage ophalen connection string

U hebt eerder een Azure Storage-account gemaakt voor gebruik door de functie-app. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. Door de instelling in het bestand *Local. settings. json* te downloaden, kunt u deze verbinding schrijven naar een opslag wachtrij in hetzelfde account wanneer u de functie lokaal uitvoert. 

1. Voer in de hoofdmap van het project de volgende opdracht uit en vervang `<app_name>` door de naam van uw functie-app uit de vorige Snelstartgids. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open *Local. settings. json* en zoek de waarde met de naam `AzureWebJobsStorage`, het opslag account Connection String. U gebruikt de naam `AzureWebJobsStorage` en de connection string in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *Local. settings. json* bevat geheimen die zijn gedownload van Azure, moet u dit bestand altijd uitsluiten van broncode beheer. Het *. gitignore* -bestand dat is gemaakt met een lokale functie project, sluit het bestand standaard uit.

### <a name="add-an-output-binding-to-functionjson"></a>Een uitvoer binding toevoegen aan function. json

In Azure Functions moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand *Function. json* . De *functie. json* bevat al een invoer binding voor het type ' http trigger ' en een uitvoer binding voor het HTTP-antwoord. Als u een binding wilt toevoegen aan een opslag wachtrij, wijzigt u het bestand als volgt, waarmee een uitvoer binding wordt toegevoegd voor het type wachtrij, waarbij de wachtrij wordt weer gegeven als een invoer argument met de naam `msg`. De wachtrij binding vereist ook de naam van de wachtrij die moet worden gebruikt, in dit geval `outqueue`en de naam van de instellingen die de connection string bevatten, in dit geval `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

In een C# klassen bibliotheek project worden de bindingen gedefinieerd als bindings kenmerken voor de functie methode. Het bestand *Function. json* wordt vervolgens automatisch gegenereerd op basis van deze kenmerken.

1. Voer voor de binding van de wachtrij de volgende [DotNet add package](/dotnet/core/tools/dotnet-add-package) opdracht uit om het opslag extensie pakket toe te voegen aan uw project.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Open het *HttpTrigger.cs* -bestand en voeg de volgende `using`-instructie toe:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Voeg de volgende para meter toe aan de `Run` methode definitie:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    De definitie van de `Run` methode moet nu overeenkomen met de volgende code:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

De para meter `msg` is een `ICollector<T>` type dat een verzameling berichten vertegenwoordigt die naar een uitvoer binding worden geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslag wachtrij met de naam `outqueue`. De connection string voor het opslag account is ingesteld door de `StorageAccountAttribute`. Dit kenmerk geeft de instelling aan die het opslag account bevat connection string en kan worden toegepast op het niveau van de klasse, methode of para meter. In dit geval kunt u `StorageAccountAttribute` weglaten omdat u het standaard opslag account al gebruikt.

::: zone-end

::: zone pivot="programming-language-javascript"

Update *Function. json* zodat deze overeenkomt met het volgende door de binding van de wachtrij toe te voegen na de HTTP-binding:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Update *Function. json* zodat deze overeenkomt met het volgende door de binding van de wachtrij toe te voegen na de HTTP-binding:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Update *Function. json* zodat deze overeenkomt met het volgende door de binding van de wachtrij toe te voegen na de HTTP-binding:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Update *Function. json* zodat deze overeenkomt met het volgende door de binding van de wachtrij toe te voegen na de HTTP-binding:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen voor het gebruik van de uitvoer binding

Nadat de binding is gedefinieerd, wordt de naam van de binding, in dit geval `msg`, weer gegeven in de functie code als een argument (of in het `context`-object in Java script en type script). U kunt deze variabele vervolgens gebruiken om berichten naar de wachtrij te schrijven. U moet elke code schrijven voor verificatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. Al deze integratie taken kunnen worden verwerkt in de Azure Functions runtime-en wachtrij-uitvoer binding.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>De installatie kopie in het REGI ster bijwerken

1. Voer `docker build` opnieuw uit in de hoofdmap en werk de versie in de tag bij naar `v1.0.1`. Vervang `<docker_id>` als voorheen door de account-ID van uw docker hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Push de bijgewerkte installatie kopie terug naar de opslag plaats met `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Omdat u continue levering hebt geconfigureerd, wordt de functie-app in azure automatisch bijgewerkt wanneer u de installatie kopie in het REGI ster bijwerkt.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht in de wachtrij van Azure Storage weer geven

Gebruik in een browser dezelfde URL als voordat u de functie aanroept. De browser moet hetzelfde antwoord weer geven als voorheen, omdat u dat deel van de functie code niet hebt gewijzigd. De toegevoegde code schrijft echter een bericht met behulp van de para meter `name` URL naar de `outqueue`-opslag wachtrij.

U kunt de wachtrij weer geven in de [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer](https://storageexplorer.com/). U kunt de wachtrij ook weer geven in de Azure CLI, zoals wordt beschreven in de volgende stappen:

1. Open het bestand *Local. setting. json* van het functie project en kopieer de Connection String waarde. Voer in een Terminal-of opdracht venster de volgende opdracht uit om een omgevings variabele met de naam `AZURE_STORAGE_CONNECTION_STRING`te maken en uw specifieke connection string in plaats van `<connection_string>`te plakken. (Deze omgevings variabele houdt in dat u de connection string niet hoeft op te geven bij elke volgende opdracht met behulp van het argument `--connection-string`.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Beschrijving Gebruik de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht om de opslag wachtrijen in uw account weer te geven. De uitvoer van deze opdracht moet een wachtrij bevatten met de naam `outqueue`, die is gemaakt toen de functie het eerste bericht naar die wachtrij heeft geschreven.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Gebruik de [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) opdracht om de berichten in deze wachtrij weer te geven. dit moet de eerste naam zijn die u hebt gebruikt bij het testen van de functie. De opdracht haalt het eerste bericht in de wachtrij op in [Base64-code ring](functions-bindings-storage-queue.md#encoding), dus u moet het bericht ook decoderen om als tekst weer te geven.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Omdat u de verwijzing naar de berichten verzameling moet decoderen en van base64 wilt ontsleutelen, voert u Power shell uit en gebruikt u de Power shell-opdracht.

    ---

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven werken met de Azure-functie met behulp van de resources die u in deze zelf studie hebt gemaakt, kunt u alle resources op locatie laten staan. Omdat u een Premium-abonnement voor Azure Functions hebt gemaakt, kunt u een of twee USD per dag besparen op lopende kosten.

Om lopende kosten te voor komen, verwijdert u de `AzureFunctionsContainer-rg` resource groep om alle resources in de groep op te schonen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Volgende stappen

+ [Bewakings functies](functions-monitoring.md)
+ [Opties voor schalen en hosten](functions-scale.md)
+ [Op Kubernetes gebaseerde serverloze hosting](functions-kubernetes-keda.md)
