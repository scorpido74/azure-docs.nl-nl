---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240275"
---
1. Open het gedownloade clientproject met Xcode.

2. Ga naar de [Azure-portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek `Overview` op het blad naar de URL die het openbare eindpunt is voor uw mobiele app. Voorbeeld - de sitenaam voor mijn app-naam https://test123.azurewebsites.net"test123" is .

3. Open voor Swift-project `ToDoTableViewController.swift` het bestand in deze map - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. De toepassingsnaam `ZUMOAPPNAME`is .

4. Vervang `viewDidLoad()` in `ZUMOAPPURL` de methode de parameter door het openbare eindpunt hierboven.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    Wordt
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Open het bestand `QSTodoService.m` in deze map voor het Objective-C-project - ZUMOAPPNAME/ZUMOAPPNAME. De toepassingsnaam `ZUMOAPPNAME`is .

6. Vervang `init` in `ZUMOAPPURL` de methode de parameter door het openbare eindpunt hierboven.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    Wordt
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten in de iOS-emulator.

8. Klik in de app**+** op het pluspictogram ( ) en typ betekenisvolle tekst, zoals *De zelfstudie voltooien*en klik vervolgens op de knop Opslaan. Er wordt nu een POST-aanvraag verzonden naar de back-end van Azure die u eerder hebt geïmplementeerd. De back-end voegt gegevens van de aanvraag toe aan de SQL-takentabel en stuurt informatie over de nieuw opgeslagen items terug naar de mobiele app. Deze gegevens worden in de lijst in de mobiele app weergegeven.

   ![Snelstartgids-app uitgevoerd op iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
