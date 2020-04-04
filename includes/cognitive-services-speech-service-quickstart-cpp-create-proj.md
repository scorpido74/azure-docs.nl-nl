---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 72e9334b4df58b1b90288cb7363a7d94bb531661
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658652"
---
Als u een Visual Studio-project wilt maken voor c++ desktopontwikkeling, moet u de ontwikkelingsopties van Visual Studio instellen, het project maken, de doelarchitectuur selecteren en de Speech SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelingsopties voor Visual Studio instellen

Zorg ervoor dat u correct bent ingesteld in Visual Studio voor c++ bureaubladontwikkeling:

1. Open Visual Studio 2019 om het **venster Start** weer te geven.

   ![Startvenster - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Selecteer **Doorgaan zonder code** om naar de Visual Studio IDE te gaan.

1. Selecteer in de menubalk van Visual Studio de optie > **Extra's en -onderdelen ophalen** om Visual Studio Installer te openen en het dialoogvenster **Wijzigen weer te** geven. **Tools**

   ![Tabblad Workloads, dialoogvenster Wijzigen, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Zoek op het tabblad **Werkbelastingen** onder **Windows**de **bureaubladontwikkeling met C++-werkbelasting.** Als het selectievakje naast die werkbelasting nog niet is ingeschakeld, selecteert u deze optie.

1. Zoek op het tabblad **Afzonderlijke onderdelen** het selectievakje **Nuget-pakketbeheer.** Als het selectievakje nog niet is ingeschakeld, schakelt u het selectievakje in.

1. Selecteer de knop in de hoek met het label **Sluiten** of **Wijzigen**. (De naam van de knop is afhankelijk van of u functies voor installatie hebt geselecteerd.) Als u **Wijzigen**selecteert, begint de installatie, wat even kan duren.

1. Visual Studio Installer sluiten.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doelarchitectuur selecteren

Maak vervolgens uw project:

1. Kies in de menubalk Visual Studio de optie**Nieuw** > **project** **bestand** > om het venster Een nieuw project **maken** weer te geven.

   ![Een nieuw project maken, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Zoeken en selecteren **console-app**. Zorg ervoor dat u de C++-versie van dit projecttype selecteert (in tegenstelling tot C# of Visual Basic).

1. Selecteer **Volgende** om het **nieuwe projectscherm configureren** weer te geven.

   ![Uw nieuwe project configureren, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Voer **in Projectnaam**. `helloworld`

1. Navigeer **in Locatie**naar en selecteer of maak de map om uw project op te slaan.

Selecteer nu uw doelplatformarchitectuur. Zoek op de werkbalk Visual Studio de vervolgkeuzelijst **Oplossingsplatforms.** (Als u deze niet ziet, kiest u**Werkbalken** >  **weergeven** > **standaard** om de werkbalk weer te geven met **oplossingsplatforms**.) Als u 64-bits Windows uitvoert, kiest u **x64** in de vervolgkeuzelijst. 64-bits Windows kan ook 32-bits toepassingen uitvoeren, zodat u **desgevraagd x86** kiezen.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer ten slotte het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget)en verwijs naar de Speech SDK in uw project:

1. Klik in **Solution Explorer**met de rechtermuisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing** om naar het venster **Nuget - Oplossing** te gaan.

1. Selecteer **Bladeren**.

   ![NuGet - Tabblad Oplossing, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Kies **in Pakketbron** **nuget.org**.

1. Voer **in** het `Microsoft.CognitiveServices.Speech`vak Zoeken het pakket in en kies dat pakket nadat het in de zoekresultaten is weergegeven.

   ![Microsoft.CognitiveServices.Speech C++ pakket installeren - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Selecteer in het venster met de pakketstatus naast de zoekresultaten uw **helloworld-project.**

1. Selecteer **Installeren**.

1. Selecteer **OK**in het dialoogvenster **Voorvertoningswijzigingen.**

1. Bekijk de licentie in het dialoogvenster **Licentieacceptatie** en selecteer **I Accept**. De installatie van het pakket begint en wanneer de installatie is voltooid, wordt in het deelvenster Uitvoer een bericht weergegeven **dat** lijkt op de volgende tekst: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`.
