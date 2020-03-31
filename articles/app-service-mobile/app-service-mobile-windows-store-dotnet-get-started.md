---
title: Een UWP-app maken
description: Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor Universal Windows Platform (UWP)-app-ontwikkeling in C#, Visual Basic of JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461382"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Een Windows-app maken met een Azure-back-end

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Overzicht

Deze zelfstudie laat zien hoe u een back-endservice toevoegt aan een Universal Windows Platform (UWP)-app in de cloud. Zie [What are Mobile Apps](app-service-mobile-value-prop.md) (Wat zijn Mobile Apps?) voor meer informatie. Hier volgen enkele schermopnamen van een voltooide app:

![Voltooide bureaublad-app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Het voltooien van deze zelfstudie is een vereiste voor alle andere zelfstudies over Mobile Apps voor UWP-apps.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal tien gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Windows 10.
* Visual Studio Community 2017
* Bekendheid met UWP-app-ontwikkeling. Raadpleeg de [UWP-documentatie](https://docs.microsoft.com/windows/uwp/) voor meer informatie over de [configuratie](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) die u nodig hebt om UWP-apps te bouwen.

## <a name="create-a-new-azure-mobile-app-backend"></a>Een nieuwe back-end voor mobiele apps van Azure maken

Volg deze stappen voor het maken van een nieuwe back-end voor Mobile Apps.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een databaseverbinding maken en het client- en serverproject configureren
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Het clientproject uitvoeren

1. Open het UWP-project.

2. Ga naar de [Azure-portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek `Overview` op het blad naar de URL die het openbare eindpunt is voor uw mobiele app. Voorbeeld - de sitenaam voor mijn app-naam https://test123.azurewebsites.net"test123" is .

3. Open het `App.xaml.cs` bestand in deze map - windows-uwp-cs/ZUMOAPPNAME/. De toepassingsnaam `ZUMOAPPNAME`is .

4. Vervang `App` `ZUMOAPPURL` in de klasse parameter met het bovenstaande openbare eindpunt.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    Wordt
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Druk op de F5-toets om de app te implementeren en uit te voeren.

6. Typ zinvolle tekst in de app, zoals *Voltooi de zelfstudie*, in het tekstvak **Nieuwe taak invoegen** en klik op **Opslaan**.

    ![Windows-snelstartgids: bureaublad voltooien](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure.
