---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675605"
---
Als u een Visual Studio-project wilt maken voor multi-platform Mobile App .NET-ontwikkeling met Xamarin, moet u het volgende doen:
- Ontwikkel opties voor Visual Studio instellen.
- Maak het project en selecteer de doel architectuur. 
- Installeer de spraak-SDK.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkel opties voor Visual Studio instellen

Om te beginnen, moet u ervoor zorgen dat u op de juiste wijze in Visual Studio hebt ingesteld voor mobiele ontwikkeling op meerdere platforms met .NET:

1. Open Visual Studio 2019.

1. Selecteer in de menu balk van Visual Studio **extra** > **hulp middelen en functies ophalen** om Visual Studio Installer te openen en het dialoog venster **wijzigen** te bekijken.

   ![Het tabblad werk belastingen, het dialoog venster wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Zoek op het tabblad **workloads** onder **Windows**de **mobiele ontwikkeling met .net** -workload. Als het selectie vakje naast deze werk belasting al is geselecteerd, sluit u het dialoog venster **wijzigen** en gaat u naar stap 5.

1. Schakel het selectie vakje **Mobile Development with .net** in en selecteer **wijzigen**. Selecteer in het dialoog venster voordat u aan de **slag** gaat, de optie **door gaan** om de mobiele ontwikkeling te installeren met .net-workload. De installatie van de nieuwe functie kan enige tijd duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project"></a>Het project maken

1. Selecteer in de menu balk van Visual Studio **File**  > **New**  > **project** om het venster **een nieuw project maken** weer te geven.

   ![Een nieuw project maken-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Zoek en selecteer de **mobiele app (Xamarin. Forms)** .

1. Selecteer **volgende** om het scherm **uw nieuwe project configureren** weer te geven.

   ![Uw nieuwe project configureren-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Voer in **project naam** *HelloWorld*in.

1. Ga in **locatie**naar en selecteer de map waarin u uw project wilt opslaan.

1. Selecteer **maken** om naar het nieuwe project venster van de Xamarin-formulieren van de **mobiele app** te gaan.

   ![Het dialoog venster Nieuw Universeel Windows-platform project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecteer de **lege** sjabloon.

1. Selecteer in **platform**de vakken voor **Android**, **IOS**en **Windows (UWP)** .

1. Selecteer **OK**. U keert terug naar Visual Studio IDE, met het nieuwe project gemaakt en zichtbaar in het deel venster **Solution Explorer** .

   ![Het HelloWorld-project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Selecteer nu uw doel platform architectuur en opstart project. Ga in de Visual Studio-werk balk naar de vervolg keuzelijst **platformen voor oplossingen** . (Als u dit niet ziet, selecteert u  > **werk balken** **weer geven**  > **standaard** om de werk balk weer te geven die **platformen**voor de oplossing bevat.) Als u een 64-bits versie van Windows gebruikt, selecteert u **x64** in de vervolg keuzelijst. U kunt indien gewenst **x86** selecteren, omdat 64-bits Windows ook 32-bits toepassingen kan uitvoeren. Stel in de vervolg keuzelijst **Start projecten** het **HelloWorld in. UWP (Universal Windows)** .

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project.

1. Klik in **Solution Explorer**met de rechter muisknop op uw oplossing. Selecteer **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet-Solution** te gaan.

1. Selecteer **Bladeren**.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Selecteer in **pakket bron**de optie nuget.org.

1. Voer *micro soft. CognitiveServices. speech*in het **zoekvak** in. Selecteer vervolgens dat pakket nadat het is weer gegeven in de zoek resultaten.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Voor de iOS-bibliotheek in `Microsoft.CognitiveServices.Speech` NuGet is bitcode niet ingeschakeld. Als u de bitcode-bibliotheek voor uw toepassing nodig hebt, gebruikt u `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet voor het iOS-project.

1. Selecteer in het deel venster pakket status naast de zoek resultaten alle projecten: **HelloWorld**, **HelloWorld. Android**, **HelloWorld. IOS**en **HelloWorld. UWP**.

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoog venster **voor beeld van wijzigingen** .

1. Bekijk in het dialoog venster **licentie acceptatie** de licentie en selecteer vervolgens **Ik ga akkoord**. Installeer de naslag informatie voor het Speech SDK-pakket naar alle projecten. Nadat de installatie is voltooid, ziet u mogelijk de volgende waarschuwing voor HelloWorld. iOS. Dit is een bekend probleem en heeft geen invloed op de functionaliteit van uw app.

   > Kan verwijzing "C:\Users\Default \.nuget \packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a" niet omzetten. Als deze verwijzing wordt vereist door uw code, kunnen er compilatie fouten optreden.
