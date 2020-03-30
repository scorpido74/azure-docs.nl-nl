---
title: Kies het front-end ontwikkelplatform voor het bouwen van clienttoepassingen met Visual Studio- en Azure-services
description: Meer informatie over de ondersteunde native en cross-platform talen om clienttoepassingen te bouwen.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240866"
---
# <a name="choose-mobile-development-frameworks"></a>Kies mobiele ontwikkelingskaders
Ontwikkelaars kunnen client-side technologieën gebruiken om zelf mobiele applicaties te bouwen door specifieke frameworks en patronen te gebruiken voor een cross-platform aanpak. Op basis van hun beslissingsfactoren kunnen ontwikkelaars bouwen:
- Native single-platform applicaties met talen als Objective C en Java
- Cross-platform applicaties met Xamarin, .NET en C #
- Hybride toepassingen met behulp van Cordova en zijn varianten

## <a name="native-platforms"></a>Native platforms
Het bouwen van een native applicatie vereist platformspecifieke programmeertalen, SDK's, ontwikkelomgevingen en andere tools die worden geleverd door OS-leveranciers.

### <a name="ios"></a>iOS
IOS is gemaakt en ontwikkeld door Apple en wordt gebruikt om apps te bouwen op Apple-apparaten, namelijk de iPhone en de iPad.

- **Programmeertalen**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Ontworpen door Google en het populairste besturingssysteem ter wereld, wordt Android gebruikt om toepassingen te bouwen die kunnen draaien op een reeks smartphones en tablets.

- **Programmeertaal**: Java, Kotlin 
- **IDE**: Android Studio en Android developer tools 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programmeertaal**: C #
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Voordelen
- Goede gebruikerservaring
- Responsieve toepassingen met hoge prestaties en de mogelijkheid om te communiceren met native bibliotheken
- Zeer veilige toepassingen

#### <a name="cons"></a>Nadelen
- Toepassing draait op slechts één platform
- Meer ontwikkelaar resource intensief en duur om een applicatie te bouwen
- Lagere code hergebruik

## <a name="cross-platforms-and-hybrid-applications"></a>Cross-platforms en hybride toepassingen
Cross-platform applicaties geven je de kracht om eenmaal native mobiele applicaties te schrijven, code te delen en ze uit te voeren op iOS, Android en Windows.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) is eigendom van Microsoft en wordt gebruikt om robuuste, cross-platform mobiele applicaties te bouwen in C#. Xamarin heeft een klassenbibliotheek en runtime die werkt op veel platforms, zoals iOS, Android en Windows. Het compileert ook native (niet-geïnterpreteerde) toepassingen die hoge prestaties leveren. Xamarin combineert alle mogelijkheden van de native platforms en voegt een aantal krachtige functies van zijn eigen.

- **Programmeertaal**: C #
- **IDE**: Visual Studio op Windows of Mac

### <a name="react-native"></a>Reageren Native
[React Native,](https://facebook.github.io/react-native/) uitgebracht door Facebook in 2015, is een [open-source JavaScript-framework](https://github.com/facebook/react-native) voor het schrijven van echte, native renderende mobiele applicaties voor iOS en Android. Het is gebaseerd op React, Facebook's JavaScript-bibliotheek voor het bouwen van gebruikersinterfaces. In plaats van zich op de browser te richten, richt het zich op mobiele platforms. React Native gebruikt native componenten in plaats van webcomponenten als bouwstenen.
 
- **Programmeertaal**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Unity is een engine die geoptimaliseerd is voor het maken van games. U het gebruiken om 2D- of 3D-toepassingen van hoge kwaliteit te maken met C# voor platforms zoals Windows, iOS, Android en Xbox.

### <a name="cordova"></a>Cordova
Met Cordova u hybride toepassingen bouwen met Behulp van Visual Studio Tools voor Apache Cordova of Visual Studio Code met extensies voor Cordova. Met de hybride aanpak u componenten delen met websites en webservergebaseerde toepassingen hergebruiken met gehoste webapplicatiebenaderingen op basis van Cordova.

#### <a name="pros"></a>Voordelen
- Verhoogde gebruiksvriendelijkheid van code door één codebase te maken voor meerdere platforms
- Tegemoet te komen aan een breder publiek op vele platforms
- Drastische vermindering van de ontwikkelingstijd
- Eenvoudig te starten en bij te werken

#### <a name="cons"></a>Nadelen
- Lagere prestaties
- Gebrek aan flexibiliteit
- Elk platform heeft een unieke set functies en functionaliteit om de native applicatie creatiever te maken
- Verhoogde ui-ontwerptijd
- Gereedschapsbeperking
