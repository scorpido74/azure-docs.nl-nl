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
1. Open in Xamarin.Studio **Info.plist**en werk de **bundel-id** bij met de bundel-id die u eerder hebt gemaakt met uw nieuwe app-id.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Scroll omlaag naar **achtergrondmodi**. Schakel het vak **Achtergrondmodi inschakelen** in en het vak **Externe meldingen** in.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dubbelklik op uw project in het oplossingspaneel om Projectopties te **openen.**
4. Kies **onder Bouwen**de optie **iOS Bundle Signing**en selecteer het bijbehorende identiteits- en inrichtingsprofiel dat u zojuist voor dit project hebt ingesteld.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Dit zorgt ervoor dat het project het nieuwe profiel gebruikt voor het ondertekenen van code. Zie [Xamarin Device Provisioning]voor de officiële documentatie voor het inrichten van xamarin-apparaten.

#### <a name="configure-the-ios-project-in-visual-studio"></a>Het iOS-project configureren in Visual Studio
1. Klik in Visual Studio met de rechtermuisknop op het project en klik vervolgens op **Eigenschappen**.
2. Klik op de eigenschappenpagina's op het tabblad **iOS-toepassing** en werk de **id** bij met de id die u eerder hebt gemaakt.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Selecteer op het tabblad **IOS Bundle Signing** het bijbehorende identiteits- en inrichtingsprofiel dat u zojuist voor dit project hebt ingesteld.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Dit zorgt ervoor dat het project het nieuwe profiel gebruikt voor het ondertekenen van code. Zie [Xamarin Device Provisioning]voor de officiële documentatie voor het inrichten van xamarin-apparaten.
4. Dubbelklik op Info.plist om deze te openen en schakel **vervolgens Extern bureaublad-meldingen** in onder **Achtergrondmodi**.

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
