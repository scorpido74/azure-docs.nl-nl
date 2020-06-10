---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 5ab0b9abd32ef2e5eb7ea02d6118e387ef753348
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609280"
---
Als u een Visual Studio project for Universeel Windows-platform-ontwikkeling (UWP) wilt maken, moet u Visual Studio-ontwikkel opties instellen, het project maken, de doel architectuur selecteren, audio-opname instellen en de spraak-SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkel opties voor Visual Studio instellen

Zorg ervoor dat u op de juiste wijze is ingesteld in Visual Studio for UWP Development:

1. Open Visual Studio 2019 om het **Start** venster weer te geven.

   ![Start venster-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecteer **door gaan zonder code** om naar de Visual Studio IDE te gaan.

1. Selecteer in de menu balk van Visual Studio **hulpprogram**ma's  >  **en functies ophalen** om Visual Studio Installer te openen en het dialoog venster **wijzigen** te bekijken.

   ![Het tabblad werk belastingen, het dialoog venster wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Ga op het tabblad **workloads** naar de **universeel Windows-platform Development** **-werk**belasting. Als het selectie vakje naast deze werk belasting al is geselecteerd, sluit u het dialoog venster **wijzigen** en gaat u naar stap 6.

1. Schakel het selectie vakje **universeel Windows-platform-ontwikkeling** in, selecteer **wijzigen**en selecteer vervolgens in het dialoog venster voordat u aan de **slag** gaat, de optie **door gaan** om de UWP Development-werk belasting te installeren. De installatie van de nieuwe functie kan enige tijd duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doel architectuur selecteren

Maak vervolgens uw project:

1. Kies in de menu balk van Visual Studio de optie **bestand**  >  **Nieuw**  >  **project** om het venster **een nieuw project maken** weer te geven.

   ![Een nieuw project maken-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Zoek en selecteer een **lege app (Universal Windows)**. Zorg ervoor dat u de C#-versie van dit project type selecteert (in plaats van Visual Basic).

1. Selecteer **volgende** om het scherm **uw nieuwe project configureren** weer te geven.

   ![Uw nieuwe project configureren-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Voer in **project naam**in `helloworld` .

1. Navigeer in **locatie**naar en selecteer of maak de map waarin u uw project wilt opslaan.

1. Selecteer **maken** om naar het **nieuwe universeel Windows-platform project** venster te gaan.

   ![Het dialoog venster Nieuw Universeel Windows-platform project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. In **minimale versie** (de tweede vervolg keuzelijst) kiest u update van **Windows 10 najaar-makers (10,0; Build 16299)**: dit is de minimale vereiste voor de spraak-SDK.

1. Kies in **doel versie** (de eerste vervolg keuzelijst) een waarde die gelijk is aan of hoger dan de waarde in **minimum versie**.

1. Selecteer **OK**. U keert terug naar Visual Studio IDE, met het nieuwe project gemaakt en zichtbaar in het deel venster **Solution Explorer** .

   ![HelloWorld-project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Selecteer nu de architectuur van het doel platform. Zoek in de werk balk van Visual Studio de vervolg keuzelijst **platformen voor oplossingen** . (Als dit niet wordt weer gegeven, kiest u **weer gave**  >  **Werk balken**  >  **Standaard** om de werk balk met **oplossings platforms**weer te geven.) Als u een 64-bits versie van Windows gebruikt, kiest u **x64** in de vervolg keuzelijst. 64-bits Windows kan ook 32-bits toepassingen uitvoeren, zodat u kunt kiezen voor **x86** als u dat wilt.

> [!NOTE]
> De Speech SDK biedt alleen ondersteuning voor Intel-compatibele processors. ARM-processors worden momenteel niet ondersteund.

### <a name="set-up-audio-capture"></a>Audio-opname instellen

Vervolgens kunt u met het project audio-invoer vastleggen:

1. Dubbel klik in **Solution Explorer**op **package. appxmanifest** om het manifest van het pakket toepassing te openen.

1. Selecteer het tabblad **Mogelijkheden**.

   ![Tabblad mogelijkheden, pakket toepassings manifest-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Schakel het selectie vakje in voor de **microfoon** mogelijkheid.

1. Kies in de menu balk **File**  >  **Save package. appxmanifest** om uw wijzigingen op te slaan.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer tot slot het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project:

1. Klik in **Solution Explorer**met de rechter muisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet-Solution** te gaan.

1. Selecteer **Bladeren**.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Kies in **pakket bron**de optie **nuget.org**.

1. Voer in het **zoekvak** in `Microsoft.CognitiveServices.Speech` en kies vervolgens dat pakket nadat het in de zoek resultaten wordt weer gegeven.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Selecteer uw **HelloWorld** -project in het deel venster pakket status naast de zoek resultaten.

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoog venster **voor beeld van wijzigingen** .

1. Bekijk in het dialoog venster **licentie acceptatie** de licentie en selecteer vervolgens **Ik ga akkoord**. De installatie van het pakket wordt gestart en wanneer de installatie is voltooid, wordt in het deel venster **uitvoer** een bericht weer gegeven dat vergelijkbaar is met de volgende tekst: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.12.1' to helloworld` .
