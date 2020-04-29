---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71327066"
---
Als u een Visual Studio-project voor Windows-ontwikkeling wilt maken, moet u het project maken, Visual Studio voor .NET Desktop Development instellen, de spraak-SDK installeren en de doel architectuur kiezen.

### <a name="create-the-project-and-add-the-workload"></a>Het project maken en de workload toevoegen

Als u wilt beginnen, maakt u het project in Visual Studio en zorgt u ervoor dat Visual Studio is ingesteld voor .NET Desktop Development:

1. Open Visual Studio 2019.

1. Selecteer in het Start venster **een nieuw project maken**. 

1. Kies in het venster **een nieuw project maken** de optie **console-app (.NET Framework)** en selecteer vervolgens **volgende**.

1. Voer in het venster **uw nieuwe project configureren** *HelloWorld* in bij **project naam**, kies of maak het mappad op **locatie**en selecteer vervolgens **maken**.

1. Selecteer in de menu balk van Visual Studio **hulpprogram** > ma's**en functies ophalen**waarmee Visual Studio Installer wordt geopend en het dialoog venster **wijzigen** wordt weer gegeven.

1. Controleer of de werk belasting van **.net desktop Development** beschikbaar is. Als de werk belasting niet is geïnstalleerd, schakelt u het selectie vakje ernaast in en selecteert u **wijzigen** om de installatie te starten. Het downloaden en installeren kan een paar minuten duren.

   Als het selectie vakje naast **.net desktop Development** al is geselecteerd, selecteert u **sluiten** om het dialoog venster af te sluiten.

   ![.NET-desktopontwikkeling inschakelen](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Sluit Visual Studio Installer.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

De volgende stap is het installeren van het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget), zodat u ernaar kunt verwijzen in de code.

1. Klik in de Solution Explorer met de rechter muisknop op het project **HelloWorld** en selecteer vervolgens **NuGet-pakketten beheren** om de NuGet-pakket manager weer te geven.

   ![NuGet-pakketbeheer](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Zoek in de rechter bovenhoek de vervolg keuzelijst **pakket bron** en zorg ervoor dat **nuget.org** is geselecteerd.

1. Selecteer in de linkerbovenhoek de optie **Bladeren**.

1. Typ *micro soft. CognitiveServices. speech* in het zoekvak en selecteer **Enter**.

1. Selecteer in de zoek resultaten het pakket **micro soft. CognitiveServices. speech** en selecteer vervolgens **installeren** om de nieuwste stabiele versie te installeren.

   ![Het NuGet-pakket micro soft. CognitiveServices. speech installeren](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt er een bevestiging weer gegeven in het console venster van **Package Manager** .

### <a name="choose-the-target-architecture"></a>De doel architectuur kiezen

Maak nu een platform configuratie die overeenkomt met de architectuur van uw computer om de console toepassing te bouwen en uit te voeren.

1. Selecteer in de menu balk de optie **Build** > **Configuration Manager**. Het dialoog venster **Configuration Manager** wordt weer gegeven.

   ![Het dialoog venster Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Selecteer in de vervolg keuzelijst **actief oplossings platform** **nieuwe**. Het dialoog venster **nieuw platform voor oplossingen** wordt weer gegeven.

1. **Typ of selecteer** in het vak de vervolg keuzelijst nieuw platform:
   - Als u een 64-bits versie van Windows gebruikt, selecteert u **x64**.
   - Als u een 32-bits versie van Windows gebruikt, selecteert u **x86**.

1. Selecteer **OK** en vervolgens **sluiten**.
