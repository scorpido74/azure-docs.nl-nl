---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66482392"
---
1. Start Visual Studio 2019.

1. Zorg ervoor dat de workload voor **.NET platformoverschrijdende ontwikkeling** beschikbaar is. Kies **Hulpmiddelen** > **en functies ophalen in** de menubalk van Visual Studio om het installatieprogramma Visual Studio te openen. Als deze workload al is ingeschakeld, sluit u het dialoogvenster.

   ![Schermopname van het Visual Studio-installatieprogramma met het tabblad Workloads gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Selecteer anders het vak naast **platformoverschrijdende ontwikkelmogelijkheden van .NET Core** en selecteer **Wijzigen** in de rechterbenedenhoek van het dialoogvenster. De installatie van de nieuwe functie kan even duren.

1. Maak een nieuwe Visual C# .NET Core-consoletoepassing. Vouw in het dialoogvenster **Nieuw project**, vanuit het linkerdeelvenster, **Geïnstalleerd** > **Visual C#** > **.NET Core** uit. Selecteer vervolgens **Consoletoepassing (.NET Core)**. Voer als projectnaam *helloworld* in.

   ![Schermafbeelding van het dialoogvenster New Project](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C#Console-app maken (.NET-kern)")

1. Installeer en bekijk het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget). Klik in Solution Explorer met de rechtermuisknop op de oplossing en selecteer **NuGet-pakketten voor oplossing beheren.**

   ![Schermafbeelding van Solution Explorer, met NuGet-pakketten beheren voor oplossing gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet-pakketten beheren voor oplossing")

1. Selecteer in de rechterbovenhoek in het veld **Pakketbron** **de**nuget.org . Zoek naar `Microsoft.CognitiveServices.Speech` het pakket en installeer het in het **helloworld-project.**

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet-pakket installeren")

1. Ga akkoord met de weergegeven licentie om te beginnen met het installeren van het NuGet-pakket.

   ![Schermafbeelding van het licentievenster](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accepteer de licentie")

Wanneer het pakket is geïnstalleerd, wordt er een bevestiging weergegeven in de Package Manager-console.
