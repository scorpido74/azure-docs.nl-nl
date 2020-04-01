---
title: Een virtueel Azure-netwerkpeering maken, wijzigen of verwijderen | Microsoft Documenten
description: Meer informatie over het maken, wijzigen of verwijderen van een virtueel netwerk peering.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 97acac61d0397a4e13fb64d39a6aba92e4de2afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123311"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Een virtueel netwerk peering maken, wijzigen of verwijderen

Meer informatie over het maken, wijzigen of verwijderen van een virtueel netwerk peering. Met virtuele netwerkpeering u virtuele netwerken in dezelfde regio en in verschillende regio's (ook bekend als Global VNet Peering) verbinden via het Azure-backbonenetwerk. Eenmaal peered, worden de virtuele netwerken nog steeds beheerd als afzonderlijke bronnen. Als u nieuw bent in virtueel netwerkpeeren, u er meer over te weten komen in het [overzicht van virtuele netwerkpeering](virtual-network-peering-overview.md) of door een zelfstudie in te [vullen.](tutorial-connect-virtual-networks-portal.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent en logt u in met een account dat de [benodigde machtigingen](#permissions) heeft om met peerings te werken.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet `Connect-AzAccount` u ook worden uitgevoerd met een account dat de [benodigde machtigingen](#permissions) heeft om met peering te werken, om een verbinding met Azure te maken.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Deze zelfstudie vereist de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd met een account dat de [benodigde machtigingen](#permissions) heeft om met peering te werken, om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="create-a-peering"></a>Een peering maken

