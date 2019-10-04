---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837480"
---
Als u een Visual Studio-project wilt maken voor het platform voor mobiele apps .NET-ontwikkeling met Xamarin, moet u Visual Studio-ontwikkel opties instellen, het project maken, de doel architectuur selecteren en de spraak-SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkel opties voor Visual Studio instellen

Om te beginnen, moet u ervoor zorgen dat u op de juiste wijze in Visual Studio hebt ingesteld voor mobiele ontwikkeling op meerdere platforms met .NET:

1. Open Visual Studio 2019.

1. Selecteer in de menu balk van Visual Studio **extra** > **hulp middelen en functies ophalen** om Visual Studio Installer te openen en het dialoog venster **wijzigen** te bekijken.

   ![Het tabblad werk belastingen, het dialoog venster wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Zoek op het tabblad **workloads** onder **Windows**de **Mobile Development with .net** -workload. Als het selectie vakje naast deze werk belasting al is geselecteerd, sluit u het dialoog venster **wijzigen** en gaat u naar stap 5.

1. Schakel het selectie vakje **Mobile Development with .net** in, selecteer **wijzigen**en selecteer vervolgens in het dialoog venster voordat u aan de **slag** gaat, de optie **door gaan** om de mobiele ontwikkeling met .net-workload te installeren. De installatie van de nieuwe functie kan enige tijd duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project"></a>Het project maken

1. Kies in de menu balk van Visual Studio **File** > **New** > **project** om het venster **een nieuw project maken** weer te geven.

   ![Een nieuw project maken-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Zoek en selecteer de **mobiele app (Xamarin Forms)** .

1. Selecteer **volgende** om het scherm **uw nieuwe project configureren** weer te geven. 

   ![Uw nieuwe project configureren-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Voer in **project naam**`helloworld` in.

1. Navigeer in **locatie**naar en selecteer of maak de map waarin u uw project wilt opslaan.

1. Selecteer **maken** om naar het nieuwe project venster van de Xamarin-formulieren van de **mobiele app** te gaan.

   ![Het dialoog venster Nieuw Universeel Windows-platform project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **Lege** sjabloon selecteren

1. In **platform**, selectie vakjes voor **Android**, **IOS** en **Windows (UWP)** .

1. Selecteer **OK**. U keert terug naar Visual Studio IDE, met het nieuwe project gemaakt en zichtbaar in het deel venster **Solution Explorer** .

   ![HelloWorld-project-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Selecteer nu uw doel platform architectuur en start-project. Zoek in de werk balk van Visual Studio de vervolg keuzelijst **platformen voor oplossingen** . (Als dit niet wordt weer gegeven, kiest u **weer gave** > **werk balken** > **standaard** om de werk balk met de **oplossings platforms**weer te geven.) Als u een 64-bits versie van Windows gebruikt, kiest u **x64** in de vervolg keuzelijst. 64-bits Windows kan ook 32-bits toepassingen uitvoeren, zodat u kunt kiezen voor **x86** als u dat wilt. Stel HelloWorld in voor de vervolg keuzelijst **opstart projecten** . UWP (Universal Windows).

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project:

1. Klik in **Solution Explorer**met de rechter muisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet-Solution** te gaan.

1. Selecteer **Bladeren**.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Kies in **pakket bron**de optie **nuget.org**.

1. Voer in het **zoekvak** `Microsoft.CognitiveServices.Speech` in en kies vervolgens dat pakket nadat het is weer gegeven in de zoek resultaten.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Opmerking: de iOS-bibliotheek in micro soft. CognitiveServices. speech nuget heeft bitcode niet ingeschakeld. Als u bitcode ingeschakelde bibliotheek voor uw toepassing nodig hebt, gebruikt u micro soft. CognitiveServices. speech. Xamarin. iOS nuget voor het iOS-project specifiek.

1. Selecteer in het deel venster pakket status naast de zoek resultaten alle projecten. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. IOS** en **HelloWorld. UWP**.

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoog venster **voor beeld van wijzigingen** .

1. Bekijk in het dialoog venster **licentie acceptatie** de licentie en selecteer vervolgens **Ik ga akkoord** en installeer de naslag informatie voor het Speech SDK-pakket naar alle projecten. Nadat de installatie is voltooid, ziet u mogelijk de volgende waarschuwing voor HelloWorld. iOS. Dit is een bekend probleem en heeft geen invloed op de functionaliteit van uw app.

> Kan verwijzing ' C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a ' niet omzetten. Als deze verwijzing wordt vereist door uw code, kunnen er compilatie fouten optreden.
