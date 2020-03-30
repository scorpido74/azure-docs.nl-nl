---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240321"
---
1. Open het project met **Android Studio** via **Project importeren (Eclipse ADT, Gradle enzovoort)**. Zorg ervoor dat u deze importselectie maakt om eventuele JDK-fouten te voorkomen.

2. Open het `ToDoActivity.java` bestand in deze map - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. De toepassingsnaam `ZUMOAPPNAME`is .

3. Ga naar de [Azure-portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek `Overview` op het blad naar de URL die het openbare eindpunt is voor uw mobiele app. Voorbeeld - de sitenaam voor mijn app-naam https://test123.azurewebsites.net"test123" is .

4. Vervang `onCreate()` in `ZUMOAPPURL` de methode de parameter door het openbare eindpunt hierboven.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    Wordt
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Druk op de knop **App uitvoeren** om het project te bouwen en de app te starten in de Android-emulator.

4. Typ zinvolle tekst in de app, zoals *De zelfstudie voltooien* en klik vervolgens op de knop Toevoegen. Er wordt nu een POST-aanvraag verzonden naar de back-end van Azure die u eerder hebt geïmplementeerd. De back-end voegt gegevens van de aanvraag toe aan de SQL-takentabel en stuurt informatie over de nieuw opgeslagen items terug naar de mobiele app. Deze gegevens worden in de lijst in de mobiele app weergegeven.
    ![Quickstart Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)