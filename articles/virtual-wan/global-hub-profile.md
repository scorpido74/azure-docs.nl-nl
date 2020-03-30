---
title: Azure Virtual WAN global of hub-based VPN-profielen downloaden | Microsoft Documenten
description: Meer informatie over virtuele WAN-geautomatiseerde schaalbare branch-to-branch-connectiviteit, beschikbare regio's en partners.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965227"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Een wereldwijd of hub-gebaseerd profiel downloaden voor VPN-clients van gebruikers

Azure Virtual WAN biedt twee soorten connectiviteit voor externe gebruikers: globaal en hubgebaseerd. Gebruik de volgende secties om meer te weten te komen over en een profiel te downloaden.

## <a name="global-profile"></a>Wereldwijd profiel

Het profiel wijst naar een load balancer die alle actieve User VPN-hubs bevat. De gebruiker wordt doorverwezen naar de hub die het dichtst bij de geografische locatie van de gebruiker ligt. Dit type connectiviteit is handig wanneer gebruikers vaak naar verschillende locaties reizen. Ga als lid van het globale profiel als lid van het **wereldprofiel:**

1. Navigeer naar uw virtuele WAN.
2. Klik **op Gebruikers-VPN-configuratie**.
3. Markeer de configuratie waarvoor u het profiel wilt downloaden.
4. Klik **op Virtueel WAN-gebruikersVPN-profiel downloaden**.

   ![Wereldwijd profiel](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profiel op basis van hub's

Het profiel wijst naar één hub. De gebruiker kan alleen verbinding maken met de specifieke hub met behulp van dit profiel. Ga als lid van het **hubprofiel:**

1. Navigeer naar uw virtuele WAN.
2. Klik **op Hub** op de pagina Overzicht.

    ![Hubprofiel 1](./media/global-hub-profile/hub1.png)
3. Klik **op GebruikersVPN (Punt naar site)**.
4. Klik **op Virtueel VPN-profiel van de hubgebruiker downloaden**.

   ![Hubprofiel 2](./media/global-hub-profile/hub2.png)
5. Controleer **EAPTLS**.
6. Klik **op Profiel genereren en downloaden**.

   ![Hubprofiel 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
