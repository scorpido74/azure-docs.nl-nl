---
title: Azure Functions op Linux maken met behulp van een aangepaste installatie kopie
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: fee4e16bd77664e541eeb36cb807a77d13191899
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165719"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Een functie in Linux maken met behulp van een aangepaste container

In deze zelf studie maakt en implementeert u uw code om Azure Functions als een aangepaste docker-container met een Linux-basis installatie kopie. Normaal gesp roken gebruikt u een aangepaste installatie kopie wanneer uw functies een specifieke taal versie vereisen of een specifieke afhankelijkheid of configuratie hebben die niet wordt opgegeven door de ingebouwde installatie kopie.

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

U kunt deze zelf studie volgen op elke computer waarop Windows, macOS of Linux wordt uitgevoerd. Als u de zelf studie voltooit, worden er kosten in rekening gebracht voor een of meer Amerikaanse dollars in uw Azure-account.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Een [docker-id](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Voer `docker login` uit om aan te melden bij docker. Deze opdracht mislukt als docker niet actief is. in dat geval wordt docker gestart en wordt de opdracht opnieuw uitgevoerd.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Het project met lokale functies maken en testen

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer in een Terminal-of opdracht prompt de volgende opdracht uit voor de taal die u hebt gekozen om een functie-app- `LocalFunctionsProject`project te maken in een map met de naam.  
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

# <a name="bash"></a>[bash](#tab/bash)
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

Maven vraagt u om de waarden die nodig zijn om het project te kunnen genereren tijdens de implementatie.   
Geef de volgende waarden op wanneer u hierom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **Groep** | `com.fabrikam` | Een waarde die uw project op unieke wijze identificeert voor alle projecten, volgens de [pakket naamgevings regels](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artifactId** | `fabrikam-functions` | Een waarde die bestaat uit de naam van het jar, zonder een versie nummer. |
| **Versie** | `1.0-SNAPSHOT` | Kies de standaard waarde. |
| **pakket** | `com.fabrikam.functions` | Een waarde die het Java-pakket voor de gegenereerde functie code is. Gebruik de standaard. |

Typ `Y` of druk op ENTER om te bevestigen.

Maven maakt de project bestanden in een nieuwe map met de naam _artifactId_, in dit voor beeld `fabrikam-functions`. 
::: zone-end
Met `--docker` deze optie wordt `Dockerfile` een voor het project gegenereerd, waarmee een geschikte aangepaste container wordt gedefinieerd voor gebruik met Azure functions en de geselecteerde runtime.

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
Voeg een functie toe aan uw project met behulp van de volgende opdracht `--name` , waarbij het argument de unieke naam van de functie `--template` is en het argument de trigger van de functie specificeert. `func new`Maak een submap die overeenkomt met de naam van de functie die een code bestand bevat dat geschikt is voor de geselecteerde taal van het project en een configuratie bestand met de naam *Function. json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Als u de functie lokaal wilt testen, start u de lokale Azure Functions runtime host in de hoofdmap van de projectmap: 
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
Wanneer het `HttpExample` eind punt wordt weer gegeven in de uitvoer, gaat `http://localhost:7071/api/HttpExample?name=Functions`u naar. De browser moet een bericht ' Hallo ' weer geven dat terugkeert `Functions`, de waarde die is `name` opgegeven voor de query parameter.

Gebruik **CTRL**-**C** om de host te stoppen.

## <a name="build-the-container-image-and-test-locally"></a>De container installatie kopie bouwen en lokaal testen

Beschrijving Bekijk de * Dockerfile in de hoofdmap van de projectmap. De Dockerfile beschrijft de vereiste omgeving voor het uitvoeren van de functie-app in Linux.  U vindt de volledige lijst met ondersteunde basis installatie kopieën voor Azure Functions op de [pagina basis installatie kopie van Azure functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
Voer in de hoofdmap van het hoofd project de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) uit en geef een naam `azurefunctionsimage`,, en label `v1.0.0`, op. Vervang `<DOCKER_ID>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Wanneer de opdracht is voltooid, kunt u de nieuwe container lokaal uitvoeren.
    
Als u de build wilt testen, voert u de installatie kopie in een lokale container met behulp van de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) uit, vervangt u opnieuw `<DOCKER_ID` met uw `-p 8080:80`docker-id en voegt u het argument poorten toe:

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Zodra de installatie kopie in een lokale container wordt uitgevoerd, opent u een `http://localhost:8080`browser naar, waarin de tijdelijke aanduiding moet worden weer gegeven hieronder. De afbeelding wordt op dit moment weer gegeven omdat uw functie wordt uitgevoerd in de lokale container, zoals in azure, wat betekent dat deze wordt beveiligd door een toegangs sleutel zoals gedefinieerd in *Function. json* met de `"authLevel": "function"` eigenschap. De container is nog niet gepubliceerd naar een functie-app in azure, maar de sleutel is nog niet beschikbaar. Als u wilt testen op de lokale container, beëindigt u docker, wijzigt u de autorisatie `"authLevel": "anonymous"`-eigenschap in, bouwt u de installatie kopie opnieuw en start u docker opnieuw op. Vervolgens opnieuw `"authLevel": "function"` instellen in *Function. json*. Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie.

![Tijdelijke afbeelding die aangeeft dat de container lokaal wordt uitgevoerd](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Zodra de installatie kopie in een lokale container wordt uitgevoerd, bladert u naar `http://localhost:8080/api/HttpExample?name=Functions`, waarin hetzelfde bericht ' Hallo ' moet worden weer gegeven. Omdat de Maven archetype een door HTTP geactiveerde functie genereert die gebruikmaakt van anonieme autorisatie, kunt u de functie toch aanroepen, zelfs als deze wordt uitgevoerd in de container. 
::: zone-end  

Nadat u de functie-app in de container hebt geverifieerd, stopt u docker met **CTRL**+**C**.

## <a name="push-the-image-to-docker-hub"></a>De installatie kopie pushen naar docker hub

Docker hub is een container register dat installatie kopieën host en installatie kopie-en container Services biedt. Als u uw installatie kopie wilt delen, met inbegrip van implementatie naar Azure, moet u deze naar een REGI ster pushen.

1. Als u zich nog niet hebt aangemeld bij docker, doet u dit met de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) , vervangen `<docker_id>` door uw docker-id. Met deze opdracht wordt u gevraagd om uw gebruikers naam en wacht woord. In het bericht ' Aanmelden is geslaagd ' wordt bevestigd dat u bent aangemeld.

    ```
    docker login
    ```
    
1. Nadat u zich hebt aangemeld, pusht u de installatie kopie naar docker hub met behulp van de opdracht [docker push](https://docs.docker.com/engine/reference/commandline/push/) en vervangt `<docker_id>` u de docker-ID opnieuw.

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
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource `AzureFunctionsContainers-rg` groep met `westeurope` de naam in de regio gemaakt. (In het algemeen maakt u de resource groep en-resources in een regio bij u in de buurt met `az account list-locations` behulp van een beschik bare regio van de opdracht.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met `AzureFunctionsContainers-rg` de naam met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.
    
1. Maak een opslag account voor algemeen gebruik in uw resource groep en regio met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) . Vervang `<storage_name>` in het volgende voor beeld door een globaal unieke naam die geschikt is voor u. Namen mogen alleen uit drie tot 24 tekens bestaan en mag alleen kleine letters bevatten. `Standard_LRS`Hiermee geeft u een typische account voor algemeen gebruik op.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Het opslag account maakt voor deze zelf studie slechts een enkele USD cent.
    
1. Gebruik de opdracht voor het maken van een Premium-plan `myPremiumPlan` voor Azure functions met de naam in de prijs categorie **elastisch Premium 1** (`--sku EP1`),`-location westeurope`in de Europa-West regio (of gebruik een geschikte regio bij u in de buurt)`--is-linux`, en in een Linux-container ().

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux-hosting voor aangepaste functies-containers wordt ondersteund op [speciale (app service) plannen](functions-scale.md#app-service-plan) en [Premium-abonnementen](functions-premium-plan.md#features). We gebruiken hier het Premium-plan, dat kan worden geschaald naar behoefte. Zie [deze vergelijking van hostingabonnementen van Azure Functions](functions-scale.md) voor meer informatie over hosting. Zie de pagina met prijzen voor [functies](https://azure.microsoft.com/pricing/details/functions/)voor het berekenen van de kosten.

    Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht, waarmee u uw functie-app kunt bewaken en logboeken weer geven. Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie. Er worden geen kosten in rekening gebracht totdat u deze activeert.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Een functie-app in azure maken en configureren met de installatie kopie

Een functie-app in azure beheert de uitvoering van uw functies in uw hosting abonnement. In deze sectie gebruikt u de Azure-resources uit de vorige sectie om een functie-app te maken op basis van een installatie kopie op docker hub en deze te configureren met een connection string voor Azure Storage.

1. Maak de functions-app met de opdracht [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . Vervang `<storage_name>` in het volgende voor beeld door de naam die u hebt gebruikt in de vorige sectie voor het opslag account. Vervang `<app_name>` ook door een globaal unieke naam die geschikt is voor u `<docker_id>` en met uw docker-id.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Met de para meter *implementatie-container-image-name* wordt de afbeelding opgegeven die moet worden gebruikt voor de functie-app. U kunt de opdracht [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) gebruiken om informatie weer te geven over de installatie kopie die wordt gebruikt voor de implementatie. U kunt ook de opdracht [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) gebruiken om vanaf een andere installatie kopie te implementeren.

1. Haal de connection string op voor het opslag account dat u hebt gemaakt met behulp van de opdracht [AZ Storage account show-Connection-String](/cli/azure/storage/account) en wijs `storageConnectionString`deze toe aan een shell-variabele:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Voeg deze instelling toe aan de functie-app met behulp van de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) . Vervang `<app_name>` in de volgende opdracht door de naam van uw functie-app en vervang `<connection_string>` door de Connection String uit de vorige stap (een lange gecodeerde teken reeks die begint met ' DefaultEndpointProtocol = '):
 
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
> Als u uw aangepaste installatie kopie naar een persoonlijk container account publiceert, moet u in plaats daarvan omgevings variabelen in de Dockerfile voor de connection string gebruiken. Zie de [env-instructie](https://docs.docker.com/engine/reference/builder/#env)voor meer informatie. U moet ook de variabelen `DOCKER_REGISTRY_SERVER_USERNAME` en `DOCKER_REGISTRY_SERVER_PASSWORD`opgeven. Als u de waarden wilt gebruiken, moet u de installatie kopie opnieuw opbouwen, de installatie kopie naar het REGI ster pushen en vervolgens de functie-app opnieuw starten in Azure.

## <a name="verify-your-functions-on-azure"></a>Uw functies in azure controleren

Als de installatie kopie is geïmplementeerd in de functie-app in azure, kunt u de functie nu via HTTP-aanvragen aanroepen. Omdat de *functie. json* -definitie de eigenschap `"authLevel": "function"`bevat, moet u eerst de toegangs sleutel (ook wel de functie sleutel genoemd) ophalen en deze opnemen als een URL-para meter in alle aanvragen voor het eind punt.

1. Haal de functie-URL op met de Access-sleutel (functie) met behulp van de Azure Portal of gebruik de Azure `az rest` CLI met de opdracht.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Meld u aan bij de Azure Portal en zoek vervolgens uw functie-app door de naam van uw functie-app in te voeren in het **zoekvak** boven aan de pagina. Selecteer in de resultaten de **app service** resource.

    1. Selecteer in het navigatie paneel aan de linkerkant onder **functies (alleen-lezen)** de naam van uw functie.

    1. Selecteer in het deel venster Details **</> functie-URL ophalen**:
    
        ![De opdracht functie-URL ophalen in het Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Selecteer in de pop-up de optie **standaard (functie toets)** en **Kopieer**vervolgens. De sleutel is de teken reeks die volgt `?code=`op de tekens.

        ![De functie-URL uit de Azure Portal kopiëren](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Omdat uw functie-app als een container is geïmplementeerd, kunt u geen wijzigingen aanbrengen in uw functie code in de portal. U moet in plaats daarvan het project bijwerken in de lokale installatie kopie, de installatie kopie naar het REGI ster pushen en vervolgens opnieuw implementeren naar Azure. U kunt in een later stadium doorlopende implementatie instellen.
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

    1. Een URL-teken reeks in de volgende indeling maken `<subscription_id>`, `<resource_group>`vervangen, `<app_name>` en met uw Azure-abonnements-id, de resource groep van de functie-app en de naam van uw functie-app:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        De URL kan bijvoorbeeld het volgende adres zien:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Voor het gemak kunt u in plaats daarvan de URL toewijzen aan een omgevings variabele en deze `az rest` gebruiken in de opdracht.
    
    1. Voer de volgende `az rest` opdracht uit (beschikbaar in de Azure CLI-versie 2.0.77 en hoger) `<uri>` , vervangen door de URI-teken reeks uit de laatste stap, inclusief de aanhalings tekens:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. De uitvoer van de opdracht is de functie toets. De volledige functie-URL is `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`vervolgens vervangen `<app_name>`, `<function_name>`en `<key>` met uw specifieke waarden.
    
        > [!NOTE]
        > De sleutel die hier wordt opgehaald is de *host* -sleutel die werkt voor alle functies in de app functions. met de methode die wordt weer gegeven voor de portal, wordt de sleutel voor de ene functie opgehaald.

    ---

1. Plak de functie-URL in de adres balk van uw browser en voeg `&name=Azure` de para meter toe aan het einde van deze URL. Tekst als ' Hello Azure ' moet worden weer gegeven in de browser.

    ![Het antwoord van de functie in de browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. U kunt de autorisatie testen door de code = para meter uit de URL te verwijderen en te controleren of u geen reactie krijgt van de functie.


## <a name="enable-continuous-deployment-to-azure"></a>Continue implementatie naar Azure inschakelen

U kunt Azure Functions instellen dat uw implementatie van een installatie kopie automatisch wordt bijgewerkt wanneer u de installatie kopie in het REGI ster bijwerkt.

1. Continue implementatie inschakelen met behulp van [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) Command `<app_name>` , vervangen door de naam van uw functie-app:

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

1. Voeg in uw Dockerfile de teken reeks `-appservice` toe aan de basis installatie kopie `FROM` in uw instructie:

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

1. Bouw de installatie kopie opnieuw met behulp van de `docker build` opdracht `<docker_id>` opnieuw, vervangen door uw docker-id:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push de bijgewerkte installatie kopie naar docker hub. deze moet aanzienlijk minder tijd in beslag nemen dan de eerste push alleen de bijgewerkte segmenten van de afbeelding moeten worden geüpload.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions de installatie kopie automatisch opnieuw implementeert in uw functions-app; het proces vindt minder dan een minuut plaats.

1. Open `https://<app_name>.scm.azurewebsites.net/`in een browser, waarbij u `<app_name>` vervangt door uw unieke naam. Deze URL is het kudu-eind punt (Advanced tools) voor uw functie-app-container.

1. Meld u aan bij uw Azure-account en selecteer vervolgens de **SSH** om een verbinding met de container tot stand te brengen. Het maken van een verbinding kan enige tijd duren als Azure de container installatie kopie nog steeds bijwerkt.

1. Nadat een verbinding tot stand is gebracht met uw container, `top` voert u de opdracht uit om de actieve processen weer te geven. 

    ![Bovenste Linux-opdracht die in een SSH-sessie wordt uitgevoerd](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Schrijven naar een Azure Storage wachtrij

Met Azure Functions kunt u uw functies verbinden met andere Azure-Services en-resources die uw eigen integratie code schrijven. Deze *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd in de functie definitie. Gegevens van bindingen worden als parameters aan de functie geleverd. Een *trigger* is een speciaal type invoer binding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer-en uitvoer bindingen hebben. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In deze sectie wordt beschreven hoe u uw functie integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen voor het gebruik van de uitvoer binding

Als de wachtrij binding is gedefinieerd, kunt u de functie nu bijwerken om de `msg` uitvoer parameter te ontvangen en berichten te schrijven naar de wachtrij.

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

### <a name="update-the-image-in-the-registry"></a>De installatie kopie in het REGI ster bijwerken

1. Voer `docker build` de bewerking opnieuw uit in de hoofdmap en werk de versie in de tag bij naar `v1.0.1`. Vervang als voorheen door `<docker_id>` de account-id van uw docker hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Push de bijgewerkte installatie kopie terug naar de opslag `docker push`plaats met:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Omdat u continue levering hebt geconfigureerd, wordt de functie-app in azure automatisch bijgewerkt wanneer u de installatie kopie in het REGI ster bijwerkt.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht in de wachtrij van Azure Storage weer geven

Gebruik in een browser dezelfde URL als voordat u de functie aanroept. De browser moet hetzelfde antwoord weer geven als voorheen, omdat u dat deel van de functie code niet hebt gewijzigd. De toegevoegde code schrijft echter een bericht met behulp van `name` de URL-para `outqueue` meter naar de opslag wachtrij.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven werken met de Azure-functie met behulp van de resources die u in deze zelf studie hebt gemaakt, kunt u alle resources op locatie laten staan. Omdat u een Premium-abonnement voor Azure Functions hebt gemaakt, kunt u een of twee USD per dag besparen op lopende kosten.

Om lopende kosten te voor komen, `AzureFunctionsContainer-rg` verwijdert u de resource groep om alle resources in de groep op te schonen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Volgende stappen

+ [Bewakings functies](functions-monitoring.md)
+ [Opties voor schalen en hosten](functions-scale.md)
+ [Op Kubernetes gebaseerde serverloze hosting](functions-kubernetes-keda.md)
