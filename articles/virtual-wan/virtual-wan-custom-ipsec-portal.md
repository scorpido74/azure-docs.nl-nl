---
title: 'Aangepast IPsec-beleid voor Azure Virtual WAN configureren: Portal | Microsoft Docs'
description: Meer informatie over het configureren van een aangepast IPsec-beleid voor Azure Virtual WAN met behulp van de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515744"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Een aangepast IPsec-beleid voor virtuele WAN configureren met behulp van de portal

U kunt aangepaste IPsec-beleids regels configureren voor virtuele WAN-het Azure Portal. Aangepaste beleids regels zijn handig wanneer u wilt dat beide zijden (on-premises en Azure VPN-gateway) dezelfde instellingen gebruiken voor IKE fase 1 en IKE fase 2.

## <a name="working-with-custom-policies"></a>Werken met aangepaste beleids regels

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Een beleid configureren

1. **Zoek de virtuele hub**. Open een browser, ga naar [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) en meld u aan met uw Azure-account. Zoek de virtuele hub voor uw site.
2. **Selecteer de VPN-site**. Selecteer op de pagina hub de VPN-site waarvoor u een aangepast beleid wilt instellen.

   ![Selecteer](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Bewerk de VPN-verbinding**. Selecteer in het **context menu** **...** de optie **VPN-verbinding bewerken**.

   ![bewerken](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configureer de instellingen**. Configureer de instellingen op de pagina **VPN-verbinding bewerken** . Selecteer **Opslaan** om uw instellingen op te slaan.

   ![configureren en opslaan](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).