---
title: Het front-end-ontwikkel platform voor het bouwen van client toepassingen met Visual Studio en Azure-Services kiezen
description: Meer informatie over de ondersteunde systeem eigen en andere platform talen voor het bouwen van client toepassingen.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240866"
---
# <a name="choose-mobile-development-frameworks"></a>Kaders voor mobiele ontwikkeling kiezen
Ontwikkel aars kunnen client-side technologieën gebruiken om mobiele toepassingen zelf te bouwen met behulp van specifieke frameworks en patronen voor een platformoverschrijdende benadering. Ontwikkel aars kunnen op basis van hun beslissings factoren het volgende bouwen:
- Systeem eigen toepassingen met één platform door gebruik te maken van talen als objectieve C en Java
- Toepassingen voor meerdere platforms met Xamarin, .NET en C #
- Hybride toepassingen met behulp van Cordova en de varianten daarvan

## <a name="native-platforms"></a>Systeem eigen platforms
Voor het bouwen van een systeem eigen toepassing zijn platformspecifieke programmeer talen, Sdk's, ontwikkel omgevingen en andere hulpprogram ma's van leveranciers van het besturings systeem vereist.

### <a name="ios"></a>iOS
IOS wordt gemaakt en ontwikkeld door Apple en wordt gebruikt om apps op Apple-apparaten te bouwen, namelijk de iPhone en de iPad.

- **Programmeer talen**: objectief-C, Swift
- **IDE**: Xcode
- **SDK**: IOS SDK

### <a name="android"></a>Android
Android is ontworpen door Google en het populairste besturings systeem ter wereld en wordt gebruikt voor het bouwen van toepassingen die kunnen worden uitgevoerd op verschillende smartphones en tablets.

- **Programmeer taal**: Java, Kotlin 
- **IDE**: Android Studio-en Android-ontwikkel hulpprogramma's 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programmeer taal**: C #
- **IDE**: Visual Studio, Visual Studio code
- **SDK**: Windows SDK

#### <a name="pros"></a>Voordelen
- Goede gebruikers ervaring
- Responsieve toepassingen met hoge prestaties en de mogelijkheid om te voorzien in een interface met systeem eigen bibliotheken
- Zeer veilige toepassingen

#### <a name="cons"></a>Nadelen
- Toepassing wordt op slechts één platform uitgevoerd
- Meer resource-intensieve en dure resources voor het bouwen van een toepassing
- Lagere code gebruiken

## <a name="cross-platforms-and-hybrid-applications"></a>Meerdere platforms en hybride toepassingen
Toepassingen voor meerdere platformen bieden u de mogelijkheid om systeem eigen mobiele toepassingen te schrijven, code te delen en ze uit te voeren op iOS, Android en Windows.

### <a name="xamarin"></a>Xamarin
Het eigendom van micro soft, [Xamarin](https://visualstudio.microsoft.com/xamarin/) wordt gebruikt voor het bouwen van robuuste, platformoverschrijdende mobiele toepassingen in C#. Xamarin heeft een klassen bibliotheek en runtime die werkt op verschillende platforms, zoals iOS, Android en Windows. Het compileert ook systeem eigen (niet-geïnterpreteerde) toepassingen die hoge prestaties leveren. Xamarin combineert alle mogelijkheden van de systeem eigen platforms en voegt een aantal krachtige functies van eigen platformen toe.

- **Programmeer taal**: C #
- **IDE**: Visual Studio in Windows of Mac

### <a name="react-native"></a>Systeem eigen
Op basis van Facebook in 2015, [reageert systeem eigen](https://facebook.github.io/react-native/) is een [open source](https://github.com/facebook/react-native) java script-Framework voor het schrijven van echte, systeem eigen mobiele toepassingen voor IOS en Android. Het is gebaseerd op reageren, de Java script-bibliotheek van Facebook voor het bouwen van gebruikers interfaces. In plaats van de browser te richten, streeft het naar mobiele platforms. Systeem eigen systeem eigen onderdelen reageren in plaats van webonderdelen als bouw stenen.
 
- **Programmeer taal**: Java script
- **IDE**: Visual Studio code

### <a name="unity"></a>Unity
 Unity is een engine die is geoptimaliseerd voor het maken van games. U kunt dit gebruiken om 2D-of 3D-toepassingen van hoge kwaliteit te voorzien van C# voor platforms zoals Windows, iOS, Android en Xbox.

### <a name="cordova"></a>Cordova
Met Cordova kunt u hybride toepassingen bouwen met behulp van Visual Studio Tools voor Apache Cordova of Visual Studio code met extensies voor Cordova. Met de hybride benadering kunt u onderdelen delen met websites en webserver toepassingen hergebruiken met gehoste webtoepassingen op basis van Cordova.

#### <a name="pros"></a>Voordelen
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
