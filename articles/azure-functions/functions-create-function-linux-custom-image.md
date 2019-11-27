---
title: Azure Functions op Linux maken met behulp van een aangepaste installatie kopie
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 7bf079f84978539735f3bbf5bb13b18130871fb1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484390"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Een functie in Linux maken met behulp van een aangepaste installatie kopie

Met Azure Functions kunt u uw functies voor Linux hosten in uw eigen aangepaste container. U kunt ze ook [hosten op een standaard-Azure App Service-container](functions-create-first-azure-function-azure-cli-linux.md). Deze functionaliteit vereist [de functies 2. x runtime](functions-versions.md).

In deze zelfstudie leert u hoe u uw functies in Azure implementeert als een aangepaste Docker-installatiekopie. Dit patroon is handig wanneer u de ingebouwde container installatie kopie moet aanpassen. U kunt een aangepaste installatiekopie gebruiken wanneer uw functies een specifieke taalversie nodig hebben of een specifieke afhankelijkheid of configuratie vereisen die niet binnen de ingebouwde installatiekopie aanwezig is. Ondersteunde basis installatie kopieën voor Azure Functions vindt u in de [Azure functions basis installatie kopieën opslag plaats](https://hub.docker.com/_/microsoft-azure-functions-base). 

In deze zelfstudie leert u hoe u Azure Functions Core Tools gebruikt om een functie te maken in een aangepaste Linux-installatiekopie. U publiceert deze installatiekopie naar een functie-app in Azure, die is gemaakt met de Azure CLI. Later werkt u de functie bij om verbinding te maken met Azure Queue-opslag. U schakelt ook in.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een functie-app en Dockerfile maken met behulp van Core Tools.
> * Een aangepaste installatiekopie met behulp van Docker bouwen.
> * Een aangepaste installatiekopie naar een containerregister publiceren.
> * Een Azure Storage-account maken.
> * Een Premium-hosting plan maken.
> * Een functie-app vanuit Docker Hub implementeren.
> * Toepassingsinstellingen toevoegen aan de functie-app.
> * Continue implementatie inschakelen.
> * Schakel SSH-verbindingen met de container in.
> * Voeg een uitvoer binding voor de wachtrij opslag toe. 

De volgende stappen worden ondersteund op een Mac-, Windows- of Linux-computer. 

## <a name="prerequisites"></a>Vereisten

Voordat u dit voorbeeld kunt uitvoeren moet u ervoor zorgen dat u het volgende hebt:

* Installeer [Azure Core Tools versie 2.x](functions-run-local.md#v2).

* Installeer de [Azure CLI]( /cli/azure/install-azure-cli). In dit artikel is Azure CLI versie 2.0 of hoger vereist. Voer `az --version` uit om te zien welke versie u hebt.  
U kunt ook de [Azure Cloud Shell](https://shell.azure.com/bash) gebruiken.

* Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Het lokale project maken

Voer de volgende opdracht uit vanaf de opdrachtregel om een functie-app-project te maken in de map `MyFunctionProj` van de huidige lokale map. Voor een python-project [moet u in een virtuele omgeving worden uitgevoerd](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

Wanneer u de optie `--docker` opneemt, wordt een dockerfile voor het project gegenereerd. Dit bestand wordt gebruikt voor het maken van een aangepaste container waarin het project wordt uitgevoerd. De basisinstallatiekopie die wordt gebruikt, is afhankelijk van de gekozen taal voor de runtime van de werkrol.  

Wanneer u hierom wordt gevraagd, kiest u een runtime voor de werkrol uit de volgende talen:

* `dotnet`: Hiermee maakt u een .NET core Class Library-project (. csproj).
* `node`: hiermee maakt u een JavaScript-project.
* `python`: hiermee maakt u een Python-project.  

Gebruik de volgende opdracht om naar de nieuwe projectmap `MyFunctionProj` te navigeren.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Bouwen vanuit het docker-bestand

Bekijk de _Dockerfile_ in de hoofdmap van het project. Dit bestand beschrijft de omgeving die vereist is om de functie-app uit te voeren in Linux. Het volgende voorbeeld is een Dockerfile dat een container maakt die een functie-app uitvoert in runtime van de werkrol voor JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> U vindt de volledige lijst met ondersteunde basis installatie kopieën voor Azure Functions op de [pagina basis installatie kopie van Azure functions](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>De opdracht `build` uitvoeren

Voer in de hoofdmap de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) uit en geef vervolgens een naam, `mydockerimage` en een tag, `v1.0.0`, op. Vervang `<docker-id>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Wanneer de opdracht is voltooid, kunt u de nieuwe container lokaal uitvoeren.

### <a name="run-the-image-locally"></a>De installatie kopie lokaal uitvoeren
Controleer of de installatiekopie die u hebt gebouwd werkt door de Docker-installatiekopie uit te voeren in een lokale container. Geef de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) en geef de naam en het label van de installatiekopie door. Zorg ervoor dat u de poort opgeeft met behulp van het argument `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Met de aangepaste installatiekopie die wordt uitgevoerd in een lokale Docker-container controleert u of de functie-app en container goed werken door te bladeren naar <http://localhost:8080>.

![Voer de functie-app lokaal uit.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Wanneer u op dit moment probeert om uw specifieke HTTP-functie aan te roepen, ontvangt u een HTTP 401-fout melding. Dit komt doordat de functie in de lokale container wordt uitgevoerd zoals in azure, wat betekent dat de functie sleutel vereist is. Omdat de container nog niet is gepubliceerd naar een functie-app, is er geen functie sleutel beschikbaar. U ziet later dat wanneer u de basis Hulpprogramma's gebruikt voor het publiceren van uw container, de functie toetsen worden weer gegeven. Als u de functie die wordt uitgevoerd in de lokale container wilt testen, kunt u de [autorisatie sleutel](functions-bindings-http-webhook.md#authorization-keys) wijzigen in `anonymous`. 

Nadat u de functie-app in de container hebt geverifieerd, stopt u de uitvoering. U kunt nu de aangepaste installatiekopie naar uw Docker Hub-account pushen.

## <a name="push-to-docker-hub"></a>Pushen naar docker hub

Een register is een toepassing die als host fungeert voor installatiekopieën en biedt installatiekopie- en containerservices. Om uw installatiekopie te delen, moet u deze naar een register pushen. Docker Hub is een register voor Docker-installatiekopieën waarmee u uw eigen openbare of particuliere opslagplaatsen kunt hosten.

Voordat u een installatiekopie kunt pushen, moet u zich aanmelden bij Docker Hub met behulp van de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/). Vervang `<docker-id>` door uw accountnaam en typ uw wachtwoord bij de opdrachtprompt in de console. Zie [documentatie bij de opdracht voor dockeraanmelding](https://docs.docker.com/engine/reference/commandline/login/) voor andere wachtwoordopties voor Docker Hub.

```bash
docker login --username <docker-id>
```

Bij een bericht dat de aanmelding is geslaagd, wordt bevestigd dat u bent aangemeld. Nadat u zich hebt aangemeld, pusht u de installatiekopie naar Docker Hub met behulp van de [docker push](https://docs.docker.com/engine/reference/commandline/push/)-opdracht.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Nadat de push is voltooid, kunt u de installatie kopie gebruiken als de implementatie bron voor een nieuwe functie-app in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

Linux-hosting voor aangepaste functies containers wordt ondersteund op [speciale (app service) plannen](functions-scale.md#app-service-plan) en [Premium-abonnementen](functions-premium-plan.md#features). In deze zelf studie wordt een Premium-abonnement gebruikt dat naar behoefte kan worden geschaald. Zie [deze vergelijking van hostingabonnementen van Azure Functions](functions-scale.md) voor meer informatie over hosting.

In het volgende voor beeld wordt een Premium-plan gemaakt met de naam `myPremiumPlan` in de prijs categorie **elastisch Premium 1** (`--sku EP1`), in de regio vs West (`-location WestUS`) en in een Linux-container (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Een app maken op basis van de installatie kopie

De functie-app beheert de uitvoering van uw functies in uw hosting abonnement. Een functie-app maken op basis van een Docker Hub-installatiekopie met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Vervang in de volgende opdracht de plaatsaanduiding `<app_name>` en de opslagaccountnaam voor `<storage_name>` met een unieke functie-appnaam. De `<app_name>` wordt gebruikt als het standaard DNS-domein voor de functie-app. Om die reden moet de naam uniek zijn in alle apps in Azure. Net als voorheen is `<docker-id>` de naam van uw Docker-account.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

De _deployment-container-image-name_-parameter geeft aan dat de afbeelding die wordt gehost op Docker Hub wordt gebruikt om de functie-app te maken. Gebruik de opdracht [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) om informatie weer te geven over de installatie kopie die wordt gebruikt voor de implementatie. Gebruik de opdracht [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) om vanaf een andere installatie kopie te implementeren.

## <a name="configure-the-function-app"></a>De functie-app configureren

De functie heeft de verbindingsreeks nodig om verbinding te maken met het standaardopslagaccount. Wanneer u uw aangepaste installatie kopie publiceert naar een persoonlijk container account, moet u deze toepassings instellingen instellen als omgevings variabelen in de Dockerfile met behulp van de [env-instructie](https://docs.docker.com/engine/reference/builder/#env)of iets dergelijks.

In dit geval is `<storage_name>` de naam van het opslagaccount dat u hebt gemaakt. Haal de verbindingsreeks op met de opdracht [az storage account show-connection-string](/cli/azure/storage/account). Voeg deze toepassingsinstellingen toe aan de functie-app met de opdracht [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Als u een privécontainer gebruikt, moet u ook de volgende toepassingsinstellingen instellen  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> U moet uw functie-app stoppen en opnieuw starten om deze waarden op te halen

## <a name="verify-your-functions"></a>Uw functies controleren

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

De door HTTP geactiveerde functie die u hebt gemaakt, vereist een [functie sleutel](functions-bindings-http-webhook.md#authorization-keys) bij het aanroepen van het eind punt. Op dit moment is de gemakkelijkste manier om de URL van de functie, inclusief de sleutel, op te halen uit de [Azure Portal]. 

> [!TIP]
> U kunt ook uw functie sleutels verkrijgen met behulp van de [Key Management-api's](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), waarvoor u een [Bearer-token moet gebruiken voor verificatie](/cli/azure/account#az-account-get-access-token).

Zoek de nieuwe functie-app in het [Azure Portal] door de naam van uw functie-app in het **zoekvak** boven aan de pagina te typen en de **app service** resource te selecteren.

Selecteer de functie **MyHttpTrigger** , selecteer **</> functie-URL ophalen** > **standaard (functie toets)**  > **kopiëren**.

![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

In deze URL is de functie sleutel de `code` query parameter. 

> [!NOTE]  
> Omdat uw functie-app als een container is geïmplementeerd, kunt u geen wijzigingen aanbrengen in uw functie code in de portal. U moet in plaats daarvan het project bijwerken in de lokale container en opnieuw publiceren naar Azure.

Plak de URL van de functie in de adresbalk van uw browser. Voeg waarde `&name=<yourname>` voor de querytekenreeks toe aan het einde van deze URL, en druk op de toets `Enter` op het toetsenbord om de aanvraag uit te voeren. U ziet het geretourneerde antwoord van de functie nu in de browser.

Het volgende voorbeeld toont het antwoord in de browser:

![Het antwoord van de functie in de browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie. 

## <a name="enable-continuous-deployment"></a>Continue implementatie inschakelen

Een van de voor delen van het gebruik van containers is ondersteuning voor continue implementatie. Met functies kunt u automatisch updates implementeren wanneer de container in het REGI ster wordt bijgewerkt. Schakel doorlopende implementatie in met de opdracht [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Met deze opdracht wordt de URL van de implementatie-webhook geretourneerd nadat de continue implementatie is ingeschakeld. U kunt ook de opdracht [AZ functionapp Deployment container show-cd-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) gebruiken om deze URL te retour neren. 

Kopieer de implementatie-URL en blader naar uw DockerHub opslag plaats, kies het tabblad **webhooks** , typ de **naam** van de webhook voor de webhook, plak uw URL in de **webhook-URL**en kies vervolgens het plus teken ( **+** ).

![De webhook in uw DockerHub opslag plaats toevoegen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Als de webhook is ingesteld, worden alle updates van de gekoppelde afbeelding in DockerHub resultaat in de functie-app voor het downloaden en installeren van de nieuwste installatie kopie.

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als SSH is ingeschakeld, kunt u verbinding maken met uw container met behulp van App Service geavanceerde hulp middelen (kudu). Functies bieden een basis installatie kopie waarvoor SSH al is ingeschakeld, zodat u eenvoudig verbinding kunt maken met uw container via SSH. 

### <a name="change-the-base-image"></a>De basis installatie kopie wijzigen

Voeg in uw dockerfile de teken reeks `-appservice` toe aan de basis installatie kopie in uw `FROM`-instructie, die voor een Java script-project er als volgt uitziet.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Met de verschillen in de twee basis installatie kopieën worden SSH-verbindingen in uw container ingeschakeld. Deze verschillen worden beschreven in [deze app Services zelf studie](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>De installatie kopie opnieuw samen stellen en implementeren

Voer in de hoofdmap de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) opnieuw uit, zoals voorheen, vervang `<docker-id>` door de account-id van uw docker hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push de bijgewerkte installatie kopie terug naar docker hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

De bijgewerkte installatie kopie wordt opnieuw geïmplementeerd in uw functie-app.

### <a name="connect-to-your-container-in-azure"></a>Verbinding maken met uw container in azure

Ga in de browser naar de volgende geavanceerde tools (kudu) `scm.`-eind punt voor uw functie-app-container en vervang `<app_name>` door de naam van uw functie-app.

```
https://<app_name>.scm.azurewebsites.net/
```

Meld u aan bij uw Azure-account en selecteer vervolgens het tabblad **SSH** om een SSH-verbinding in uw container te maken.

Nadat de verbinding tot stand is gebracht, voert u de `top` opdracht uit om de actieve processen weer te geven. 

![De bovenste Linux-opdracht die in een SSH-sessie wordt uitgevoerd.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Schrijven naar wachtrij opslag

Met functies kunt u Azure-Services en andere resources verbinden met functies zonder dat u uw eigen integratie code hoeft te schrijven. Deze *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd in de functie definitie. Gegevens van bindingen worden aan de functie door gegeven als para meters. Een *trigger* is een speciaal type invoer binding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer-en uitvoer bindingen hebben. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In deze sectie wordt beschreven hoe u uw functie integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

### <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

Omdat u een uitvoer binding voor de wachtrij opslag gebruikt, moet u de extensie opslag bindingen hebben geïnstalleerd voordat u het project uitvoert. 


# <a name="javascript--pythontabnodejspython"></a>[Java script/python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Met uitzonde ring van HTTP-en timer triggers worden bindingen geïmplementeerd als uitbreidings pakketten. Voer de volgende [DotNet-opdracht add package](/dotnet/core/tools/dotnet-add-package) uit in het Terminal venster om het opslag extensie pakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Wanneer u Visual Studio gebruikt, kunt u ook NuGet Package Manager gebruiken om dit pakket toe te voegen.

---

U kunt nu een opslag-uitvoer binding toevoegen aan uw project.

### <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand function. json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

# <a name="javascript--pythontabnodejspython"></a>[Java script/python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` van de binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>De gehoste container bijwerken

Voer in de hoofdmap de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) opnieuw uit en deze tijd werkt de versie in de tag bij naar `v1.0.2`. Vervang `<docker-id>` door de account-ID van uw docker hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push de bijgewerkte installatie kopie terug naar de opslag plaats.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>De updates in azure controleren

Gebruik dezelfde URL als voorheen in de browser om uw functie te activeren. U ziet hetzelfde antwoord. Deze keer wordt echter de teken reeks die u doorgeeft als de `name` para meter, naar de `outqueue`-opslag wachtrij geschreven.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u uw aangepaste container hebt geïmplementeerd in een functie-app in azure, kunt u meer lezen over de volgende onderwerpen:

+ [Bewakings functies](functions-monitoring.md)
+ [Opties voor schalen en hosten](functions-scale.md)
+ [Op Kubernetes gebaseerde serverloze hosting](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
