---
title: Azure Functions-bindingsextensies handmatig installeren of bijwerken
description: Meer informatie over het installeren of bijwerken van bindende extensies voor Azure Functions voor geïmplementeerde functie-apps.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768857"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Azure-functies bindingsextensies handmatig installeren of bijwerken vanuit de portal

Vanaf versie 2.x gebruikt de runtime van Azure Functions bindende extensies om code voor triggers en bindingen te implementeren. Bindende extensies worden geleverd in NuGet-pakketten. Om een extensie te registreren, installeert u in wezen een pakket. Bij het ontwikkelen van functies is de manier waarop u bindende extensies installeert afhankelijk van de ontwikkelomgeving. Zie [Bindende extensies registreren](./functions-bindings-register.md) in het artikel triggers en bindingen voor meer informatie.

Soms moet u uw bindende extensies handmatig installeren of bijwerken in de Azure-portal. Het kan bijvoorbeeld nodig zijn om een geregistreerde binding aan een nieuwere versie bij te werken. Mogelijk moet u ook een ondersteunde binding registreren die niet kan worden geïnstalleerd op het tabblad **Integreren** in de portal.

## <a name="install-a-binding-extension"></a>Een bindende extensie installeren

Gebruik de volgende stappen om extensies handmatig te installeren of bij te werken vanuit de portal.

1. Zoek in de [Azure-portal](https://portal.azure.com)uw functie-app en selecteer deze. Kies het tabblad **Overzicht** en selecteer **Stoppen**.  Als u de functie-app stopt, worden bestanden ontgrendeld, zodat er wijzigingen kunnen worden aangebracht.

1. Kies het tabblad **Platformfuncties** en selecteer onder **Hulpmiddelen voor Ontwikkeling** Geavanceerde **hulpprogramma's (Kudu)**. Het Kudu-eindpunt`https://<APP_NAME>.scm.azurewebsites.net/`( ) wordt geopend in een nieuw venster.

1. Selecteer in het venster Kudu de optie **Foutopsporingsconsole** > **CMD**.  

1. Navigeer in het opdrachtvenster naar `D:\home\site\wwwroot` en kies `bin` het pictogram Verwijderen naast om de map te verwijderen. Selecteer **OK** om de verwijdering te bevestigen.

1. Kies het bewerkingspictogram naast `extensions.csproj` het bestand, waarin de bindende extensies voor de functie-app worden gedefinieerd. Het projectbestand wordt geopend in de online editor.

1. Voer de vereiste toevoegingen en updates van **PackageReference-items** in de **itemgroep**uit en selecteer **Opslaan**. De huidige lijst met ondersteunde pakketversies is te vinden in het [welke pakketten heb ik nodig?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) Voor alle drie de Azure Storage-bindingen is het pakket Microsoft.Azure.WebJobs.Extensions.Storage vereist.

1. Voer `wwwroot` in de map de volgende opdracht uit om `bin` de verwijzingen naar de map opnieuw op te bouwen.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Terug in het tabblad **Overzicht** in de portal kiest u **Start** om de functie-app opnieuw te starten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
