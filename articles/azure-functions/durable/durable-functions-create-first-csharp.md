---
title: Uw eerste Durable Function maken in Azure met behulp van C#
description: Maak en publiceer een duurzame Azure-functie met Visual Studio of Visual Studio code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80132784"
---
# <a name="create-your-first-durable-function-in-c"></a>Uw eerste Durable Function maken in C\#

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

::: zone pivot="code-editor-vscode"

In dit artikel leert u hoe u met Visual Studio code lokaal een ' Hallo wereld '-functie kunt maken en testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogram ma's zijn beschikbaar als onderdeel van de extensie VS code [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Durable Function uitvoeren in Azure](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* Installeer de volgende VS code-extensies:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [G #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Zorg ervoor dat u de nieuwste versie van de [Azure functions core tools](../functions-run-local.md)hebt.

* Durable Functions een Azure-opslag account vereist. U hebt een Azure-abonnement nodig.

* Zorg ervoor dat versie 3,1 of een latere versie van de [.net core SDK](https://dotnet.microsoft.com/download) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio code om een lokaal Azure Functions project te maken. 

1. Druk in Visual Studio code op F1 (of Ctrl/Cmd + Shift + P) om het opdracht palet te openen. In het opdracht palet zoekt en selecteert u `Azure Functions: Create New Project...`.

    ![Een functieproject maken](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Kies een lege maplocatie voor het project en kies **selecteren**.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een taal voor uw functie-app-project | C# | Maak een lokaal C# functions-project. |
    | Een versie selecteren | Azure Functions v3 | U ziet deze optie alleen wanneer de kern Hulpprogramma's niet al zijn geïnstalleerd. In dit geval worden de kern Hulpprogramma's geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Selecteer een sjabloon voor de eerste functie van uw project | Nu overs Laan | |
    | Selecteer hoe u uw project wilt openen | In het huidige venster openen | Opent de VS code in de map die u hebt geselecteerd. |

Met Visual Studio code worden de Azure Functions Core Tools, indien nodig, geïnstalleerd. Er wordt ook een functie-app-project gemaakt in een map. Dit project bevat de configuratie bestanden [host. json](../functions-host-json.md) en [Local. settings. json](../functions-run-local.md#local-settings-file) .

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. In het opdracht palet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor de functie | DurableFunctionsOrchestration | Een Durable Functions indeling maken |
    | Een functie naam opgeven | HelloOrchestration | De naam van de klasse waarin de functies zijn gemaakt |
    | Geef een naam ruimte op | Company. function | Naam ruimte voor de gegenereerde klasse |

1. Wanneer VS-code u vraagt een opslag account te selecteren, kiest u **opslag account selecteren**. Volg de aanwijzingen om de volgende informatie op te geven voor het maken van een nieuw opslag account in Azure.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *de naam van uw abonnement* | selecteer uw Azure-abonnement |
    | Een opslag account selecteren | Een nieuw opslagaccount maken |  |
    | Voer de naam van het nieuwe opslag account in | *unieke naam* | De naam van het opslag account dat moet worden gemaakt |
    | Een resourcegroep selecteren | *unieke naam* | De naam van de resource groep die u wilt maken |
    | Een locatie selecteren | *deel* | Selecteer een regio die dicht bij u ligt |

Een klasse die de nieuwe functies bevat, wordt toegevoegd aan het project. VS code voegt ook het opslag account toe connection string aan *Local. settings. json* en een verwijzing naar [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) het NuGet-pakket naar het *. csproj* -project bestand.

Open het nieuwe *HelloOrchestration.cs* -bestand om de inhoud weer te geven. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `HelloOrchestration_Hello` | De functie geeft 'Hello' als resultaat. Het is de functie die de bedrijfs logica bevat die wordt georchestrator. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Een [door HTTP geactiveerde functie](../functions-bindings-http-webhook.md) die een exemplaar van de indeling start en een reactie voor de controlestatus retourneert. |

Nu u uw functieproject en een Durable Function hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Als u uw functie wilt testen, stelt u een `SayHello` onderbrekings punt in de functie code voor de activiteit in en drukt u op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg het [Durable functions diagnostische](durable-functions-diagnostics.md#debugging) gegevens voor meer informatie over fout opsporing.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Verzend met een hulp programma zoals [postman](https://www.getpostman.com/) of [krul](https://curl.haxx.se/)een HTTP POST-aanvraag naar het URL-eind punt.

   De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde `statusQueryGetUri` voor en plak deze in de adres balk van de browser en voer de aanvraag uit. U kunt postman ook blijven gebruiken om de GET-aanvraag uit te geven.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eindige reactie krijgen, waarin wordt weer gegeven dat het exemplaar is voltooid en dat de uitvoer of resultaten van de duurzame functie bevat. Het ziet er als volgt uit: 

    ```json
    {
        "name": "HelloOrchestration",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Als u het fout opsporingsprogramma wilt stoppen, drukt u op **SHIFT + F5** in VS code.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio code gebruikt voor het maken en publiceren van een duurzame C#-functie-app.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

In dit artikel leert u hoe u met Visual Studio 2019 lokaal een duurzame functie Hallo wereld kunt maken en testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogram ma's zijn beschikbaar als onderdeel van de Azure Development-workload in Visual Studio 2019.

![Durable Function uitvoeren in Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* Installeer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Zorg ervoor dat de werkbelasting **Azure development** ook is geïnstalleerd. Visual Studio 2017 biedt ook ondersteuning voor Durable Functions ontwikkeling, maar de gebruikers interface en stappen verschillen.

* Controleer of de [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) is geïnstalleerd en wordt uitgevoerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

De Azure Functions-sjabloon maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

1. Selecteer in Visual Studio **Nieuw** > **project** in het menu **bestand** .

1. Zoek in het dialoog venster **een nieuw project maken** naar `functions`, kies de **Azure functions** sjabloon en selecteer **volgende**. 

    ![Het dialoogvenster Nieuw project om een functie in Visual Studio te maken](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Typ een **project naam** voor het project en selecteer **OK**. De project naam moet geldig zijn als C#-naam ruimte, dus gebruik geen onderstrepings tekens, afbreek streepjes of andere niet-alfanumerieke karakters.

1. Gebruik in **een nieuwe Azure functions-toepassing maken**de instellingen die zijn opgegeven in de tabel die volgt op de installatie kopie.

    ![Een nieuw dialoog venster voor Azure Functions toepassing maken in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versie** | Azure Functions 3,0 <br />(.NET Core) | Hiermee maakt u een functie project dat gebruikmaakt van versie 3,0 runtime van Azure Functions, dat .NET Core 3,1 ondersteunt. Zie [Een versie kiezen voor de runtime van Azure Functions](../functions-versions.md) voor meer informatie.   |
    | **Sjabloon** | Leeg | Hiermee wordt een lege functie-app gemaakt. |
    | **Storage-account**  | Opslagemulator | Een opslagaccount is vereist voor het statusbeheer van Durable Functions. |

4. Selecteer **maken** om een leeg functie project te maken. Dit project bevat de basisconfiguratiebestanden die nodig zijn voor het uitvoeren van uw functies.

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. Klik met de rechter muisknop op het project in Visual Studio en selecteer**nieuwe Azure-functie** **toevoegen** > .

    ![Nieuwe functie toevoegen](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Controleer of de **Azure-functie** is geselecteerd in het menu toevoegen, typ een naam voor het C#-bestand en selecteer vervolgens **toevoegen**.

1. Selecteer de **Durable functions Orchestration** -sjabloon en selecteer vervolgens **OK** .

    ![Durable sjabloon selecteren](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Er wordt een nieuwe Durable Function toegevoegd aan de app.  Open het nieuwe CS-bestand om de inhoud te bekijken. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `<file-name>_Hello` | De functie geeft 'Hello' als resultaat. Het is de functie die de bedrijfs logica bevat die wordt georchestrator. |
| **`HttpStart`** | `<file-name>_HttpStart` | Een [door HTTP geactiveerde functie](../functions-bindings-http-webhook.md) die een exemplaar van de indeling start en een reactie voor de controlestatus retourneert. |

Nu u uw functieproject en een Durable Function hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om Azure Functions Core (CLI)-hulpprogramma's te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser en voer de aanvraag uit. Hieronder ziet u de reactie op de lokale GET-aanvraag die door de functie wordt geretourneerd, weergegeven in de browser:

    ![De reactie van de lokale host van de functie in de browser](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart.  Dit is nog niet het eindresultaat van de orchestrator.  De reactie bevat enkele nuttige URL's.  Maar eerst gaan we de status van de orchestrator opvragen.

4. Kopieer de URL-waarde voor `statusQueryGetUri`, plak deze in de adresbalk van de browser en voer de aanvraag uit.

    De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet uiteindelijk een reactie krijgen die lijkt op de volgende.  In deze uitvoer ziet u dat het exemplaar is voltooid en dat de uitvoer of resultaten van de duurzame functie zijn opgenomen.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Als u het fout opsporingsprogramma wilt stoppen, drukt u op **SHIFT + F5**.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren naar Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. U kunt rechtstreeks vanuit Visual Studio een functie-app maken.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app van de pagina Profiel publiceren. Vervang het `localhost:port`-deel van de URL dat u hebt gebruikt bij het lokaal testen van de functie door de nieuwe basis-URL.

    De URL die de HTTP-trigger van uw Durable Function aanroept, moet de volgende indeling hebben:

        https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio gebruikt om een Durable Function-app in C# te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)

::: zone-end
