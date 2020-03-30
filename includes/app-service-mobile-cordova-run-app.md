---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240231"
---
1. Navigeer naar het oplossingsbestand in het clientproject (.sln) en open het met Visual Studio.

2. Kies in Visual Studio het oplossingsplatform (Android, iOS of Windows) in de vervolgkeuzelijst naast de begin-pijl. Selecteer een specifiek implementatieapparaat of een emulator door op de vervolgkeuzelijst op de groene pijl te klikken. U kunt het standaard-Android-platform en de standaard-Ripple-emulator gebruiken. Voor geavanceerdere zelfstudies (bijv. over pushmeldingen) moet u een ondersteund apparaat of ondersteunde emulator selecteren.

3. Open het `ToDoActivity.java` bestand in deze map - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. De toepassingsnaam `ZUMOAPPNAME`is .

4. Ga naar de [Azure-portal](https://portal.azure.com/) en navigeer naar de mobiele app die u hebt gemaakt. Zoek `Overview` op het blad naar de URL die het openbare eindpunt is voor uw mobiele app. Voorbeeld - de sitenaam voor mijn app-naam https://test123.azurewebsites.net"test123" is .

5. Ga naar `index.js` het bestand in ZUMOAPPNAME/www/js/index.js `ZUMOAPPURL` en vervang parameter in `onDeviceReady()` de methode door het bovenstaande openbare eindpunt.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    Wordt
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Druk op F5 of klik op de groene pijl om uw Cordova-app te bouwen en uit te voeren. Accepteer het beveiligingsdialoogvenster in de emulator waarin om toegang tot het netwerk wordt gevraagd, als dit wordt weergegeven.

7. Nadat de app is gestart op het apparaat of de emulator, typt u zinvolle tekst in **Nieuwe tekst invoeren,** zoals *De zelfstudie voltooien* en klikt u op de knop **Toevoegen.**

De back-end voegt gegevens van de aanvraag toe aan de TodoItem-tabel in de SQL Database en stuurt informatie over de nieuw opgeslagen items terug naar de mobiele app. Deze gegevens worden in de lijst in de mobiele app weergegeven.

U kunt stap 3 t/m 5 herhalen voor andere platforms.