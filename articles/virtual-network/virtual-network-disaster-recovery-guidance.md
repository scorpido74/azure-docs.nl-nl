---
title: Bedrijfscontinuïteit van virtuele netwerken | Microsoft Documenten
description: Meer informatie over wat u moet doen in het geval van een Azure-serviceonderbreking die van invloed is op Azure Virtual Networks.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876176"
---
# <a name="virtual-network--business-continuity"></a>Virtueel netwerk – Bedrijfscontinuïteit

## <a name="overview"></a>Overzicht
Een Virtual Network (VNet) is een logische weergave van uw netwerk in de cloud. Hiermee u uw eigen IP-adresruimte definiëren en het netwerk segmenteren in subnetten. VNets fungeert als een vertrouwensgrens om uw rekenbronnen te hosten, zoals Azure Virtual Machines en Cloud Services (web-/worker-rollen). Een VNet maakt directe privé-IP-communicatie mogelijk tussen de bronnen die erin worden gehost. U een virtueel netwerk koppelen aan een on-premises netwerk via een VPN-gateway of ExpressRoute.

Een VNet wordt gemaakt binnen het bereik van een regio. U *kunt* VNets maken met dezelfde adresruimte in twee verschillende regio's (bijvoorbeeld US East en US West), maar omdat ze dezelfde adresruimte hebben, u ze niet met elkaar verbinden. 

## <a name="business-continuity"></a>Bedrijfscontinuïteit

Er kunnen verschillende manieren zijn waarop uw toepassing kan worden verstoord. Een regio kan volledig worden afgesneden als gevolg van een natuurramp, of een gedeeltelijke ramp, als gevolg van een storing van meerdere apparaten of diensten. De impact op de VNet-service is in elk van deze situaties verschillend.

**V: Wat moet ik doen als er een storing optreedt voor een hele regio? Bijvoorbeeld, als een regio volledig is afgesneden als gevolg van een natuurramp? Wat gebeurt er met de virtuele netwerken gehost in de regio?**

A: Het virtuele netwerk en de bronnen in de getroffen regio blijven ontoegankelijk tijdens de onderbreking van de service.

![Eenvoudig virtueel netwerkdiagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**V: Wat kan ik doen om hetzelfde virtuele netwerk opnieuw te maken in een andere regio?**

A: Virtuele netwerken zijn vrij lichtgewicht bronnen. U Azure API's aanroepen om een VNet te maken met dezelfde adresruimte in een andere regio. Als u dezelfde omgeving wilt opnieuw maken als in het getroffen gebied aanwezig was, doet u API-aanroepen om de web- en werknemersrollen van Cloud Services en de virtuele machines die u had opnieuw te implementeren. Als u on-premises connectiviteit hebt, zoals in een hybride implementatie, moet u een nieuwe VPN-gateway implementeren en verbinding maken met uw on-premises netwerk.

Zie Een virtueel netwerk maken als u een virtueel netwerk wilt [maken.](manage-virtual-network.md#create-a-virtual-network)

**V: Kan een replica van een VNet in een bepaalde regio van tevoren opnieuw worden gemaakt in een andere regio?**

A: Ja, u van tevoren twee VNets maken met dezelfde privé-IP-adresruimte en -bronnen in twee verschillende regio's. Als u internetservices host in het VNet, u Traffic Manager instellen voor georouteverkeer naar de regio die actief is. U echter geen twee VNets met dezelfde adresruimte aansluiten op uw on-premises netwerk, omdat dit problemen met de routering zou veroorzaken. Op het moment van een ramp en verlies van een VNet in de ene regio, u de andere VNet in de beschikbare regio aansluiten, met de overeenkomende adresruimte op uw on-premises netwerk.

Zie Een virtueel netwerk maken als u een virtueel netwerk wilt [maken.](manage-virtual-network.md#create-a-virtual-network)

