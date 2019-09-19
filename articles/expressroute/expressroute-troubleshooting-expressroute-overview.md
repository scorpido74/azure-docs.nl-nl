---
title: 'Connectiviteit controleren-ExpressRoute-probleemoplossings gids: Azure| Microsoft Docs'
description: Op deze pagina vindt u instructies voor het oplossen van problemen en het valideren van end-to-end-connectiviteit van een ExpressRoute-circuit.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123446"
---
# <a name="verifying-expressroute-connectivity"></a>Connectiviteit ExpressRoute controleren
Dit artikel helpt u bij het controleren en oplossen van problemen met ExpressRoute-connectiviteit. ExpressRoute, dat een on-premises netwerk uitbreidt naar de micro soft-Cloud via een particuliere verbinding die wordt vereenvoudigd door een connectiviteits provider, omvat de volgende drie afzonderlijke netwerk zones:

-   Klantnetwerk
-   Provider netwerk
-   Micro soft Data Center

Het doel van dit document is om de gebruiker te helpen bij het identificeren van de locatie waar (of zelfs als) een connectiviteits probleem bestaat en binnen welke zone, waardoor hulp wordt gezocht van het juiste team om het probleem op te lossen. Als er ondersteuning voor micro soft nodig is om een probleem op te lossen, opent u een ondersteunings ticket met [Microsoft ondersteuning][Support].

> [!IMPORTANT]
> Dit document is bedoeld om eenvoudige problemen op te sporen en te verhelpen. Het is niet bedoeld als vervanging van micro soft-ondersteuning. Open een ondersteunings ticket met [Microsoft ondersteuning][Support] als u het probleem niet kunt oplossen met behulp van de meegeleverde richt lijnen.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
In het volgende diagram ziet u de logische verbinding van een klanten netwerk met het micro soft-netwerk met behulp van ExpressRoute.
[![1]][1]

In het voor gaande diagram duiden de getallen op belang rijke netwerk punten. In dit artikel wordt regel matig naar de netwerk punten verwezen met het bijbehorende aantal.

Afhankelijk van het ExpressRoute-connectiviteits model (co-locatie, Point-to-Point Ethernet-verbinding in de Cloud en een wille keurige (IPVPN)), kunnen de netwerk punten 3 en 4 switches zijn (laag 2-apparaten). De belangrijkste netwerk punten worden als volgt geïllustreerd:

1.  Klant Compute Device (bijvoorbeeld een server of PC)
2.  CEs Klant-Edge-routers 
3.  PEs (CE gericht): Provider Edge routers/switches die zijn gericht zijn op klanten Edge-routers. In dit document wordt PE-CEs genoemd.
4.  Wachtwoordexportserver (MSEE gericht): Provider Edge routers/switches die zijn gericht op Msee's. In dit document wordt ' PE-Msee's ' genoemd.
5.  Msee's Micro soft Enter prise Edge (MSEE) ExpressRoute-routers
6.  Virtual Network (VNet)-gateway
7.  Compute-apparaat op het Azure-VNet

Als de co-locatie of Point-to-Point-verbindings modellen van de Cloud Exchange worden gebruikt, stelt de klant edge-router (2) BGP-peering in met Msee's (5). Netwerk punten 3 en 4 kunnen nog steeds bestaan, maar zijn enigszins transparant als laag-2-apparaten.

Als het verbindings model any-to-any (IPVPN) wordt gebruikt, stelt de PEs (MSEE tegenover) (4) BGP-peering in met Msee's (5). Routes worden vervolgens via het netwerk van de IPVPN-service provider teruggegeven aan het netwerk van de klant.

> [!NOTE]
>Voor een hoge Beschik baarheid van ExpressRoute vereist micro soft een redundant paar BGP-sessies tussen Msee's (5) en PE-Msee's (4). Er wordt ook een redundant paar netwerk paden aanbevolen tussen het netwerk van de klant en de PE-CEs. In any-to-any (IPVPN)-verbindings model kan één CE-apparaat (2) echter worden verbonden met een of meer PEs (3).
>
>

