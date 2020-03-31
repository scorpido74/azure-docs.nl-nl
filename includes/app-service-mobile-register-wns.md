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
1. Klik in Visual Studio Solution Explorer met de rechtermuisknop op het Windows Store-app-project. Selecteer vervolgens App Voor **medewerker opslaan** > **in de Store met de Store**.

    ![App koppelen aan Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. **Selecteer**volgende in de wizard . Meld u vervolgens aan met uw Microsoft-account. Typ in **Een nieuwe app-naam reserveren**een naam voor uw app en selecteer Vervolgens **Reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de nieuwe app-naam. Selecteer **Volgende**en selecteer **Vervolgens Associate**. Met dit proces worden de vereiste Windows Store-registratiegegevens toegevoegd aan het toepassingsmanifest.
4. Herhaal stap 1 en 3 voor het Windows Phone Store-app-project met dezelfde registratie die u eerder voor de Windows Store-app hebt gemaakt.  
5. Ga naar het [Windows Dev Center](https://dev.windows.com/en-us/overview)en meld u aan met uw Microsoft-account. Selecteer in **Mijn apps**de nieuwe app-registratie. Vouw vervolgens**Pushmeldingen** **services** > uit .
6. Selecteer op de pagina **Pushmeldingen** onder **Windows Push Notification Services (WNS) en Microsoft Azure Mobile Apps**de optie Live **Services-site**.  Noteer de waarden van het **pakket SID** en de *huidige* waarde in **Application Secret**. 

    ![App-instelling in het ontwikkelaarscentrum](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Het toepassingsgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden met niemand of distribueer ze niet met uw app.
   >
   >
