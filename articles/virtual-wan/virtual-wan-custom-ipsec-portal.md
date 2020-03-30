---
title: 'Aangepaste IPsec-beleid configureren voor Azure Virtual WAN: Portal | Microsoft Documenten'
description: Meer informatie over het configureren van aangepast IPsec-beleid voor Azure Virtual WAN met behulp van de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515744"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Een aangepast IPsec-beleid configureren voor Virtual WAN met behulp van de portal

U aangepast IPsec-beleid voor Virtual WAN configureren in de Azure-portal. Aangepaste beleidsregels zijn handig wanneer u wilt dat beide zijden (on-premises en Azure VPN-gateway) dezelfde instellingen gebruiken voor IKE Phase 1 en IKE Phase 2.

## <a name="working-with-custom-policies"></a>Werken met aangepast beleid

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Een beleid configureren

1. **Zoek de virtuele hub.** Open een browser, ga naar [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) en meld u aan met uw Azure-account. Zoek de virtuele hub voor uw site.
2. **Selecteer de VPN-site**. Selecteer op de hubpagina de VPN-site waarvoor u een aangepast beleid wilt instellen.

   ![Selecteer](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Bewerk de VPN-verbinding**. Selecteer VPN-verbinding bewerken in het **menu Context** **...**... selecteer **VPN-verbinding bewerken**.

   ![bewerken](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **De instellingen configureren**. Configureer op de pagina **VPN-verbinding bewerken** de instellingen. Selecteer **Opslaan** om uw instellingen op te slaan.

   ![configureren en opslaan](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).