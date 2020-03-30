---
title: Functies verbinden met Azure Storage met Visual Studio
description: Meer informatie over het toevoegen van een uitvoerbinding om uw C#-klassebibliotheekfuncties te verbinden met een Azure Storage-wachtrij met Behulp van Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849204"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Functies verbinden met Azure Storage met Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel ziet u hoe u Visual Studio gebruiken om de functie die u in het [vorige quickstart-artikel] hebt gemaakt, te verbinden met Azure Storage. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van het HTTP-verzoek naar een bericht in een Azure Queue-opslagwachtrij. 

Voor de meeste bindingen is een opgeslagen verbindingstekenreeks vereist die functies gebruiken om toegang te krijgen tot de gebonden service. Om het u gemakkelijker te maken, gebruikt u het opslagaccount dat u met uw functie-app hebt gemaakt. De verbinding met dit account is al `AzureWebJobsStorage`opgeslagen in een app-instelling met de naam .  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u: 

 - Voltooi [deel 1 van de Visual Studio snelaan.](./functions-create-first-function-vs-code.md) 

- Meld u aan bij uw Azure-abonnement bij Visual Studio.

## <a name="download-the-function-app-settings"></a>De instellingen van de functie-app downloaden

In het [vorige quickstart-artikel](functions-create-first-function-vs-code.md)hebt u een functie-app in Azure gemaakt, samen met het vereiste opslagaccount. De verbindingstekenreeks voor dit account wordt veilig opgeslagen in app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw opslagaccount wanneer u de functie lokaal uitvoert, moet u app-instellingen downloaden naar het bestand *local.settings.json.* 

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**. 

1. Selecteer **onder Acties**de optie Azure App Service Settings **bewerken**. 

    ![De toepassingsinstellingen bewerken](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Kopieer onder **AzureWebJobsStorage**de waarde van de **externe** tekenreeks naar **Lokaal**en selecteer **OK**. 

De opslagbinding, die `AzureWebJobsStorage` de instelling voor de verbinding gebruikt, kan nu verbinding maken met uw wachtrijopslag wanneer deze lokaal wordt uitgevoerd.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een wachtrijopslaguitvoerbinding gebruikt, moet de extensie Opslagbindingen worden geïnstalleerd voordat u het project uitvoert. Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. 

1. Selecteer **nuGet Package Manager** > **Package Manager Console**in het menu **Extra** . 

1. Voer in de console de volgende opdracht [Installatiepakket](/nuget/tools/ps-ref-install-package) uit om de opslagextensies te installeren:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

U nu de binding van de opslaguitvoer toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, `name` kunt u de binding gebruiken om toegang te krijgen tot de binding als kenmerk in de functiehandtekening. Door een uitvoerbinding te gebruiken, hoeft u de Azure Storage SDK-code niet te gebruiken voor verificatie, het verkrijgen van een wachtrijverwijzing of het schrijven van gegevens. De runtime en queue output binding van functies doen deze taken voor u.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Er wordt `outqueue` een nieuwe wachtrij met de naam in uw opslagaccount gemaakt door de runtime Van Functies wanneer de uitvoerbinding voor het eerst wordt gebruikt. U gebruikt Cloud Explorer om te controleren of de wachtrij samen met het nieuwe bericht is gemaakt.

## <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Selecteer **Cloud Explorer**in Visual Studio in het menu **Weergave** .

1. Vouw in **Cloud Explorer**uw Azure-abonnement en **opslagaccounts**uit en vouw vervolgens het opslagaccount uit dat door uw functie wordt gebruikt. Als u de naam van het opslagaccount niet meer weet, controleert u de instelling van de `AzureWebJobsStorage` verbindingstekenreeks in het bestand *local.settings.json.*  

1. Vouw het knooppunt **Wachtrijen** uit en dubbelklik op de wachtrij met de naam **outqueue** om de inhoud van de wachtrij in Visual Studio weer te geven. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde `name` van *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrijbericht weergegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit, stuur een ander verzoek en u ziet een nieuw bericht in de wachtrij verschijnen.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer **Publiceren**en kies **Vervolgens Publiceren** om het project opnieuw te publiceren naar Azure.

1. Nadat de implementatie is voltooid, u de browser opnieuw gebruiken om de opnieuw geïmplementeerde functie te testen. Net als voorheen wordt `&name=<yourname>` de querytekenreeks toegevoegd aan de URL.

1. Bekijk opnieuw [het bericht in de opslagwachtrij](#examine-the-output-queue) om te controleren of de uitvoerbinding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt de functie HTTP-geactiveerd bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Zie [Azure-functies ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie over het ontwikkelen van functies.

Vervolgens moet u Application Insights-monitoring inschakelen voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[vorige quickstart artikel]: functions-create-your-first-function-visual-studio.md
