---
title: Werken met VM's en NSGs in Azure Bastion
description: In dit artikel wordt beschreven hoe u NSG-toegang integreren met Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087269"
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

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion wordt specifiek geïmplementeerd in het AzureBastionSubnet.

* **Ingress Verkeer:**

   * **Ingress Verkeer van openbaar internet:** Het Azure Bastion maakt een openbaar IP dat poort 443 moet inschakelen op het openbare IP-adres voor invallend verkeer. Poort 3389/22 hoeft NIET te worden geopend op het AzureBastionSubnet.
   * **Ingress Traffic from Azure Bastion control plane:** Schakel poort 443-binnenkomend poort 443 in vanuit **gatewaymanager-servicetag** in. Hierdoor kan het besturingsvlak, dat wil zeggen Gateway Manager, met Azure Bastion kunnen praten.

* **Uitgangsverkeer:**

   * **Egress Traffic to target VM's:** Azure Bastion bereikt de doel-VM's via privé-IP. De NSG's moeten het verkeer naar andere doel-VM-subnetten voor poort 3389 en 22 toestaan.
   * **Verkeer naar andere openbare eindpunten in Azure:** Azure Bastion moet verbinding kunnen maken met verschillende openbare eindpunten binnen Azure (bijvoorbeeld voor het opslaan van diagnostische logboeken en metinglogboeken). Azure Bastion heeft daarom uitgaande naar 443 naar **AzureCloud-servicetag** nodig.

* **Subnet DoelVM:** Dit is het subnet dat de doelvirtuele machine bevat waar u RDP/SSH aan wilt hebben.

   * **Ingress-verkeer vanuit Azure Bastion:** Azure Bastion bereikt de doel-VM via privé-IP. RDP/SSH-poorten (poorten respectievelijk 3389/22) moeten worden geopend aan de doel-VM-kant via private IP. Als aanbevolen praktijk u het IP-adresbereik azure Bastion-subnet in deze regel toevoegen, zodat alleen Bastion deze poorten kan openen op de doel-VM's in uw doel-VM-subnet.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>NsGs toepassen op AzureBastionSubnet

Als u een NSG maakt en toepast op ***AzureBastionSubnet,*** controleert u of u de volgende regels in uw NSG hebt toegevoegd. Als u deze regels niet toevoegt, mislukt de NSG-creatie/-update:

* **De verbinding met het vliegtuig regelen:** Binnenkomend op 443 van GatewayManager
* **Diagnostische logboekregistratie en anderen:** Uitgaand op 443 naar AzureCloud. Regionale tags binnen deze servicetag worden nog niet ondersteund.
* **DoelVM:** Uitgaand voor 3389 en 22 naar VirtualNetwork

Een NSG-regelvoorbeeld is beschikbaar voor referentie in deze [snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen voor](bastion-faq.md)meer informatie over Azure Bastion.
