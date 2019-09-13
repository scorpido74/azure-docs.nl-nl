---
title: Uw eerste Durable Function maken in Azure met behulp van C#
description: Lees hoe u een Azure Durable Function maakt en publiceert met Visual Studio.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure-functies, functies, gebeurtenisverwerking, berekenen, architectuur zonder server
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: azfuncdf
ms.openlocfilehash: 1579a4dfbab1ec9d9aa6bb3995bd88d948d6d5e2
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933974"
---
# <a name="create-your-first-durable-function-in-c"></a>Uw eerste Durable Function maken in C\#

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

In dit artikel leert u hoe u met Visual Studio 2019 lokaal een ' Hallo wereld ' duurzame functie kunt maken en testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogram ma's zijn beschikbaar als onderdeel van de Azure Development-workload in Visual Studio 2019.

![Duurzame functie uitvoeren in Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Installeer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Zorg ervoor dat de werkbelasting **Azure development** ook is geïnstalleerd. Visual Studio 2017 biedt ook ondersteuning voor Durable Functions ontwikkeling, maar de gebruikers interface en stappen verschillen.

* Controleer of de [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) is geïnstalleerd en wordt uitgevoerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

De Azure Functions-sjabloon maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources kunt beheren, implementeren en delen.

1. Selecteer **Nieuw** > **Project** in het menu **Bestand** in Visual Studio.

1. In het dialoog venster **een nieuw project toevoegen** zoekt `functions`, kiest u de sjabloon **Azure functions** en selecteert u **volgende**. 

    ![Het dialoogvenster Nieuw project om een functie in Visual Studio te maken](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Typ een **project naam** voor het project en selecteer **OK**. De project naam moet geldig zijn als een C# naam ruimte, dus gebruik geen onderstrepings tekens, afbreek streepjes of andere niet-alfanumerieke karakters.

1. Gebruik in **een nieuwe Azure functions-toepassing maken**de instellingen die zijn opgegeven in de tabel die volgt op de installatie kopie.

    ![Een nieuw dialoog venster voor Azure Functions toepassing maken in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Instelling      | Voorgestelde waarde  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versie** | Azure Functions 2.x <br />(.NET Core) | Hiermee wordt een functieproject gemaakt dat gebruikmaakt van versie 2.x van de runtime van Azure Functions, die ondersteuning biedt voor .NET Core. Azure Functions 1.x ondersteunt .NET Framework. Zie [Een versie kiezen voor de runtime van Azure Functions](../functions-versions.md) voor meer informatie.   |
    | **Sjabloon** | Leeg | Hiermee wordt een lege functie-app gemaakt. |
    | **Opslagaccount**  | Opslagemulator | Een opslagaccount is vereist voor het statusbeheer van Durable Functions. |

4. Selecteer **maken** om een leeg functie project te maken. Dit project bevat de basisconfiguratiebestanden die nodig zijn voor het uitvoeren van uw functies.

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. Klik met de rechtermuisknop op het project in Visual Studio en selecteer **Toevoegen** > **Nieuwe Azure-functie**.

    ![Nieuwe functie toevoegen](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Controleer of de **Azure-functie** is geselecteerd in het menu toevoegen, typ een C# naam voor het bestand en selecteer vervolgens **toevoegen**.

1. Selecteer de **Durable functions Orchestration** -sjabloon en selecteer vervolgens **OK** .

    ![Durable sjabloon selecteren](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

Er wordt een nieuwe Durable Function toegevoegd aan de app.  Open het nieuwe CS-bestand om de inhoud te bekijken. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Description |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `<file-name>_Hello` | De functie geeft 'Hello' als resultaat. Dit is de functie die de bedrijfslogica bevat die wordt ingedeeld. |
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

    De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet uiteindelijk een reactie krijgen die lijkt op de volgende.  Dit laat zien dat het exemplaar is voltooid en dat het de uitvoer of resultaten van de Durable Function bevat.

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
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

5. Als u wilt stoppen met fouten opsporen, drukt u op **Shift + F5**.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren naar Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. U kunt rechtstreeks vanuit Visual Studio een functie-app maken.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app van de pagina Profiel publiceren. Vervang het `localhost:port`-deel van de URL dat u hebt gebruikt bij het lokaal testen van de functie door de nieuwe basis-URL.

    De URL die de HTTP-trigger van uw Durable Function aanroept, moet de volgende indeling hebben:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio gebruikt om een Durable Function-app in C# te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)