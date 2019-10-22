---
title: Een door HTTP geactiveerde functie maken in azure
description: Informatie over hoe u met de Azure Functions Core Tools en de Azure CLI uw eerste Python-functie maakt in Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: f2602e5a13f83090291656e7062c74c245bc6568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693353"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Een door HTTP geactiveerde functie maken in azure

In dit artikel leest u hoe u opdracht regel Programma's kunt gebruiken om een python-project te maken dat wordt uitgevoerd in Azure Functions. U maakt ook een functie die wordt geactiveerd door een HTTP-aanvraag. Ten slotte publiceert u uw project om te worden uitgevoerd als een [serverloze functie](functions-scale.md#consumption-plan) in Azure.

Dit artikel is de eerste van twee python-Quick starts voor Azure Functions. Nadat u deze Snelstartgids hebt voltooid, kunt u [een Azure Storage wachtrij-uitvoer binding toevoegen](functions-add-output-binding-storage-queue-python.md) aan uw functie.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende doen:

+ [Python 3.6.8](https://www.python.org/downloads/)installeren. Deze versie van python wordt gecontroleerd met functies. 3,7 en latere versies worden nog niet ondersteund.

+ Installeer [Azure functions core tools](./functions-run-local.md#v2) versie 2.7.1575 of een latere versie.

+ Installeer de [Azure cli](/cli/azure/install-azure-cli) versie 2. x of een nieuwere versie.

+ Een actief Azure-abonnement hebben.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Een virtuele omgeving maken en activeren (optioneel)

U moet een python 3.6. x-omgeving gebruiken voor het lokaal ontwikkelen van python-functies. Voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv` te maken.

> [!NOTE]
> Als python venv niet heeft geïnstalleerd op uw Linux-distributie, kunt u dit installeren met de volgende opdracht:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>Power shell of een Windows-opdracht prompt:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Nu u de virtuele omgeving hebt geactiveerd, voert u de resterende opdrachten uit. Als u van de virtuele omgeving wilt profiteren, voert u `deactivate` uit.

## <a name="create-a-local-functions-project"></a>Een project met lokale functies maken

Een functions-project is het equivalent van een functie-app in Azure. Het kan meerdere functies hebben die allemaal dezelfde lokale en hosting configuraties delen.

1. Voer de volgende opdracht uit in de virtuele omgeving:

    ```console
    func init MyFunctionProj
    ```

1. Selecteer **python** als uw runtime voor de werk nemer.

    Met de opdracht maakt u een map _MyFunctionProj_ . Deze bevat de volgende drie bestanden:

    * *Local. settings. json*: wordt gebruikt voor het opslaan van app-instellingen en verbindings reeksen bij het lokaal uitvoeren. Dit bestand wordt niet gepubliceerd naar Azure.
    * *Requirements. txt*: bevat de lijst met pakketten die door het systeem worden geïnstalleerd bij het publiceren naar Azure.
    * *host. json*: bevat globale configuratie opties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt gepubliceerd naar Azure.

1. Ga naar de map New *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Een functie maken

Een functie toevoegen aan het nieuwe project.

1. Als u een functie wilt toevoegen aan uw project, voert u de volgende opdracht uit:

    ```console
    func new
    ```

1. Gebruik de pijl-omlaag om de sjabloon voor **http-triggers** te selecteren.

1. Wanneer u wordt gevraagd om een functie naam, voert u *http trigger* in en drukt u op ENTER.

Met deze opdrachten maakt u een submap met de naam _http trigger_. Het bevat de volgende bestanden:

* *Function. json*: configuratie bestand dat de functie, trigger en andere bindingen definieert. U ziet dat in dit bestand de waarde voor `scriptFile` verwijst naar het bestand met de functie en de `bindings`-matrix definieert de aanroep-en bindingen.

    Elke binding vereist een richting, type en een unieke naam. De HTTP-trigger heeft een invoer binding van het type [`httpTrigger`](functions-bindings-http-webhook.md#trigger) en de uitvoer binding van het type [`http`](functions-bindings-http-webhook.md#output).

* *\_ \_init \_ \_. py*: script bestand dat uw door http geactiveerde functie is. U ziet dat dit script een standaard `main()` heeft. HTTP-gegevens van de trigger worden door gegeven aan de functie met behulp van de `req` met de naam `binding parameter`. De `req`, die is gedefinieerd in function. json, is een instantie van de [klasse Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    Het retour object, gedefinieerd als `$return` in *Function. json*, is een instantie van de [klasse Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure functions HTTP-triggers en-bindingen](functions-bindings-http-webhook.md)voor meer informatie.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

De functie wordt lokaal uitgevoerd met behulp van de Azure Functions runtime.

1. Met deze opdracht wordt de functie-app gestart:

    ```console
    func host start
    ```

    Wanneer de Azure Functions host wordt gestart, wordt er iets zoals de volgende uitvoer geschreven. Het is hier afgekapt, zodat u het kunt lezen:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Kopieer de URL van uw `HttpTrigger`-functie uit de uitvoer van de runtime en plak deze in de adresbalk van uw browser.

1. Voeg de queryreeks `?name=<yourname>` toe aan de URL en voer de aanvraag uit. In de volgende scherm afbeelding ziet u het antwoord op de GET-aanvraag die de lokale functie retourneert naar de browser:

    ![Lokaal verifiëren in de browser](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Selecteer CTRL + C om uw functie-app af te sluiten.

Nu u de functie lokaal hebt uitgevoerd, kunt u de functie-app en andere vereiste resources maken in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Een functie-app maken in azure

Een functie-app biedt een omgeving voor het uitvoeren van de functie code. U kunt er functies mee groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Voer de volgende opdracht uit. Vervang `<APP_NAME>` door een unieke naam voor de functie-app. Vervang `<STORAGE_NAME>` door de naam van het opslag account. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. Deze naam moet uniek zijn in alle apps in Azure.

> [!NOTE]
> U kunt geen Linux-en Windows-apps hosten in dezelfde resource groep. Als u een bestaande resource groep hebt met de naam `myResourceGroup` met een Windows-functie-app of web-app, moet u een andere resource groep gebruiken.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Met de voor gaande opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht. U kunt deze instantie gebruiken om uw functie-app te controleren en logboeken te bekijken.

U bent nu klaar om uw lokale functions-project te publiceren naar de functie-app in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Het functie-appproject implementeren in Azure

Nadat u de functie-app in azure hebt gemaakt, kunt u de opdracht [func Azure functionapp Publish](functions-run-local.md#project-file-deployment) core tools gebruiken om uw project code te implementeren in Azure. Vervang `<APP_NAME>` in dit voor beeld door de naam van uw app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

Met de optie `--build remote` bouwt u uw python-project op afstand in azure van de bestanden in het implementatie pakket. 

U ziet uitvoer die vergelijkbaar is met het volgende bericht. Het is hier afgekapt, zodat u het kunt lezen:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

U kunt de `Invoke url` waarde voor uw `HttpTrigger` kopiëren en gebruiken om de functie in azure te controleren. De URL bevat een `code` query teken reeks waarde die uw functie sleutel, waardoor het voor anderen moeilijk is om het eind punt van de HTTP-trigger aan te roepen in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Gebruik de [Application Insights Live Metrics stream](functions-monitoring.md#streaming-logs)om bijna realtime logboeken voor een gepubliceerde python-app weer te geven.

## <a name="next-steps"></a>Volgende stappen

U hebt een python-functies project gemaakt met een door HTTP geactiveerde functie, deze uitvoeren op uw lokale computer en geïmplementeerd in Azure. Breid uw functie nu uit door...

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij-uitvoer binding toevoegen](functions-add-output-binding-storage-queue-python.md)
