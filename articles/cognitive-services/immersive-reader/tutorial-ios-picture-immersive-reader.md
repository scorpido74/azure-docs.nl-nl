---
title: 'Zelfstudie: Maak een iOS-app die een foto maakt en deze start in de Immersive Reader (Swift)'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie bouw je een iOS-app vanaf nul en voeg je de Picture toe aan immersive reader-functionaliteit.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841843"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Zelfstudie: Maak een iOS-app die de immersive reader start met inhoud van een foto (Swift)

De [Immersive Reader](https://www.onenote.com/learningtools) is een inclusief ontworpen tool die beproefde technieken implementeert om het begrijpen van lezen te verbeteren.

De [Computer Vision Cognitive Services Read API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) detecteert tekstinhoud in een afbeelding met behulp van de nieuwste herkenningsmodellen van Microsoft en zet de geïdentificeerde tekst om in een machineleesbare tekenstroom.

In deze zelfstudie bouw je een iOS-app vanaf nul en integreer je de Read API en de Immersive Reader met behulp van de Immersive Reader SDK. Een volledig werkende steekproef van deze tutorial is [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift)beschikbaar.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Een Immersive Reader-bron die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om je op te stellen. U hebt een aantal waarden nodig die hier zijn gemaakt bij het configureren van de projecteigenschappen van het voorbeeld. Sla de uitvoer van uw sessie op in een tekstbestand voor toekomstige verwijzingen.
* Voor het gebruik van dit voorbeeld is een Azure-abonnement op de Computer Vision Cognitive Service vereist. [Maak een Computer Vision Cognitive Service-bron in de Azure-portal.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Maak een nieuw project in Xcode.

![Nieuw project](./media/ios/xcode-create-project.png)

Kies **App voor één weergave**.

![Nieuwe Single View-app](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Haal de SDK CocoaPod
De eenvoudigste manier om de Immersive Reader SDK te gebruiken is via CocoaPods. Installeren via Cocoapods:
1. [Installeer CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - Volg de handleiding voor het installeren van Cocoapods.
2. Maak een Podfile `pod init` door uit te voeren in de hoofdmap van het Xcode-project.
3.  Voeg de CocoaPod toe aan `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`uw Podfile door toevoeging . Uw Podfile moet er als volgt uitzien, waarbij de naam van uw doelwit picture-to-immersive-reader-swift vervangt:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. Voer in de terminal in de map van `pod install` uw Xcode-project de opdracht uit om de SDK-pod Immersive Reader te installeren.
5. Toevoegen `import immersive_reader_sdk` aan alle bestanden die moeten verwijzen naar de SDK.
6. Zorg ervoor dat u `.xcworkspace` het project `.xcodeproj` opent door het bestand te openen en niet het bestand.

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken aanschaffen

U hebt een bepaalde waarden nodig uit de vereiste stap voor azure AD-verificatieconfiguratie boven dit onderdeel. Ga terug naar het tekstbestand dat u van die sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Maak in de hoofdmap van het project, die het bestand ViewController.swift bevat, een Swift-klassebestand met de naam Constants.swift. Vervang de klasse door de volgende code en voeg waar van toepassing uw waarden toe. Bewaar dit bestand als een lokaal bestand dat alleen op uw machine bestaat en zorg ervoor dat u dit bestand niet in bronbeheer vastlegt, omdat het geheimen bevat die niet openbaar mogen worden gemaakt. Het wordt aanbevolen dat u geen geheimen in uw app hebt. In plaats daarvan raden we aan om een backend-service te gebruiken om het token te verkrijgen, waarbij de geheimen buiten de app en buiten het apparaat kunnen worden bewaard. Het backend API-eindpunt moet worden beveiligd achter een of andere vorm van verificatie (bijvoorbeeld [OAuth](https://oauth.net/2/)) om te voorkomen dat onbevoegde gebruikers tokens verkrijgen om te gebruiken tegen uw Immersive Reader-service en facturering; dat werk valt buiten het bereik van deze tutorial.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>De app instellen om te worden uitgevoerd zonder storyboard

Open AppDelegate.swift en vervang het bestand door de volgende code.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Functionaliteit toevoegen voor het maken en uploaden van foto's

Wijzig de naam van ViewController.swift naar PictureLaunchViewController.swift en vervang het bestand door de volgende code.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Stel het archiefschema in Xcode in door een simulator of apparaatdoel te selecteren.
![Archiefschema](./media/ios/xcode-archive-scheme.png)<br/>
![Doel selecteren](./media/ios/xcode-select-target.png)

Druk in Xcode op Ctrl + R of klik op de afspeelknop om het project uit te voeren en de app moet worden gestart op de opgegeven simulator of het opgegeven apparaat.

In uw app ziet u het:

![Voorbeeldapp](./media/ios/picture-to-immersive-reader-ipad-app.png)

Maak of upload een foto van tekst in de app door op de knop 'Foto maken' of 'Foto uit bibliotheek kiezen' te drukken en de Immersive Reader start vervolgens met het weergeven van de tekst uit de foto.

![Insluitende lezer](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Meeslepende Reader SDK-referentie](./reference.md)