Voordat u een peering maakt, moet u vertrouwd raken met de vereisten en beperkingen en [vereiste machtigingen.](#permissions)

1. Voer in het zoekvak boven aan de Azure-portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze. Selecteer **geen virtuele netwerken (klassiek)** als deze in de lijst worden weergegeven, omdat u geen peering maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatiemodel.
2. Selecteer het virtuele netwerk in de lijst waarvoor u een peering wilt maken.
3. Selecteer **Peerings**onder **INSTELLINGEN**.
4. Selecteer **+ Toevoegen**. 
5. <a name="add-peering"></a>Voer waarden in of selecteer voor de volgende instellingen:
    - **Naam:** De naam voor het peering moet uniek zijn binnen het virtuele netwerk.
    - **Virtueel netwerkimplementatiemodel:** Selecteer via welk implementatiemodel het virtuele netwerk waarmee u wilt peeren is geïmplementeerd.
    - **Ik ken mijn resource-ID:** Als u de toegang tot het virtuele netwerk waarmee u wilt peeren hebt gelezen, laat u dit selectievakje onaangevinkt. Als u geen leestoegang hebt tot het virtuele netwerk of abonnement waarmee u wilt peeren, schakelt u dit selectievakje in. Voer de volledige resource-id in van het virtuele netwerk waarmee u wilt peeren in het vak **Resource-id** dat is verschenen toen u het selectievakje aanvinkte. De bron-id die u invoert, moet zijn voor een virtueel netwerk dat in dezelfde of [ondersteunde verschillende](#requirements-and-constraints) [Azure-regio's](https://azure.microsoft.com/regions) als dit virtuele netwerk bestaat. De volledige resource-ID `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`lijkt op . U de bron-ID voor een virtueel netwerk krijgen door de eigenschappen voor een virtueel netwerk te bekijken. Zie [Virtuele netwerken beheren](manage-virtual-network.md#view-virtual-networks-and-settings)voor meer informatie over het weergeven van de eigenschappen voor een virtueel netwerk. Als het abonnement is gekoppeld aan een andere Azure Active Directory-tenant dan het abonnement met het virtuele netwerk waaruit u peering maakt, voegt u eerst een gebruiker van elke tenant toe als [gastgebruiker](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) in de tegenovergestelde tenant.
    - **Abonnement:** Selecteer het [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) van het virtuele netwerk waarmee u wilt peeren. Er worden een of meer abonnementen weergegeven, afhankelijk van het aantal abonnementen waartoe uw account toegang heeft. Als u het selectievakje **Resource-id** hebt ingeschakeld, is deze instelling niet beschikbaar.
    - **Virtueel netwerk:** Selecteer het virtuele netwerk waarmee u wilt peeren. U een virtueel netwerk selecteren dat is gemaakt via een Azure-implementatiemodel. Als u een virtueel netwerk in een andere regio wilt selecteren, moet u een virtueel netwerk selecteren in een [ondersteund gebied](#cross-region). U moet leestoegang hebben tot het virtuele netwerk om deze zichtbaar te maken in de lijst. Als een virtueel netwerk wordt vermeld, maar grijs wordt, kan dit zijn omdat de adresruimte voor het virtuele netwerk overlapt met de adresruimte voor dit virtuele netwerk. Als virtuele netwerkadresruimten elkaar overlappen, kunnen ze niet worden gekeken. Als u het selectievakje **Resource-id** hebt ingeschakeld, is deze instelling niet beschikbaar.
    - **Virtuele netwerktoegang toestaan:** Selecteer **Ingeschakeld** (standaard) als u de communicatie tussen de twee virtuele netwerken wilt inschakelen. Door de communicatie tussen virtuele netwerken mogelijk te maken, kunnen resources die zijn verbonden met een virtueel netwerk met elkaar communiceren met dezelfde bandbreedte en latentie als of ze zijn verbonden met hetzelfde virtuele netwerk. Alle communicatie tussen resources in de twee virtuele netwerken gaat via het Azure private netwerk. De **VirtualNetwork-servicetag** voor netwerkbeveiligingsgroepen omvat het virtuele netwerk en het peered virtuele netwerk. Zie [Overzicht netwerkbeveiligingsgroepen](security-overview.md#service-tags)voor meer informatie over servicetags voor netwerkbeveiligingsgroepen. Selecteer **Uitgeschakeld** als u niet wilt dat het verkeer naar het virtuele netwerk met peered wordt doorgestroomd. U **Uitgeschakeld** selecteren als u een virtueel netwerk met een ander virtueel netwerk hebt gekeken, maar af en toe de verkeersstroom tussen de twee virtuele netwerken wilt uitschakelen. Mogelijk is het inschakelen/uitschakelen handiger dan het verwijderen en opnieuw maken van peerings. Wanneer deze instelling is uitgeschakeld, stroomt het verkeer niet tussen de virtuele netwerken met peered.
    - **Doorsturend verkeer toestaan:** Schakel dit selectievakje in om verkeer *dat door* een virtueel netwerktoestel in een virtueel netwerk wordt doorgestuurd (dat niet afkomstig is van het virtuele netwerk) via een peering naar dit virtuele netwerk kan stromen. Denk bijvoorbeeld aan drie virtuele netwerken met de naam Spoke1, Spoke2 en Hub. Er bestaat een peering tussen elk gesproken virtueel netwerk en het virtuele hubnetwerk, maar peerings bestaan niet tussen de gesproken virtuele netwerken. Een virtueel netwerktoestel wordt geïmplementeerd in het virtuele Hub-netwerk en door de gebruiker gedefinieerde routes worden toegepast op elk gesproken virtueel netwerk dat het verkeer tussen de subnetten via het virtuele netwerktoestel route. Als dit selectievakje niet is ingeschakeld voor het peeren tussen elk gesproken virtuele netwerk en het virtuele netwerk van de hub, stroomt er geen verkeer tussen de gesproken virtuele netwerken omdat de hub het verkeer tussen de virtuele netwerken niet doorstuurt. Hoewel het inschakelen van deze mogelijkheid het doorgestuurde verkeer door het peering mogelijk maakt, maakt het geen door de gebruiker gedefinieerde routes of virtuele netwerkapparaten. Door de gebruiker gedefinieerde routes en virtuele netwerkapparaten worden afzonderlijk gemaakt. Meer informatie over [door de gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined). U hoeft deze instelling niet te controleren als verkeer wordt doorgestuurd tussen virtuele netwerken via een Azure VPN-gateway.
    - **Gateway-doorvoer toestaan:** Schakel dit selectievakje in als u een virtuele netwerkgateway hebt gekoppeld aan dit virtuele netwerk en verkeer van het peered virtuele netwerk door de gateway wilt laten stromen. Dit virtuele netwerk kan bijvoorbeeld via een virtuele netwerkgateway aan een on-premises netwerk worden gekoppeld. De gateway kan een ExpressRoute of VPN-gateway zijn. Als u dit vakje aangeeft, kan het verkeer van het peered virtuele netwerk via de gateway die is gekoppeld aan dit virtuele netwerk naar het on-premises netwerk stromen. Als u dit selectievakje inschakelt, kan het virtuele netwerk met peered geen gateway hebben geconfigureerd. Het peered virtuele netwerk moet het selectievakje **Externe gateways gebruiken** hebben ingeschakeld bij het instellen van het peering van het andere virtuele netwerk naar dit virtuele netwerk. Als u dit vakvak niet hebt aangevinkt (standaard), stroomt het verkeer van het peered virtuele netwerk nog steeds naar dit virtuele netwerk, maar kan het niet door een virtuele netwerkgateway stromen die is gekoppeld aan dit virtuele netwerk. Als de peering zich bevindt tussen een virtueel netwerk (Resource Manager) en een virtueel netwerk (klassiek), moet de gateway zich in het virtuele netwerk bevinden (Resource Manager).

       Naast het doorsturen van verkeer naar een on-premises netwerk, kan een VPN-gateway netwerkverkeer doorsturen tussen virtuele netwerken die zijn gekeken met het virtuele netwerk waarin de gateway zich bevindt, zonder dat de virtuele netwerken met elkaar hoeven te worden gekeken. Het gebruik van een VPN-gateway voor doorsturen van verkeer is handig wanneer u een VPN-gateway in een hub wilt gebruiken (zie de hub en het gesproken voorbeeld beschreven voor **Toestaan doorgestuurd verkeer)** virtueel netwerk om verkeer te routeren tussen gesproken virtuele netwerken die niet met elkaar zijn gekeken. Zie [Een VPN-gateway configureren voor doorvoer in een virtueel netwerk peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het toestaan van het gebruik van een gateway voor doorvoer. Dit scenario vereist het implementeren van door de gebruiker gedefinieerde routes die de virtuele netwerkgateway als het volgende hoptype opgeven. Meer informatie over [door de gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined). U een VPN-gateway alleen opgeven als een volgend hoptype in een door de gebruiker gedefinieerde route, u geen ExpressRoute-gateway opgeven als het volgende hoptype in een door de gebruiker gedefinieerde route.

    - **Externe gateways gebruiken:** Schakel dit selectievakje in om verkeer van dit virtuele netwerk door een virtuele netwerkgateway te laten stromen die is gekoppeld aan het virtuele netwerk waarmee u kijkt. Het virtuele netwerk waarmee u peert, is bijvoorbeeld gekoppeld aan een VPN-gateway die communicatie naar een on-premises netwerk mogelijk maakt.  Als u dit vakje aangeeft, kan het verkeer van dit virtuele netwerk door de VPN-gateway stromen die is gekoppeld aan het peered virtuele netwerk. Als u dit selectievakje inschakelt, moet het virtuele netwerk peered een virtuele netwerkgateway hebben en moet het selectievakje **Gateway-doorvoer toestaan** zijn ingeschakeld. Als u dit vakvak niet hebt aangevinkt (standaard), kan verkeer van het peered virtuele netwerk nog steeds naar dit virtuele netwerk stromen, maar kan het niet door een virtuele netwerkgateway stromen die is gekoppeld aan dit virtuele netwerk.
    
      Slechts één peering voor dit virtuele netwerk kan deze instelling hebben ingeschakeld.

      U geen externe gateways gebruiken als u al een gateway hebt geconfigureerd in uw virtuele netwerk. Zie [Een VPN-gateway configureren voor doorvoer in een virtueel netwerk peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het gebruik van een gateway voor doorvoer
        
    > [!NOTE]
    > Als u een Virtual Network Gateway gebruikt om on-premises verkeer transitively naar een peered VNet te verzenden, moet het peered VNet IP-bereik voor het on-premises VPN-apparaat worden ingesteld op 'interessant' verkeer. Anders kunnen uw on-premises resources niet communiceren met bronnen in het vnet met peered.

6. Selecteer **OK** om het peering toe te voegen aan het virtuele netwerk dat u hebt geselecteerd.

Zie [de volgende stappen](#next-steps)voor stapsgewijze instructies voor het implementeren van peering tussen virtuele netwerken in verschillende abonnementen en implementatiemodellen.

### <a name="commands"></a>Opdrachten

- **Azure CLI**: [vnet peering van AZ-netwerk](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Peering-instellingen weergeven of wijzigen

Voordat u een peering wijzigt, moet u vertrouwd raken met de vereisten en beperkingen en [vereiste machtigingen.](#permissions)

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze. Selecteer **geen virtuele netwerken (klassiek)** als deze in de lijst worden weergegeven, omdat u geen peering maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatiemodel.
2. Selecteer het virtuele netwerk in de lijst waarvoor u peering-instellingen wilt wijzigen.
3. Selecteer **Peerings**onder **INSTELLINGEN**.
4. Selecteer het peering waarvoor u instellingen wilt weergeven of wijzigen.
5. Wijzig de juiste instelling. Lees meer over de opties voor elke instelling in [stap 5](#add-peering) van Een peering maken.
6. Selecteer **Opslaan**.

**Opdrachten**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) to list peerings for a virtual network, az network [vnet peering show](/cli/azure/network/vnet/peering) to show setting for a specific peering, and az network [vnet peering update](/cli/azure/network/vnet/peering) to change peering settings.|
- **PowerShell:** [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) om peering-instellingen en [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) op te halen om instellingen te wijzigen.

## <a name="delete-a-peering"></a>Een peering verwijderen

Voordat u een peering uitschrijft, moet u ervoor zorgen dat uw account over de [vereiste machtigingen beschikt.](#permissions)

Wanneer een peering wordt verwijderd, stroomt het verkeer van een virtueel netwerk niet meer naar het virtuele netwerk met peered. Wanneer virtuele netwerken die via Resource Manager worden geïmplementeerd, worden gekeken, heeft elk virtueel netwerk een peering naar het andere virtuele netwerk. Hoewel het verwijderen van het peering van het ene virtuele netwerk de communicatie tussen de virtuele netwerken uitschakelt, verwijdert het peering niet van het andere virtuele netwerk. De peeringstatus voor het peering dat in het andere virtuele netwerk **bestaat,** is losgekoppeld . U het peering niet opnieuw maken totdat u het peering opnieuw maakt in het eerste virtuele netwerk en de peeringstatus voor beide virtuele netwerken wordt gewijzigd in *Verbonden*.

Als u wilt dat virtuele netwerken soms communiceren, maar niet altijd, in plaats van een peering te verwijderen, u in plaats daarvan de instelling **Virtuele netwerktoegang toestaan** instellen op **Uitgeschakeld.** Lees stap 6 van het gedeelte Een peering maken van dit artikel voor meer informatie over hoe u stap 6 van het gedeelte Een peering maken wilt lezen. Mogelijk u netwerktoegang gemakkelijker uitschakelen en inschakelen dan het verwijderen en opnieuw maken van peerings.

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze. Selecteer **geen virtuele netwerken (klassiek)** als deze in de lijst worden weergegeven, omdat u geen peering maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatiemodel.
2. Selecteer het virtuele netwerk in de lijst waarvoor u een peering wilt verwijderen.
3. Selecteer **Peerings**onder **INSTELLINGEN**.
4. Aan de rechterkant van het peering dat u wilt verwijderen, selecteert u **...**, selecteer **Verwijderen**en selecteert u **Ja** om het peering uit het eerste virtuele netwerk te verwijderen.
5. Voer de vorige stappen uit om het peering uit het andere virtuele netwerk in peering te verwijderen.

**Opdrachten**

- **Azure CLI**: [vnet peering van az-netwerk verwijderen](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Vereisten en beperkingen

- <a name="cross-region"></a>U virtuele netwerken in dezelfde regio of verschillende regio's peeren. Peering virtuele netwerken in verschillende regio's wordt ook wel aangeduid als *Global VNet Peering*. 
- Bij het maken van een globale peering kunnen de peered virtuele netwerken bestaan in elke Azure public cloud regio of China cloud regio's of Government cloud regio's. Je niet over wolken turen. Een VNet in Azure public cloud kan bijvoorbeeld niet worden gekeken naar een VNet in Azure China cloud.
- Resources in het ene virtueel netwerk kunnen niet communiceren met het front-end IP-adres van een interne Basic-load balancer in een wereldwijd gekoppeld virtueel netwerk. Ondersteuning voor Basic-load balancers bestaat alleen binnen dezelfde regio. Ondersteuning voor Standard-load balancers bestaat voor zowel VNet-peering als Global VNet-peering. Services die gebruik maken van een Basic load balancer die niet werkt via Global VNet Peering worden [hier gedocumenteerd.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- U externe gateways gebruiken of gatewaytransit toestaan in virtuele netwerken met een peer-and-to-peer van wereldwijd en lokaal gepeerde virtuele netwerken.
- De virtuele netwerken kunnen in dezelfde of verschillende abonnementen zijn. Wanneer u virtuele netwerken in verschillende abonnementen peer, kunnen beide abonnementen worden gekoppeld aan dezelfde of verschillende Azure Active Directory-tenant. Als u nog geen AD-tenant hebt, u [er een maken.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant) Ondersteuning voor peering in virtuele netwerken van abonnementen die zijn gekoppeld aan verschillende Azure Active Directory-tenants is niet beschikbaar in Portal. U CLI, PowerShell of Sjablonen gebruiken.
- De virtuele netwerken die u peert, moeten niet-overlappende IP-adresruimten hebben.
- U adresbereiken niet toevoegen aan of adresbereiken verwijderen uit de adresruimte van een virtueel netwerk zodra een virtueel netwerk is gekoppeld aan een ander virtueel netwerk. Als u adresbereiken wilt toevoegen of verwijderen, verwijdert u de peering, voegt u de adresbereiken toe of verwijdert u deze opnieuw en maakt u de peering opnieuw. Zie [Virtuele netwerken beheren](manage-virtual-network.md)als u adresbereiken wilt toevoegen aan of adresbereiken uit virtuele netwerken wilt verwijderen.
- U twee virtuele netwerken die zijn geïmplementeerd via Resource Manager of een virtueel netwerk dat via Resource Beheer wordt geïmplementeerd, met een virtueel netwerk dat via het klassieke implementatiemodel wordt geïmplementeerd, peer.You can peer two virtual networks deployment through Resource Manager or a virtual network ployed through Resource Manager with Resource Manager with a virtual network ployed through the classic deployment model. U niet met twee virtuele netwerken gekeken die zijn gemaakt via het klassieke implementatiemodel. Als u niet bekend bent met Azure-implementatiemodellen, leest u het artikel [Azure-implementatiemodellen begrijpen.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) U kunt [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) gebruiken om twee virtuele netwerken te koppelen die zijn gemaakt via het klassieke implementatiemodel.
- Wanneer u twee virtuele netwerken koppelt die zijn gemaakt via Resource Manager, moet voor elk virtueel netwerk een peering worden geconfigureerd. U ziet een van de volgende typen voor peeringstatus: 
  - *Gestart:* Wanneer u het peering naar het tweede virtuele netwerk maakt vanaf het eerste virtuele netwerk, wordt de peeringstatus *gestart.* 
  - *Verbonden:* Wanneer u peering maakt van het tweede virtuele netwerk naar het eerste virtuele netwerk, is de peeringstatus *Verbonden*. Als u de peeringstatus voor het eerste virtuele netwerk bekijkt, wordt de status gewijzigd van *Gestart* naar *Verbonden*. Het peering is niet met succes vastgesteld totdat de peering status voor beide virtuele netwerk peerings is *verbonden*.
- Wanneer u een virtueel netwerk peering dat is gemaakt via Resource Beheer met een virtueel netwerk dat is gemaakt via het klassieke implementatiemodel, configureert u alleen een peering voor het virtuele netwerk dat via Resource Beheer wordt geïmplementeerd. U peering niet configureren voor een virtueel netwerk (klassiek) of tussen twee virtuele netwerken die zijn geïmplementeerd via het klassieke implementatiemodel. Wanneer u het peering maakt van het virtuele netwerk (Resource Manager) naar het virtuele netwerk (Classic), wordt de peeringstatus *bijgewerkt*en wordt deze binnenkort gewijzigd *in Verbonden*.
- Een peering wordt vastgesteld tussen twee virtuele netwerken. Peerings zijn niet transitief. Als u peerings maakt tussen:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Er is geen peering tussen VirtualNetwork1 en VirtualNetwork3 via VirtualNetwork2. Als u een virtueel netwerk wilt maken dat peering tussen VirtualNetwork1 en VirtualNetwork3, moet u een peering tussen VirtualNetwork1 en VirtualNetwork3 maken.
- U namen in virtuele netwerken met peered niet oplossen met behulp van standaardAzure-naamomzetting. Als u namen in andere virtuele netwerken wilt oplossen, moet u Azure DNS gebruiken [voor privédomeinen](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of een aangepaste DNS-server. Zie [Naamomzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie over het instellen van uw eigen DNS-server.
- Bronnen in peered virtuele netwerken in dezelfde regio kunnen met elkaar communiceren met dezelfde bandbreedte en latentie als wanneer ze zich in hetzelfde virtuele netwerk bevinden. Elke virtuele machine grootte heeft zijn eigen maximale netwerkbandbreedte echter. Zie [Windows-](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux-virtuele](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) machineformaten voor meer informatie over maximale netwerkbandbreedte voor verschillende virtuele machineformaten.
- Een virtueel netwerk kan worden gekoppeld aan een ander virtueel netwerk en ook worden verbonden met een ander virtueel netwerk met een azure virtuele netwerkgateway. Wanneer virtuele netwerken zijn verbonden via zowel peering als een gateway, stroomt het verkeer tussen de virtuele netwerken door de peering-configuratie, in plaats van de gateway.
- Point-to-Site VPN-clients moeten opnieuw worden gedownload nadat virtuele netwerkpeering is geconfigureerd om ervoor te zorgen dat de nieuwe routes naar de client worden gedownload.
- Er wordt een nominaal bedrag in rekening gebracht voor inkomend en uitgaand verkeer dat gebruikmaakt van een virtueel netwerk-peering. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/virtual-network) voor meer informatie.

## <a name="permissions"></a>Machtigingen

De accounts die u gebruikt om met virtuele netwerkpeering te werken, moeten worden toegewezen aan de volgende rollen:

- [Netwerkbijdrager:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)voor een virtueel netwerk dat wordt geïmplementeerd via ResourceManager.
- [Klassieke netwerkbijdrager:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)voor een virtueel netwerk dat wordt geïmplementeerd via het klassieke implementatiemodel.

Als uw account niet is toegewezen aan een van de vorige rollen, moet het worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de nodige acties uit de volgende tabel zijn toegewezen:

| Actie                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Vereist voor het maken van een peering van virtueel netwerk A naar virtueel netwerk B. Virtueel netwerk A moet een virtueel netwerk zijn (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Vereist om een peering te maken van virtueel netwerk B (Resource Manager) naar virtueel netwerk A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Vereist om een peering te maken van virtueel netwerk B (klassiek) naar virtueel netwerk A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Lees een virtueel netwerk peering   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Een virtueel netwerk peering verwijderen |

## <a name="next-steps"></a>Volgende stappen

- Peering in virtuele netwerken kan tot stand worden gebracht tussen virtuele netwerken die zijn gemaakt via hetzelfde implementatiemodel of via verschillende implementatiemodellen die tot hetzelfde abonnement of tot verschillende abonnementen behoren. Volg een zelfstudie voor een van de volgende scenario's:

  |Azure-implementatiemodel             | Abonnement  |
  |---------                          |---------|
  |Beide in Resource Manager              |[Hetzelfde](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Verschillend](create-peering-different-subscriptions.md)|
  |Eén in Resource Manager, één klassiek  |[Hetzelfde](create-peering-different-deployment-models.md)|
  |                                   |[Verschillend](create-peering-different-deployment-models-subscriptions.md)|

- Meer informatie over het maken van een [hub-en-spoke-netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Een virtueel netwerkpeering maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager-sjablonen](template-samples.md) gebruiken
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
