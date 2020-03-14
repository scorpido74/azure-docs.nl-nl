---
title: Een subnet van een virtueel netwerk van Azure toevoegen, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen, wijzigen of verwijderen van een subnet van een virtueel netwerk in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: de80094c3fd2df7d2f8b32d1e968e9bebea847a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245042"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Een subnet van een virtueel netwerk toevoegen, wijzigen of verwijderen

Meer informatie over het toevoegen, wijzigen of verwijderen van een subnet van een virtueel netwerk. Alle Azure-resources die zijn geïmplementeerd in een virtueel netwerk, worden geïmplementeerd in een subnet binnen een virtueel netwerk. Als u geen ervaring hebt met virtuele netwerken, kunt u meer informatie hierover vinden in het [overzicht van Virtual Network](virtual-networks-overview.md) of door een [zelf studie](quick-create-portal.md)te volt ooien. Als u een virtueel netwerk wilt maken, wijzigen of verwijderen, raadpleegt u [een virtueel netwerk beheren](manage-virtual-network.md).

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent u https://portal.azure.comen meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook `az login` uitvoeren om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="add-a-subnet"></a>Een subnet toevoegen

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waaraan u een subnet wilt toevoegen.
3. Selecteer onder **INSTELLINGEN** **Subnetten**.
4. Selecteer **+ subnet**.
5. Voer waarden in voor de volgende para meters:
   - **Naam**: de naam moet uniek zijn binnen het virtuele netwerk. Voor een maximale compatibiliteit met andere Azure-Services kunt u het beste een letter gebruiken als het eerste teken van de naam. Azure-toepassing gateway kan bijvoorbeeld niet worden geïmplementeerd in een subnet met een naam die begint met een getal.
   - **Adres bereik**: het bereik moet uniek zijn binnen de adres ruimte voor het virtuele netwerk. Het bereik mag niet overlappen met andere adresbereiken in het virtuele netwerk. De adres ruimte moet worden opgegeven met behulp van de CIDR-notatie (Classless Inter-Domain Routing). U kunt bijvoorbeeld in een virtueel netwerk met adres ruimte 10.0.0.0/16 een adres ruimte van het subnet van 10.0.0.0/24 definiëren. Het kleinste bereik dat u kunt opgeven, is/29, dat acht IP-adressen voor het subnet bevat. Azure reserveert het eerste en laatste adres in elk subnet voor protocol conformiteit. Er zijn drie extra adressen gereserveerd voor Azure-service gebruik. Als gevolg hiervan resulteert het definiëren van een subnet met een/29-adres bereik in drie bruikbare IP-adressen in het subnet. Als u van plan bent om een virtueel netwerk te verbinden met een VPN-gateway, moet u een gateway-subnet maken. Meer informatie over [specifieke aandachtspunten voor de adres bereik voor gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). U kunt het adres bereik wijzigen nadat het subnet is toegevoegd, onder specifieke voor waarden. Zie [subnet Settings wijzigen](#change-subnet-settings)voor meer informatie over het wijzigen van het adres bereik van een subnet.
   - **Netwerk beveiligings groep**: u kunt nul of één bestaande netwerk beveiligings groep koppelen aan een subnet voor het filteren van binnenkomend en uitgaand netwerk verkeer voor het subnet. De netwerk beveiligings groep moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [netwerk beveiligings groepen](security-overview.md) en [het maken van een netwerk beveiligings groep](tutorial-filter-network-traffic.md).
   - **Route tabel**: u kunt nul of één bestaande route tabel koppelen aan een subnet om de route ring van netwerk verkeer naar andere netwerken te beheren. De route tabel moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [Azure-route ring](virtual-networks-udr-overview.md) en [het maken van een route tabel](tutorial-create-route-table-portal.md)
   - **Service-eind punten:** Er kunnen voor een subnet nul of meerdere service-eind punten worden ingeschakeld. Als u een service-eind punt voor een service wilt inschakelen, selecteert u de service of services waarvoor u service-eind punten in de lijst met **Services** wilt inschakelen. De locatie wordt automatisch voor een eind punt geconfigureerd. Standaard worden service-eind punten geconfigureerd voor de regio van het virtuele netwerk. Voor Azure Storage voor de ondersteuning van regionale failover-scenario's, worden eind punten automatisch geconfigureerd voor [gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   
       Als u een service-eind punt wilt verwijderen, moet u de service waarvoor u het service-eind punt wilt verwijderen, uitschakelen. Zie [overzicht van virtuele netwerk service-eind punten](virtual-network-service-endpoints-overview.md)voor meer informatie over service-eind punten en de services waarvoor ze kunnen worden ingeschakeld. Zodra u een service-eind punt voor een service inschakelt, moet u ook netwerk toegang inschakelen voor het subnet voor een resource die is gemaakt met de service. Als u bijvoorbeeld het service-eind punt voor *micro soft. Storage*inschakelt, moet u ook netwerk toegang inschakelen voor alle Azure Storage accounts waaraan u netwerk toegang wilt verlenen. Voor meer informatie over het inschakelen van netwerk toegang tot subnetten waarvoor een service-eind punt is ingeschakeld, raadpleegt u de documentatie voor de afzonderlijke service waarvoor u het service-eind punt hebt ingeschakeld.

     Als u wilt controleren of een service-eind punt is ingeschakeld voor een subnet, bekijkt u de [efficiënte routes](diagnose-network-routing-problem.md) voor elke netwerk interface in het subnet. Wanneer een eind punt is geconfigureerd, ziet u een *standaard* route met de adres voorvoegsels van de service en een NextHopType van **VirtualNetworkServiceEndpoint**. Zie [route ring-overzicht](virtual-networks-udr-overview.md)voor meer informatie over route ring.
   - **Subnet-overdracht:** Er kunnen voor een subnet nul naar meerdere delegaties worden ingeschakeld. Subnet delegering geeft expliciete machtigingen voor de service om servicespecifieke bronnen in het subnet te maken met behulp van een unieke id bij het implementeren van de service. Als u een service wilt delegeren, selecteert u de service die u wilt delegeren in de lijst met **Services** .

6. Selecteer **OK**om het subnet toe te voegen aan het virtuele netwerk dat u hebt geselecteerd.

**Opdrachten**

- Azure CLI: [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet)
- Power shell: [add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Subnet-instellingen wijzigen

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk dat het subnet bevat waarvan u de instellingen wilt wijzigen.
3. Selecteer onder **INSTELLINGEN** **Subnetten**.
4. Selecteer in de lijst met subnetten het subnet waarvoor u de instellingen wilt wijzigen. U kunt de volgende instellingen wijzigen:

    - **Adres bereik:** Als er geen resources binnen het subnet zijn geïmplementeerd, kunt u het adres bereik wijzigen. Als er resources in het subnet aanwezig zijn, moet u de resources naar een ander subnet verplaatsen of eerst verwijderen uit het subnet. De stappen die u moet nemen om een resource te verplaatsen of te verwijderen, variëren afhankelijk van de resource. Raadpleeg de documentatie voor elk resource type dat u wilt verplaatsen of verwijderen voor meer informatie over het verplaatsen of verwijderen van resources in subnetten. Zie de beperkingen voor het **adres bereik** in stap 5 van [een subnet toevoegen](#add-a-subnet).
    - **Gebruikers**: u kunt de toegang tot het subnet beheren door gebruik te maken van ingebouwde rollen of uw eigen aangepaste rollen. Zie [roltoewijzing gebruiken om de toegang tot uw Azure-resources te beheren](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)voor meer informatie over het toewijzen van rollen en gebruikers om toegang te krijgen tot het subnet.
    - **Netwerk beveiligings groep** en **route tabel**: Zie stap 5 van [een subnet toevoegen](#add-a-subnet).
    - **Service-eind punten**: Zie Service-eind punten in stap 5 van [een subnet toevoegen](#add-a-subnet). Wanneer u een service-eind punt voor een bestaand subnet inschakelt, moet u ervoor zorgen dat er geen kritieke taken worden uitgevoerd op een wille keurige bron in het subnet. Service-eind punten scha kelen tussen de routes op elke netwerk interface in het subnet van het gebruik van de standaard route met het adres voorvoegsel *0.0.0.0/0* en het type volgende hop van *Internet*, om een nieuwe route te gebruiken met de adres prefixen van de service en het volgende hop-type *VirtualNetworkServiceEndpoint*. Tijdens de switch kunnen alle open TCP-verbindingen worden beëindigd. Het service-eind punt is niet ingeschakeld totdat verkeer naar de service voor alle netwerk interfaces wordt bijgewerkt met de nieuwe route. Zie [route ring-overzicht](virtual-networks-udr-overview.md)voor meer informatie over route ring.
    - **Subnet-overdracht:** Zie Service-eind punten in stap 5 van [een subnet toevoegen](#add-a-subnet). Subnet delegering kan worden gewijzigd in nul of er zijn meerdere overdrachten ingeschakeld. Als een resource voor een service al in het subnet is geïmplementeerd, kan er geen subnet-overdracht worden toegevoegd of verwijderd totdat alle resources voor de service zijn verwijderd. Als u wilt delegeren voor een andere service, selecteert u de service die u wilt delegeren in de lijst met **Services** .
5. Selecteer **Opslaan**.

**Opdrachten**

- Azure CLI: [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- Power shell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Een subnet verwijderen

U kunt een subnet alleen verwijderen als er geen resources in het subnet aanwezig zijn. Als er resources in het subnet aanwezig zijn, moet u de resources in het subnet verwijderen voordat u het subnet kunt verwijderen. De stappen die u moet nemen om een resource te verwijderen, variëren afhankelijk van de resource. Raadpleeg de documentatie voor elk bron type dat u wilt verwijderen voor meer informatie over het verwijderen van bronnen in subnetten.

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk dat het subnet bevat dat u wilt verwijderen.
3. Selecteer onder **INSTELLINGEN** **Subnetten**.
4. Selecteer in de lijst met subnetten **...** , aan de rechter kant, voor het subnet dat u wilt verwijderen
5. Selecteer **verwijderen**en selecteer vervolgens **Ja**.

**Opdrachten**

- Azure CLI: [AZ Network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- Power shell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Machtigingen

Voor het uitvoeren van taken op subnetten moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

|Bewerking                                                                   |   Naam                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Een subnet van een virtueel netwerk lezen              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Een subnet van een virtueel netwerk maken of bijwerken  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Subnet van een virtueel netwerk verwijderen            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Lid worden van een virtueel netwerk                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Een service-eind punt inschakelen voor een subnet     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   De virtuele machines in een subnet ophalen       |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk en subnetten maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en Toep assen voor virtuele netwerken
