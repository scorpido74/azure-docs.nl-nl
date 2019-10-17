---
title: Aan de slag met Mobile Apps met behulp van Xamarin.Forms
description: Volg deze zelfstudie om aan de slag te gaan met Mobile Apps voor Xamarin.Forms-ontwikkeling
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e891bc34840e8c8bb61be1a9da607c43a93f2d17
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388451"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Een Xamarin.Forms-app maken met Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Xamarin.Forms-app door de functie Mobile Apps van Azure App Service als de back-end te gebruiken. U maakt zowel een nieuwe back-end voor Mobile Apps als een eenvoudige Xamarin.Forms-app voor takenlijsten die app-gegevens opslaat in Azure.

Het voltooien van deze zelfstudie is een vereiste voor alle andere zelfstudies over Mobile Apps voor Xamarin.Forms.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal tien gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Maak vandaag nog uw gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

* Visual Studio Tools voor Xamarin, in Visual Studio 2017 of hoger, of Visual Studio voor Mac. Zie de [installatie pagina van Xamarin][Install Xamarin] voor instructies.

* (optioneel) Als u een iOS-app wilt maken, is een Mac met Xcode 9.0 of hoger vereist. Visual Studio voor Mac kan worden gebruikt om iOS-apps te ontwikkelen, of Visual Studio 2017 of hoger kan worden gebruikt (zolang de Mac beschikbaar is in het netwerk).

## <a name="create-a-new-mobile-apps-back-end"></a>Een nieuwe back-end voor Mobile Apps maken
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een database verbinding maken en het client-en server project configureren
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>De oplossing Xamarin. Forms uitvoeren

De Visual Studio Tools voor Xamarin zijn vereist om de oplossing te openen. Zie de [Xamarin-installatie-instructies][Install Xamarin]. Als de hulpprogramma's al zijn geïnstalleerd, voert u de volgende stappen uit om de oplossing te downloaden en te openen:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows en Mac)

