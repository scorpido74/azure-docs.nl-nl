---
title: 'Quickstart: Een iOS-app maken waarmee de insluitende lezer wordt gestart (SWIFT)'
titleSuffix: Azure Cognitive Services
description: In deze Snelstartgids bouwt u een volledig nieuwe iOS-app en voegt u de functionaliteit voor insluitende lezer toe.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899534"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Quickstart: Een iOS-app maken waarmee de insluitende lezer wordt gestart (SWIFT)

De [insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulp programma waarmee bewezen technieken worden geïmplementeerd om de Lees vaardigheid te verbeteren.

In deze Quick Start bouwt u een volledig nieuwe iOS-app en integreert u de insluitende lezer met behulp van de insluitende lezer-SDK. [Hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift)vindt u een volledig werkend voor beeld van deze Quick Start.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie (Azure AD). Volg [deze instructies om de](./azure-active-directory-authentication.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de voorbeeld project eigenschappen. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Maak een nieuw project in Xcode.

![Nieuw project](./media/ios/xcode-create-project.png)

Kies **app met één weer gave**.

![Nieuwe app voor één weer gave](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>De SDK-CocoaPod ophalen
De eenvoudigste manier om de insluitende lezer-SDK te gebruiken is via CocoaPods. Installeren via Cocoapods:
1. [Installeer Cocoapods](http://guides.cocoapods.org/using/getting-started.html) -Volg de aan de slag-hand leiding om Cocoapods te installeren.
2. Maak een Podfile door uit `pod init` te voeren in de hoofdmap van uw Xcode-project.
3.  Voeg de CocoaPod toe aan uw Podfile door `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`toe te voegen. Uw Pofile moet er als volgt uitzien, met de naam van de doel computer vervangen-SWIFT:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. Voer in de Terminal, in de map van uw Xcode-project, de `pod install` opdracht uit om de insluitende Reader SDK pod te installeren.
5. Voeg `import immersive_reader_sdk` toe aan alle bestanden die moeten verwijzen naar de SDK.
6. Zorg ervoor dat u het project opent door `.xcworkspace` het bestand en niet `.xcodeproj` het bestand te openen.

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatie token verkrijgen

U hebt een aantal waarden nodig van de hierboven genoemde Azure AD-verificatie configuratie voor dit onderdeel. Ga terug naar het tekst bestand dat u van deze sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Maak in de hoofdmap van het project, dat het bestand view controller. Swift bevat, een Swift-klasse bestand met de naam constanten. Swift. Vervang de klasse door de volgende code door de waarden toe te voegen, indien van toepassing. Bewaar dit bestand als een lokaal bestand dat alleen op uw computer bestaat en zorg ervoor dat dit bestand niet wordt door gegeven aan broncode beheer, omdat het geheimen bevat dat niet openbaar mag worden gemaakt. U wordt aangeraden geen geheimen in uw app te bedenken. In plaats daarvan raden we u aan om een back-end-service te gebruiken om het token te verkrijgen, waarbij de geheimen kunnen worden bewaard buiten de app en van het apparaat. Het back-end-API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat niet-geautoriseerde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering. Dit werk valt buiten het bereik van deze Quick Start.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Instellen dat de app wordt uitgevoerd zonder een Story Board

Open AppDelegate. Swift en vervang het bestand door de volgende code.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>De start weergave controller maken en voorbeeld inhoud toevoegen

Wijzig de naam van view controller. Swift in LaunchViewController. Swift en vervang het bestand door de volgende code.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Stel het archief schema in Xcode in door een Simulator of apparaat doel te selecteren.
![Archief schema](./media/ios/xcode-archive-scheme.png)<br/>
![Doel selecteren](./media/ios/xcode-select-target.png)

In Xcode, drukt u op CTRL + R of klikt u op de knop afspelen om het project uit te voeren. de app moet worden gestart op de opgegeven Simulator of op het apparaat.

In uw app ziet u het volgende:

![Voor beeld-app](./media/ios/sample-app-ipad.png)

Wanneer u op de knop ' insluitende lezer ' klikt, ziet u dat de insluitende lezer wordt gestart met de inhoud van de app.

![Insluitende lezer](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Volgende stappen

* Verken de insluitende [lezer IOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) en de referentie voor de insluitende [lezer IOS SDK](./ios-reference.md)
