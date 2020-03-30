---
title: 'Azure ExpressRoute: Connectiviteit verifiëren - Handleiding voor probleemoplossing'
description: Op deze pagina vindt u instructies over het oplossen van problemen en het valideren van end-to-end connectiviteit van een ExpressRoute-circuit.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330954"
---
# <a name="verifying-expressroute-connectivity"></a>Connectiviteit ExpressRoute controleren
In dit artikel u de ExpressRoute-connectiviteit verifiëren en oplossen. ExpressRoute breidt een on-premises netwerk uit naar de Microsoft-cloud via een privéverbinding die vaak wordt gefaciliteerd door een connectiviteitsprovider. ExpressRoute-connectiviteit omvat traditioneel drie verschillende netwerkzones, als volgt:

-   Klantnetwerk
-   Providernetwerk
-   Microsoft-datacenter

> [!NOTE]
> In het Direct Connectivit-model (aangeboden met een bandbreedte van 10/100 Gbps) kunnen klanten rechtstreeks verbinding maken met de poort van Microsoft Enterprise Edge (MSEE). Daarom zijn er in het directe connectiviteitsmodel alleen netwerkzones van klanten en Microsoft.
>


Het doel van dit document is om de gebruiker te helpen te identificeren of en waar een verbindingsprobleem bestaat. Daarbij, om te helpen ondersteuning te zoeken bij het juiste team om een probleem op te lossen. Als Microsoft-ondersteuning nodig is om een probleem op te lossen, opent u een ondersteuningsticket met [Microsoft Support.][Support]

> [!IMPORTANT]
> Dit document is bedoeld om eenvoudige problemen te diagnosticeren en op te lossen. Het is niet bedoeld als vervanging voor Microsoft-ondersteuning. Open een ondersteuningsticket met [Microsoft Support][Support] als u het probleem niet oplossen met behulp van de meegeleverde richtlijnen.
>
>

## <a name="overview"></a>Overzicht
In het volgende diagram ziet u de logische verbinding van een klantennetwerk met het Microsoft-netwerk via ExpressRoute.
[![1]][1]

In het voorgaande diagram geven de getallen belangrijke netwerkpunten aan. Deze netwerkpunten worden in dit artikel soms verwezen op basis van het bijbehorende nummer. Afhankelijk van het ExpressRoute-connectiviteitsmodel --Cloud Exchange Co-locatie, Point-to-Point Ethernet Connection of Any-to-any (IPVPN)---kunnen de netwerkpunten 3 en 4 switches (Layer 2-apparaten) of routers (Layer 3-apparaten) zijn. In het direct connectivity-model zijn er geen netwerkpunten 3 en 4; in plaats daarvan zijn CE's (2) rechtstreeks via donkere vezels met mte's verbonden. De belangrijkste netwerkpunten die worden geïllustreerd zijn als volgt:

1.  Computerapparaat van de klant (bijvoorbeeld een server of pc)
2.  CEs: Customer edge routers 
3.  PO's (CE-facing): Provider edge routers/switches die worden geconfronteerd met customer edge routers. In dit document wordt PE-CE's genoemd.
4.  Po's (MSEE facing): Provider edge routers/switches die te maken hebben met ME's. In dit document wordt pe-me's genoemd.
5.  MEE's: Microsoft Enterprise Edge (MSEE) ExpressRoute-routers
6.  VNet-gateway (Virtual Network)
7.  Compute-apparaat op het Azure VNet

Als de Cloud Exchange Co-locatie, Point-to-Point Ethernet of directe connectiviteitsmodellen worden gebruikt, maken CE's (2) BGP-peering met MEEs (5). 

Als het Any-to-any (IPVPN) connectiviteitsmodel wordt gebruikt, stellen PE-MEEs (4) BGP-peering met MEEs vast (5). PE-MEEs verspreiden de routes die Microsoft via het IPVPN-serviceprovidernetwerk van Microsoft heeft ontvangen.

> [!NOTE]
>Voor hoge beschikbaarheid creëert Microsoft een volledig redundante parallelle connectiviteit tussen mte's (5) en PE-MEE's (4) paren. Een volledig redundant parallel netwerkpad wordt ook aangemoedigd tussen klantennetwerk en PE-CEs pair. Zie het artikel [Ontwerpen voor hoge beschikbaarheid met ExpressRoute voor][HA] meer informatie over hoge beschikbaarheid
>
>

De volgende zijn de logische stappen in het probleemiaal oplossen van het ExpressRoute-circuit:

