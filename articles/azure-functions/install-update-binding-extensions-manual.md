---
title: Azure Functions binding-extensies hand matig installeren of bijwerken
description: Meer informatie over het installeren of bijwerken van Azure Functions bindings extensies voor geïmplementeerde functie-apps.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: 49e8e2ce7eb0267d5a4e6fc0f5566dffaed82661
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226510"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Azure Functions bindings uitbreidingen hand matig installeren of bijwerken vanuit de portal

In de runtime van Azure Functions versie 2. x worden bindings uitbreidingen gebruikt voor het implementeren van code voor triggers en bindingen. Er worden bindings uitbreidingen gegeven in NuGet-pakketten. Als u een uitbrei ding wilt registreren, installeert u een pakket. Bij het ontwikkelen van functies is de manier waarop u bindings uitbreidingen installeert afhankelijk van de ontwikkelings omgeving. Zie [bindings uitbreidingen registreren](./functions-bindings-register.md) in het artikel triggers en bindingen voor meer informatie.

Soms moet u de bindings extensies hand matig installeren of bijwerken in de Azure Portal. U moet bijvoorbeeld een geregistreerde binding bijwerken naar een nieuwere versie. Mogelijk moet u ook een ondersteunde binding registreren die niet kan worden geïnstalleerd op het tabblad **integreren** in de portal.

## <a name="install-a-binding-extension"></a>Een bindings uitbreiding installeren

Gebruik de volgende stappen om uitbrei dingen hand matig te installeren of bij te werken vanuit de portal.

1. Zoek in de [Azure Portal](https://portal.azure.com)de functie-app en selecteer deze. Kies het tabblad **overzicht** en selecteer **stoppen**.  Als u de functie-app stopt, worden bestanden ontgrendeld zodat er wijzigingen kunnen worden aangebracht.

1. Kies het tabblad **platform functies** en selecteer onder **ontwikkelingsprogram ma's** **geavanceerde hulp middelen (kudu)** . Het kudu-eind punt (`https://<APP_NAME>.scm.azurewebsites.net/`) wordt in een nieuw venster geopend.

1. Selecteer in het venster kudu de optie **debug console** > **cmd**.  

1. Navigeer in het opdracht venster naar `D:\home\site\wwwroot` en kies het pictogram verwijderen naast `bin` om de map te verwijderen. Selecteer **OK** om de verwijdering te bevestigen.

1. Kies het bewerkings pictogram naast het `extensions.csproj` bestand dat de bindings extensies voor de functie-app definieert. Het project bestand wordt geopend in de online editor.

1. Maak de vereiste toevoegingen en updates van **PackageReference** -items in de **ItemGroup**en selecteer vervolgens **Opslaan**. De huidige lijst met ondersteunde pakket versies vindt u in het artikel over [welke pakketten heb ik nodig?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) Voor alle drie de Azure Storage bindingen is het pakket micro soft. Azure. webjobs. Extensions. Storage vereist.

1. Voer de volgende opdracht uit in de map `wwwroot` om de assembly's in de map `bin` opnieuw te maken.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Klik op het tabblad **overzicht** in de portal op **Start** om de functie-app opnieuw te starten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
