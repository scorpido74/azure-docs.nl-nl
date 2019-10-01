---
title: Werken met Vm's en Nsg's in azure Bastion | Microsoft Docs
description: In dit artikel wordt beschreven hoe u NSG Access integreert met Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694934"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Werken met NSG-toegang en Azure Bastion (preview-versie)

Wanneer u met Azure Bastion werkt, kunt u netwerk beveiligings groepen (Nsg's) gebruiken. Zie [beveiligings groepen](../virtual-network/security-overview.md)voor meer informatie. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architectuur](./media/bastion-nsg/nsg_architecture.png)

In dit diagram:

* De bastion-host wordt geïmplementeerd in het virtuele netwerk.
* De gebruiker maakt verbinding met de Azure Portal met behulp van een HTML5-browser.
* De gebruiker selecteert de virtuele machine waarmee verbinding moet worden gemaakt.
* Met één klik wordt de RDP/SSH-sessie geopend in de browser.
* Op de Azure-VM is geen openbaar IP-adres vereist.

## <a name="nsg"></a>Netwerk beveiligings groepen

* **AzureBastionSubnet:** Azure Bastion wordt geïmplementeerd in de specifieke AzureBastionSubnet.  
    * **Binnenkomend verkeer via het open bare Internet:** De Azure-Bastion maakt een openbaar IP-adres waarvoor poort 443 is ingeschakeld op het open bare IP-adres voor binnenkomend verkeer. Poort 3389/22 hoeft niet te worden geopend op de AzureBastionSubnet.
    * **Uitgaand verkeer naar doel-Vm's:** Azure Bastion bereikt de doel-Vm's via privé-IP. De Nsg's moet uitgaand verkeer naar andere doel-VM-subnetten toestaan.
* **Doel-VM-subnet:** Dit is het subnet dat de virtuele doel machine bevat waarnaar u RDP/SSH wilt.
    * **Binnenkomend verkeer van Azure Bastion:** Azure Bastion is bereikbaar voor de doel-VM via een particulier IP-adres. RDP/SSH-poorten (respectievelijk de poorten 3389 en 22) moeten worden geopend op de doel-VM naast het privé-IP-adres.

## <a name="apply"></a>Nsg's Toep assen op AzureBastionSubnet

Als u Nsg's toepast op de **AzureBastionSubnet**, staan de volgende twee service tags toe voor Azure Control-vlak en-infra structuur:

* **GatewayManager (alleen Resource Manager)** : Met deze tag worden de adresvoorvoegsels van de service Gatewaybeheer van Azure aangeduid. Als u GatewayManager voor de waarde opgeeft, wordt verkeer toegestaan of geweigerd voor GatewayManager.  Als u Nsg's maakt in de AzureBastionSubnet, schakelt u de label GatewayManager in voor inkomend verkeer.

* **Cloud (alleen Resource Manager)** : Met deze tag wordt de IP-adres ruimte voor Azure aangegeven, inclusief alle open bare IP-adressen van data centers. Als u Cloud voor de waarde opgeeft, wordt verkeer toegestaan of geweigerd voor open bare IP-adressen van Azure. Als u alleen toegang tot cloud in een bepaalde regio wilt toestaan, kunt u de regio opgeven. Als u bijvoorbeeld alleen toegang wilt toestaan tot Azure Cloud in de regio VS-Oost, kunt u Cloud. Oostus als een servicetag opgeven. Als u Nsg's maakt in de AzureBastionSubnet, schakelt u het label Cloud in voor uitgaand verkeer. Als u poort 443 voor binnenkomend verkeer naar Internet opent, moet u de Cloud-label niet inschakelen voor inkomend gegevens overdracht.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](bastion-faq.md) voor meer informatie over Azure Bastion
