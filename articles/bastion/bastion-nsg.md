---
title: Werken met VM's en NSGs in Azure Bastion
description: In dit artikel wordt beschreven hoe u NSG-toegang integreren met Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732203"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Werken met NSG-toegang en Azure Bastion

Wanneer u met Azure Bastion werkt, u netwerkbeveiligingsgroepen (NSG's) gebruiken. Zie [Beveiligingsgroepen voor](../virtual-network/security-overview.md)meer informatie. 

![Architectuur](./media/bastion-nsg/nsg-architecture.png)

In dit diagram:

* De Bastion-host wordt geïmplementeerd in het virtuele netwerk.
* De gebruiker maakt verbinding met de Azure-portal met behulp van een HTML5-browser.
* De gebruiker navigeert naar de virtuele Azure-machine naar RDP/SSH.
* Integratie verbinden - RDP/SSH-sessie met één klik in de browser
* Er is geen openbaar IP vereist op de Azure VM.

## <a name="network-security-groups"></a><a name="nsg"></a>Netwerkbeveiligingsgroepen

In deze sectie ziet u het netwerkverkeer tussen de gebruiker en Azure Bastion en door naar vm's in uw virtuele netwerk:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion wordt specifiek geïmplementeerd op ***AzureBastionSubnet.***

* **Ingress Verkeer:**

   * **Ingress Verkeer van openbaar internet:** Het Azure Bastion maakt een openbaar IP dat poort 443 moet inschakelen op het openbare IP-adres voor invallend verkeer. Poort 3389/22 hoeft NIET te worden geopend op het AzureBastionSubnet.
   * **Ingress Traffic from Azure Bastion control plane:** Schakel poort 443-binnenkomend poort 443 in vanuit **gatewaymanager-servicetag** in. Hierdoor kan het besturingsvlak, dat wil zeggen Gateway Manager, met Azure Bastion kunnen praten.

* **Uitgangsverkeer:**

   * **Egress Traffic to target VM's:** Azure Bastion bereikt de doel-VM's via privé-IP. De NSG's moeten het verkeer naar andere doel-VM-subnetten voor poort 3389 en 22 toestaan.
   * **Verkeer naar andere openbare eindpunten in Azure:** Azure Bastion moet verbinding kunnen maken met verschillende openbare eindpunten binnen Azure (bijvoorbeeld voor het opslaan van diagnostische logboeken en metinglogboeken). Azure Bastion heeft daarom uitgaande naar 443 naar **AzureCloud-servicetag** nodig.

### <a name="target-vm-subnet"></a>Subnet doel-VM
Dit is het subnet dat de doelvirtuele machine bevat waar u RDP/SSH aan wilt hebben.

   * **Ingress-verkeer vanuit Azure Bastion:** Azure Bastion bereikt de doel-VM via privé-IP. RDP/SSH-poorten (poorten respectievelijk 3389/22) moeten worden geopend aan de doel-VM-kant via private IP. Als aanbevolen praktijk u het IP-adresbereik azure Bastion-subnet in deze regel toevoegen, zodat alleen Bastion deze poorten kan openen op de doel-VM's in uw doel-VM-subnet.


## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen voor](bastion-faq.md)meer informatie over Azure Bastion.
