---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 942636a6ff6dcfe74a04a66d67a4490224b4538e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391608"
---
Als u een Visual Studio-project C++ voor het ontwikkelen van bureau blad wilt maken, moet u Visual Studio-ontwikkel opties instellen, het project maken, de doel architectuur selecteren en de spraak-SDK installeren. 

### <a name="set-up-visual-studio-development-options"></a>Ontwikkel opties voor Visual Studio instellen

Als u wilt starten, moet u ervoor zorgen dat u juist is ingesteld C++ in Visual Studio voor desktop ontwikkeling:

1. Open Visual Studio 2019 om het **Start** venster weer te geven.

   ![Start venster-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. Selecteer **door gaan zonder code** om naar de Visual Studio IDE te gaan.

1. Selecteer in de menu balk van Visual Studio **extra** > **hulp middelen en functies ophalen** om Visual Studio Installer te openen en het dialoog venster **wijzigen** te bekijken.

   ![Het tabblad werk belastingen, het dialoog venster wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Zoek op het tabblad **workloads** onder **Windows**de **bureaublad ontwikkeling C++ met** de werk belasting. Als het selectie vakje naast deze werk belasting nog niet is geselecteerd, selecteert u deze.

1. Zoek het selectie vakje **Nuget package manager** op het tabblad **afzonderlijke onderdelen** . Als het selectie vakje nog niet is ingeschakeld, selecteert u dit.

1. Selecteer de knop in de hoek met **sluiten** of **wijzigen**. (De naam van de knop is afhankelijk van het feit of u onderdelen voor de installatie hebt geselecteerd.) Als u **wijzigen**selecteert, wordt de installatie gestart. Dit kan enige tijd duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doel architectuur selecteren

Maak vervolgens uw project:

1. Kies in de menu balk van Visual Studio **File** > **New** > **project** om het venster **een nieuw project maken** weer te geven.

   ![Een nieuw project maken C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Zoek en selecteer **console-app**. Zorg ervoor dat u de C++ versie van dit project type selecteert (in plaats van C# of Visual Basic).

1. Selecteer **volgende** om het scherm **uw nieuwe project configureren** weer te geven.

   ![Uw nieuwe project,- C++ Visual Studio, configureren](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Voer in **project naam**`helloworld` in.

1. Navigeer in **locatie**naar en selecteer of maak de map waarin u uw project wilt opslaan.

Selecteer nu de architectuur van het doel platform. Zoek in de werk balk van Visual Studio de vervolg keuzelijst **platformen voor oplossingen** . (Als dit niet wordt weer gegeven, kiest u **weer gave** > **werk balken** > **standaard** om de werk balk met de **oplossings platforms**weer te geven.) Als u een 64-bits versie van Windows gebruikt, kiest u **x64** in de vervolg keuzelijst. 64-bits Windows kan ook 32-bits toepassingen uitvoeren, zodat u kunt kiezen voor **x86** als u dat wilt.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer tot slot het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project:

1. Klik in **Solution Explorer**met de rechter muisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet-Solution** te gaan.

1. Selecteer **Bladeren**.

   ![NuGet-tabblad oplossing, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Kies in **pakket bron**de optie **nuget.org**.

1. Voer in het **zoekvak** `Microsoft.CognitiveServices.Speech` in en kies vervolgens dat pakket nadat het is weer gegeven in de zoek resultaten.

   ![Micro soft. CognitiveServices. C++ speech-pakket installeren-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Selecteer uw **HelloWorld** -project in het deel venster pakket status naast de zoek resultaten.

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoog venster **voor beeld van wijzigingen** .

1. Bekijk in het dialoog venster **licentie acceptatie** de licentie en selecteer vervolgens **Ik ga akkoord**. De installatie van het pakket wordt gestart en wanneer de installatie is voltooid, wordt in het deel venster **uitvoer** een bericht weer gegeven dat vergelijkbaar is met de volgende tekst: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.7.0' to helloworld`. 
