---
title: Uw Java-functie verbinden met Azure Storage
description: Meer informatie over het verbinden van een door HTTP geactiveerde Java-functie naar Azure Storage met behulp van een uitvoer binding voor de wachtrij opslag.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673302"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Uw Java-functie verbinden met Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel wordt beschreven hoe u de functie die u hebt gemaakt in het [vorige Quick](functions-create-first-java-maven.md) start-artikel integreert met een Azure Storage wachtrij. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Als u deze verbinding eenvoudiger wilt maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling `AzureWebJobsStorage`met de naam.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u de stappen in [deel 1 van de Java-Snelstartgids](functions-create-first-java-maven.md)volt ooien.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidings bundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U kunt nu de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

U bent nu klaar om de nieuwe uitvoer binding lokaal uit te proberen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Gebruik, net als voorheen, de volgende opdracht om het project te bouwen en de functions-runtime lokaal te starten:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Omdat u uitbreidings bundels in de host. json hebt ingeschakeld, is de [opslag bindings uitbreiding](functions-bindings-storage-blob.md#add-to-your-functions-app) tijdens het opstarten gedownload en geïnstalleerd, samen met de andere micro soft-bindings extensies.

Activeer, net als voorheen, de functie vanaf de opdracht regel met behulp van krul in een nieuw terminal venster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

De uitvoer binding maakt ook een wachtrij met de naam `outqueue` in uw opslag account en voegt een bericht toe met dezelfde teken reeks.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij te bekijken en te controleren of er een bericht is toegevoegd. U kunt uw wachtrij ook weer geven met behulp van de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Het project opnieuw implementeren 

Voer de volgende opdracht opnieuw uit om uw gepubliceerde app bij te werken:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

U kunt ook krul gebruiken om de geïmplementeerde functie te testen. Geef net als voorheen de waarde `AzureFunctions` door in de hoofd tekst van de post-aanvraag naar de URL, zoals in dit voor beeld:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

U kunt [de opslag wachtrij opnieuw bekijken](#query-the-storage-queue) om te controleren of de uitvoer binding een nieuw bericht in de wachtrij genereert, zoals verwacht.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw door HTTP geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van Azure Functions met Java de [Azure functions Java-ontwikkelaars handleiding](functions-reference-java.md) en [Azure functions triggers en bindingen](functions-triggers-bindings.md). Zie voor voor beelden van complete functie projecten in Java de [Java functions](/samples/browse/?products=azure-functions&languages=Java)-voor beelden. 

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
