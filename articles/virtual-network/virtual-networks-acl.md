---
title: Wat is een Azure Network Access Control List?
description: Meer informatie over toegangs beheer lijsten in azure
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 5e410dc4e7f47223430497825dc2a26e41c6af67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979554"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Wat is een toegangs beheer lijst voor het eind punt?

> [!IMPORTANT]
> Azure heeft twee verschillende [implementatie modellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van en werken met resources: Resource Manager en klassiek. Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan de meeste nieuwe implementaties het Resource Manager-implementatie model te gebruiken. 

Een toegangs beheer lijst (ACL) voor eind punten is een beveiligings uitbreiding die beschikbaar is voor uw Azure-implementatie. Een ACL biedt de mogelijkheid om selectief verkeer toe te staan of te weigeren voor een eind punt van een virtuele machine. Deze functie voor pakket filtering biedt een extra beveiligingslaag. U kunt alleen netwerk-Acl's opgeven voor eind punten. U kunt geen ACL opgeven voor een virtueel netwerk of een specifiek subnet dat zich in een virtueel netwerk bevindt. Het is raadzaam om waar mogelijk netwerk beveiligings groepen (Nsg's) in plaats van Acl's te gebruiken. Wanneer u Nsg's gebruikt, wordt de toegangs beheer lijst van het eind punt vervangen en wordt deze niet meer afgedwongen. Zie [overzicht van netwerk beveiligings groepen](security-overview.md) voor meer informatie over nsg's

U kunt Acl's configureren met Power shell of de Azure Portal. Zie [toegangscontrole lijsten voor eind punten beheren met Power shell](virtual-networks-acl-powershell.md)voor meer informatie over het configureren van een netwerk toegangs beheer lijst met behulp van Power shell. Zie [eind punten instellen voor een virtuele machine](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor het configureren van een netwerk toegangs beheer lijst met behulp van de Azure Portal.

Met behulp van netwerk-Acl's kunt u het volgende doen:

* Selectief binnenkomend verkeer toestaan of weigeren op basis van een IPv4-adres bereik van het externe subnet naar een invoer eindpunt van de virtuele machine.
* IP-adressen op zwarte lijst
* Meerdere regels per eind punt van de virtuele machine maken
* Regel volgorde gebruiken om ervoor te zorgen dat de juiste set regels wordt toegepast op een bepaald eind punt van de virtuele machine (laagste naar hoogste)
* Geef een ACL op voor een specifiek IPv4-adres van het externe subnet.

