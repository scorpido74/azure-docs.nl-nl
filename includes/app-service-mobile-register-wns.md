---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176532"
---
1. Klik in Visual Studio Solution Explorer met de rechter muisknop op het Windows Store-app-project. Selecteer vervolgens **Store** > **de App Store-koppeling in de Store**.

    ![App aan de Windows Store koppelen](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Selecteer **volgende**in de wizard. Meld u vervolgens aan met uw Microsoft-account. Typ in **een nieuwe app-naam reserveren**een naam voor uw app en selecteer vervolgens **reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de naam van de nieuwe app. Selecteer **volgende**en selecteer vervolgens **koppelen**. Met dit proces worden de vereiste registratie gegevens voor Windows Store toegevoegd aan het toepassings manifest.
4. Herhaal stap 1 en 3 voor het Windows Phone Store-app-project met behulp van dezelfde registratie die u eerder hebt gemaakt voor de Windows Store-app.  
5. Ga naar het [Windows-ontwikkelaars centrum](https://dev.windows.com/en-us/overview)en meld u vervolgens aan met uw Microsoft-account. Selecteer in **mijn apps**de nieuwe app-registratie. Vouw vervolgens **Services** > **Push meldingen**uit.
6. Selecteer op de pagina **Push meldingen** onder **Windows Push Notification Services (WNS) en Microsoft Azure Mobile apps**de optie **Live Services-site**.  Noteer de waarden van de **pakket-sid** en de *huidige* waarde in het **toepassings geheim**. 

    ![App-instelling in het ontwikkelaars centrum](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Het toepassingsgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden niet met iemand of distribueer ze met uw app.
   >
   >
