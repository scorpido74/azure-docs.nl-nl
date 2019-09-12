---
title: Peering-problemen van virtueel netwerk oplossen
description: Stappen om de meeste problemen met peering van het virtuele netwerk op te lossen.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901761"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Peering-problemen van virtueel netwerk oplossen

Deze hand leiding bevat stappen die u helpen bij het oplossen van de meeste problemen met [peering in het virtuele netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![BITMAPAFBEELDING](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scenario 1: Virtuele netwerk peering tussen twee virtuele netwerken configureren

Bevinden de virtuele netwerken zich in hetzelfde abonnement of in verschillende abonnementen?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Verbindings type 1: De virtuele netwerken bevinden zich in hetzelfde abonnement

Gebruik de methoden die zijn opgenomen in de volgende artikelen, indien van toepassing, voor het configureren van de peering van het virtuele netwerk voor de virtuele netwerken die zich in hetzelfde abonnement bevinden:

* Als de virtuele netwerken zich in **dezelfde regio**bevinden, volgt u de stappen voor het [maken van een peering voor virtuele netwerken in hetzelfde abonnement](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Als de virtuele netwerken zich in de **verschillende regio's**bevinden, volgt u de stappen voor het instellen van [globale virtuele netwerk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Connectiviteit werkt niet via globale VNet-peering voor de volgende resources: 
>
> * Vm's achter Basic ILB SKU
> * Redis Cache (maakt gebruik van Basic ILB SKU)
> * Application Gateway (maakt gebruik van Basic ILB SKU)
> * Schaal sets (maakt gebruik van Basic ILB SKU)
> * Service Fabric clusters (maakt gebruik van Basic ILB SKU)
> * SQL always-on (maakt gebruik van Basic ILB SKU)
> * App Service omgevingen (ASE) (maakt gebruik van Basic ILB SKU)
> * API Management (maakt gebruik van Basic ILB SKU)
> * Azure Active Directory Domain Service (toevoegen) (maakt gebruik van Basic ILB SKU)

Zie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van wereld wijde peering voor meer informatie.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Verbindings type 2: De virtuele netwerken bevinden zich in verschillende abonnementen of AD-tenants

Volg de stappen in [peering maken in verschillende abonnementen voor Azure cli](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)om virtuele netwerk peering te configureren voor virtuele netwerken in verschillende abonnementen of Active Directory tenants.

> [!Note]
> Als u Network-peering wilt configureren, moet u in beide abonnementen over de machtiging **netwerk Inzender** beschikken. Zie [machtigingen voor peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions)voor meer informatie.

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scenario 2: Configureer peering voor het virtuele netwerk met hub-spoke-topologie die gebruikmaakt van on-premises resources

![BITMAPAFBEELDING](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Verbindings type 1: Voor site-naar-site-verbinding of ExpressRoute-verbinding

Volg de stappen in: [Configureer VPN-gateway doorvoer voor peering van virtuele netwerken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Verbindings type 2: Voor punt-naar-site-verbindingen

1. Volg de stappen in: [Configureer VPN-gateway doorvoer voor peering van virtuele netwerken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Nadat de peering voor het virtuele netwerk is ingesteld of gewijzigd, moet het punt-naar-site-pakket opnieuw worden gedownload en geïnstalleerd, zodat de punt-naar-site-clients de bijgewerkte routes naar het spoke-virtuele netwerk kunnen verkrijgen.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scenario 3: Peering voor het virtuele netwerk configureren met hub-spoke-topologie voor Azure Virtual Network

![BITMAPAFBEELDING](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Verbindings type 1: De virtuele netwerken bevinden zich in dezelfde regio

U moet een virtueel netwerk apparaat (NVA) configureren in het virtuele hub-netwerk en door de gebruiker gedefinieerde routes met de volgende hop ' netwerk virtueel apparaat ' hebben toegepast in de spoke-virtuele netwerken. Zie [service Chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)voor meer informatie.

> [!Note]
> Als u hulp nodig hebt bij het instellen van een NVA, [neemt u contact op met de leverancier van NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Zie [problemen met virtuele netwerk apparaten in azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)voor hulp bij het oplossen van problemen met de installatie en route ring van het NVA-apparaat.

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Verbindings type 2: De virtuele netwerken bevinden zich in verschillende regio's

Transit via Global VNet-peering wordt nu ondersteund. De connectiviteit werkt niet via globale VNet-peering voor de volgende resources:

* Vm's achter Basic ILB SKU
* Redis Cache (maakt gebruik van Basic ILB SKU)
* Application Gateway (maakt gebruik van Basic ILB SKU)
* Schaal sets (maakt gebruik van Basic ILB SKU)
* Service Fabric clusters (maakt gebruik van Basic ILB SKU)
* SQL always-on (maakt gebruik van Basic ILB SKU)
* App Service omgevingen (ASE) (maakt gebruik van Basic ILB SKU)
* API Management (maakt gebruik van Basic ILB SKU)
* Azure Active Directory Domain Service (toevoegen) (maakt gebruik van Basic ILB SKU)

Zie [Virtual Network-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)voor meer informatie over algemene vereisten en beperkingen voor peering.

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scenario 4: Ik heb een connectiviteits probleem tussen twee gekoppelde virtuele netwerken

Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account met de benodigde [rollen en machtigingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions). Selecteer het virtuele netwerk, selecteer **peering**en controleer vervolgens het veld **status** . Wat is de status?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Verbindings type 1: Met de status van de peering wordt ' connected ' weer gegeven

Voer de volgende stappen uit om het probleem op te lossen:

1. Controleer het netwerk verkeer:

   Gebruik [verbindings problemen oplossen](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) en [IP-stroom controleren](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) van de bron-VM naar de doel-VM om te bepalen of er sprake is van een NSG of UDR dat storingen veroorzaakt in verkeers stromen.

   Als u een firewall of een NVA-apparaat gebruikt, voert u de volgende stappen uit: 
   1. Documenteer de UDR-para meters, zodat u ze kunt herstellen nadat deze stap is voltooid.
   2. Verwijder de UDR uit het subnet van de bron-VM of de NIC die verwijst naar de NVA als de volgende hop. Controleer de connectiviteit van de bron-VM rechtstreeks naar de bestemming die de NVA omzeilt. Als deze stap werkt, raadpleegt u de [probleem oplosser voor NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Een netwerk tracering uitvoeren: 
   1. Start een netwerk tracering op de doel-VM. Voor Windows kunt u **netsh**gebruiken. Gebruik **TCPDump**voor Linux.
   2. Voer **TcpPing** of **PsPing** uit van de bron naar het doel-IP.

   * Dit is een voor beeld van een **TcpPing** -opdracht:`tcping64.exe -t <destination VM address> 3389`

   3. Stop de netwerk tracering op de bestemming nadat de **TcpPing** is voltooid.
   4. Als pakketten van de bron arriveren, is er geen netwerk probleem. Controleer zowel de VM-firewall als de toepassing die op die poort luistert om het configuratie probleem te vinden.

   > [!Note]
   > U kunt geen verbinding maken met de volgende bron typen via globale virtuele netwerk peering (virtuele netwerken in verschillende regio's):
   >
   > * Vm's achter Basic ILB SKU
   > * Redis Cache (maakt gebruik van Basic ILB SKU)
   > * Application Gateway (maakt gebruik van Basic ILB SKU)
   > * Schaal sets (maakt gebruik van Basic ILB SKU)
   > * Service Fabric clusters (maakt gebruik van Basic ILB SKU)
   > * SQL always-on (maakt gebruik van Basic ILB SKU)
   > * App Service omgevingen (ASE) (maakt gebruik van Basic ILB SKU)
   > * API Management (maakt gebruik van Basic ILB SKU)
   > * Azure Active Directory Domain Service (toevoegen) (maakt gebruik van Basic ILB SKU)

Zie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van wereld wijde peering voor meer informatie.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Verbindings type 2: Met de status van de peering wordt de verbinding verbroken weer gegeven

U moet peerings uit beide VNets verwijderen en opnieuw maken.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scenario 5: Ik heb een connectiviteits probleem tussen een hub-spoke-netwerk en een on-premises resource

Gebruikt u een NVA of VPN-gateway van derden?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Verbindings type 1: Mijn netwerk maakt gebruik van een NVA-of VPN-gateway van derden

Raadpleeg de volgende artikelen om verbindings problemen op te lossen die van invloed zijn op een NVA of VPN-gateway van derden:

* [NVA-probleem Oplosser](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Service keten](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Verbindings type 2: Mijn netwerk is geen NVA of VPN-gateway van derden

Hebben zowel de hub als de spoke-virtuele netwerken een VPN-gateway?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Zowel de hub-als spoke-virtuele netwerken hebben een VPN-gateway

Het gebruik van een externe gateway wordt niet ondersteund.

Vanwege een beperking van het VNet-peering, **gebruikt u externe gateway** wordt niet ondersteund op het spoke-vnet als de spoke vnet al een VPN-gateway heeft.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Zowel de hub-als spoke-virtuele netwerken hebben geen VPN-gateway

Voor site-naar-site-of ExpressRoute-verbindingen controleert u deze primaire oorzaken van connectiviteits problemen met het externe virtuele netwerk van on-premises.

* Controleer of het selectie vakje **doorgestuurd verkeer toestaan** is ingeschakeld op het virtuele netwerk met een gateway.
* Controleer of het selectie vakje **externe gateway gebruiken** is ingeschakeld op het virtuele netwerk dat geen gateway heeft.
* Zorg ervoor dat uw netwerk beheerder uw on-premises apparaten controleert om te controleren of de externe adres ruimte van het virtuele netwerk is toegevoegd.

Voor punt-naar-site-verbindingen:

* Controleer of het selectie vakje **doorgestuurd verkeer toestaan** is ingeschakeld op het virtuele netwerk met een gateway.
* Controleer of het selectie vakje **externe gateway gebruiken** is ingeschakeld op het virtuele netwerk dat geen gateway heeft.
* Down load en installeer het punt-naar-site-client pakket opnieuw. Routes van virtuele netwerken die nieuw zijn, worden niet automatisch toegevoegd aan punt-naar-site-clients.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scenario 6: Ik heb een hub-Spaak netwerk connectiviteits probleem tussen spoke-virtuele netwerken in dezelfde regio

U moet een NVA in een hub-netwerk hebben, Udr's in spokes configureren waarvoor een NVA is ingesteld als de volgende hop en Schakel **doorgestuurd verkeer toestaan** in het virtuele hub-netwerk in.

Zie [service Chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)voor meer informatie en bespreek deze vereisten met de [NVA leverancier](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) van uw keuze.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scenario 7: Ik heb een hub-Spaak netwerk connectiviteits probleem tussen spoke-virtuele netwerken in verschillende regio's

Transit via Global VNet-peering wordt nu ondersteund. Connectiviteit werkt niet via globale VNet-peering voor de volgende resources:

* Vm's achter Basic ILB SKU
* Redis Cache (maakt gebruik van Basic ILB SKU)
* Application Gateway (maakt gebruik van Basic ILB SKU)
* Schaal sets (maakt gebruik van Basic ILB SKU)
* Service Fabric clusters (maakt gebruik van Basic ILB SKU)
* SQL always-on (maakt gebruik van Basic ILB SKU)
* App Service omgevingen (ASE) (maakt gebruik van Basic ILB SKU)
* API Management (maakt gebruik van Basic ILB SKU)
* Azure Active Directory Domain Service (toevoegen) (maakt gebruik van Basic ILB SKU)

Zie voor meer informatie de [vereisten en beperkingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) van globale peering en [verschillende VPN-topologieën](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scenario 8: Ik heb een hub-Spaak netwerk connectiviteits probleem tussen een web-app en het spoke-virtuele netwerk

Voer de volgende stappen uit om dit probleem op te lossen:

1. Meld u aan bij de Azure Portal. Ga naar de web-app, selecteer **netwerken**en selecteer vervolgens **VNet-integratie**.
2. Controleer of u het externe virtuele netwerk kunt zien. Voer hand matig de adres ruimte van het externe virtuele netwerk in (**synchronisatie netwerk** en **Voeg routes toe**).

Raadpleeg voor meer informatie de volgende artikelen:

* [Uw app integreren met een Azure-Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Over punt-naar-site VPN-route ring](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scenario 9: Er wordt een fout bericht weer gegeven bij het configureren van peering voor het virtuele netwerk

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Fout 1: De huidige `<TENANT ID>` Tenant is niet gemachtigd om toegang te krijgen tot het gekoppelde abonnement

Volg de stappen in [peering maken-Azure cli](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)om dit probleem op te lossen.

### <a name="error-2-not-connected"></a>Fout 2: Geen verbinding

U moet peerings uit beide VNets verwijderen en opnieuw maken.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Fout 3: Kan geen peer van een virtueel Databricks-netwerk uitvoeren

U kunt dit probleem oplossen door de peering van het virtuele netwerk te configureren vanaf de Blade **Azure Databricks** en vervolgens het virtuele doel netwerk op te geven met behulp van de **resource-id**. Zie [een virtueel Databricks-netwerk koppelen aan een extern virtueel netwerk](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)