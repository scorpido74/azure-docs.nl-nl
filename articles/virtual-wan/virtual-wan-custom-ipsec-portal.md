---
title: 'Aangepast IPsec-beleid voor Azure Virtual WAN configureren: Portal | Microsoft Docs'
description: Meer informatie over het configureren van een aangepast IPsec-beleid voor Azure Virtual WAN met behulp van de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 0ea4523d1558f6887e1aef344198026591dac617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752615"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Een aangepast IPsec-beleid voor virtuele WAN configureren met behulp van de portal

U kunt aangepaste IPsec-beleids regels configureren voor virtuele WAN-het Azure Portal. Aangepaste beleids regels zijn handig wanneer u wilt dat beide zijden (on-premises en Azure VPN-gateway) dezelfde instellingen gebruiken voor IKE fase 1 en IKE fase 2.

## <a name="working-with-custom-policies"></a>Werken met aangepaste beleids regels

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Een beleid configureren

1. **Zoek de virtuele hub**. Ga in een browser naar het [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) en meld u aan met uw Azure-account. Zoek de virtuele hub voor uw site.
2. **Selecteer de VPN-site**. Selecteer op de pagina hub de VPN-site waarvoor u een aangepast beleid wilt instellen.

   ![Selecteer](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Bewerk de VPN-verbinding**. Selecteer in het **context menu** **...** de optie **VPN-verbinding bewerken**.

   ![bewerken](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configureer de instellingen**. Configureer de instellingen op de pagina **VPN-verbinding bewerken** . Selecteer **Opslaan** om uw instellingen op te slaan.

   ![configureren en opslaan](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).