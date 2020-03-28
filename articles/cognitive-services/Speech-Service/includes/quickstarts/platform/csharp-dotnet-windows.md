---
title: 'Snelstart: SpraakSDK voor .NET Framework (Windows) platform setup - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform voor C# in te stellen onder .NET Framework voor Windows met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925596"
---
In deze handleiding ziet u hoe u de [SpraakSDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor .NET Framework (Windows) installeert. Als je alleen wilt dat de naam van `Install-Package Microsoft.CognitiveServices.Speech` het pakket alleen aan de slag gaat, voer je uit in de NuGet-console.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Een Visual Studio-project maken en de Speech SDK installeren

U moet het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) installeren, zodat u er naar verwijzen in uw code. Om dat te doen, moet u mogelijk eerst een **helloworld-project** maken. Als u al een project hebt met de **.NET-werkbelasting voor bureaubladontwikkeling** beschikbaar, u dat project gebruiken en [NuGet Package Manager gebruiken om de SpraakSDK te installeren.](#use-nuget-package-manager-to-install-the-speech-sdk)

### <a name="create-helloworld-project"></a>Maak helloworld project

1. Open Visual Studio 2019.

1. Selecteer in het venster Start de optie **Een nieuw project maken**. 

1. Kies **console-app (.NET Framework)** in **het venster Een nieuw project maken** en selecteer **Volgende**.

1. Voer in het **venster Uw nieuwe project configureren** *helloworld* in **Projectnaam**in, kies of maak het mappad in **Locatie**en selecteer **Vervolgens Maken**.

1. Selecteer in de menubalk van Visual Studio de optie > **Extra's en -onderdelen ophalen**, waarmee Visual Studio Installer wordt geopend en het dialoogvenster **Wijzigen wordt** weergegeven. **Tools**

1. Controleer of de workload **.NET-bureaubladontwikkeling** beschikbaar is. Als de werkbelasting niet is geïnstalleerd, schakelt u het selectievakje ernaast in en selecteert u **Wijzigen** om de installatie te starten. Het downloaden en installeren kan een paar minuten duren.

   Als het selectievakje naast **.NET-bureaubladontwikkeling** al is ingeschakeld, selecteert u **Sluiten** om het dialoogvenster af te sluiten.

   ![.NET-desktopontwikkeling inschakelen](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio Installer sluiten.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>NuGet Package Manager gebruiken om de SpraakSDK te installeren

1. Klik in de Solution Explorer met de rechtermuisknop op het **helloworld-project** en selecteer **NuGet-pakketten beheren** om de NuGet-pakketbeheer weer te geven.

   ![NuGet-pakketbeheer](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Zoek in de rechterbovenhoek het vervolgkeuzeveld **Package Source** en **`nuget.org`** controleer of dit is geselecteerd.

1. Selecteer **Bladeren**in de linkerbovenhoek.

1. Typ *Microsoft.CognitiveServices.Speech* in het zoekvak en selecteer **Enter**.

1. Selecteer in de zoekresultaten het pakket **Microsoft.CognitiveServices.Speech** en selecteer **Installeren** om de nieuwste stabiele versie te installeren.

   ![Microsoft.CognitiveServices.Speech NuGet-pakket installeren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt een bevestiging weergegeven in het venster **Package Manager Console.**

### <a name="choose-target-architecture"></a>Doelarchitectuur kiezen

Als u de consoletoepassing wilt bouwen en uitvoeren, maakt u een platformconfiguratie die overeenkomt met de architectuur van uw computer.

1. Selecteer**Configuratiebeheer** **bouwen** > op de menubalk . Het dialoogvenster **Configuratiebeheer** wordt weergegeven.

   ![Dialoogvenster Configuratiebeheer](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **Selecteer**Nieuw in het vervolgkeuzevak Actieve **oplossingsplatform** . Het dialoogvenster **Nieuw oplossingsplatform** wordt weergegeven.

1. Selecteer in de vervolgkeuzelijst Tekst of selecteer het nieuwe **platform:**
   - Als u 64-bits Windows uitvoert, selecteert u **x64**.
   - Als u 32-bits Windows uitvoert, selecteert u **x86**.

1. Selecteer **OK** en sluit **.**

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
