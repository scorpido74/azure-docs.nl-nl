---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66482392"
---
1. Start Visual Studio 2019.

1. Zorg ervoor dat de workload voor **.NET platformoverschrijdende ontwikkeling** beschikbaar is. Kies **hulpprogram** > ma's**en functies ophalen** in de menu balk van Visual Studio om het Visual Studio-installatie programma te openen. Als deze workload al is ingeschakeld, sluit u het dialoogvenster.

   ![Schermopname van het Visual Studio-installatieprogramma met het tabblad Workloads gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Selecteer anders het vak naast **platformoverschrijdende ontwikkelmogelijkheden van .NET Core** en selecteer **Wijzigen** in de rechterbenedenhoek van het dialoogvenster. De installatie van de nieuwe functie kan even duren.

1. Maak een nieuwe Visual C# .NET Core-consoletoepassing. Vouw in het dialoogvenster **Nieuw project**, vanuit het linkerdeelvenster, **Geïnstalleerd** > **Visual C#** > **.NET Core** uit. Selecteer vervolgens **Consoletoepassing (.NET Core)**. Voer als projectnaam *helloworld* in.

   ![Schermafbeelding van het dialoogvenster New Project](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Een Visual C#-console-app maken (.NET core)")

1. Installeer en bekijk het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget). Klik in Solution Explorer met de rechter muisknop op de oplossing en selecteer **NuGet-pakketten beheren voor oplossing**.

   ![Scherm opname van Solution Explorer, waarbij NuGet-pakketten voor gemarkeerde oplossing worden beheerd](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet-pakketten beheren voor oplossing")

1. Selecteer in de rechter bovenhoek in het veld **pakket bron** de optie **nuget.org**. Zoek het `Microsoft.CognitiveServices.Speech` pakket en installeer het in het project **HelloWorld** .

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet-pakket installeren")

1. Ga akkoord met de weergegeven licentie om te beginnen met het installeren van het NuGet-pakket.

   ![Schermafbeelding van het licentievenster](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "De licentie accepteren")

Wanneer het pakket is geïnstalleerd, wordt er een bevestiging weergegeven in de Package Manager-console.
