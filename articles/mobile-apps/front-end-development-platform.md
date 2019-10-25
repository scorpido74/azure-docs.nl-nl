---
title: Het front-end-ontwikkel platform voor het bouwen van client toepassingen met Visual Studio en Azure-Services kiezen
description: Meer informatie over de ondersteunde systeem eigen en andere platform talen voor het bouwen van client toepassingen.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795495"
---
# <a name="choose-mobile-development-framework"></a>Mobile Development-Framework kiezen
Ontwikkel aars kunnen client-side technologieën gebruiken om mobiele toepassingen zelf te bouwen, met behulp van specifieke frameworks en patronen voor een platformoverschrijdende benadering. Ontwikkel aars kunnen systeem eigen (systeem eigen, single-platformen) bouwen met behulp van talen als objectieve C-en Java-toepassingen, platformoverschrijdende toepassingen C#die gebruikmaken van Xamarin, .net en en hybride (met Cordova) en de bijbehorende varianten, afhankelijk van hun beslissing elementen.

## <a name="native-platforms"></a>Systeem eigen platforms
Voor het bouwen van een systeem eigen toepassing zijn platformspecifieke programmeer talen, Sdk's, ontwikkel omgeving en andere hulpprogram ma's van leveranciers van het besturings systeem vereist.

### <a name="ios"></a>iOS
Apps die zijn gebouwd op iOS worden door Apple gemaakt en ontwikkeld, namelijk iPhone en iPad.

- **Programmeer talen** -objectief-C, Swift
- **IDE** -Xcode
- **SDK** -IOS-SDK

### <a name="android"></a>Android
Toepassingen die zijn gebouwd op Android, ontworpen door Google en het populairste besturings systeem in de wereld, kunnen worden uitgevoerd op verschillende smartphones en tablets.

- **Programmeer taal** -Java, Kotlin 
- **IDE** -Android Studio en Android-Ontwikkelhulpprogramma's 
- **SDK** -ANDROID-SDK

### <a name="windows"></a>Windows
- **Programmeer taal** -C#
- **IDE** -Visual Studio, Visual Studio code
- **SDK** -Windows SDK

#### <a name="pros"></a>-Professionals
- Goede gebruikers ervaring
- Responsieve toepassingen met hoge prestaties en mogelijkheden voor de interface met systeem eigen bibliotheken
- Zeer veilige toepassingen

#### <a name="cons"></a>Nadelen
- Toepassing wordt alleen op één platform uitgevoerd
- Meer resources voor ontwikkel aars en dure functies voor het bouwen van een toepassing
- Lagere code gebruiken

## <a name="cross-platforms-and-hybrid-applications"></a>Meerdere platforms en hybride toepassingen
Toepassingen voor meerdere platformen bieden u de mogelijkheid om systeem eigen mobiele toepassingen te schrijven, code te delen en ze uit te voeren op iOS, Android en Windows.

### <a name="xamarin"></a>Xamarin
Met [Xamarin](https://visualstudio.microsoft.com/xamarin/) kunt u robuuste, platformoverschrijdende mobiele toepassingen bouwen in C#, met een klassen bibliotheek en runtime die werkt op verschillende platforms, waaronder IOS, Android en Windows, terwijl de systeem eigen (niet-geïnterpreteerde ) toepassingen die zeer worden uitgevoerd. Xamarin combineert alle mogelijkheden van de systeem eigen platforms en voegt een aantal krachtige functies van eigen platformen toe.

- **Programmeer taal** -C#
- **IDE** -Visual Studio op Windows of Mac

### <a name="react-native"></a>React Native
Op basis van Facebook in 2015, [reageert systeem eigen](https://facebook.github.io/react-native/) is een [open source](https://github.com/facebook/react-native) java script-Framework voor het schrijven van echte, systeem eigen mobiele toepassingen voor IOS en Android. Het is gebaseerd op reageren, de Java script-bibliotheek van Facebook voor het bouwen van gebruikers interfaces, maar in plaats van te richten op de browser, streeft deze naar mobiele platforms. Systeem eigen systeem eigen onderdelen reageren in plaats van webonderdelen als bouw stenen.
 
- **Programmeer taal** -java script
- **IDE** -Visual Studio code

### <a name="unity"></a>Unity
 Unit is een engine die is geoptimaliseerd voor het maken van spellen waarmee ontwikkel aars 2D/3D-toepassingen van C# hoge kwaliteit kunnen bouwen met voor verschillende platforms, waaronder Windows, Ios, Android en Xbox.

### <a name="cordova"></a>Cordova
Met Cordova kunt u hybride toepassingen bouwen met behulp van Visual Studio Tools voor Apache Codova of Visual Studio code met extensies voor Cordova. Met de hybride benadering kunt u onderdelen delen met websites en webserver toepassingen hergebruiken met gehoste webtoepassingen op basis van Cordova.

#### <a name="pros"></a>-Professionals
- Verhoogde code-bruikbaarheid door één code basis voor meerdere platforms te maken
- Keuken naar een breder publiek op verschillende platforms
- Aanzienlijke vermindering van de ontwikkelings tijd
- Eenvoudig te starten en bij te werken

#### <a name="cons"></a>Nadelen
- Lagere prestaties
- Geen flexibiliteit
- Elk platform heeft een unieke set functies en functionaliteit om de systeem eigen toepassing creatiever te maken
- Verbeterde ontwerp tijd van de gebruikers interface
- Beperking van het hulp programma
