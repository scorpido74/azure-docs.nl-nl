---
title: Durable Functions maken met behulp van de Azure Portal
description: Meer informatie over het installeren van de Durable Functions extensie voor Azure Functions voor portal ontwikkeling.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769639"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Durable Functions maken met behulp van de Azure Portal

De uitbrei ding voor de [Durable functions](durable-functions-overview.md) voor Azure functions is opgenomen in het NuGet-pakket [micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Deze uitbrei ding moet worden geïnstalleerd in uw functie-app. In dit artikel wordt beschreven hoe u dit pakket installeert zodat u duurzame functies kunt ontwikkelen in de Azure Portal.

> [!NOTE]
> 
> * Als u duurzame functies in C# ontwikkelt, moet u in plaats daarvan [Visual Studio 2019-ontwikkeling](durable-functions-create-first-csharp.md)overwegen.
> * Als u duurzame functies ontwikkelt in Java script, moet u in plaats daarvan [Visual Studio code Development](./quickstart-js-vscode.md)overwegen.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben om de uitvoering van een functie te hosten. Met een functie-app kunt u uw functies groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen. U kunt een .NET-of Java script-app maken.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

De functie-app die is gemaakt, maakt standaard gebruik van versie 2. x van de Azure Functions runtime. De uitbrei ding Durable Functions werkt op versie 1. x en 2. x van de Azure Functions runtime in C# en versie 2. x in Java script. Sjablonen zijn echter alleen beschikbaar bij het doel versie 2. x van de runtime, ongeacht de gekozen taal.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Het NPM-pakket met duurzame functies installeren (alleen Java script)

Als u Java script-Durable functions maakt, moet u het [ `durable-functions` NPM-pakket](https://www.npmjs.com/package/durable-functions)installeren.

1. Selecteer de naam van uw functie-app, gevolgd door **platform functies**, en **Geavanceerde Hulpprogram ma's (kudu)**.

   ![Functies platform functies kiezen kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Selecteer in de kudu-console **fout opsporing console** en vervolgens **cmd**.

   ![Console voor fout opsporing kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. De structuur van de bestands directory van de functie-app moet worden weer gegeven. Navigeer naar de map `site/wwwroot`. Van daaruit kunt u een `package.json` bestand uploaden door het te slepen en neer te zetten in het venster van de bestands directory. Hieronder ziet `package.json` u een voor beeld:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu upload package. json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Nadat uw `package.json` is geüpload, voert u `npm install` de opdracht uit vanuit de kudu-console voor externe uitvoering.

   ![Kudu uitvoeren NPM installeren](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Een Orchestrator-functie maken

1. Vouw de functie-app uit en **+** Klik op de knop naast **functies**. Als dit de eerste functie in de functie-app is, selecteert u **In de portal** en vervolgens **Doorgaan**. Anders gaat u verder met stap drie.

   ![De Quick Start-pagina van Functions in Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Kies **Meer sjablonen** en vervolgens **Voltooien en sjablonen weergeven**.

    ![De Quick Start-pagina ‘Meer sjablonen kiezen’ van Functions](./media/durable-functions-create-portal/add-first-function.png)

1. Typ `durable` in het zoek veld en kies vervolgens de sjabloon **Durable functions http-starter** .

1. Wanneer u hierom wordt gevraagd, selecteert u **installeren** om de Azure DurableTask-extensie en eventuele afhankelijkheden in de functie-app te installeren. U hoeft de uitbreiding slechts eenmaal te installeren voor een functie-app voor geven. Wanneer de installatie is voltooid, selecteert u **Doorgaan**.

    ![Binding-extensies installeren](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Wanneer de installatie is voltooid, kunt u de nieuwe `HttpStart` functie een naam en kies **maken**. De functie die wordt gemaakt, wordt gebruikt om de indeling te starten.

1. Maak een andere functie in de functie-app, dit keer door gebruik te maken van de **Durable functions Orchestrator** -sjabloon. Geef een naam op voor de `HelloSequence`nieuwe Orchestration-functie.

1. Maak een derde functie met `Hello` de naam met behulp van de sjabloon **Durable functions activiteit** .

## <a name="test-the-durable-function-orchestration"></a>De functie voor de integratie van duurzame functies testen

1. Ga terug naar de functie **HttpStart** , kies **</> functie-URL ophalen** en **Kopieer** de URL. U gebruikt deze URL om de functie **HelloSequence** te starten.

1. Gebruik een HTTP-hulp programma zoals postman of krul om een POST-aanvraag te verzenden naar de URL die u hebt gekopieerd. Het volgende voor beeld is een krul opdracht waarmee een POST-aanvraag wordt verzonden naar de functie duurzame:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    In dit voor beeld `{your-function-app-name}` is het domein de naam van de functie-app. Het antwoordbericht bevat een serie URI-eindpunten die u kunt gebruiken om de uitvoering te controleren en beheren. De uitvoering ziet eruit zoals in het volgende voorbeeld:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Roep de `statusQueryGetUri` URI van het eind punt aan en u ziet de huidige status van de duurzame functie. deze kan er als volgt uitzien:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Ga door met `statusQueryGetUri` het aanroepen van het eind punt totdat de status is gewijzigd in **voltooid**en er een antwoord wordt weer geven, zoals in het volgende voor beeld:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Uw eerste duurzame functie is nu actief en wordt uitgevoerd in Azure.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
