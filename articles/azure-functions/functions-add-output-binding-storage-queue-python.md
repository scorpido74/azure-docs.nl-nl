---
title: Een Azure Storage wachtrij binding toevoegen aan uw python-functie
description: Meer informatie over het toevoegen van een Azure Storage wachtrij-uitvoer binding aan uw python-functie.
author: ggailey777
ms.author: glenga
ms.date: 10/02/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: da3fb604bfb65f67e50d56a4520620cabc292b93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082824"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Een Azure Storage wachtrij binding toevoegen aan uw python-functie

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel wordt beschreven hoe u de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-function-python.md) start-artikel integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Als u deze verbinding eenvoudiger wilt maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u de stappen in [deel 1 van de python-Snelstartgids](functions-create-first-function-python.md)volt ooien.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U kunt nu de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand function. json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Wanneer u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Gebruik, net als voorheen, de volgende opdracht om de functions-runtime lokaal te starten:

```bash
func host start
```

> [!NOTE]  
> Omdat u uitbreidings bundels in de host. json hebt ingeschakeld, is de [opslag bindings uitbreiding](functions-bindings-storage-blob.md#packages---functions-2x) tijdens het opstarten gedownload en geïnstalleerd, samen met de andere micro soft-bindings extensies.

Kopieer de URL van uw `HttpTrigger`-functie uit de uitvoer van de runtime en plak deze in de adresbalk van uw browser. Voeg de query reeks `?name=<yourname>` toe aan deze URL en voer de aanvraag uit. U ziet hetzelfde antwoord in de browser zoals u dat in het vorige artikel hebt gedaan.

De uitvoer binding maakt ook een wachtrij met de naam `outqueue` in uw opslag account en voegt een bericht toe met dezelfde teken reeks.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij te bekijken en te controleren of er een bericht is toegevoegd. U kunt uw wachtrij ook weer geven met behulp van de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Het project opnieuw implementeren 

Als u uw gepubliceerde app wilt bijwerken, gebruikt u de opdracht [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) core-hulpprogram ma's om uw project code te implementeren in Azure. Vervang `<APP_NAME>` in dit voor beeld door de naam van uw app.

```command
func azure functionapp publish <APP_NAME> --build remote
```

U kunt ook krul of een browser gebruiken om de geïmplementeerde functie te testen. Voeg, net als voorheen, de query reeks `&name=<yourname>` toe aan de URL, zoals in dit voor beeld:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

U kunt [de opslag wachtrij opnieuw bekijken](#query-the-storage-queue) om te controleren of de uitvoer binding een nieuw bericht in de wachtrij genereert, zoals verwacht.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw door HTTP geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van Azure Functions met python de [Azure functions python-ontwikkelaars handleiding](functions-reference-python.md) en [Azure functions triggers en bindingen](functions-triggers-bindings.md). Zie voor voor beelden van complete functie projecten in python de [python functions](/samples/browse/?products=azure-functions&languages=python)-voor beelden. Zie de [pagina met prijzen voor functies](https://azure.microsoft.com/pricing/details/functions/) en het artikel kosten voor het [schatten van verbruiks plannen](functions-consumption-costs.md) voor meer informatie over prijzen.

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/