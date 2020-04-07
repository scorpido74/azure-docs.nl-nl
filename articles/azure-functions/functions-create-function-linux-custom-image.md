---
title: Azure-functies op Linux maken met een aangepaste afbeelding
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 44ca8f721967b90be283f867f8656344ec3f1906
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673417"
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

U deze zelfstudie volgen op elke computer met Windows, macOS of Linux. Als u de zelfstudie voltooit, worden kosten in rekening gebracht voor een paar Amerikaanse dollars in uw Azure-account.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Een [Docker-id](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Ren `docker login` om je aan te melden bij Docker. Deze opdracht mislukt als Docker niet wordt uitgevoerd, in welk geval docker starten en de opdracht opnieuw proberen.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Het project lokale functies maken en testen

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer in een terminal- of opdrachtprompt de volgende opdracht uit voor de `LocalFunctionsProject`door u gekozen taal om een functie-app-project te maken in een map met de naam .  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven vraagt u om waarden die nodig zijn om het genereren van het project op implementatie te voltooien.   
Geef de volgende waarden op wanneer daarom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de regels voor [pakketnaamgeving](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artefactId** | `fabrikam-functions` | Een waarde die de naam van de pot is, zonder versienummer. |
| **Versie** | `1.0-SNAPSHOT` | Kies de standaardwaarde. |
| **Pakket** | `com.fabrikam.functions` | Een waarde die het Java-pakket is voor de gegenereerde functiecode. Gebruik de standaard. |

Typ `Y` of druk op Enter om dit te bevestigen.

Maven maakt de projectbestanden in een nieuwe map met een `fabrikam-functions`naam van _artefactId_, die in dit voorbeeld is . 
::: zone-end
De `--docker` optie `Dockerfile` genereert een voor het project, die een geschikte aangepaste container definieert voor gebruik met Azure-functies en de geselecteerde runtime.

Navigeer naar de projectmap:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Voeg een functie toe aan uw project `--name` met behulp van de volgende `--template` opdracht, waarbij het argument de unieke naam van uw functie is en het argument de trigger van de functie opgeeft. `func new`een submap maken die overeenkomt met de functienaam die een codebestand bevat dat past bij de gekozen taal van het project en een configuratiebestand met de naam *function.json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Als u de functie lokaal wilt testen, start u de lokale runtime-host van Azure-functies in de hoofdmap van de projectmap: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Zodra u `HttpExample` het eindpunt in de uitvoer [`http://localhost:7071/api/HttpExample?name=Functions`](http://localhost:7071/api/HttpExample?name=Functions)ziet verschijnen, navigeert u naar . De browser moet een "hallo" `Functions`bericht dat echo's `name` terug, de waarde die wordt geleverd aan de query parameter weer te geven.

Gebruik **Ctrl**-**C** om de host te stoppen.

## <a name="build-the-container-image-and-test-locally"></a>De containerafbeelding maken en lokaal testen

(Optioneel) Onderzoek het *Dockerfile" in de hoofdmap van de projectmap. Het Dockerfile beschrijft de vereiste omgeving om de functie-app op Linux uit te voeren.  De volledige lijst met ondersteunde basisafbeeldingen voor Azure-functies is te vinden op de [basisafbeeldingspagina azure functions.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
Voer in de map hoofdproject de opdracht [Docker](https://docs.docker.com/engine/reference/commandline/build/) `azurefunctionsimage`build uit `v1.0.0`en geef een naam en tag op. Vervang `<DOCKER_ID>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Wanneer de opdracht is voltooid, u de nieuwe container lokaal uitvoeren.
    
Als u de build wilt testen, voert u de afbeelding uit `<DOCKER_ID` in een lokale container met `-p 8080:80`de opdracht [Docker run,](https://docs.docker.com/engine/reference/commandline/run/) vervangt u opnieuw met uw Docker-id en voegt u het argument poorten toe:

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Zodra de afbeelding in een lokale container `http://localhost:8080`wordt uitgevoerd, opent u een browser naar , die de onderstaande tijdelijke aanduiding moet weergeven. De afbeelding wordt op dit punt weergegeven omdat uw functie wordt uitgevoerd in de lokale container, zoals in Azure, wat betekent `"authLevel": "function"` dat deze wordt beschermd door een toegangssleutel zoals gedefinieerd in *function.json* met de eigenschap. De container is echter nog niet gepubliceerd in een functie-app in Azure, dus de sleutel is nog niet beschikbaar. Als u wilt testen tegen de lokale container, stop `"authLevel": "anonymous"`docker, wijzigt u de eigenschap autorisatie om, herbouwen van de afbeelding en herstart docker. Vervolgens `"authLevel": "function"` opnieuw instellen in *function.json*. Zie [autorisatiesleutels voor](functions-bindings-http-webhook-trigger.md#authorization-keys)meer informatie.

![Tijdelijke aanduiding die aangeeft dat de container lokaal wordt uitgevoerd](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Zodra de afbeelding wordt uitgevoerd in [`http://localhost:8080/api/HttpExample?name=Functions`](http://localhost:8080/api/HttpExample?name=Functions)een lokale container, blader naar , die moet hetzelfde "hallo" bericht weer te geven als voorheen. Omdat het archetype Maven een HTTP-geactiveerde functie genereert die anonieme autorisatie gebruikt, u de functie nog steeds aanroepen, ook al wordt deze in de container uitgevoerd. 
::: zone-end  

Nadat u de functie-app in de container hebt geverifieerd, stopt u docker met **Ctrl**+**C**.

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

1. Meld u aan bij uw Azure-account en selecteer vervolgens de **SSH** om een verbinding met de container tot stand te brengen. Het maken van verbinding kan enkele ogenblikken duren als Azure de containerafbeelding nog steeds bijwerkt.

1. Nadat er een verbinding met uw `top` container is gemaakt, voert u de opdracht uit om de momenteel lopende processen weer te geven. 

    ![Linux top commando draait in een SSH sessie](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Schrijven naar een Azure Storage-wachtrij

Met Azure Functions u uw functies verbinden met andere Azure-services en -resources die uw eigen integratiecode moeten schrijven. Deze *bindingen*, die zowel input als output vertegenwoordigen, worden gedeclareerd binnen de functiedefinitie. Gegevens van bindingen worden als parameters aan de functie geleverd. Een *trigger* is een speciaal type invoerbinding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer- en uitvoerbindingen hebben. Zie Azure Functions triggers [and bindings concepts](functions-triggers-bindings.md)voor meer informatie.

In deze sectie ziet u hoe u uw functie integreren met een Azure Storage-wachtrij. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-verzoek naar een bericht in de wachtrij.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen om de uitvoerbinding te gebruiken

Met de wachtrijbinding gedefinieerd, u nu `msg` uw functie bijwerken om de uitvoerparameter te ontvangen en berichten naar de wachtrij te schrijven.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
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

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
