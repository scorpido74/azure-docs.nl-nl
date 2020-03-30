---
title: Duurzame functies maken met de Azure-portal
description: Meer informatie over het installeren van de extensie Duurzame functies voor Azure-functies voor portalontwikkeling.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769639"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Duurzame functies maken met de Azure-portal

De extensie [Duurzame functies](durable-functions-overview.md) voor Azure-functies wordt geleverd in het NuGet-pakket [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Deze extensie moet worden geïnstalleerd in uw functie-app. In dit artikel ziet u hoe u dit pakket installeert, zodat u duurzame functies ontwikkelen in de Azure-portal.

> [!NOTE]
> 
> * Als u duurzame functies in C#ontwikkelt, moet u in plaats daarvan de ontwikkeling van [Visual Studio 2019](durable-functions-create-first-csharp.md)overwegen.
> * Als u duurzame functies in JavaScript ontwikkelt, moet u in plaats daarvan de ontwikkeling van [Visual Studio Code](./quickstart-js-vscode.md)overwegen.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben om de uitvoering van een functie te hosten. Met een functie-app u uw functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources. U een .NET- of JavaScript-app maken.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Standaard maakt de gemaakte functie-app gebruik van versie 2.x van de runtime azure-functies. De extensie Duurzame functies werkt op beide versies 1.x en 2.x van de runtime van Azure-functies in C#en versie 2.x in JavaScript. Sjablonen zijn echter alleen beschikbaar bij het targeten van versie 2.x van de runtime, ongeacht de gekozen taal.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Installeer het npm-pakket met duurzame functies (alleen JavaScript)

Als u JavaScript-duurzame functies maakt, moet u het [ `durable-functions` npm-pakket](https://www.npmjs.com/package/durable-functions)installeren.

1. Selecteer de naam van de functie-app, gevolgd door **Platform Features,** vervolgens **Advanced tools (Kudu)**.

   ![Functies platform functies kiezen Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Selecteer in de Kudu-console de optie **Foutopsporingsconsole** en **VERVOLGENS CMD**.

   ![Kudu debug console](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. De bestandsmapstructuur van uw functie-app moet worden weergegeven. Navigeer naar de map `site/wwwroot`. Van daaruit u `package.json` een bestand uploaden door het te slepen en in het archiefmapvenster te laten vallen. Een `package.json` voorbeeld is hieronder:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu upload package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Zodra `package.json` je bent geüpload, voer je de `npm install` opdracht uit vanaf de Kudu Remote Execution Console.

   ![Kudu run npm installeren](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Een orchestrator-functie maken

1. Vouw de functie-app **+** uit en klik op de knop naast **Functies**. Als dit de eerste functie in de functie-app is, selecteert u **In de portal** en vervolgens **Doorgaan**. Anders gaat u verder met stap drie.

   ![De Quick Start-pagina van Functions in Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Kies **Meer sjablonen** en vervolgens **Voltooien en sjablonen weergeven**.

    ![De Quick Start-pagina ‘Meer sjablonen kiezen’ van Functions](./media/durable-functions-create-portal/add-first-function.png)

1. Typ `durable` in het zoekveld de **http-startersjabloon Duurzame functies** en kies deze.

1. Selecteer **deoptie Installeren** om de Azure DurableTask-extensie en eventuele afhankelijkheden in de functie-app te installeren wanneer u daarom wordt gevraagd. U hoeft de uitbreiding slechts eenmaal te installeren voor een functie-app voor geven. Wanneer de installatie is voltooid, selecteert u **Doorgaan**.

    ![Binding-extensies installeren](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Nadat de installatie is voltooid, `HttpStart` geeft u de nieuwe functie een naam en kiest **u Maken**. De functie die is gemaakt, wordt gebruikt om de orkestratie te starten.

1. Maak een andere functie in de functie-app, dit keer met behulp van de sjabloon **Duurzame functies Orchestrator.** Geef uw nieuwe `HelloSequence`orchestration-functie een naam.

1. Maak een derde `Hello` functie met de naam **Duurzame functies Activiteit.**

## <a name="test-the-durable-function-orchestration"></a>Test de duurzame functieorkestratie

1. Ga terug naar de functie **HttpStart,** kies **</> Functie-URL ophalen** en **Kopieer** de URL. U gebruikt deze URL om de functie **HelloSequence** te starten.

1. Gebruik een HTTP-tool zoals Postman of cURL om een POST-verzoek te verzenden naar de URL die u hebt gekopieerd. Het volgende voorbeeld is een cURL-opdracht die een POST-verzoek naar de duurzame functie verzendt:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    In dit `{your-function-app-name}` voorbeeld is dit het domein dat de naam van uw functie-app is. Het antwoordbericht bevat een serie URI-eindpunten die u kunt gebruiken om de uitvoering te controleren en beheren. De uitvoering ziet eruit zoals in het volgende voorbeeld:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Roep `statusQueryGetUri` het eindpunt URI aan en u ziet de huidige status van de duurzame functie, die er in het voorbeeld uit zou kunnen zien:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Blijf het `statusQueryGetUri` eindpunt aanroepen totdat de status is gewijzigd in **Voltooid**en u ziet een antwoord zoals het volgende voorbeeld:

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

Uw eerste duurzame functie is nu operationeel in Azure.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
