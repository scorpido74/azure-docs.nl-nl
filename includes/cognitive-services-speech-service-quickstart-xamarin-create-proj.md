---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675605"
---
Als u een Visual Studio-project wilt maken voor cross-platform mobiele app .NET-ontwikkeling met Xamarin, moet u:
- Stel ontwikkelingsopties voor Visual Studio in.
- Maak het project en selecteer de doelarchitectuur. 
- Installeer de Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelingsopties voor Visual Studio instellen

Om te beginnen moet u ervoor zorgen dat u correct bent ingesteld in Visual Studio voor mobiele ontwikkeling op cross-platform met .NET:

1. Open Visual Studio 2019.

1. Selecteer in de menubalk van Visual Studio de optie > **Extra's en -onderdelen ophalen** om Visual Studio Installer te openen en het dialoogvenster **Wijzigen weer te** geven. **Tools**

   ![Tabblad Workloads, dialoogvenster Wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Zoek op het tabblad **Workloads** onder **Windows**de **mobiele ontwikkeling met .NET-workload.** Als het selectievakje naast die werkbelasting al is ingeschakeld, sluit u het dialoogvenster **Wijzigen** en gaat u naar stap 5.

1. Schakel het selectievakje **Mobiele ontwikkeling met .NET** in en schakel **Wijzigen in.** Selecteer **Doorgaan** om de mobiele ontwikkeling te installeren met .NET-workload in het dialoogvenster **Voordat we aan de slag gaan.** Het installeren van de nieuwe functie kan even duren.

1. Visual Studio Installer sluiten.

### <a name="create-the-project"></a>Het project maken

1. Selecteer op de menubalk van Visual Studio de optie**Nieuw** > **project** **bestand** > om het venster Een nieuw project **maken** weer te geven.

   ![Een nieuw project maken - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Mobiele app zoeken en selecteren **(Xamarin.Forms)**.

1. Selecteer **Volgende** om het **nieuwe projectscherm configureren** weer te geven.

   ![Uw nieuwe project configureren - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Voer **in Projectnaam** *helloworld*in.

1. Ga **in Locatie**naar en selecteer of maak de map om uw project op te slaan.

1. Selecteer **Maken** om naar het venster **Xamarin Forms Project voor nieuwe mobiele apps** te gaan.

   ![Dialoogvenster Nieuw Universeel Windows Platform Project - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecteer de sjabloon **Leeg.**

1. Selecteer **in Platform**de vakken voor **Android,** **iOS**en **Windows (UWP)**.

1. Selecteer **OK**. U wordt teruggezet naar de Visual Studio IDE, waarbij het nieuwe project is gemaakt en zichtbaar is in het deelvenster **Solution Explorer.**

   ![Het helloworld project - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Selecteer nu uw doelplatformarchitectuur en opstartproject. Zoek op de werkbalk Visual Studio de vervolgkeuzelijst **Oplossingsplatforms.** (Als u deze niet ziet, selecteert u**Werkbalkwerkbalken** >  **weergeven** > om de werkbalk weer te geven die **oplossingsplatforms**bevat .)**Standard** Als u 64-bits Windows uitvoert, selecteert u **x64** in de vervolgkeuzelijst. U **x86** selecteren als u wilt, omdat 64-bits Windows ook 32-bits toepassingen kan uitvoeren. Stel helloworld in de vervolgkeuzelijst **Start-up Projects** **in. UWP (Universal Windows)**.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project.

1. Klik in **Solution Explorer**met de rechtermuisknop op uw oplossing. Selecteer **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet - Oplossing** te gaan.

1. Selecteer **Bladeren**.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Selecteer in **pakketbron**nuget.org.

1. Typ *Microsoft.CognitiveServices.Speech*in het vak **Zoeken** . Selecteer dat pakket vervolgens nadat het in de zoekresultaten wordt weergegeven.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > De iOS-bibliotheek in `Microsoft.CognitiveServices.Speech` NuGet heeft geen bitcode ingeschakeld. Als je de bitcode-bibliotheek speciaal voor `Microsoft.CognitiveServices.Speech.Xamarin.iOS` je toepassing wilt inschakelen, gebruik je NuGet specifiek voor het iOS-project.

1. Selecteer in het venster pakketstatus naast de zoekresultaten alle projecten: **helloworld**, **helloworld. Android**, **helloworld.iOS**en **helloworld. UWP**.

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoogvenster **Voorvertoningswijzigingen.**

1. Bekijk de licentie in het dialoogvenster **Licentieacceptatie** en selecteer **I Accept**. Installeer de verwijzing naar het Speech SDK-pakket naar alle projecten. Nadat de installatie is voltooid, ziet u mogelijk de volgende waarschuwing voor helloworld.iOS. Dit is een bekend probleem en mag geen invloed hebben op de functionaliteit van uw app.

   > Kan de verwijzing "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.speech.core.a" niet oplossen. Als deze verwijzing vereist is door uw code, u compilatiefouten krijgen.
