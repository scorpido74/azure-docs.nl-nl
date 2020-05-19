---
title: 'Zelf studie: een iOS-app maken die een foto maakt en deze start in de insluitende lezer (SWIFT)'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie bouwt u een volledig nieuwe iOS-app en voegt u de afbeelding toe aan de functionaliteit voor insluitende lezers.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: a7e0cb41f32a60e4f00cb60cc3c86e40ab926785
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735127"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Zelf studie: een iOS-app maken waarmee de insluitende lezer wordt gestart met inhoud van een foto (SWIFT)

De [insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulp programma waarmee bewezen technieken worden geïmplementeerd om de Lees vaardigheid te verbeteren.

Met de [Computer Vision-Cognitive Services Lees-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) wordt tekst inhoud in een afbeelding gedetecteerd met de nieuwste herkennings modellen van micro soft en wordt de geïdentificeerde tekst geconverteerd naar een door een machine Lees bare teken stroom.

In deze zelf studie bouwt u een volledig nieuwe iOS-app en integreert u de Lees-API en de insluitende lezer met behulp van de insluitende lezer-SDK. [Hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios)vindt u een volledig werkend voor beeld van deze zelf studie.

Als u nog geen abonnement voor Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Een resource voor insluitende lezer die is geconfigureerd voor Azure Active Directory authenticatie. Volg [deze instructies om de](./how-to-create-immersive-reader.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de voorbeeld project eigenschappen. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.
* Voor het gebruik van dit voor beeld is een Azure-abonnement op de Computer Vision cognitieve service vereist. [Maak een computer vision cognitieve service resource in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Maak een nieuw project in Xcode.

![Nieuw project](./media/ios/xcode-create-project.png)

Kies **app met één weer gave**.

![Nieuwe app voor één weer gave](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>De SDK-CocoaPod ophalen
De eenvoudigste manier om de insluitende lezer-SDK te gebruiken is via CocoaPods. Installeren via Cocoapods:
1. [Installeer CocoaPods](http://guides.cocoapods.org/using/getting-started.html) -Volg de aan de slag-hand leiding om CocoaPods te installeren.
2. Maak een Podfile door uit te voeren `pod init` in de hoofdmap van uw Xcode-project.
3.  Voeg de CocoaPod toe aan uw Podfile door toe te voegen `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` . Uw Podfile moet er als volgt uitzien, met de naam van uw doel, waarbij Picture-to-ondergedompeld-Reader-SWIFT wordt vervangen:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. Voer in de Terminal, in de map van uw Xcode-project, de opdracht uit `pod install` om de insluitende Reader SDK pod te installeren.
5. Voeg toe `import immersive_reader_sdk` aan alle bestanden die moeten verwijzen naar de SDK.
6. Zorg ervoor dat u het project opent door het `.xcworkspace` bestand en niet het bestand te openen `.xcodeproj` .

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatie token verkrijgen

U hebt een aantal waarden nodig van de hierboven genoemde Azure AD-verificatie configuratie voor dit onderdeel. Ga terug naar het tekst bestand dat u van deze sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Maak in de hoofdmap van het project, dat het bestand view controller. Swift bevat, een Swift-klasse bestand met de naam constanten. Swift. Vervang de klasse door de volgende code door de waarden toe te voegen, indien van toepassing. Bewaar dit bestand als een lokaal bestand dat alleen op uw computer bestaat en zorg ervoor dat dit bestand niet wordt door gegeven aan broncode beheer, omdat het geheimen bevat dat niet openbaar mag worden gemaakt. U wordt aangeraden geen geheimen in uw app te bedenken. In plaats daarvan raden we u aan om een back-end-service te gebruiken om het token te verkrijgen, waarbij de geheimen kunnen worden bewaard buiten de app en van het apparaat. Het back-end-API-eind punt moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voor komen dat niet-geautoriseerde gebruikers tokens verkrijgen om te gebruiken voor uw insluitende lezer-service en facturering. Dit werk valt buiten het bereik van deze zelf studie.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Instellen dat de app wordt uitgevoerd zonder een Story Board

Open AppDelegate. Swift en vervang het bestand door de volgende code.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Functionaliteit toevoegen voor het maken en uploaden van Foto's

Wijzig de naam van view controller. Swift in PictureLaunchViewController. Swift en vervang het bestand door de volgende code.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Stel het archief schema in Xcode in door een Simulator of apparaat doel te selecteren.
![Archief schema](./media/ios/xcode-archive-scheme.png)<br/>
![Doel selecteren](./media/ios/xcode-select-target.png)

In Xcode, drukt u op CTRL + R of klikt u op de knop afspelen om het project uit te voeren. de app moet worden gestart op de opgegeven Simulator of op het apparaat.

In uw app ziet u het volgende:

![Voorbeeldapp](./media/ios/picture-to-immersive-reader-ipad-app.png)

In de app kunt u een foto van tekst nemen of uploaden door op de knop foto nemen of de knop foto van de bibliotheek kiezen te drukken en de insluitende lezer vervolgens te starten met het weer geven van de tekst van de foto.

![Immersive Reader](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Volgende stappen

* De referentie voor de [insluitende lezer-SDK](./reference.md) verkennen
