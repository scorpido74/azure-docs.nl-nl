---
title: Werken met Vm's en Nsg's in azure Bastion | Microsoft Docs
description: In dit artikel wordt beschreven hoe u NSG Access integreert met Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: 4b64c420290bec687388690f1c901c2ae789a3c8
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707551"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Werken met NSG-toegang en Azure Bastion

Wanneer u met Azure Bastion werkt, kunt u netwerk beveiligings groepen (Nsg's) gebruiken. Zie [beveiligings groepen](../virtual-network/security-overview.md)voor meer informatie. 

![Architectuur](./media/bastion-nsg/nsg-architecture.png)

In dit diagram:

* De bastion-host wordt geïmplementeerd in het virtuele netwerk.
* De gebruiker maakt verbinding met de Azure Portal met behulp van een HTML5-browser.
* De gebruiker navigeert naar de virtuele machine van Azure naar RDP/SSH.
* Connect Integration-een RDP/SSH-sessie met één klik binnen de browser
* Op de Azure-VM is geen openbaar IP-adres vereist.

## <a name="nsg"></a>Netwerk beveiligings groepen

In deze sectie ziet u het netwerk verkeer tussen de gebruiker en Azure Bastion, en tot doel-Vm's in uw virtuele netwerk:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion wordt specifiek geïmplementeerd voor de AzureBastionSubnet.

* **Binnenkomend verkeer:**

   * **Binnenkomend verkeer via het open bare Internet:** De Azure-Bastion maakt een openbaar IP-adres waarvoor poort 443 is ingeschakeld op het open bare IP-adres voor binnenkomend verkeer. Poort 3389/22 hoeft niet te worden geopend op de AzureBastionSubnet.
   * **Binnenkomend verkeer vanuit het Azure Bastion-besturings vlak:** Schakel voor de connectiviteit van het besturings element het argument poort 443 binnenkomend van de **GatewayManager** -service in. Hiermee wordt het besturings vlak ingeschakeld, dat wil zeggen dat gateway beheer met Azure Bastion kan communiceren.

* **Uitgaand verkeer:**

   * Uitgaand **verkeer naar doel-vm's:** Azure Bastion bereikt de doel-Vm's via privé-IP. De Nsg's moet uitgaand verkeer naar andere doel-VM-subnetten toestaan voor poort 3389 en 22.
   * Uitgaand **verkeer naar andere open bare eind punten in Azure:** Azure Bastion moet verbinding kunnen maken met verschillende open bare eind punten in azure (bijvoorbeeld voor het opslaan van Diagnostische logboeken en logboeken voor meters). Daarom heeft Azure Bastion een uitgaand verkeer naar 443 naar **Cloud** service-tag nodig.

* **Doel-VM-subnet:** Dit is het subnet dat de virtuele doel machine bevat waarnaar u RDP/SSH wilt.

   * **Binnenkomend verkeer van Azure Bastion:** Azure Bastion is bereikbaar voor de doel-VM via een particulier IP-adres. RDP/SSH-poorten (poorten 3389/22 respectievelijk) moeten worden geopend op de doel-VM naast het particuliere IP-adres. Als best practice kunt u het IP-adres bereik van het Azure Bastion-subnet in deze regel toevoegen zodat alleen Bastion deze poorten op de doel-Vm's in het subnet van de doel-VM kunnen openen.

## <a name="apply"></a>Nsg's Toep assen op AzureBastionSubnet

Als u een NSG maakt en toepast op ***AzureBastionSubnet***, moet u ervoor zorgen dat u de volgende regels in uw NSG hebt toegevoegd. Als u deze regels niet toevoegt, mislukt het maken/bijwerken van de NSG:

* **Connectiviteit voor beheer vlak:** Inkomend op 443 van GatewayManager
* **Logboek registratie van diagnostische gegevens en anderen:** Uitgaand op 443 tot cloud. Regionale Tags binnen deze service code worden nog niet ondersteund.
* **Doel-VM:** Uitgaand voor 3389 en 22 tot VirtualNetwork

Een voor beeld van een NSG-regel is beschikbaar als referentie in deze Quick Start- [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](bastion-faq.md)voor meer informatie over Azure Bastion.