* [Circuitinrichting en status verifiëren](#verify-circuit-provisioning-and-state)
  
* [Peering-configuratie valideren](#validate-peering-configuration)
  
* [ARP valideren](#validate-arp)
  
* [BGP en routes valideren op de MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [De verkeersstroom bevestigen](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Circuitinrichting en status verifiëren
De inrichting van een ExpressRoute-circuit legt een redundante Layer 2-verbindingen tussen CE/PE-MEEs (2)/(4) en MEE's (5). Zie het artikel [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]voor meer informatie over het maken, wijzigen, inrichten en verifiëren van een ExpressRoute-circuit.

>[!TIP]
>Een servicesleutel identificeert op unieke wijze een ExpressRoute-circuit. Als u hulp nodig hebt van Microsoft of van een ExpressRoute-partner om een ExpressRoute-probleem op te lossen, dient u de servicesleutel op te geven om het circuit gemakkelijk te identificeren.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure-portal
Open in de Azure-portal het schakelblad van het ExpressRoute-circuit. In het ![3][3] gedeelte van het blad worden de ExpressRoute essentials vermeld zoals weergegeven in de volgende schermafbeelding:

![4][4]    

In de ExpressRoute Essentials geeft *de status van circuit* de status van het circuit aan de Microsoft-kant aan. *De providerstatus* geeft aan of het circuit is *ingericht/niet is ingericht* aan de kant van de serviceprovider. 

Om een ExpressRoute-circuit operationeel te maken, moet de *circuitstatus* zijn *ingeschakeld* en moet de *providerstatus* zijn *ingericht.*

> [!NOTE]
> Neem na het configureren van een ExpressRoute-circuit contact op met [Microsoft Support][Support]als de *status Circuit* niet is ingeschakeld. Als de *providerstatus* daarentegen niet is ingericht, neemt u contact op met uw serviceprovider.
>
>

### <a name="verification-via-powershell"></a>Verificatie via PowerShell
Als u alle ExpressRoute-circuits in een resourcegroep wilt weergeven, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Als u op zoek bent naar de naam van een resourcegroep, u deze krijgen door alle brongroepen in uw abonnement op te sommen met de opdracht *Get-AzResourceGroup*
>


Als u een bepaald ExpressRoute-circuit in een resourcegroep wilt selecteren, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Een voorbeeldreactie is:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Om te controleren of een ExpressRoute-circuit operationeel is, moet u bijzondere aandacht besteden aan de volgende velden:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Neem na het configureren van een ExpressRoute-circuit contact op met [Microsoft Support][Support]als de *status Circuit* niet is ingeschakeld. Als de *providerstatus* daarentegen niet is ingericht, neemt u contact op met uw serviceprovider.
>
>

## <a name="validate-peering-configuration"></a>Peering-configuratie valideren
Nadat de dienstverlener het inrichten van het ExpressRoute-circuit heeft voltooid, kunnen meerdere op eBGP gebaseerde routeconfiguraties worden gemaakt over het ExpressRoute-circuit tussen CE's/MSEE-PO's (2)/(4) en MEEs (5). Elk ExpressRoute-circuit kan hebben: Azure private peering (verkeer naar particuliere virtuele netwerken in Azure) en/of Microsoft-peering (verkeer naar openbare eindpunten van PaaS en SaaS). Zie het artikel [Routering maken en wijzigen voor een ExpressRoute-circuit voor][CreatePeering]meer informatie over het maken en wijzigen van de routeconfiguratie.

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure-portal

> [!NOTE]
> In ipvpn-connectiviteitsmodel nemen serviceproviders de verantwoordelijkheid voor het configureren van de peerings (layer 3-services). Probeer in een dergelijk model, nadat de serviceprovider een peering heeft geconfigureerd en als het peering leeg is in de portal, de circuitconfiguratie te vernieuwen met behulp van de vernieuwingsknop op de portal. Deze bewerking haalt de huidige routeringsconfiguratie uit uw circuit. 
>

In de Azure-portal kan de status van een ExpressRoute-circuitpeering worden gecontroleerd onder het circuitblad ExpressRoute. In het ![3][3] gedeelte van het blad worden de ExpressRoute-peerings weergegeven zoals weergegeven in de volgende schermafbeelding:

![5][5]

In het voorgaande voorbeeld is zoals opgemerkt Azure private peering ingericht, terwijl Azure public en Microsoft peerings niet zijn ingericht. Een met succes ingerichte peering context zou ook de primaire en secundaire point-to-point subnetten worden vermeld. De /30-subnetten worden gebruikt voor het interface-IP-adres van de ME's en CE's/PE-ME's. Voor de peerings die zijn ingericht, geeft de aanbieding ook aan wie de configuratie het laatst heeft gewijzigd. 

> [!NOTE]
> Als het inschakelen van een peering mislukt, controleert u of de primaire en secundaire subnetten die zijn toegewezen overeenkomen met de configuratie op de gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId,* *AzureASN*en *PeerASN* worden gebruikt op MEEs en of deze waarden worden toegewezen aan de waarden die worden gebruikt op de gekoppelde CE/PE-MSEE. Als MD5 hashing wordt gekozen, moet de gedeelde sleutel hetzelfde zijn op msee en PE-MSEE/CE-paar. Eerder geconfigureerde gedeelde sleutel wordt om veiligheidsredenen niet weergegeven. Als u een van deze configuratie op een MSEE-router moet wijzigen, raadpleegt u [Routering maken en wijzigen voor een ExpressRoute-circuit.][CreatePeering]  
>

> [!NOTE]
> Op een /30-subnet dat is toegewezen voor de interface, kiest Microsoft het tweede bruikbare IP-adres van het subnet voor de MSEE-interface. Zorg er daarom voor dat het eerste bruikbare IP-adres van het subnet is toegewezen aan de peered CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Verificatie via PowerShell
Als u de configuratiegegevens van Azure Private Peering wilt opvragen, gebruikt u de volgende opdrachten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Een voorbeeldreactie voor een met succes geconfigureerde private peering is:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Een peeringcontext met succes wordt weergegeven in de primaire en secundaire adresvoorvoegsels. De /30-subnetten worden gebruikt voor het interface-IP-adres van de ME's en CE's/PE-ME's.

Als u de configuratiegegevens van Azure public peering wilt krijgen, gebruikt u de volgende opdrachten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Als u de configuratiegegevens van Microsoft-peering wilt krijgen, gebruikt u de volgende opdrachten:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Als een peering niet is geconfigureerd, wordt er een foutbericht weergegeven. Een voorbeeldreactie wanneer de vermelde peering (Azure Public-peering in dit voorbeeld) niet is geconfigureerd binnen het circuit:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Als het inschakelen van een peering mislukt, controleert u of de primaire en secundaire subnetten die zijn toegewezen overeenkomen met de configuratie op de gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId,* *AzureASN*en *PeerASN* worden gebruikt op MEEs en of deze waarden worden toegewezen aan de waarden die worden gebruikt op de gekoppelde CE/PE-MSEE. Als MD5 hashing wordt gekozen, moet de gedeelde sleutel hetzelfde zijn op msee en PE-MSEE/CE-paar. Eerder geconfigureerde gedeelde sleutel wordt om veiligheidsredenen niet weergegeven. Als u een van deze configuratie op een MSEE-router moet wijzigen, raadpleegt u [Routering maken en wijzigen voor een ExpressRoute-circuit.][CreatePeering]  
>
>

> [!NOTE]
> Op een /30-subnet dat is toegewezen voor de interface, kiest Microsoft het tweede bruikbare IP-adres van het subnet voor de MSEE-interface. Zorg er daarom voor dat het eerste bruikbare IP-adres van het subnet is toegewezen aan de peered CE/PE-MSEE.
>

## <a name="validate-arp"></a>ARP valideren

De ARP-tabel biedt een toewijzing van het IP-adres en het MAC-adres voor een bepaalde peering. De ARP-tabel voor een ExpressRoute-circuitpeering biedt de volgende informatie voor elke interface (primair en secundair):
* Ip-adres van de on-premises routerinterface toewijzen aan het MAC-adres
* Ip-adres van de ExpressRoute-routerinterface toewijzen aan het MAC-adres
* De leeftijd van de ARP-tabellen voor het toewijzen kan helpen bij het valideren van laag 2-configuratie en het oplossen van problemen met de basislaag 2-connectiviteit.


Zie [ARP-tabellen opvragen in het resourcebeheermodeldocument,][ARP] voor het weergeven van de ARP-tabel van een ExpressRoute-peering en voor het gebruik van de informatie om het probleem met de verbinding met laag 2 op te lossen.


## <a name="validate-bgp-and-routes-on-the-msee"></a>BGP en routes valideren op de MSEE

Als u de routeringstabel van MSEE op het *primaire* pad voor de *context Privéroutering* wilt ophalen, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Een voorbeeld reactie is:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Als de status van een eBGP-peering tussen een MSEE en een CE/PE-MSEE zich in Actief of Inactief bevindt, controleert u of de toegewezen primaire en secundaire peer-subnetten overeenkomen met de configuratie op de gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureAsn*en *PeerAsn* worden gebruikt op MEEs en of deze waarden worden toegewezen aan de waarden die worden gebruikt op de gekoppelde PE-MSEE/ CE. Als MD5 hashing wordt gekozen, moet de gedeelde sleutel hetzelfde zijn op het msee- en CE/PE-MSEE-paar. Als u een van deze configuratie op een MSEE-router moet wijzigen, raadpleegt u [Routering maken en wijzigen voor een ExpressRoute-circuit.][CreatePeering]
>


> [!NOTE]
> Als bepaalde bestemmingen niet bereikbaar zijn via een peering, raadpleegt u de routetabel van de ME's voor de overeenkomstige peeringcontext. Als er een overeenkomend voorvoegsel (kan NATed IP) aanwezig is in de routeringstabel, controleert u of er firewalls/NSG/ACL's op het pad zijn die het verkeer blokkeren.
>


In het volgende voorbeeld ziet u de reactie van de opdracht voor een peering die niet bestaat:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>De verkeersstroom bevestigen
Als u de gecombineerde primaire en secundaire padverkeersstatistieken wilt ontvangen - bytes in en buiten een peering-context, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Een voorbeelduitvoer van de opdracht is:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Een voorbeelduitvoer van de opdracht voor een niet-bestaande peering is:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie of hulp naar de volgende links:

- [Microsoft-ondersteuning][Support]
- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Routering voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logische Express Route-connectiviteit"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Pictogram Alle bronnen"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Pictogram Overzicht"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Voorbeeld van ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Voorbeeld van ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





