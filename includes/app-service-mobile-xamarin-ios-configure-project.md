---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176535"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Het iOS-project configureren in Xamarin Studio
1. Open in Xamarin. Studio **info. plist**en werk de bundel- **id** bij met de bundel-id die u eerder hebt gemaakt met uw nieuwe app-id.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Schuif omlaag naar de **modus achtergrond**. Schakel het selectie vakje **achtergrond modi inschakelen** en het vak **externe meldingen** in.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dubbel klik in het deel venster oplossing op het project om **project opties**te openen.
4. Kies onder **Build**de optie **ondertekenen van Ios bundel**en selecteer het bijbehorende identiteits-en inrichtings profiel dat u net hebt ingesteld voor dit project.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Dit zorgt ervoor dat het project het nieuwe profiel gebruikt voor het ondertekenen van programma code. Zie [Xamarin Device Provisioning](Engelstalig) voor de officiële documentatie voor Xamarin Device Provisioning.

#### <a name="configure-the-ios-project-in-visual-studio"></a>Het iOS-project in Visual Studio configureren
1. Klik in Visual Studio met de rechter muisknop op het project en klik vervolgens op **Eigenschappen**.
2. Klik op de pagina eigenschappen op het tabblad **IOS-toepassing** en werk de **id** bij met de id die u eerder hebt gemaakt.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Selecteer op het tabblad **ondertekenen van Ios-bundel** het bijbehorende identiteits-en inrichtings profiel dat u net hebt ingesteld voor dit project.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Dit zorgt ervoor dat het project het nieuwe profiel gebruikt voor het ondertekenen van programma code. Zie [Xamarin Device Provisioning](Engelstalig) voor de officiële documentatie voor Xamarin Device Provisioning.
4. Dubbel klik op info. plist om het te openen en schakel vervolgens **RemoteNotifications** in op de **achtergrond modus**.

[Xamarin apparaat inrichten]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
