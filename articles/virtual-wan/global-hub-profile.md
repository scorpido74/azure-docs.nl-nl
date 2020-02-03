---
title: Azure Virtual WAN Global of hub-based VPN-profielen downloaden | Microsoft Docs
description: Meer informatie over de geautomatiseerde schaal bare vertakking-to-Branch connectiviteit van Virtual WAN, beschik bare regio's en partners.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965227"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Een globaal of op de hub gebaseerd profiel voor VPN-clients van de gebruiker downloaden

Azure Virtual WAN biedt twee soorten connectiviteit voor externe gebruikers: globaal en op basis van de hub. Gebruik de volgende secties voor meer informatie over en het downloaden van een profiel.

## <a name="global-profile"></a>Algemeen profiel

Het profiel verwijst naar een load balancer dat alle VPN-hubs van de actieve gebruiker bevat. De gebruiker wordt omgeleid naar de hub die zich het dichtst bij de geografische locatie van de gebruiker bevindt. Dit type verbinding is handig wanneer gebruikers regel matig naar verschillende locaties reizen. Het **globale** profiel downloaden:

1. Navigeer naar uw virtuele WAN.
2. Klik op **VPN-gebruikers configuratie**.
3. Markeer de configuratie waarvoor u het profiel wilt downloaden.
4. Klik op **VPN-gebruikers profiel voor virtuele WAN downloaden**.

   ![Algemeen profiel](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Op de hub gebaseerd profiel

Het profiel verwijst naar één hub. De gebruiker kan alleen verbinding maken met de betreffende hub met behulp van dit profiel. Het op de **hub gebaseerde** profiel downloaden:

1. Navigeer naar uw virtuele WAN.
2. Klik op **hub** op de pagina overzicht.

    ![Hub-profiel 1](./media/global-hub-profile/hub1.png)
3. Klik op **gebruikers VPN (punt naar site)** .
4. Klik op **VPN-gebruikers profiel voor virtuele hub downloaden**.

   ![Hub-profiel 2](./media/global-hub-profile/hub2.png)
5. Controleer **EAPTLS**.
6. Klik op **profiel genereren en downloaden**.

   ![Hub-profiel 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
