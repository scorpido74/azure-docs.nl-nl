---
title: Een lab maken met een gedeelde resource | Azure Lab Services
description: Meer informatie over het maken van een Lab waarvoor een resource moet worden gedeeld tussen de studenten.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 06/05/2020
ms.author: enewman
ms.openlocfilehash: c322f650233919bec6192308de99d0f96e4d8cee
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637100"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Een lab maken met een gedeelde bron in Azure Lab Services

Wanneer u een leslokaal Lab maakt, zijn er mogelijk enkele resources die moeten worden gedeeld tussen alle studenten in een lab.  U hebt bijvoorbeeld een licentie server of SQL Server voor een database klasse.  In dit artikel worden de stappen beschreven voor het inschakelen van de gedeelde resource voor een lab.  Ook wordt uitgelegd hoe u de toegang tot die gedeelde bron kunt beperken.

## <a name="architecture"></a>Architectuur

Zoals u in het onderstaande diagram kunt zien, hebben we een Lab-account met een lab.  Het lab-account heeft de instellingen voor de vnet-peering, zodat het virtuele netwerk van de test omgeving is verbonden met het netwerk van de gedeelde bron.  In het onderstaande diagram bevinden zich twee virtuele netwerken met niet-overlappende IP-adresbereiken.  Deze IP-bereiken zijn alleen voor beelden van reeksen.  Houd er ook rekening mee dat het virtuele netwerk van de gedeelde bron zich in hetzelfde abonnement bevindt als het lab-account.

![Test services met een gedeelde bron architectuur](../media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Gedeelde bron instellen

Het virtuele netwerk voor de gedeelde bron moet worden gemaakt voordat het lab wordt gemaakt.  Zie [een virtueel netwerk maken](../../virtual-network/quick-create-portal.md)voor meer informatie over het maken van een virtueel netwerk.  Het is belang rijk om de bereiken van virtuele netwerken te plannen, zodat ze niet overlappen met het IP-adres van de test machines.  Zie het artikel [virtuele netwerken plannen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over het plannen van uw netwerk. In ons voor beeld bevindt de gedeelde resource zich in een virtueel netwerk met het bereik 10.2.0.0/16.  Als u dit nog niet hebt gedaan, [maakt u een subnet](../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) voor de gedeelde bron.  In het voor beeld gebruiken we het 10.2.0.0/24-bereik, maar uw bereik is afhankelijk van de behoeften van uw netwerk.

De gedeelde resource kan worden uitgevoerd op een virtuele machine of in een door Azure aangelegde service. De gedeelde bron moet beschikbaar zijn via een privé-IP-adres.  Door de gedeelde bron alleen beschikbaar te maken via een particulier IP-adres, beperkt u de toegang tot die gedeelde bron.

Het diagram toont ook een netwerk beveiligings groep (NSG) die kan worden gebruikt om verkeer te beperken dat afkomstig is van de student-VM.  U kunt bijvoorbeeld een beveiligings regel schrijven die toeneemt dat verkeer van de IP-adressen van de student-VM alleen toegang heeft tot één gedeelde bron en niets anders.  Zie [netwerk beveiligings groep beheren](../../virtual-network/manage-network-security-group.md#work-with-security-rules)voor meer informatie over het instellen van beveiligings regels. Als u de toegang tot een gedeelde bron wilt beperken tot een specifiek Lab, haalt u het IP-adres voor het lab op uit de [Lab-instellingen van het lab-account](manage-labs.md#view-labs-in-a-lab-account) en stelt u een regel voor binnenkomende verbindingen in om alleen toegang vanaf dat IP-adres toe te staan.  Vergeet niet om poorten 49152 tot 65535 voor dat IP-adres toe te staan.  U kunt desgewenst het privé-IP-adres van de Vm's van de student vinden met behulp van de [pagina groep van virtuele machines](how-to-set-virtual-machine-passwords.md).

Als uw gedeelde resource een Azure virtual machine is die de benodigde software uitvoert, moet u mogelijk de standaard firewall regels voor de virtuele machine wijzigen.

## <a name="lab-account"></a>Lab-account

Als u een gedeelde bron wilt gebruiken, moet het lab-account worden ingesteld voor het gebruik van een gekoppeld [virtueel netwerk](how-to-connect-peer-virtual-network.md).  In dit geval gaan we peering naar het virtuele netwerk dat de gedeelde bron bevat.

>[!WARNING]
>Het lab voor uw klasse moet worden gemaakt **nadat** het lab-account is gekoppeld aan het virtuele netwerk van de gedeelde bron.  
Sjabloon machine

Zodra uw Lab-account is gekoppeld aan het virtuele netwerk, moet de sjabloon machine nu toegang hebben tot de gedeelde bron.  Mogelijk moet u de firewall regels bijwerken, afhankelijk van de gedeelde bron waartoe toegang wordt verkregen.
