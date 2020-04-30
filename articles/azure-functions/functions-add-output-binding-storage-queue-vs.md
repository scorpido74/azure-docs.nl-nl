---
title: Functies verbinden met Azure Storage met behulp van Visual Studio
description: Meer informatie over het toevoegen van een uitvoer binding om uw C#-klassen bibliotheek functies te koppelen aan een Azure Storage wachtrij met behulp van Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74849204"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Functies verbinden met Azure Storage met behulp van Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel wordt beschreven hoe u Visual Studio gebruikt om de functie die u in het [vorige Quick] start-artikel hebt gemaakt, te verbinden met Azure Storage. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van de HTTP-aanvraag naar een bericht in een Azure Queue-opslag wachtrij. 

Voor de meeste bindingen is een opgeslagen connection string vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om het eenvoudiger te maken, gebruikt u het opslag account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling `AzureWebJobsStorage`met de naam.  

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel begint, moet u het volgende doen: 

 - Voltooi [deel 1 van de Snelstartgids voor Visual Studio](./functions-create-first-function-vs-code.md). 

- Meld u aan bij uw Azure-abonnement vanuit Visual Studio.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het [vorige Quick](functions-create-first-function-vs-code.md)start-artikel hebt u een functie-app gemaakt in azure, samen met het vereiste opslag account. De connection string voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslag wachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslag account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand *Local. settings. json* . 

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**. 

1. Onder **acties**selecteert u **Azure app service instellingen bewerken**. 

    ![De toepassings instellingen bewerken](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Kopieer de waarde voor **externe** teken reeks onder **AzureWebJobsStorage**naar **Local**en selecteer **OK**. 

De opslag binding, die gebruikmaakt van `AzureWebJobsStorage` de instelling voor de verbinding, kan nu verbinding maken met uw wachtrij opslag wanneer deze lokaal wordt uitgevoerd.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een uitvoer binding voor de wachtrij opslag gebruikt, hebt u de extensie opslag bindingen geïnstalleerd voordat u het project uitvoert. Met uitzonde ring van HTTP-en timer-triggers, worden bindingen geïmplementeerd als uitbreidings pakketten. 

1. Selecteer in het menu **extra** de optie **NuGet package manager** > **Package Manager console**. 

1. Voer in de-console de volgende [installatie-pakket](/nuget/tools/ps-ref-install-package) opdracht uit om de opslag extensies te installeren:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nu kunt u de opslag-uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoer binding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor authenticatie, het ophalen van een wachtrij verwijzing of het schrijven van gegevens. De functies runtime en wachtrij-uitvoer binding voeren deze taken voor u uit.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Er wordt een nieuwe `outqueue` wachtrij met de naam in uw opslag account gemaakt door de functions-runtime wanneer de uitvoer binding voor het eerst wordt gebruikt. U gebruikt cloud Explorer om te controleren of de wachtrij samen met het nieuwe bericht is gemaakt.

## <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Selecteer in Visual Studio in het menu **weer gave** de optie **Cloud Explorer**.

1. Vouw in **Cloud Explorer**uw Azure-abonnement en **opslag accounts**uit en vouw vervolgens het opslag account uit dat door uw functie wordt gebruikt. Als u de naam van het opslag account niet meer weet `AzureWebJobsStorage` , controleert u de instelling Connection String in het bestand *Local. settings. json* .  

1. Vouw het knoop punt **wacht rijen** uit en dubbel klik vervolgens op de wachtrij met de naam **outqueue** om de inhoud van de wachtrij in Visual Studio weer te geven. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde `name` van *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrij bericht weer gegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit, verzend een andere aanvraag en er wordt een nieuw bericht weer gegeven in de wachtrij.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer **publiceren**. Kies vervolgens **publiceren** om het project opnieuw te publiceren naar Azure.

1. Nadat de implementatie is voltooid, kunt u de opnieuw geïmplementeerde functie opnieuw gebruiken met behulp van de browser. Voeg net als voor de query reeks `&name=<yourname>` toe aan de URL.

1. [Bekijk nogmaals het bericht in de opslag wachtrij](#examine-the-output-queue) om te controleren of de uitvoer binding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslag wachtrij te schrijven. Zie voor meer informatie over het ontwikkelen van functies [Azure functions ontwikkelen met behulp van Visual Studio](functions-develop-vs.md).

Schakel vervolgens Application Insights bewaking in voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[vorig artikel over Snelstartgids]: functions-create-your-first-function-visual-studio.md
