---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 6804834dab81b0774dd29b1ede836492d0dfea4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383937"
---
Als u een Visual Studio-project wilt maken voor de ontwikkeling van het Universal Windows Platform (UWP), moet u de ontwikkelingsopties van Visual Studio instellen, het project maken, de doelarchitectuur selecteren, audio-opname instellen en de Speech SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelingsopties voor Visual Studio instellen

Zorg ervoor dat u correct bent ingesteld in Visual Studio voor UWP-ontwikkeling om te beginnen:

1. Open Visual Studio 2019 om het **venster Start** weer te geven.

   ![Startvenster - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecteer **Doorgaan zonder code** om naar de Visual Studio IDE te gaan.

1. Selecteer in de menubalk van Visual Studio de optie > **Extra's en -onderdelen ophalen** om Visual Studio Installer te openen en het dialoogvenster **Wijzigen weer te** geven. **Tools**

   ![Tabblad Workloads, dialoogvenster Wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Zoek op het tabblad **Workloads** onder **Windows**de **werkbelasting voor de ontwikkeling van het Universele Windows-platform.** Als het selectievakje naast die werkbelasting al is ingeschakeld, sluit u het dialoogvenster **Wijzigen** en gaat u naar stap 6.

1. Schakel het selectievakje **Ontwikkeling van het Universele Windows-platform** in, schakel **Wijzigen**in en selecteer Vervolgens in het dialoogvenster Voordat we **aan de slag gaan** de werkbelasting voor UWP-ontwikkeling blijven installeren. **Continue** De installatie van de nieuwe functie kan even duren.

1. Visual Studio Installer sluiten.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doelarchitectuur selecteren

Maak vervolgens uw project:

1. Kies in de menubalk Visual Studio de optie**Nieuw** > **project** **bestand** > om het venster Een nieuw project **maken** weer te geven.

   ![Een nieuw project maken - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Zoeken en selecteer **Lege app (Universal Windows)**. Zorg ervoor dat u de C#-versie van dit projecttype selecteert (in tegenstelling tot Visual Basic).

1. Selecteer **Volgende** om het **nieuwe projectscherm configureren** weer te geven.

   ![Uw nieuwe project configureren - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Voer **in Projectnaam**. `helloworld`

1. Navigeer **in Locatie**naar en selecteer of maak de map om uw project op te slaan.

1. Selecteer **Maken** om naar het venster **Nieuw Universeel Windows Platform Project** te gaan.

   ![Dialoogvenster Nieuw Universeel Windows Platform Project - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Kies **in Minimumversie** (de tweede vervolgkeuzevak) **Windows 10 Fall Creators Update (10.0; Build 16299)**, dat is de minimale vereiste voor de Speech SDK.

1. Kies **in doelversie** (het eerste vervolgkeuzevak) een waarde die identiek is aan of later dan de waarde in **Minimumversie**.

1. Selecteer **OK**. U wordt teruggezet naar de Visual Studio IDE, waarbij het nieuwe project is gemaakt en zichtbaar is in het deelvenster **Solution Explorer.**

   ![helloworld project - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Selecteer nu uw doelplatformarchitectuur. Zoek op de werkbalk Visual Studio de vervolgkeuzelijst **Oplossingsplatforms.** (Als u deze niet ziet, kiest u**Werkbalken** >  **weergeven** > **standaard** om de werkbalk weer te geven met **oplossingsplatforms**.) Als u 64-bits Windows uitvoert, kiest u **x64** in de vervolgkeuzelijst. 64-bits Windows kan ook 32-bits toepassingen uitvoeren, zodat u **desgevraagd x86** kiezen.

> [!NOTE]
> De Speech SDK biedt alleen ondersteuning voor Intel-compatibele processors. ARM-processors worden momenteel niet ondersteund.

### <a name="set-up-audio-capture"></a>Audio-opname instellen

Laat het project vervolgens audio-invoer vastleggen:

1. Dubbelklik in **Solution Explorer**op **Package.appxmanifest** om het manifest voor pakkettoepassingen te openen.

1. Selecteer het tabblad **Mogelijkheden**.

   ![Tabblad Mogelijkheden, pakkettoepassingsmanifest - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Schakel het vak je in voor de mogelijkheid **Microfoon.**

1. Kies Op de menubalk De optie **Pakket** > **opslaan.appxmanifest** bestand om uw wijzigingen op te slaan.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer ten slotte het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project:

1. Klik in **Solution Explorer**met de rechtermuisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing** om naar het venster **NuGet - Oplossing** te gaan.

1. Selecteer **Bladeren**.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Kies **in Pakketbron** **nuget.org**.

1. Voer **in** het `Microsoft.CognitiveServices.Speech`vak Zoeken het pakket in en kies dat pakket nadat het in de zoekresultaten is weergegeven.

   ![Schermafbeelding van het dialoogvenster Manage Packages for Solution](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Selecteer in het venster met de pakketstatus naast de zoekresultaten uw **helloworld-project.**

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoogvenster **Voorvertoningswijzigingen.**

1. Bekijk de licentie in het dialoogvenster **Licentieacceptatie** en selecteer **I Accept**. De installatie van het pakket begint en wanneer de installatie is voltooid, wordt in het deelvenster Uitvoer een bericht weergegeven **dat** lijkt op de volgende tekst: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.10.0' to helloworld`.
