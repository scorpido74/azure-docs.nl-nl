---
title: Een Azure Storage wachtrij binding toevoegen aan uw python-functie
description: Meer informatie over het toevoegen van een Azure Storage wachtrij-uitvoer binding aan uw python-functie met behulp van de Azure CLI en functions core-Hulpprogram Ma's.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171091"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Een Azure Storage wachtrij binding toevoegen aan uw python-functie

Met Azure Functions kunt u Azure-Services en andere resources verbinden met functies zonder dat u uw eigen integratie code hoeft te schrijven. Deze *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd in de functie definitie. Gegevens van bindingen worden aan de functie door gegeven als para meters. Een *trigger* is een speciaal type invoer binding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer-en uitvoer bindingen hebben. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In dit artikel wordt beschreven hoe u de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-function-python.md) start-artikel integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Als u deze verbinding eenvoudiger wilt maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling `AzureWebJobsStorage`met de naam.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u de stappen in [deel 1 van de python-Snelstartgids](functions-create-first-function-python.md)volt ooien.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het vorige Quick Start-artikel hebt u een functie-app in azure gemaakt, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local. settings. json. Voer de volgende Azure functions core tools opdracht uit om instellingen te downloaden naar local. settings. json `<APP_NAME>` , vervangen door de naam van uw functie-app uit het vorige artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Mogelijk moet u zich aanmelden bij uw Azure-account.

> [!IMPORTANT]  
> Omdat deze geheimen bevat, wordt het bestand local. settings. json nooit gepubliceerd en moet het worden uitgesloten van broncode beheer.

U hebt de waarde `AzureWebJobsStorage`nodig die het opslag account Connection String. U gebruikt deze verbinding om te controleren of de uitvoer binding werkt zoals verwacht.

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U kunt nu de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, a `type`en uniek `name` worden gedefinieerd in het bestand function. json. Afhankelijk van het type binding zijn er mogelijk aanvullende eigenschappen vereist. De [uitvoer configuratie](functions-bindings-storage-queue.md#output---configuration) van de wachtrij beschrijft de velden die vereist zijn voor een Azure Storage binding van de wachtrij.

Als u een binding wilt maken, voegt u een bindings configuratie object toe aan het bestand function. json. Bewerk het bestand function. json in de map http trigger om een object toe te voegen aan de matrix met de `bindings` volgende eigenschappen:

| Eigenschap | Waarde | Description |
| -------- | ----- | ----------- |
| **`name`** | `msg` | De naam die de bindings parameter identificeert waarnaar in uw code wordt verwezen. |
| **`type`** | `queue` | De binding is een Azure Storage wachtrij binding. |
| **`direction`** | `out` | De binding is een uitvoer binding. |
| **`queueName`** | `outqueue` | De naam van de wachtrij waarnaar de binding wordt geschreven. Wanneer het `queueName` niet bestaat, wordt deze door de binding gemaakt bij het eerste gebruik. |
| **`connection`** | `AzureWebJobsStorage` | De naam van een app-instelling die de connection string voor het opslag account bevat. De `AzureWebJobsStorage` instelling bevat de Connection String voor het opslag account dat u hebt gemaakt met de functie-app. |

Het bestand function. json moet er nu uitzien als in dit voor beeld:

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

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de `name` is geconfigureerd, kunt u deze gebruiken om toegang te krijgen tot de binding als een methode kenmerk in de functie handtekening. In het volgende voor beeld `msg` is een instantie [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)van.

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

Wanneer u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Gebruik, net als voorheen, de volgende opdracht om de functions-runtime lokaal te starten:

```bash
func host start
```

> [!NOTE]  
> Omdat in de vorige Snelstartgids u de uitbreidings bundels in de host. json hebt ingeschakeld, is de [opslag bindings uitbreiding](functions-bindings-storage-blob.md#packages---functions-2x) tijdens het opstarten gedownload en geïnstalleerd, samen met de andere micro soft-bindings extensies.

Kopieer de URL van uw `HttpTrigger`-functie uit de uitvoer van de runtime en plak deze in de adresbalk van uw browser. Voeg de query reeks `?name=<yourname>` toe aan deze URL en voer de aanvraag uit. U ziet hetzelfde antwoord in de browser zoals u dat in het vorige artikel hebt gedaan.

De uitvoer binding maakt ook een wachtrij met de naam `outqueue` in uw opslag account en voegt een bericht toe met dezelfde teken reeks.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij te bekijken en te controleren of er een bericht is toegevoegd. U kunt uw wachtrij ook weer geven met behulp van de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>De verbinding voor het opslag account instellen

Open het bestand local. settings. json en kopieer de waarde van `AzureWebJobsStorage`, het opslag account Connection String. Stel de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele in op de Connection String met behulp van deze bash-opdracht:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Wanneer u de Connection String instelt in de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele, hebt u toegang tot uw opslag account zonder elke keer verificatie te hoeven opgeven.

### <a name="query-the-storage-queue"></a>Query uitvoeren op de opslag wachtrij

U kunt de [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) opdracht gebruiken om de opslag wachtrijen in uw account weer te geven, zoals in het volgende voor beeld:

```azurecli-interactive
az storage queue list --output tsv
```

De uitvoer van deze opdracht bevat een wachtrij met `outqueue`de naam, die de wachtrij is die is gemaakt toen de functie werd uitgevoerd.

Gebruik vervolgens de [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) opdracht om de berichten in deze wachtrij weer te geven, zoals in dit voor beeld:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

De geretourneerde teken reeks moet hetzelfde zijn als het bericht dat u hebt verzonden om de functie te testen.

> [!NOTE]  
> In het vorige voor beeld wordt de geretourneerde teken reeks van base64 gedecodeerd. De reden hiervoor is dat de bindingen van de wachtrij opslag worden geschreven naar en gelezen van Azure Storage als [Base64-teken reeksen](functions-bindings-storage-queue.md#encoding).

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

U kunt ook krul of een browser gebruiken om de geïmplementeerde functie te testen. Voeg, net als voorheen, de `&name=<yourname>` query reeks toe aan de URL, zoals in dit voor beeld:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

U kunt [het bericht van de opslag wachtrij bekijken](#query-the-storage-queue) om te controleren of de uitvoer binding opnieuw een nieuw bericht in de wachtrij genereert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw door HTTP geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van Azure Functions met python de [Azure functions python-ontwikkelaars handleiding](functions-reference-python.md) en [Azure functions triggers en bindingen](functions-triggers-bindings.md). Zie voor voor beelden van complete functie projecten in python de [python functions](/samples/browse/?products=azure-functions&languages=python)-voor beelden. 

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/