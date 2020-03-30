---
title: Ondersteuning inschakelen voor Avere vFXT - Azure
description: Ondersteuninguploads van Avere vFXT voor Azure inschakelen
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415374"
---
# <a name="enable-support-uploads"></a>Ondersteuningsuploads inschakelen

Avere vFXT voor Azure kan automatisch ondersteuningsgegevens over uw cluster uploaden. Met deze uploads kunnen ondersteuningsmedewerkers de best mogelijke klantenservice bieden.

## <a name="steps-to-enable-uploads"></a>Stappen om uploads in te schakelen

Volg deze stappen vanuit het Configuratiescherm van Avere om ondersteuning te activeren. (Lees [Access het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het openen van het configuratiescherm.)

1. Navigeer naar het tabblad **Instellingen** bovenaan.
1. Klik op de koppeling **Ondersteuning** aan de linkerkant en accepteer het privacybeleid.

   ![Schermafbeelding van het Configuratiescherm van Avere en het pop-upvenster met de knop Bevestigen om het privacybeleid te accepteren](media/avere-vfxt-privacy-policy.png)

1. Open op de pagina ondersteuningsconfiguratie de sectie **Klantgegevens** door op het driehoekje aan de linkerkant te klikken.
1. Klik op de knop **Uploadgegevens opnieuw valideren.**
1. Stel de ondersteuningsnaam van het cluster in **op unieke clusternaam**. Zorg ervoor dat deze naam uw cluster op unieke wijze identificeert om het personeel te ondersteunen.
1. Schakel de selectievakjes voor **statistiekenbewaking,** **algemene informatie uploaden**en **crashinformatie uploaden**in .
1. Klik **op Verzenden**.

   ![Schermafbeelding van de sectie voltooide pagina met klantgegevens van de pagina Ondersteuningsinstellingen](media/avere-vfxt-support-info.png)

1. Klik op het driehoekje links van **Secure Proactive Support (SPS)** om de sectie uit te vouwen.
1. Schakel het selectievakje in voor **SPS-koppeling inschakelen**.
1. Klik **op Verzenden**.

   ![Schermafbeelding van de voltooide sectie Secure Proactive Support op de pagina ondersteuningsinstellingen](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Als u een on-premises of bestaand cloudopslagsysteem aan het cluster wilt toevoegen, volgt u de instructies in [Opslag configureren.](avere-vfxt-add-storage.md)

Als u klaar bent om clients aan het cluster te koppelen, leest u [Het Avere vFXT-cluster monteren.](avere-vfxt-mount-clients.md)
