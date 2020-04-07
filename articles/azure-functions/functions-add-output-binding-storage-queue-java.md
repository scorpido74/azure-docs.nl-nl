---
title: Uw Java-functie verbinden met Azure Storage
description: Meer informatie over het verbinden van een Java-functie die door HTTP is geactiveerd met Azure Storage met behulp van een binding voor de uitvoer van wachtrijopslag.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673302"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Uw Java-functie verbinden met Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel ziet u hoe u de functie die u in het [vorige quickstart-artikel](functions-create-first-java-maven.md) hebt gemaakt, integreren met een Azure Storage-wachtrij. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-verzoek naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen verbindingstekenreeks vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om deze verbinding eenvoudiger te maken, gebruikt u het opslagaccount dat u met uw functie-app hebt gemaakt. De verbinding met dit account is al `AzureWebJobsStorage`opgeslagen in een app-instelling met de naam .  

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel start, voert u de stappen in [deel 1 van de Java quickstart](functions-create-first-java-maven.md)uit.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uitbreidingsbundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

U nu de binding van de opslaguitvoer toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

U bent nu klaar om de nieuwe uitvoerbinding lokaal uit te proberen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Net als voorheen gebruikt u de volgende opdracht om het project te bouwen en de runtime Functies lokaal te starten:

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
> Omdat u extensiebundels inschakelt in host.json, is de [extensie Opslagbinding](functions-bindings-storage-blob.md#add-to-your-functions-app) tijdens het opstarten voor u gedownload en geïnstalleerd, samen met de andere bindende extensies van Microsoft.

Net als voorheen activeert u de functie vanaf de opdrachtregel met behulp van cURL in een nieuw terminalvenster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Deze keer maakt de uitvoerbinding `outqueue` ook een wachtrij met de naam in uw opslagaccount en voegt een bericht met dezelfde tekenreeks toe.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij weer te geven en te controleren of er een bericht is toegevoegd. U uw wachtrij ook bekijken met de [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in de [Azure-portal.](https://portal.azure.com)

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

Nogmaals, u cURL gebruiken om de geïmplementeerde functie te testen. Net als voorheen `AzureFunctions` geeft u de waarde in de hoofdtekst van het POST-verzoek door aan de URL, zoals in dit voorbeeld:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

U [het wachtrijbericht Opslag opnieuw onderzoeken](#query-the-storage-queue) om te controleren of de uitvoerbinding zoals verwacht een nieuw bericht in de wachtrij genereert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt de functie die http-geactiveerd wordt bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Zie de triggers [en bindingen](functions-triggers-bindings.md)voor [Azure Functions Java voor](functions-reference-java.md) meer informatie over het ontwikkelen van Azure-functies met Java. Zie de voorbeelden van [voorbeelden](/samples/browse/?products=azure-functions&languages=Java)van volledige functieprojecten op Java. 

Vervolgens moet u Application Insights-monitoring inschakelen voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