Zie het artikel over [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor ACL-limieten.

## <a name="how-acls-work"></a>Hoe Acl's werken
Een ACL is een object dat een lijst met regels bevat. Wanneer u een ACL maakt en toepast op een eind punt van een virtuele machine, worden pakket filtering uitgevoerd op het knoop punt host van uw VM. Dit betekent dat het verkeer van externe IP-adressen wordt gefilterd door het host-knoop punt voor overeenkomende ACL-regels in plaats van op uw virtuele machine. Zo voor komt u dat uw virtuele machine de kost bare CPU-cycli op pakket filtering kan uitgeven.

Wanneer een virtuele machine wordt gemaakt, wordt er een standaard-ACL geplaatst om al het binnenkomende verkeer te blok keren. Als er echter een eind punt is gemaakt voor (poort 3389), wordt de standaard-ACL zodanig gewijzigd dat alle binnenkomend verkeer voor dat eind punt wordt toegestaan. Binnenkomend verkeer van elk extern subnet is dan toegestaan voor dat eind punt en er is geen firewall inrichting vereist. Alle andere poorten worden voor inkomend verkeer geblokkeerd, tenzij er eind punten voor die poorten worden gemaakt. Uitgaand verkeer is standaard toegestaan.

**Voor beeld van standaard-ACL-tabel**

| **Budgetoverboekingsregel #** | **Extern subnet** | **Endpoint** | **Toestaan/weigeren** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Toestaan |

## <a name="permit-and-deny"></a>Toestaan en weigeren
U kunt het netwerk verkeer voor een invoer eindpunt van een virtuele machine selectief toestaan of weigeren door regels te maken waarmee "toestaan" of "weigeren" worden opgegeven. Het is belang rijk te weten dat wanneer er een eind punt wordt gemaakt, al het verkeer is toegestaan voor het eind punt. Daarom is het belang rijk om te begrijpen hoe regels voor toestaan/weigeren te maken en deze in de juiste volg orde te plaatsen als u gedetailleerde controle over het netwerk verkeer wilt toestaan dat u toestaat om het eind punt van de virtuele machine te bereiken.

Punten om rekening mee te houden:

1. **Geen ACL-** Wanneer een eind punt wordt gemaakt, worden standaard alle voor het eind punt toegestaan.
2. **Toestaan-** Wanneer u een of meer bereik ' toestaan ' toevoegt, worden standaard alle andere bereiken geweigerd. Alleen pakketten van het toegestane IP-bereik kunnen communiceren met het eind punt van de virtuele machine.
3. **Weigeren-** Wanneer u een of meer bereiken voor weigeren toevoegt, wordt standaard alle andere bereiken van verkeer.
4. **Combi natie van toestaan en weigeren-** U kunt een combi natie van "toestaan" en "weigeren" gebruiken als u een specifiek IP-adres bereik wilt reserveren om te worden toegestaan of geweigerd.

## <a name="rules-and-rule-precedence"></a>Prioriteit van regels en regel
Netwerk-Acl's kunnen worden ingesteld voor specifieke eind punten van virtuele machines. U kunt bijvoorbeeld een netwerk toegangs beheer lijst opgeven voor een RDP-eind punt dat is gemaakt op een virtuele machine, waardoor de toegang voor bepaalde IP-adressen wordt vergrendeld. De volgende tabel toont een manier om toegang te verlenen tot open bare virtuele Ip's (Vip's) van een bepaald bereik om toegang voor RDP toe te staan. Alle andere externe IP-adressen worden geweigerd. We volgen een van de *laagste prioriteits* regels.

### <a name="multiple-rules"></a>Meerdere regels
Als u in het onderstaande voor beeld alleen toegang tot het RDP-eind punt wilt toestaan vanaf twee open bare IPv4-adresbereiken (65.0.0.0/8 en 159.0.0.0/8), kunt u dit bereiken door twee regels voor *toestaan* op te geven. Aangezien RDP standaard is gemaakt voor een virtuele machine, wilt u mogelijk de toegang tot de RDP-poort vergren delen op basis van een extern subnet. In het onderstaande voor beeld ziet u een manier om toegang te verlenen tot open bare virtuele Ip's (Vip's) van een bepaald bereik om toegang voor RDP toe te staan. Alle andere externe IP-adressen worden geweigerd. Dit werkt omdat netwerk-Acl's kunnen worden ingesteld voor een specifiek eind punt van de virtuele machine en de toegang is standaard geweigerd.

**Voor beeld: meerdere regels**

| **Budgetoverboekingsregel #** | **Extern subnet** | **Endpoint** | **Toestaan/weigeren** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Toestaan |
| 200 |159.0.0.0/8 |3389 |Toestaan |

### <a name="rule-order"></a>Regel volgorde
Omdat er meerdere regels kunnen worden opgegeven voor een eind punt, moet er een manier zijn om regels te ordenen om te bepalen welke regel voor rang heeft. De regel volgorde bepaalt de prioriteit. Netwerk-Acl's volgen de *laagste prioriteits* regel volgorde. In het onderstaande voor beeld wordt het eind punt op poort 80 selectief alleen toegang verleend tot bepaalde IP-adresbereiken. Om dit te configureren, hebben we een regel voor weigeren (regel \# 100) voor adressen in de 175.1.0.1/24-ruimte. Er wordt vervolgens een tweede regel opgegeven met prioriteit 200 die toegang tot alle andere adressen onder 175.0.0.0/8 toestaat.

**Voor beeld: regel prioriteit**

| **Budgetoverboekingsregel #** | **Extern subnet** | **Endpoint** | **Toestaan/weigeren** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Weigeren |
| 200 |175.0.0.0/8 |80 |Toestaan |

## <a name="network-acls-and-load-balanced-sets"></a>Netwerk-Acl's en sets met gelijke taak verdeling
Netwerk-Acl's kunnen worden opgegeven op een set-eind punt met gelijke taak verdeling. Als er een ACL is opgegeven voor een set met gelijke taak verdeling, wordt de netwerk-ACL toegepast op alle virtuele machines in de set met gelijke taak verdeling. Als een set met gelijke taak verdeling bijvoorbeeld is gemaakt met ' poort 80 ' en de set met gelijke taak verdeling drie Vm's heeft, wordt de netwerk-ACL die is gemaakt op het eind punt ' poort 80 ' van de ene VM automatisch toegepast op de andere Vm's.

![Netwerk-Acl's en sets met gelijke taak verdeling](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Volgende stappen
[Toegangs beheer lijsten voor eind punten beheren met Power shell](virtual-networks-acl-powershell.md)

