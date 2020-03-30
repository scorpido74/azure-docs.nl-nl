---
title: Peering-problemen van virtueel netwerk oplossen
description: Stappen om de meeste problemen met het aantal virtuele netwerkpeering op te lossen.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796240"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Peering-problemen van virtueel netwerk oplossen

Deze handleiding voor het oplossen van problemen bevat stappen waarmee u de meeste problemen [met het aantal virtuele netwerkpeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) oplossen.

![Diagram van virtueel netwerkpeeren](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Virtueel netwerkpeeren tussen twee virtuele netwerken configureren

Zijn de virtuele netwerken in hetzelfde abonnement of in verschillende abonnementen?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>De virtuele netwerken zijn in hetzelfde abonnement

Als u virtuele netwerkpeering wilt configureren voor de virtuele netwerken die in hetzelfde abonnement zijn, gebruikt u de methoden in de volgende artikelen:

* Zie [Een peering maken](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)als de virtuele netwerken zich in *dezelfde regio bevinden.*
* Als de virtuele netwerken zich in de *verschillende regio's bevinden,* raadpleegt u [Virtueel netwerkpeeren](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> Connectiviteit werkt niet via wereldwijde virtuele netwerkpeering voor de volgende bronnen: 
>
> * Virtuele machines (VM's) achter Basic internal load balancer (ILB) SKU
> * Redis-cache (gebruikt Basic ILB SKU)
> * Toepassingsgateway (gebruikt Basic ILB SKU)
> * Virtuele machineschaalsets (gebruikt Basic ILB SKU)
> * Azure Service Fabric-clusters (gebruikt Basic ILB SKU)
> * SQL Server Always On (gebruikt Basic ILB SKU)
> * Azure App-serviceomgeving voor PowerApps (maakt gebruik van BasisILB SKU)
> * Azure API Management (gebruikt Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (gebruikt BasisILB SKU)

Zie voor meer informatie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van global peering.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>De virtuele netwerken zijn in verschillende abonnementen of Active Directory-tenants

Zie [Peering](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)maken in verschillende abonnementen voor Azure CLI voor virtuele netwerkpeering voor virtuele netwerken in verschillende abonnementen of Active Directory-tenants.

> [!Note]
> Als u netwerkpeering wilt configureren, moet u in beide abonnementen machtigingen voor **netwerkinzender** hebben. Zie [Peering-machtigingen](virtual-network-manage-peering.md#permissions)voor meer informatie.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Virtuele netwerkpeering configureren met hub-spoke topologie die on-premises resources gebruikt

![Diagram van virtueel netwerkpeeren met on-premises spaak](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Voor een site-to-site verbinding of een ExpressRoute-verbinding

Volg de stappen in: [VPN-gatewaytransit configureren voor virtueel netwerkpeeren.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)

### <a name="for-point-to-site-connections"></a>Voor point-to-site verbindingen

1. Volg de stappen in: [VPN-gatewaytransit configureren voor virtueel netwerkpeeren.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)
2. Nadat virtuele netwerkpeering is vastgesteld of gewijzigd, download en installeer t u het point-to-site-pakket, zodat de point-to-site-clients de bijgewerkte routes naar het gesproken virtuele netwerk krijgen.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Virtueel netwerkpeering configureren met virtueel netwerk met hubspaaktopologie

![Diagram van virtueel netwerkpeeren met een virtueel netwerk dat is gesproken](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>De virtuele netwerken bevinden zich in dezelfde regio


1. Configureer in het virtuele hubnetwerk een netwerkvirtueel toestel (NVA).
1. In de gesproken virtuele netwerken, hebben door de gebruiker gedefinieerde routes met de volgende hop type "netwerk virtuele toestel" toegepast.

Zie [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)voor meer informatie.

> [!Note]
> Als u hulp nodig hebt bij het instellen van een NVA, neemt [u contact op met de NVA-leverancier.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

Zie Problemen met [virtuele apparaten in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)voor hulp bij het oplossen van problemen met de NVA-apparaat.

### <a name="the-virtual-networks-are-in-different-regions"></a>De virtuele netwerken bevinden zich in verschillende regio's

Transit over wereldwijde virtuele netwerk peering wordt nu ondersteund. Connectiviteit werkt niet via wereldwijde virtuele netwerkpeering voor de volgende bronnen:

* VM's achter Basic ILB SKU
* Redis-cache (gebruikt Basic ILB SKU)
* Toepassingsgateway (gebruikt Basic ILB SKU)
* Schaalsets (gebruikt Basic ILB SKU)
* Clusters van servicefabric (maakt gebruik van Basic ILB SKU)
* SQL Server Always On (gebruikt Basic ILB SKU)
* App-serviceomgeving (gebruikt Basis ILB SKU)
* API-beheer (gebruikt Basic ILB SKU)
* Azure AD DS (gebruikt Basis ILB SKU)

Zie [Virtueel netwerkpeeren](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)voor meer informatie over wereldwijde vereisten voor peering en beperkingen.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Een verbindingsprobleem tussen twee virtuele netwerken met peered oplossen

Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een account met de nodige [rollen en machtigingen.](virtual-network-manage-peering.md#permissions) Selecteer het virtuele netwerk, selecteer **Peering**en controleer het veld **Status.** Wat is de status?

### <a name="the-peering-status-is-connected"></a>De peeringstatus is 'Verbonden'

Ga als volgt te werk om dit probleem op te lossen:

1. Controleer de netwerkverkeersstromen:

   Gebruik [Verbindingsproblemen](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) en [IP-stroom controleren](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) van de bron-VM naar de doel-VM om te bepalen of er een NSG of UDR is die interferentie veroorzaakt in de verkeersstromen.

   Als u een firewall of NVA gebruikt: 
   1. Documenteer de UDR-parameters, zodat u ze herstellen nadat deze stap is voltooid.
   2. Verwijder de UDR uit het bron-VM-subnet of NIC dat naar de NVA verwijst als de volgende hop. Controleer de verbinding van de bron-VM rechtstreeks naar de bestemming die de NVA omzeilt. Als deze stap niet werkt, raadpleegt u de [probleemoplosser nva](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Neem een netwerktracering: 
   1. Start een netwerktracering op de doel-VM. Voor Windows u **Netsh**gebruiken. Voor Linux, gebruik **TCPDump**.
   2. **TcpPing** of **PsPing** uitvoeren van de bron naar het doel-IP.

      Dit is een voorbeeld van een opdracht **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Nadat de **TcpPing** is voltooid, stopt u het netwerktracering op de bestemming.
   4. Als pakketten van de bron binnenkomen, is er geen netwerkprobleem. Onderzoek zowel de VM-firewall als de toepassing die op die poort luistert om het configuratieprobleem te lokaliseren.

   > [!Note]
   > U geen verbinding maken met de volgende resourcetypen via wereldwijde virtuele netwerkpeering (virtuele netwerken in verschillende regio's):
   >
   > * VM's achter Basic ILB SKU
   > * Redis-cache (gebruikt Basic ILB SKU)
   > * Toepassingsgateway (gebruikt Basic ILB SKU)
   > * Schaalsets (gebruikt Basic ILB SKU)
   > * Clusters van servicefabric (maakt gebruik van Basic ILB SKU)
   > * SQL Server Always On (gebruikt Basic ILB SKU)
   > * App-serviceomgeving (gebruikt Basis ILB SKU)
   > * API-beheer (gebruikt Basic ILB SKU)
   > * Azure AD DS (gebruikt Basis ILB SKU)

Zie voor meer informatie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van global peering.

### <a name="the-peering-status-is-disconnected"></a>De peeringstatus is 'Losgekoppeld'

Als u dit probleem wilt oplossen, verwijdert u het peering van beide virtuele netwerken en maakt u deze opnieuw.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Een verbindingsprobleem tussen een virtueel netwerk met hubspaaken en een on-premises bron oplossen

Gebruikt uw netwerk een NVA- of VPN-gateway van derden?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Mijn netwerk maakt gebruik van een NVA- of VPN-gateway van derden

Zie de volgende artikelen voor het oplossen van verbindingsproblemen die van invloed zijn op een NVA- of VPN-gateway van derden:

* [NVA-probleemoplosser](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Servicechaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Mijn netwerk maakt geen gebruik van een NVA- of VPN-gateway van derden

Hebben het virtuele netwerk van de hub en het gesproken virtuele netwerk een VPN-gateway?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Zowel de hub virtuele netwerk en de spaak virtuele netwerk hebben een VPN-gateway

Het gebruik van een externe gateway wordt niet ondersteund.

Als het gesproken virtuele netwerk al een VPN-gateway heeft, wordt de optie **Externe gateway** gebruiken niet ondersteund op het gesproken virtuele netwerk. Dit komt door een beperking van virtuele netwerkpeering.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Zowel de hub virtuele netwerk en de spaak virtuele netwerk hebben geen VPN-gateway

Controleer de volgende primaire oorzaken van verbindingsproblemen met het externe virtuele netwerk van on-site of Azure ExpressRoute-verbindingen vanaf on-premises:

* Controleer in het virtuele netwerk met een gateway of het selectievakje **Doorgestuurd verkeer toestaan** is ingeschakeld.
* Controleer in het virtuele netwerk dat geen gateway heeft of het selectievakje **Externe gateway gebruiken** is ingeschakeld.
* Laat uw netwerkbeheerder uw on-premises apparaten controleren om te controleren of ze allemaal de externe virtuele netwerkadresruimte hebben toegevoegd.

Voor point-to-site verbindingen:

* Controleer in het virtuele netwerk met een gateway of het selectievakje **Doorgestuurd verkeer toestaan** is ingeschakeld.
* Controleer in het virtuele netwerk dat geen gateway heeft of het selectievakje **Externe gateway gebruiken** is ingeschakeld.
* Download en installeer het point-to-site clientpakket. Virtuele netwerkroutes die nieuw zijn gepeerd, voegen niet automatisch routes toe naar point-to-site clients.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Problemen met een probleem met de netwerkconnectiviteit met hub's tussen gesproken virtuele netwerken in dezelfde regio oplossen

Een hubnetwerk moet een NVA bevatten. Configureer UDR's in spaken met een NVA-set als de volgende hop en schakel **doorgestuurd verkeer toe** in het virtuele hubnetwerk.

Zie [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)voor meer informatie en bespreek deze vereisten met de [NVA-leverancier](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) van uw keuze.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Problemen met een probleem met hub-spaaknetwerkconnectiviteit tussen gesproken virtuele netwerken in verschillende regio's oplossen

Transit over wereldwijde virtuele netwerk peering wordt nu ondersteund. Connectiviteit werkt niet via wereldwijde virtuele netwerkpeering voor de volgende bronnen:

* VM's achter Basic ILB SKU
* Redis-cache (gebruikt Basic ILB SKU)
* Toepassingsgateway (gebruikt Basic ILB SKU)
* Schaalsets (gebruikt Basic ILB SKU)
* Clusters van servicefabric (maakt gebruik van Basic ILB SKU)
* SQL Server Always On (gebruikt Basic ILB SKU)
* App-serviceomgeving (gebruikt Basis ILB SKU)
* API-beheer (gebruikt Basic ILB SKU)
* Azure AD DS (gebruikt Basis ILB SKU)

Zie voor meer informatie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van wereldwijde peering en verschillende [VPN-topologieën.](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Problemen met een probleem met een hub-spaaknetwerk tussen een web-app en het gesproken virtuele netwerk oplossen

Ga als volgt te werk om dit probleem op te lossen:

1. Meld u aan bij Azure Portal. 
1. Selecteer in de web-app **netwerken**en selecteer **Vervolgens VNet-integratie**.
1. Controleer of u het externe virtuele netwerk zien. Voer handmatig de externe virtuele netwerkadresruimte in **(Netwerk synchroniseren** en **Routes toevoegen).**

Raadpleeg voor meer informatie de volgende artikelen:

* [Uw app integreren met een virtueel Azure-netwerk](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Over point-to-site-VPN-routering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Een foutbericht voor het peering van virtuele netwerken oplossen 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Huidige `<TENANT ID>` tenant is niet bevoegd om toegang te krijgen tot gekoppeld abonnement

Zie [Peering maken - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)als u dit probleem wilt oplossen.

### <a name="not-connected"></a>Niet verbonden

Als u dit probleem wilt oplossen, verwijdert u het peering van beide virtuele netwerken en maakt u deze opnieuw.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Kan geen peer-peer van een virtueel netwerk van Databricks

Als u dit probleem wilt oplossen, configureert u het virtuele netwerkpeering onder **Azure Databricks**en geeft u het virtuele doelnetwerk op met Behulp van **Resource-id**. Zie [Peer a Databricks virtueel netwerk naar een extern virtueel netwerk](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
