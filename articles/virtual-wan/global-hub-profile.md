---
title: Azure Virtual WAN global of hub-based VPN-profielen downloaden | Microsoft Documenten
description: Meer informatie over virtuele WAN-geautomatiseerde schaalbare branch-to-branch-connectiviteit, beschikbare regio's en partners.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733191"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Een wereldwijd of hub-gebaseerd profiel downloaden voor VPN-clients van gebruikers

Azure Virtual WAN biedt twee soorten connectiviteit voor externe gebruikers: globaal en hubgebaseerd. Gebruik de volgende secties om meer te weten te komen over en een profiel te downloaden. 

> [!IMPORTANT]
> RADIUS-verificatie ondersteunt alleen het hubprofiel.

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
