---
title: Uw eerste Durable Function maken in Azure met behulp van C#
description: Een duurzame azure-functie maken en publiceren met Behulp van Visual Studio of Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132784"
---
# <a name="create-your-first-durable-function-in-c"></a>Uw eerste Durable Function maken in C\#

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

::: zone pivot="code-editor-vscode"

In dit artikel leert u hoe u Visual Studio Code gebruiken om lokaal een duurzame functie hallo wereld te maken en te testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogramma's zijn beschikbaar als onderdeel van de VS Code [Azure Functions-extensie.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

![Durable Function uitvoeren in Azure](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* Installeer de volgende VS Code-extensies:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Zorg ervoor dat u de nieuwste versie van de [Azure Functions Core Tools hebt.](../functions-run-local.md)

* Voor duurzame functies is een Azure-opslagaccount vereist. U hebt een Azure-abonnement nodig.

* Zorg ervoor dat versie 3.1 of een latere versie van de [.NET Core SDK](https://dotnet.microsoft.com/download) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure-functieproject te maken. 

1. Druk in Visual Studio Code op F1 (of Ctrl/Cmd+Shift+P) om het opdrachtpalet te openen. Zoek en selecteer `Azure Functions: Create New Project...`in het opdrachtpalet .

    ![Een functieproject maken](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Kies een lege maplocatie voor uw project en kies **Selecteren**.

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een taal selecteren voor uw functie-app-project | C# | Maak een lokaal C#-functieproject. |
    | Een versie selecteren | Azure-functies v3 | U ziet deze optie alleen als de Core Tools nog niet zijn geïnstalleerd. In dit geval worden Core Tools geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Een sjabloon selecteren voor de eerste functie van uw project | Sla voor nu | |
    | Selecteer hoe u uw project wilt openen | Openen in het huidige venster | Hiermee opent u VS-code opnieuw in de door u geselecteerde map. |

Visual Studio Code installeert indien nodig de Core Tools voor Azure Functions Core. Het maakt ook een functie-app project in een map. Dit project bevat de configuratiebestanden [host.json](../functions-host-json.md) en [local.settings.json.](../functions-run-local.md#local-settings-file)

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. Zoek en selecteer `Azure Functions: Create Function...`in het opdrachtpalet .

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een sjabloon voor uw functie selecteren | Duurzame functiesOrkestratie | Een orkestratie met duurzame functies maken |
    | Een functienaam opgeven | HelloOrchestration HelloOrchestration HelloOrchestration | Naam van de klasse waarin functies worden gemaakt |
    | Een naamruimte opgeven | Bedrijf.Functie | Naamruimte voor de gegenereerde klasse |

1. Wanneer VS Code u vraagt een opslagaccount te selecteren, kiest **u Opslagaccount selecteren**. Geef de volgende informatie naar aanleiding van de volgende aanwijzingen om een nieuw opslagaccount in Azure te maken.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *naam van uw abonnement* | selecteer uw Azure-abonnement |
    | Een opslagaccount selecteren | Een nieuw opslagaccount maken |  |
    | Voer de naam in van het nieuwe opslagaccount | *unieke naam* | Naam van het opslagaccount dat u wilt maken |
    | Een resourcegroep selecteren | *unieke naam* | Naam van de resourcegroep die moet worden gemaakt |
    | Een locatie selecteren | *regio* | Een gebied bij u in de buurt selecteren |

Aan het project wordt een klasse met de nieuwe functies toegevoegd. VS Code voegt ook de tekenreeks voor de verbinding met de [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) opslagaccount toe aan *local.settings.json* en een verwijzing naar het NuGet-pakket naar het *.csproj-projectbestand.*

Open het nieuwe *HelloOrchestration.cs* bestand om de inhoud weer te geven. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `HelloOrchestration_Hello` | De functie geeft 'Hello' als resultaat. Het is de functie die de bedrijfslogica bevat die wordt georkestreerd. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Een [door HTTP geactiveerde functie](../functions-bindings-http-webhook.md) die een exemplaar van de indeling start en een reactie voor de controlestatus retourneert. |

Nu u uw functieproject en een Durable Function hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Als u uw functie wilt `SayHello` testen, stelt u een breekpunt in de code van de activiteitsfunctie in en drukt u op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg de [Sustainable Functions Diagnostics](durable-functions-diagnostics.md#debugging) voor meer informatie over foutopsporing.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Met behulp van een tool zoals [Postman](https://www.getpostman.com/) of [cURL,](https://curl.haxx.se/)stuur je een HTTP POST-verzoek naar het URL-eindpunt.

   De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde voor `statusQueryGetUri` en plak deze in de adresbalk van de browser en voer de aanvraag uit. U ook Postman blijven gebruiken om de GET-aanvraag uit te geven.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eventueel antwoord krijgen, waaruit blijkt dat de instantie is voltooid, en omvat de uitgangen of resultaten van de duurzame functie. Het lijkt erop dat: 

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

1. Als u foutopsporing wilt stoppen, drukt u op **Shift + F5** in VS-code.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een C# duurzame functie-app te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

In dit artikel leer je hoe je Visual Studio 2019 lokaal een duurzame functie van 'hello world' maken en testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogramma's zijn beschikbaar als onderdeel van de Azure-ontwikkelworkload in Visual Studio 2019.

![Durable Function uitvoeren in Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* Visual [Studio 2019](https://visualstudio.microsoft.com/vs/)installeren . Zorg ervoor dat de werkbelasting **Azure development** ook is geïnstalleerd. Visual Studio 2017 ondersteunt ook de ontwikkeling van duurzame functies, maar de gebruikersinterface en stappen verschillen.

* Controleer of de [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) is geïnstalleerd en wordt uitgevoerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

De Azure Functions-sjabloon maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources.

1. Selecteer **in** > Visual Studio Nieuw**project** in het menu **Bestand.**

1. Zoek in het dialoogvenster Een `functions`nieuw project **maken** naar , kies de sjabloon **Azure-functies** en selecteer **Volgende**. 

    ![Het dialoogvenster Nieuw project om een functie in Visual Studio te maken](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Typ een **projectnaam** voor uw project en selecteer **OK**. De projectnaam moet geldig zijn als een C#-naamruimte, dus gebruik geen underscores, koppeltekens of andere niet-alfanumerieke tekens.

1. Gebruik **in Een nieuwe Azure Functions-toepassing maken**de instellingen die zijn opgegeven in de tabel die volgt op de afbeelding.

    ![Een nieuw dialoogvenster Azure-functiestoepassing maken in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versie** | Azure-functies 3.0 <br />(.NET Core) | Hiermee maakt u een functieproject dat gebruikmaakt van de runtime van Versie 3.0 van Azure-functies, die .NET Core 3.1 ondersteunt. Zie [Een versie kiezen voor de runtime van Azure Functions](../functions-versions.md) voor meer informatie.   |
    | **Sjabloon** | Leeg | Hiermee wordt een lege functie-app gemaakt. |
    | **Opslagaccount**  | Opslagemulator | Een opslagaccount is vereist voor het statusbeheer van Durable Functions. |

4. Selecteer **Maken** om een leeg functieproject te maken. Dit project bevat de basisconfiguratiebestanden die nodig zijn voor het uitvoeren van uw functies.

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. Klik met de rechtermuisknop op het project in Visual Studio en selecteer**Nieuwe Azure-functie** **toevoegen** > .

    ![Nieuwe functie toevoegen](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Controleer **azure-functie** is geselecteerd in het menu Toevoegen, typ een naam voor uw C#-bestand en selecteer **Vervolgens Toevoegen**.

1. Selecteer de sjabloon **Orkestratie duurzame functies** en selecteer **Ok**

    ![Durable sjabloon selecteren](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Er wordt een nieuwe Durable Function toegevoegd aan de app.  Open het nieuwe CS-bestand om de inhoud te bekijken. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `<file-name>_Hello` | De functie geeft 'Hello' als resultaat. Het is de functie die de bedrijfslogica bevat die wordt georkestreerd. |
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

    De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet uiteindelijk een reactie krijgen die lijkt op de volgende.  Deze uitvoer toont ons dat de instantie is voltooid en omvat de uitgangen of resultaten van de duurzame functie.

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

5. Als u foutopsporing wilt stoppen, drukt u op **Shift + F5**.

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