Als u een ExpressRoute-circuit wilt valideren, worden de volgende stappen behandeld (met het netwerk punt dat wordt aangegeven door het gekoppelde nummer):
1. [Circuit inrichting en-status valideren (5)](#validate-circuit-provisioning-and-state)
2. [Controleren of ten minste één ExpressRoute-peering is geconfigureerd (5)](#validate-peering-configuration)
3. [ARP valideren tussen micro soft en de service provider (koppeling tussen 4 en 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Valideer BGP en routes op de MSEE (BGP tussen 4 en 5 en 5 tot 6 als er een VNet is verbonden)](#validate-bgp-and-routes-on-the-msee)
5. [Controleer de verkeers statistieken (verkeer dat door 5 wordt door gegeven)](#check-the-traffic-statistics)

Meer validaties en controles zullen in de toekomst worden toegevoegd. Controleer de maandelijkse back-up.

## <a name="validate-circuit-provisioning-and-state"></a>Circuit inrichting en-status valideren
Ongeacht het verbindings model moet er een ExpressRoute-circuit worden gemaakt en dus een service sleutel gegenereerd voor het inrichten van een circuit. Bij het inrichten van een ExpressRoute-circuit worden een redundante Layer 2-verbinding tussen PE-Msee's (4) en Msee's (5) tot stand gebracht. Zie het artikel [een ExpressRoute-circuit maken en wijzigen][CreateCircuit]voor meer informatie over het maken, wijzigen, inrichten en verifiëren van een ExpressRoute-circuit.

>[!TIP]
>Een service sleutel is een unieke aanduiding voor een ExpressRoute-circuit. Deze sleutel is vereist voor het meren deel van de Power shell-opdrachten die in dit document worden genoemd. Als u hulp nodig hebt van micro soft of van een ExpressRoute-partner om een ExpressRoute-probleem op te lossen, geeft u de service sleutel op om het circuit eenvoudig te identificeren.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure Portal
In de Azure Portal, de status van een ExpressRoute-circuit kan worden gecontroleerd door ![2][2] te selecteren in het menu aan de linkerkant en vervolgens op het ExpressRoute-circuit te selecteren. Als u een ExpressRoute-circuit selecteert onder alle resources, wordt de Blade ExpressRoute-circuit geopend. In het gedeelte ![3][3] van de Blade worden de ExpressRoute Essentials weer gegeven, zoals wordt weer gegeven in de volgende scherm afbeelding:

![4][4]    

In de ExpressRoute Essentials geeft de status van het *circuit* de status aan van het circuit aan de kant van micro soft. *Provider status* geeft aan of het circuit is *ingericht/niet is ingericht* op de kant van de service provider. 

Voor een ExpressRoute-circuit moet de status van *het circuit* zijn *ingeschakeld* en moet de *status* van de provider worden *ingericht*.

> [!NOTE]
> Als de *status* van het circuit niet is ingeschakeld, neemt u contact op met [Microsoft ondersteuning][Support]. Als de *status* van de provider niet is ingericht, neemt u contact op met uw service provider.
>
>

### <a name="verification-via-powershell"></a>Verificatie via Power shell
Als u alle ExpressRoute-circuits in een resource groep wilt weer geven, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>U kunt de naam van de resource groep ophalen via Azure. Zie de vorige subsectie van dit document en Let op dat de naam van de resource groep wordt weer gegeven in de scherm afbeelding voor beeld.
>
>

Als u een bepaald ExpressRoute-circuit wilt selecteren in een resource groep, gebruikt u de volgende opdracht:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Een voor beeld van een antwoord is:

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

Ga als volgt te werk om te controleren of een ExpressRoute-circuit operationeel is, Let vooral op de volgende velden:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Als de *CircuitProvisioningState* niet is ingeschakeld, neemt u contact op met [Microsoft ondersteuning][Support]. Als de *ServiceProviderProvisioningState* niet is ingericht, neemt u contact op met uw service provider.
>
>

### <a name="verification-via-powershell-classic"></a>Verificatie via Power shell (klassiek)
Als u alle ExpressRoute-circuits onder een abonnement wilt weer geven, gebruikt u de volgende opdracht:

    Get-AzureDedicatedCircuit

Als u een bepaald ExpressRoute-circuit wilt selecteren, gebruikt u de volgende opdracht:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Een voor beeld van een antwoord is:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Ga als volgt te werk om te controleren of een ExpressRoute-circuit operationeel is, Let vooral op de volgende velden: ServiceProviderProvisioningState : Ingerichte status: Enabled

> [!NOTE]
> Als de *status* niet is ingeschakeld, neemt u contact op met [Microsoft ondersteuning][Support]. Als de *ServiceProviderProvisioningState* niet is ingericht, neemt u contact op met uw service provider.
>
>

## <a name="validate-peering-configuration"></a>Peering-configuratie valideren
Nadat de service provider de inrichting van het ExpressRoute-circuit heeft voltooid, kan een routerings configuratie worden gemaakt over het ExpressRoute-circuit tussen MSEE-pull (4) en Msee's (5). Elk ExpressRoute-circuit kan een, twee of drie routerings contexten hebben ingeschakeld: Persoonlijke Azure-peering (verkeer naar particuliere virtuele netwerken in Azure), open bare Azure-peering (verkeer naar open bare IP-adressen in Azure) en micro soft-peering (verkeer naar Office 365). Zie voor meer informatie over het maken en wijzigen van routerings configuratie het artikel [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure Portal

> [!NOTE]
> Als laag 3 wordt opgegeven door de service provider en de peerings zijn leeg in de portal, vernieuwt u de circuit configuratie met behulp van de knop Vernieuwen in de portal. Met deze bewerking wordt de juiste routerings configuratie op uw circuit toegepast. 
>
>

In de Azure Portal kunt u de status van een ExpressRoute-circuit controleren door ![2][2] te selecteren in het menu aan de linkerkant en vervolgens het ExpressRoute-circuit te selecteren. Als u een ExpressRoute-circuit selecteert onder alle resources, wordt de Blade ExpressRoute circuit geopend. In het gedeelte ![3][3] van de Blade wordt de ExpressRoute Essentials weer gegeven, zoals wordt weer gegeven in de volgende scherm afbeelding:

![5][5]

In het voor gaande voor beeld is de genoteerde Azure private peering-routerings context ingeschakeld, terwijl Azure Public-en micro soft-peering routing-contexten niet zijn ingeschakeld. Een geslaagde peering context heeft ook de primaire en secundaire Point-to-Point (vereist voor BGP)-subnetten vermeld. De/30 subnetten worden gebruikt voor het IP-adres van de interface van de Msee's en PE-Msee's. 

> [!NOTE]
> Als er geen peering is ingeschakeld, controleert u of de primaire en secundaire subnetten die zijn toegewezen overeenkomen met de configuratie op PE-Msee's. Als dat niet het geval is, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering] als u de configuratie van MSEE-routers wilt wijzigen
>
>

### <a name="verification-via-powershell"></a>Verificatie via Power shell
Gebruik de volgende opdrachten om de configuratie gegevens van de persoonlijke Azure-peering te verkrijgen:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Een voor beeld van een antwoord voor een correct geconfigureerde persoonlijke peering is:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Voor een geslaagde peering context zouden de primaire en secundaire adres voorvoegsels worden weer gegeven. De/30 subnetten worden gebruikt voor het IP-adres van de interface van de Msee's en PE-Msee's.

Gebruik de volgende opdrachten om de configuratie gegevens voor de open bare Azure-peering te verkrijgen:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Gebruik de volgende opdrachten om de configuratie gegevens van de micro soft-peering te verkrijgen:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Als er geen peering is geconfigureerd, wordt er een fout bericht weer gegeven. Een voor beeld van een antwoord wanneer de vermelde peering (open bare Azure-peering in dit voor beeld) niet is geconfigureerd binnen het circuit:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Als er geen peering is ingeschakeld, controleert u of de primaire en secundaire subnetten die zijn toegewezen, overeenkomen met de configuratie van de gekoppelde PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureASN*en *PeerASN* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde PE-MSEE. Als MD5-hashing is gekozen, moet de gedeelde sleutel gelijk zijn aan het MSEE-en PE-MSEE-paar. Als u de configuratie van de MSEE-routers wilt wijzigen, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verificatie via Power shell (klassiek)
Als u de configuratie details van de persoonlijke Azure-peering wilt ophalen, gebruikt u de volgende opdracht:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Een voor beeld van een antwoord voor een geslaagde, persoonlijke peering is:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Bij een geslaagde peering-context zouden de primaire en secundaire peer-subnetten worden weer gegeven. De/30 subnetten worden gebruikt voor het IP-adres van de interface van de Msee's en PE-Msee's.

Gebruik de volgende opdrachten om de configuratie gegevens voor de open bare Azure-peering te verkrijgen:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Gebruik de volgende opdrachten om de configuratie gegevens van de micro soft-peering te verkrijgen:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Als laag 3 peerings zijn ingesteld door de service provider, worden de instellingen van de service provider overschreven door de ExpressRoute-peerings via de portal of Power shell. Voor het opnieuw instellen van de instellingen voor peering aan de provider moet de service provider worden ondersteund. Wijzig de ExpressRoute-peerings alleen als u zeker weet dat de service provider alleen Layer 2-services levert.
>
>

> [!NOTE]
> Als er geen peering is ingeschakeld, controleert u of de primaire en secundaire peer-subnetten zijn toegewezen die overeenkomen met de configuratie van de gekoppelde PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureAsn*en *PeerAsn* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde PE-MSEE. Als u de configuratie van de MSEE-routers wilt wijzigen, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>ARP valideren tussen micro soft en de service provider
In deze sectie wordt gebruikgemaakt van Power shell-opdrachten (klassiek). Als u Power shell-Azure Resource Manager opdrachten hebt gebruikt, moet u ervoor zorgen dat u beheerder/co-beheerders toegang hebt tot het abonnement. Raadpleeg de [ARP-tabellen ophalen in het Resource Manager-implementatie model][ARP] document voor probleem oplossing met behulp van Azure Resource Manager-opdrachten.

> [!NOTE]
>Als u ARP wilt ophalen, kunt u zowel de Azure Portal als Azure Resource Manager Power shell-opdrachten gebruiken. Als er fouten optreden met de Azure Resource Manager Power shell-opdrachten, werken klassieke Power shell-opdrachten als klassieke Power shell-opdrachten werken ook met Azure Resource Manager ExpressRoute-circuits.
>
>

Als u de ARP-tabel van de primaire MSEE-router voor de persoonlijke peering wilt ophalen, gebruikt u de volgende opdracht:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Een voor beeld van een antwoord voor de opdracht, in het geslaagde scenario:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Op dezelfde manier kunt u de ARP-tabel controleren op basis van de MSEE in het *primaire*/*secundaire* pad, voor *privé*/*open bare*/*micro soft* -peerings.

In het volgende voor beeld ziet u het antwoord van de opdracht voor een peering bestaat niet.

    ARP Info:
       
> [!NOTE]
> Als de ARP-tabel geen IP-adressen heeft van de interfaces die zijn toegewezen aan MAC-adressen, controleert u de volgende informatie:
>1. Als het eerste IP-adres van het/30-subnet dat is toegewezen voor de koppeling tussen de MSEE-PR en MSEE, wordt gebruikt op de interface van MSEE-PR. Azure maakt altijd gebruik van het tweede IP-adres voor Msee's.
>2. Controleer of de VLAN-labels van de klant (C-tag) en de service (S-tag) overeenkomen met het MSEE-PR-en MSEE-paar.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>BGP en routes op de MSEE valideren
In deze sectie wordt gebruikgemaakt van Power shell-opdrachten (klassiek). Als u Power shell-Azure Resource Manager opdrachten hebt gebruikt, moet u ervoor zorgen dat u beheerder/co-beheerders toegang hebt tot het abonnement.

> [!NOTE]
>Als u BGP-gegevens wilt ophalen, kunt u zowel de Azure Portal als Azure Resource Manager Power shell-opdrachten gebruiken. Als er fouten optreden met de Azure Resource Manager Power shell-opdrachten, werken klassieke Power shell-opdrachten als klassieke Power shell-opdrachten werken ook met Azure Resource Manager ExpressRoute-circuits.
>
>

Als u de samen vatting van de routerings tabel (BGP-neighbor) wilt ophalen voor een bepaalde routerings context, gebruikt u de volgende opdracht:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Een voor beeld van een antwoord is:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Zoals u in het vorige voor beeld kunt zien, is de opdracht nuttig om te bepalen hoe lang de routerings context tot stand is gebracht. Ook wordt het aantal route voorvoegsels aangegeven dat door de peering router wordt geadverteerd.

> [!NOTE]
> Als de status actief of inactief is, controleert u of de primaire en secundaire peer-subnetten die zijn toegewezen, overeenkomen met de configuratie van de gekoppelde PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureAsn*en *PeerAsn* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde PE-MSEE. Als MD5-hashing is gekozen, moet de gedeelde sleutel gelijk zijn aan het MSEE-en PE-MSEE-paar. Als u de configuratie van de MSEE-routers wilt wijzigen, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].
>
>

> [!NOTE]
> Als bepaalde doelen niet bereikbaar zijn via een bepaalde peering, controleert u de route tabel van de Msee's die deel uitmaakt van de specifieke peering context. Als er een overeenkomend voor voegsel (mogelijk IP-adres) aanwezig is in de routerings tabel, controleert u of er firewalls/NSG/Acl's zijn op het pad en of het verkeer is toegestaan.
>
>

Gebruik de volgende opdracht om de volledige routerings tabel op te halen uit MSEE op het *primaire* pad voor de specifieke routerings context van een *particulier* :

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Een voor beeld van een geslaagd resultaat van de opdracht is:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Op dezelfde manier kunt u de routerings tabel controleren op basis van de MSEE in het *primaire*/*secundaire* pad, voor *privé*/-*open bare*/*micro soft* -peering-context.

In het volgende voor beeld ziet u het antwoord van de opdracht voor een peering bestaat niet:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>De verkeers statistieken controleren
Gebruik de volgende opdracht om de statistische gegevens over het gecombineerde primaire en secundaire traject te verkrijgen: bytes in en out--van een peering-context.

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Een voor beeld van een uitvoer van de opdracht is:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Een voorbeeld uitvoer van de opdracht voor een niet-bestaande peering is:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie en hulp:

- [Microsoft Ondersteuning][Support]
- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Route ring voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png  "Connectiviteit voor logische Express-route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Pictogram alle resources"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Pictogram overzicht"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Voorbeeld scherm van ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Voorbeeld scherm van ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






