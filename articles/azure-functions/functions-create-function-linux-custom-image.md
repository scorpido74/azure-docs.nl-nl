---
title: Azure-functies op Linux maken met een aangepaste afbeelding
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239628"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Een functie op Linux maken met een aangepaste container

In deze zelfstudie maakt en implementeert u Python-code naar Azure Functions als een aangepaste Docker-container met behulp van een Linux-basisafbeelding. U gebruikt meestal een aangepaste afbeelding wanneer uw functies een specifieke taalversie vereisen of een specifieke afhankelijkheid of configuratie hebben die niet wordt geleverd door de ingebouwde afbeelding.

U ook een standaard Azure App Service-container gebruiken zoals beschreven in [Uw eerste functie maken die op Linux wordt gehost.](functions-create-first-azure-function-azure-cli-linux.md) Ondersteunde basisafbeeldingen voor Azure-functies zijn te vinden in de [basisafbeeldingen van Azure Functions.](https://hub.docker.com/_/microsoft-azure-functions-base)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een functie-app en Dockerfile met behulp van de Azure Functions Core Tools.
> * Een aangepaste installatiekopie met behulp van Docker bouwen.
> * Een aangepaste installatiekopie naar een containerregister publiceren.
> * Ondersteunende resources maken in Azure voor de functie-app
> * Een functie-app vanuit Docker Hub implementeren.
> * Toepassingsinstellingen toevoegen aan de functie-app.
> * Continue implementatie inschakelen.
> * SSH-verbindingen met de container inschakelen.
> * Voeg een uitvoerbinding voor wachtrijopslag toe. 

U deze zelfstudie volgen op elke computer met Windows, Mac OS of Linux. Als u de zelfstudie voltooit, worden kosten in rekening gebracht voor een paar Amerikaanse dollars in uw Azure-account.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- De [Azure Functions Core Tools](./functions-run-local.md#v2) versie 2.7.1846 of een latere
- De [Azure CLI-versie](/cli/azure/install-azure-cli) 2.0.77 of hoger
- De [Runtime van Azure Functions 2.x](functions-versions.md)
- De volgende taalruntimecomponenten:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x of hoger](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bit](https://www.python.org/downloads/release/python-3610/) of [Python 3.7 - 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Een [Docker-id](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Vereiste controle

1. Voer in een terminal- `func --version` of opdrachtvenster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger zijn.
1. Voer `az --version` deze uit om te controleren of de Azure CLI-versie 2.0.76 of hoger is.
1. Meld `az login` u aan bij Azure en verifieer een actief abonnement.
1. Ren `docker login` om je aan te melden bij Docker. Deze opdracht mislukt als Docker niet wordt uitgevoerd, in welk geval docker starten en de opdracht opnieuw proberen.

## <a name="create-and-test-the-local-functions-project"></a>Het project lokale functies maken en testen

1. Maak in een terminal- of opdrachtprompt een map voor deze zelfstudie op een geschikte locatie en navigeer vervolgens naar die map.

1. Volg de instructies op [Maak en activeer een virtuele omgeving](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) om een virtuele omgeving te maken voor gebruik met deze zelfstudie.

1. Voer de volgende opdracht uit voor de door u `LocalFunctionsProject`gekozen taal om een functie-app-project te maken in een map met de naam . De `--docker` optie `Dockerfile` genereert een voor het project, die een geschikte aangepaste container definieert voor gebruik met Azure-functies en de geselecteerde runtime.

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
    
1. Voeg een functie toe aan uw project `--name` met behulp van de volgende `--template` opdracht, waarbij het argument de unieke naam van uw functie is en het argument de trigger van de functie opgeeft. `func new`een submap maken die overeenkomt met de functienaam die een codebestand bevat dat past bij de gekozen taal van het project en een configuratiebestand met de naam *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Als u de functie lokaal wilt testen, start u de lokale runtimehost van Azure Functions in de map *LocalFunctionsProject:*
   
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

1. Zodra u `HttpExample` het eindpunt in de uitvoer `http://localhost:7071/api/HttpExample?name=Functions`ziet verschijnen, navigeert u naar . De browser moet een bericht weergeven als "Hallo, Functies" (enigszins gevarieerd, afhankelijk van de door u gekozen programmeertaal).

1. Gebruik **Ctrl**-**C** om de host te stoppen.

## <a name="build-the-container-image-and-test-locally"></a>De containerafbeelding maken en lokaal testen

1. (Optioneel) Bestudeer het *Dockerfile" in de map *LocalFunctionsProj.* Het Dockerfile beschrijft de vereiste omgeving om de functie-app op Linux uit te voeren: 

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
    > De volledige lijst met ondersteunde basisafbeeldingen voor Azure-functies is te vinden op de [basisafbeeldingspagina azure functions.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
1. Voer in de map *LocalFunctionsProject* de opdracht [Docker](https://docs.docker.com/engine/reference/commandline/build/) `azurefunctionsimage`build uit `v1.0.0`en geef een naam en tag op. Vervang `<docker_id>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Wanneer de opdracht is voltooid, u de nieuwe container lokaal uitvoeren.
    
1. Als u de build wilt testen, voert u de afbeelding uit `<docker_id>` in een lokale container met `-p 8080:80`de opdracht [Docker run,](https://docs.docker.com/engine/reference/commandline/run/) vervangt u opnieuw met uw Docker-id en voegt u het argument poorten toe:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Zodra de afbeelding in een lokale container `http://localhost:8080`wordt uitgevoerd, opent u een browser naar , die de onderstaande tijdelijke aanduiding moet weergeven. De afbeelding wordt op dit punt weergegeven omdat uw functie wordt uitgevoerd in de lokale container, zoals in Azure, wat betekent `"authLevel": "function"` dat deze wordt beschermd door een toegangssleutel zoals gedefinieerd in *function.json* met de eigenschap. De container is echter nog niet gepubliceerd in een functie-app in Azure, dus de sleutel is nog niet beschikbaar. Als u lokaal wilt testen, stopt u docker, wijzigt u de eigenschap autorisatie om `"authLevel": "anonymous"`de afbeelding opnieuw te bouwen en de docker opnieuw op te starten. Vervolgens `"authLevel": "function"` opnieuw instellen in *function.json*. Zie [autorisatiesleutels voor](functions-bindings-http-webhook-trigger.md#authorization-keys)meer informatie.

    ![Tijdelijke aanduiding die aangeeft dat de container lokaal wordt uitgevoerd](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Nadat u de functie-app in de container hebt geverifieerd, stopt u docker met **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>De afbeelding naar Docker Hub duwen

Docker Hub is een containerregister dat afbeeldingen host en beeld- en containerservices biedt. Als u uw afbeelding wilt delen, waaronder implementeren naar Azure, moet u deze naar een register pushen.

1. Als u zich nog niet hebt aangemeld bij Docker, doet u `<docker_id>` dit met de [aanmeldingsopdracht docker,](https://docs.docker.com/engine/reference/commandline/login/) vervangen door uw Docker-id. Met deze opdracht wordt u gevraagd om uw gebruikersnaam en wachtwoord. Een bericht 'Geslaagd inloggen' bevestigt dat u bent aangemeld.

    ```
    docker login
    ```
    
1. Nadat u zich hebt aangemeld, duwt u de afbeelding naar Docker Hub `<docker_id>` met behulp van de [pushopdracht docker](https://docs.docker.com/engine/reference/commandline/push/) en opnieuw vervangen door uw Docker-id.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Afhankelijk van de snelheid van uw netwerk kan het een paar minuten duren voordat het indrukken van de afbeelding de eerste keer een paar minuten duurt (het duwen van latere wijzigingen is veel sneller). Terwijl u wacht, u doornaar de volgende sectie en Azure-resources maken in een andere terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Als u uw functiecode wilt implementeren in Azure, moet u drie bronnen maken:

- Een resourcegroep, een logische container voor gerelateerde resources.
- Een Azure Storage-account, dat status- en andere informatie over uw projecten bijhoudt.
- Een Azure-functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt toegewezen aan uw lokale functieproject en stelt u in staat functies te groeperen als een logische eenheid voor eenvoudiger beheer, implementatie en delen van resources.

U gebruikt Azure CLI-opdrachten om deze items te maken. Elke opdracht biedt JSON-uitvoer na voltooiing.

1. Meld u aan bij Azure met de opdracht [AZ-aanmelding:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld `AzureFunctionsContainers-rg` wordt `westeurope` een resourcegroep met de naam in de regio genaamerd. (U maakt over het algemeen uw resourcegroep en resources in `az account list-locations` een regio bij u in de buurt, met behulp van een beschikbare regio van de opdracht.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > U geen Linux- en Windows-apps hosten in dezelfde brongroep. Als u een bestaande `AzureFunctionsContainers-rg` resourcegroep hebt met de naam Een Windows-functie-app of web-app, moet u een andere brongroep gebruiken.
    
1. Maak een opslagaccount voor algemene doeleinden in uw resourcegroep en -regio met behulp van de opdracht [AZ-opslagaccount maken.](/cli/azure/storage/account#az-storage-account-create) Vervang in het `<storage_name>` volgende voorbeeld een wereldwijd unieke naam die bij u past. Namen moeten alleen drie tot 24 tekens en kleine letters bevatten. `Standard_LRS`geeft een typisch algemeen doelaccount op.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    De opslag rekening maakt slechts een paar USD cent voor deze tutorial.
    
1. Gebruik de opdracht om een Premium-abonnement te maken voor`--sku EP1`Azure-functies met de`-location westeurope`naam `myPremiumPlan` Elastic Premium **1** ( ), in de regio West-Europa ( of gebruik een geschikte regio bij u in de buurt) en in een Linux-container (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux hosting voor aangepaste functies containers worden ondersteund op [Dedicated (App Service) plannen](functions-scale.md#app-service-plan) en [Premium plannen](functions-premium-plan.md#features). We gebruiken hier het Premium-abonnement, dat naar behoefte kan worden geschaald. Zie [deze vergelijking van hostingabonnementen van Azure Functions](functions-scale.md) voor meer informatie over hosting. Zie de [prijspagina Functies](https://azure.microsoft.com/pricing/details/functions/)om de kosten te berekenen.

    De opdracht voorziet ook in een bijbehorende Instantie Azure Application Insights in dezelfde resourcegroep, waarmee u uw functie-app controleren en logboeken weergeven. Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie . De instantie brengt geen kosten met zich mee totdat u deze activeert.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Een functie-app op Azure maken en configureren met de afbeelding

Een functie-app op Azure beheert de uitvoering van uw functies in uw hostingplan. In deze sectie gebruikt u de Azure-bronnen uit de vorige sectie om een functie-app te maken op basis van een afbeelding op Docker Hub en deze te configureren met een verbindingstekenreeks met Azure Storage.

1. Maak de functie-app met de opdracht [AZ-functieapp create.](/cli/azure/functionapp#az-functionapp-create) Vervang in het `<storage_name>` volgende voorbeeld de naam die u in de vorige sectie voor het opslagaccount hebt gebruikt. Vervang `<app_name>` ook door een wereldwijd unieke `<docker_id>` naam die bij u past en door uw Docker-id.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    De parameter *deployment-container-image-name* geeft de afbeelding op die moet worden gebruikt voor de functie-app. U de opdracht config container show van de [AZ-functieapp gebruiken](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) om informatie weer te geven over de afbeelding die wordt gebruikt voor implementatie. U ook de opdracht [az-functieapp config containerset](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) gebruiken om vanuit een andere afbeelding te implementeren.

1. Haal de verbindingstekenreeks op voor het opslagaccount dat u hebt gemaakt met de [show-connection-string-tekenreeks van het AZ-opslagaccount](/cli/azure/storage/account) en wijs deze toe aan een shellvariabele: `storageConnectionString`

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Voeg deze instelling toe aan de functie-app met de opdracht [az-functieapp config appsettings.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) Vervang in de `<app_name>` volgende opdracht de naam van `<connection_string>` uw functie-app en vervang de verbindingstekenreeks uit de vorige stap (een lange gecodeerde tekenreeks die begint met 'DefaultEndpointProtocol='):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. De functie kan deze verbindingstekenreeks nu gebruiken om toegang te krijgen tot het opslagaccount.

> [!TIP]
> In bash u een shellvariabele gebruiken om de verbindingstekenreeks vast te leggen in plaats van het klembord te gebruiken. Gebruik eerst de volgende opdracht om een variabele met de verbindingstekenreeks te maken:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Raadpleeg vervolgens de variabele in de tweede opdracht:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Als u uw aangepaste afbeelding publiceert naar een privécontaineraccount, moet u in plaats daarvan omgevingsvariabelen in het Dockerbestand gebruiken voor de verbindingstekenreeks. Zie voor meer informatie de [ENV-instructie.](https://docs.docker.com/engine/reference/builder/#env) U moet ook de `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD`variabelen instellen en . Als u de waarden wilt gebruiken, moet u de afbeelding opnieuw opbouwen, de afbeelding naar het register duwen en de functie-app opnieuw starten op Azure.

## <a name="verify-your-functions-on-azure"></a>Uw functies op Azure verifiëren

Als de afbeelding is geïmplementeerd in de functie-app op Azure, u de functie nu aanroepen via HTTP-aanvragen. Omdat de *functie.json-definitie* de eigenschap `"authLevel": "function"`bevat, moet u eerst de toegangssleutel verkrijgen (ook wel de functiesleutel genoemd) en deze opnemen als URL-parameter in aanvragen voor het eindpunt.

1. Haal de functie-URL op met de toegangssleutel (functie) met behulp `az rest` van de Azure-portal of met de Azure CLI met de opdracht.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Meld u aan bij de Azure-portal en zoek vervolgens uw functie-app door de naam van uw functie-app in te voeren in het vak **Zoeken** boven aan de pagina. Selecteer in de resultaten de **bron App Service.**

    1. Selecteer in het linkernavigatiedeelvenster onder **Functies (Alleen lezen)** de naam van uw functie.

    1. Selecteer in het deelvenster Details **</> FUNCTIE OPHALEN:**
    
        ![De opdracht URL van de functie ophalen op de Azure-portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Selecteer in de pop-up **standaard (functiesleutel)** en **kopieer .** De sleutel is de `?code=`reeks tekens die volgen .

        ![De functie-URL kopiëren vanuit de Azure-portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Omdat uw functie-app als container wordt geïmplementeerd, u uw functiecode in de portal niet aanbrengen. In plaats daarvan moet u het project bijwerken in de lokale afbeelding, de afbeelding opnieuw naar het register duwen en vervolgens opnieuw worden geïmplementeerd in Azure. U continue implementatie in een later gedeelte instellen.
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

    1. Bouw een URL-tekenreeks in de `<subscription_id>` `<resource_group>`volgende `<app_name>` indeling, vervang , en met uw Azure-abonnements-ID, de brongroep van uw functie-app en de naam van uw functie-app:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        De URL kan er bijvoorbeeld op het volgende adres uitzien:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Voor het gemak u de URL in plaats `az rest` daarvan toewijzen aan een omgevingsvariabele en deze gebruiken in de opdracht.
    
    1. Voer de `az rest` volgende opdracht uit (beschikbaar in de Azure CLI-versie `<uri>` 2.0.77 en hoger), vervangen door de URI-tekenreeks van de laatste stap, inclusief de aanhalingstekens:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. De uitvoer van de opdracht is de functiesleutel. De volledige functie-URL `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`wordt `<app_name>` `<function_name>`dan `<key>` vervangen, vervangen en met uw specifieke waarden.
    
        > [!NOTE]
        > De sleutel die hier wordt opgehaald, is de *hostsleutel* die werkt voor alle functies in de functies-app; de methode die voor de portal wordt weergegeven, haalt de sleutel alleen voor de ene functie op.

    ---

1. Plak de functie-URL in de adresbalk `&name=Azure` van uw browser en voeg de parameter toe aan het einde van deze URL. Tekst als 'Hello Azure' moet in de browser worden weergegeven.

    ![Het antwoord van de functie in de browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Als u de autorisatie wilt testen, verwijdert u de parameter code= uit de URL en controleert u of u geen antwoord van de functie krijgt.


## <a name="enable-continuous-deployment-to-azure"></a>Continue implementatie naar Azure inschakelen

U Azure-functies inschakelen om uw implementatie van een afbeelding automatisch bij te werken wanneer u de afbeelding in het register bijwerkt.

1. Continue implementatie inschakelen met de [opdracht Az FunctionApp Deployment container config,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) vervangen `<app_name>` door de naam van uw functie-app:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Met deze opdracht u continue implementatie en retourneert u de URL van de implementatiewebhook. (U deze URL op elk later tijdstip ophalen met de opdracht [AZ Functionapp deployment container show-cd-url.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Kopieer de url van de implementatiewebhook naar het klembord.

1. Open [Docker Hub,](https://hub.docker.com/)meld u aan en selecteer **Repositories** op de navigatiebalk. Zoek en selecteer afbeelding, selecteer het tabblad **Webhooks,** geef een **Webhook-naam**op, plak uw URL in **de URL van Webhook**en selecteer **Vervolgens Maken:**

    ![Voeg de webhook toe aan uw DockerHub repo](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Met de webhook-set implementeert Azure Functions uw afbeelding opnieuw wanneer u deze bijwerkt in Docker Hub.

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Met SSH ingeschakeld, u verbinding maken met uw container met behulp van App Service Advanced Tools (Kudu). Azure Functions biedt een basisafbeelding die SSH al heeft ingeschakeld om eenvoudig verbinding te maken met uw container met SSH. U hoeft alleen het Dockerbestand te bewerken en vervolgens de afbeelding opnieuw te bouwen en opnieuw te implementeren. U vervolgens verbinding maken met de container via de Advanced Tools (Kudu)

1. Sluit in uw Dockerfile `-appservice` de tekenreeks toe `FROM` aan de basisafbeelding in uw instructie:

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

    De verschillen tussen de basisafbeeldingen worden beschreven in de [zelfstudie App Services - Aangepaste dockerafbeeldingen.](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)

1. Herbouwen van de `docker build` afbeelding met `<docker_id>` de opdracht opnieuw, vervangen door uw Docker-id:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Duw de bijgewerkte afbeelding naar Docker Hub, wat aanzienlijk minder tijd in beslag moet nemen dan de eerste push alleen de bijgewerkte segmenten van de afbeelding moeten worden geüpload.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions implementeert de afbeelding automatisch opnieuw in de functie-app. het proces vindt plaats in minder dan een minuut.

1. Open in een `https://<app_name>.scm.azurewebsites.net/`browser, `<app_name>` vervangen door uw unieke naam. Deze URL is het eindpunt van De Geavanceerde Extra's (Kudu) voor de container van uw functie-app.

1. Meld u aan bij uw Azure-account en selecteer vervolgens de **SSH** om een verbinding met de container tot stand te brengen. Het maken van verbinding kan enkele ogenblikken duren als Azure nog bezig is met het bijwerken van de containerafbeelding.

1. Nadat er een verbinding met uw `top` container is gemaakt, voert u de opdracht uit om de momenteel lopende processen weer te geven. 

    ![Linux top commando draait in een SSH sessie](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Schrijven naar een Azure Storage-wachtrij

Met Azure Functions u uw functies verbinden met andere Azure-services en -resources die uw eigen integratiecode moeten schrijven. Deze *bindingen*, die zowel input als output vertegenwoordigen, worden gedeclareerd binnen de functiedefinitie. Gegevens van bindingen worden als parameters aan de functie geleverd. Een *trigger* is een speciaal type invoerbinding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer- en uitvoerbindingen hebben. Zie Azure Functions triggers [and bindings concepts](functions-triggers-bindings.md)voor meer informatie.

In deze sectie ziet u hoe u uw functie integreren met een Azure Storage-wachtrij. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-verzoek naar een bericht in de wachtrij.

## <a name="retrieve-the-azure-storage-connection-string"></a>De tekenreeks azure storage-verbinding ophalen

Eerder hebt u een Azure Storage-account gemaakt voor gebruik door de functie-app. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. Als u de instelling downloadt naar het bestand *local.settings.json,* u die verbinding schrijven naar een opslagwachtrij in hetzelfde account gebruiken wanneer u de functie lokaal uitvoert. 

1. Voer vanuit de hoofdmap van het project `<app_name>` de volgende opdracht uit, vervangen door de naam van uw functie-app van de vorige quickstart. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open *local.settings.json* en zoek `AzureWebJobsStorage`de waarde met de naam , de tekenreeks Opslagaccountverbinding. U gebruikt `AzureWebJobsStorage` de naam en de verbindingstekenreeks in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *local.settings.json* geheimen bevat die zijn gedownload van Azure, sluit u dit bestand altijd uit van bronbeheer. Het *.gitignore-bestand* dat is gemaakt met een project voor lokale functies, sluit het bestand standaard uit.

### <a name="add-an-output-binding-to-functionjson"></a>Een uitvoerbinding toevoegen aan function.json

In Azure-functies vereist elk `direction`type `type`binding een `name` , en een unieke om te worden gedefinieerd in het *function.json-bestand.* Uw *function.json* bevat al een invoerbinding voor het type 'httpTrigger' en een uitvoerbinding voor het HTTP-antwoord. Als u een binding aan een opslagwachtrij wilt toevoegen, wijzigt u het bestand als volgt, waarbij een uitvoerbinding wordt toegevoegd voor het type 'wachtrij', waarbij de wachtrij in de code wordt weergegeven als een invoerargument met de naam `msg`. De wachtrijbinding vereist ook de naam van de `outqueue`wachtrij om te gebruiken, in dit geval `AzureWebJobStorage`, en de naam van de instellingen die de verbindingstekenreeks bevat, in dit geval .

::: zone pivot="programming-language-csharp"

In een c#-klassebibliotheekproject worden de bindingen gedefinieerd als bindende kenmerken voor de functiemethode. Het *bestand function.json* wordt vervolgens automatisch gegenereerd op basis van deze kenmerken.

1. Voer voor de wachtrijbinding de volgende [opdracht dotnet add package](/dotnet/core/tools/dotnet-add-package) uit om het uitbreidingspakket Opslag aan uw project toe te voegen.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Open het *HttpTrigger.cs* bestand `using` en voeg de volgende instructie toe:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Voeg de volgende `Run` parameter toe aan de methodedefinitie:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    De `Run` methodedefinitie moet nu overeenkomen met de volgende code:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

De `msg` parameter `ICollector<T>` is een type dat een verzameling berichten vertegenwoordigt die naar een uitvoerbinding zijn geschreven wanneer de functie is voltooid. In dit geval is de uitvoer `outqueue`een opslagwachtrij met de naam . De verbindingstekenreeks voor het opslagaccount `StorageAccountAttribute`wordt ingesteld door het . Dit kenmerk geeft de instelling aan die de tekenreeks Opslagaccountverbinding bevat en kan worden toegepast op klasse-, methode- of parameterniveau. In dit geval `StorageAccountAttribute` u weglaten omdat u het standaardopslagaccount al gebruikt.

::: zone-end

::: zone pivot="programming-language-javascript"

Update *function.json* om het volgende aan te passen door de wachtrijbinding toe te voegen na de HTTP-binding:

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

Update *function.json* om het volgende aan te passen door de wachtrijbinding toe te voegen na de HTTP-binding:

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

Update *function.json* om het volgende aan te passen door de wachtrijbinding toe te voegen na de HTTP-binding:

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

Update *function.json* om het volgende aan te passen door de wachtrijbinding toe te voegen na de HTTP-binding:

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

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen om de uitvoerbinding te gebruiken

Nadat de binding is gedefinieerd, wordt de naam `msg`van de binding in dit geval `context` in de functiecode weergegeven als argument (of in het object in JavaScript en TypeScript). U die variabele vervolgens gebruiken om berichten naar de wachtrij te schrijven. U moet een code schrijven voor verificatie, het verkrijgen van een wachtrijverwijzing of het schrijven van gegevens. Al deze integratietaken worden handig verwerkt in de runtime van Azure Functions en de binding voor wachtrijuitvoer.

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

### <a name="update-the-image-in-the-registry"></a>De afbeelding in het register bijwerken

1. Voer in de `docker build` hoofdmap opnieuw uit en werk deze `v1.0.1`keer de versie in de tag bij naar . Net als `<docker_id>` voorheen vervangt u uw Docker Hub-account-id:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Duw de bijgewerkte afbeelding terug `docker push`naar de opslagplaats met:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Omdat u continue levering hebt geconfigureerd, wordt de afbeelding in het register opnieuw bijgewerkt en wordt uw functie-app in Azure automatisch bijgewerkt.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht weergeven in de azure-opslagwachtrij

Gebruik in een browser dezelfde URL als voorheen om uw functie aan te roepen. De browser moet hetzelfde antwoord weergeven als voorheen, omdat u dat deel van de functiecode niet hebt gewijzigd. De toegevoegde code heeft echter een `name` bericht met `outqueue` de URL-parameter naar de opslagwachtrij geschreven.

U de wachtrij weergeven in de [Azure-portal](../storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer.](https://storageexplorer.com/) U de wachtrij in de Azure CLI ook weergeven zoals beschreven in de volgende stappen:

1. Open het bestand *local.setting.json* van het functieproject en kopieer de waarde van de verbindingstekenreeks. Voer in een terminal- of opdrachtvenster de volgende `AZURE_STORAGE_CONNECTION_STRING`opdracht uit om een omgevingsvariabele met de naam , waarbij u uw specifieke verbindingstekenreeks plakt in plaats van `<connection_string>`. (Deze omgevingsvariabele betekent dat u de verbindingstekenreeks niet hoeft `--connection-string` te leveren aan elke volgende opdracht met behulp van het argument.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Optioneel) Gebruik [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) de opdracht om de opslagwachtrijen in uw account weer te geven. De uitvoer van deze opdracht `outqueue`moet een wachtrij met de naam bevatten, die is gemaakt toen de functie het eerste bericht naar die wachtrij schreef.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Gebruik [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) de opdracht om de berichten in deze wachtrij weer te geven, wat de eerste naam moet zijn die u eerder hebt gebruikt bij het testen van de functie. De opdracht haalt het eerste bericht in de wachtrij in [base64-codering](functions-bindings-storage-queue-trigger.md#encoding)op, dus u moet ook het bericht decoderen om als tekst te worden weergegeven.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Omdat u de verwijzing naar de berichtverzameling en -decoderen vanaf base64 moet nagaan, voert u PowerShell uit en gebruikt u de opdracht PowerShell.

    ---

## <a name="clean-up-resources"></a>Resources opschonen

Als u met Azure Function wilt blijven werken met de resources die u in deze zelfstudie hebt gemaakt, u al deze resources op hun plaats laten. Omdat u een Premium-abonnement voor Azure-functies hebt gemaakt, maakt u één of twee USD per dag aan lopende kosten.

Verwijder de resourcegroep `AzureFunctionsContainer-rg` om alle resources in die groep op te schonen om lopende kosten te voorkomen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Volgende stappen

+ [Bewakingsfuncties](functions-monitoring.md)
+ [Opties voor schalen en hosten](functions-scale.md)
+ [Kubernetes-gebaseerde serverless hosting](functions-kubernetes-keda.md)