1. Ga naar de [Azure Portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek op de Blade `Overview` naar de URL die het open bare eind punt is voor uw mobiele app. Voor beeld-de naam van de site van mijn app ' test123 ' wordt https://test123.azurewebsites.net.

2. Open het bestand `Constants.cs` in deze map-xamarin. Forms/ZUMOAPPNAME. De naam van de toepassing is `ZUMOAPPNAME`.

3. Vervang in de klasse `Constants.cs` `ZUMOAPPURL`-variabele met een openbaar eind punt hierboven.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    steeds

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Volg de onderstaande instructies om de Android- of Windows-projecten uit te voeren en het iOS-project als er een Mac-netwerkcomputer beschikbaar is.

## <a name="optional-run-the-android-project"></a>(Optioneel) Het Android-project uitvoeren

In deze sectie voert u het Xamarin.Android-project uit. Als u niet met Android-apparaten werkt, kunt u deze sectie overslaan.

### <a name="visual-studio"></a>Visual Studio

1. Klik met de rechtermuisknop op het Android-project (Droid) en selecteer vervolgens **Instellen als opstartproject**.

2. Selecteer **Configuration Manager** in het menu **Bouwen**.

3. Schakel in het dialoogvenster **Configuration Manager** de selectievakjes **Bouwen** en **Implementeren** in naast het Android-project en zorg ervoor dat het vak **Bouwen** is ingeschakeld voor het gedeelde codeproject.

4. Druk op **F5** of klik op de knop **Start** om het project te maken en de app te starten in een Android-emulator.

### <a name="visual-studio-for-mac"></a>Visual Studio voor Mac

1. Klik met de rechtermuisknop op het Android-project en selecteer vervolgens **Set As Startup Project**.

2. Selecteer **Start Debugging** in het menu **Run** om het project te maken en de app te starten in een Android-emulator.

Typ zinvolle tekst in de app, zoals *Xamarin leren kennen*, en selecteer vervolgens het plusteken ( **+** ).

![Taak-app van Android][11]

Hierdoor wordt er een POST-aanvraag verzonden naar de nieuwe back-end voor Mobile Apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor Mobile Apps en de gegevens worden weergegeven in de lijst.

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor Mobile Apps, in het C#-bestand **TodoItemManager.cs** van het gedeelde codeproject in de oplossing.
>

## <a name="optional-run-the-ios-project"></a>(Optioneel) Het iOS-project uitvoeren

In deze sectie voert u het Xamarin.iOS-project uit voor iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overslaan.

### <a name="visual-studio"></a>Visual Studio

1. Klik met de rechtermuisknop op het iOS-project en selecteer vervolgens **Set as StartUp Project**.

2. Selecteer **Configuration Manager** in het menu **Bouwen**.

3. Schakel in het dialoogvenster **Configuration Manager** de selectievakjes **Bouwen** en **Implementeren** in naast het iOS-project en zorg ervoor dat het vak **Bouwen** is ingeschakeld voor het gedeelde codeproject.

4. Selecteer de toets **F5** om het project te bouwen en de app te starten in de iPhone-emulator.

### <a name="visual-studio-for-mac"></a>Visual Studio voor Mac

1. Klik met de rechtermuisknop op het iOS-project en selecteer vervolgens **Set As Startup Project**.

2. Selecteer **Start Debugging** in het menu **Run** om het project te bouwen en de app te starten in de iPhone-emulator.

Typ zinvolle tekst in de app, zoals *Xamarin leren kennen*, en selecteer vervolgens het plusteken ( **+** ).

![Taak-app voor iOS][10]

Hierdoor wordt er een POST-aanvraag verzonden naar de nieuwe back-end voor Mobile Apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor Mobile Apps en de gegevens worden weergegeven in de lijst.

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor Mobile Apps, in het C#-bestand **TodoItemManager.cs** van het gedeelde codeproject in de oplossing.
>

## <a name="optional-run-the-windows-project"></a>(Optioneel) Het Windows-project uitvoeren

In deze sectie voert u het UWP-project Xamarin.Forms (Universal Windows Platform) uit voor Windows-apparaten. Als u niet met Windows-apparaten werkt, kunt u deze sectie overslaan.

### <a name="visual-studio"></a>Visual Studio

1. Klik met de rechtermuisknop op het UWP-project en selecteer vervolgens **Set as StartUp Project**.

2. Selecteer **Configuration Manager** in het menu **Bouwen**.

3. Schakel in het dialoogvenster **Configuration Manager** de selectievakjes **Bouwen** en **Implementeren** in naast het gekozen Windows-project en zorg ervoor dat het vak **Bouwen** is ingeschakeld voor het gedeelde codeproject.

4. Druk op **F5** of klik op de knop **Start** om het project te maken en de app te starten in een Windows-emulator (waar **lokale machine** moet worden weergegeven).

> [!NOTE]
> Het Windows-project kan niet worden uitgevoerd op macOS.

Typ zinvolle tekst in de app, zoals *Xamarin leren kennen*, en selecteer vervolgens het plusteken ( **+** ).

Hierdoor wordt er een POST-aanvraag verzonden naar de nieuwe back-end voor Mobile Apps die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor Mobile Apps en de gegevens worden weergegeven in de lijst.

![Taak-app voor UWP][12]

> [!NOTE]
> U vindt de code die toegang geeft tot de back-end voor Mobile Apps, in het C#-bestand **TodoItemManager.cs** van het portable class library-project van uw oplossing.
>

## <a name="troubleshooting"></a>Problemen oplossen

Als u er problemen zijn met het bouwen van de oplossing, voert u NuGet Package Manager uit om bij te werken naar de nieuwste versie van `Xamarin.Forms` en werkt u in het Android-project de `Xamarin.Android`-ondersteuningspakketten bij. Quick Start-projecten bevatten mogelijk niet altijd de nieuwste versies.

Alle ondersteuningspakketten waarnaar wordt verwezen in uw Android-project, moeten dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
