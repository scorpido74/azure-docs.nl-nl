---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327066"
---
Als u een Visual Studio-project voor Windows-ontwikkeling wilt maken, moet u het project maken, Visual Studio instellen voor .NET-bureaubladontwikkeling, de SpraakSDK installeren en de doelarchitectuur kiezen.

### <a name="create-the-project-and-add-the-workload"></a>Het project maken en de werkbelasting toevoegen

Als u het project wilt maken in Visual Studio en ervoor zorgt dat Visual Studio is ingesteld voor .NET-bureaubladontwikkeling:

1. Open Visual Studio 2019.

1. Selecteer in het venster Start de optie **Een nieuw project maken**. 

1. Kies **console-app (.NET Framework)** in **het venster Een nieuw project maken** en selecteer **Volgende**.

1. Voer in het **venster Uw nieuwe project configureren** *helloworld* in **Projectnaam**in, kies of maak het mappad in **Locatie**en selecteer **Vervolgens Maken**.

1. Selecteer in de menubalk van Visual Studio de optie > **Extra's en -onderdelen ophalen**, waarmee Visual Studio Installer wordt geopend en het dialoogvenster **Wijzigen wordt** weergegeven. **Tools**

1. Controleer of de workload **.NET-bureaubladontwikkeling** beschikbaar is. Als de werkbelasting niet is geïnstalleerd, schakelt u het selectievakje ernaast in en selecteert u **Wijzigen** om de installatie te starten. Het downloaden en installeren kan een paar minuten duren.

   Als het selectievakje naast **.NET-bureaubladontwikkeling** al is ingeschakeld, selecteert u **Sluiten** om het dialoogvenster af te sluiten.

   ![.NET-desktopontwikkeling inschakelen](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio Installer sluiten.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

De volgende stap is het installeren van het [Speech SDK NuGet-pakket,](https://aka.ms/csspeech/nuget)zodat u ernaar verwijzen in de code.

1. Klik in de Solution Explorer met de rechtermuisknop op het **helloworld-project** en selecteer **NuGet-pakketten beheren** om de NuGet-pakketbeheer weer te geven.

   ![NuGet-pakketbeheer](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Zoek in de rechterbovenhoek het vervolgkeuzeveld **Package Source** en controleer of **nuget.org** is geselecteerd.

1. Selecteer **Bladeren**in de linkerbovenhoek.

1. Typ *Microsoft.CognitiveServices.Speech* in het zoekvak en selecteer **Enter**.

1. Selecteer in de zoekresultaten het pakket **Microsoft.CognitiveServices.Speech** en selecteer **Installeren** om de nieuwste stabiele versie te installeren.

   ![Microsoft.CognitiveServices.Speech NuGet-pakket installeren](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt een bevestiging weergegeven in het venster **Package Manager Console.**

### <a name="choose-the-target-architecture"></a>De doelarchitectuur kiezen

Als u nu de consoletoepassing wilt bouwen en uitvoeren, maakt u een platformconfiguratie die overeenkomt met de architectuur van uw computer.

1. Selecteer**Configuratiebeheer** **bouwen** > op de menubalk . Het dialoogvenster **Configuratiebeheer** wordt weergegeven.

   ![Dialoogvenster Configuratiebeheer](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **Selecteer**Nieuw in het vervolgkeuzevak Actieve **oplossingsplatform** . Het dialoogvenster **Nieuw oplossingsplatform** wordt weergegeven.

1. Selecteer in de vervolgkeuzelijst Tekst of selecteer het nieuwe **platform:**
   - Als u 64-bits Windows uitvoert, selecteert u **x64**.
   - Als u 32-bits Windows uitvoert, selecteert u **x86**.

1. Selecteer **OK** en sluit **.**
