---
title: Azure VMware-oplossing van CloudSimple - Een laag 2-netwerk on-premises uitrekken tot Private Cloud
description: Beschrijft hoe u een Layer 2 VPN instellen tussen NSX-T op een CloudSimple Private Cloud en een on-premises standalone NSX Edge-client
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2ddfa9611143d5c3f823539e018c8afc885c6a46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083222"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Workloads migreren met behulp van uitgerekte netwerken van Laag 2

In deze handleiding leert u hoe u Layer 2 VPN (L2VPN) gebruiken om een Layer 2-netwerk uit te rekken van uw on-premises omgeving naar uw CloudSimple Private Cloud. Met deze oplossing kunnen workloads die in uw on-premises VMware-omgeving worden uitgevoerd, worden uitgevoerd naar de Private Cloud in Azure binnen dezelfde subnetadresruimte zonder dat u uw workloads opnieuw hoeft te IP-eren.

L2VPN-gebaseerde stretching van Layer 2-netwerken kan werken met of zonder NSX-gebaseerde netwerken in uw on-premises VMware-omgeving. Als u geen NSX-netwerken hebt voor on-premises workloads, u een zelfstandige NSX Edge Services Gateway gebruiken.

> [!NOTE]
> Deze handleiding behandelt het scenario waarin on-premises en de Private Cloud-datacenters zijn verbonden via Site-to-Site VPN.

## <a name="deployment-scenario"></a>Implementatiescenario

Om uw on-premises netwerk uit te rekken met L2VPN, moet u een L2VPN-server (doel NSX-T Tier0-router) en een L2VPN-client (bron standalone client) configureren.  

In dit implementatiescenario is uw Private Cloud verbonden met uw on-premises omgeving via een Site-to-Site VPN-tunnel waarmee on-premises beheer- en vMotion-subnetten kunnen communiceren met de subnetten Private Cloud-beheer en vMotion. Deze regeling is noodzakelijk voor Cross vCenter vMotion (xVC-vMotion). Een NSX-T Tier0-router wordt geïmplementeerd als L2VPN-server in de Private Cloud.

Standalone NSX Edge wordt geïmplementeerd in uw on-premises omgeving als een L2VPN-client en vervolgens gekoppeld aan de L2VPN-server. Aan elke kant wordt een GRE-tunneleindpunt gemaakt en geconfigureerd om het on-premises Layer 2-netwerk naar uw Private Cloud te 'rekken'. Deze configuratie wordt weergegeven in de volgende figuur.

![Implementatiescenario](media/l2vpn-deployment-scenario.png)

Zie [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) in de VMware-documentatie voor meer informatie over migratie via L2 VPN.

## <a name="prerequisites-for-deploying-the-solution"></a>Voorwaarden voor het implementeren van de oplossing

Controleer of de volgende functies aanwezig zijn voordat u de oplossing implementeert en configureert:

* De on-premises vSphere-versie is 6.7U1+ of 6.5P03+.
* De on-premises vSphere-licentie bevindt zich op Enterprise Plus-niveau (voor vSphere Distributed Switch).
* Identificeer de workload Layer 2-netwerk die moet worden uitgerekt naar uw Private Cloud.
* Identificeer een Layer 2-netwerk in uw on-premises omgeving voor het implementeren van uw L2VPN-clientappliance.
* [Er is al een Private Cloud gemaakt.](create-private-cloud.md)
* De versie van het standalone NSX-T Edge-toestel is compatibel met de NSX-T Manager-versie (NSX-T 2.3.0) die in uw Private Cloud-omgeving wordt gebruikt.
* In het on-premises vCenter is een trunk port-groep gemaakt met ingeschakelde vervalste zendobjecten.
* Een openbaar IP-adres is gereserveerd voor gebruik voor het NSX-T standalone client uplink IP-adres en 1:1 NAT is aanwezig voor vertaling tussen de twee adressen.
* DNS forwarding is ingesteld op de on-premises DNS-servers voor het az.cloudsimple.io domein om naar de Private Cloud DNS-servers te wijzen.
* De RTT-latentie is minder dan of gelijk aan 150 ms, zoals vereist voor vMotion om op de twee sites te werken.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

In de volgende tabel worden ondersteunde vSphere-versies en netwerkadaptertypen weergegeven.  

| vSphere-versie | Bron vSwitch-type | Virtuele NIC-driver | DoelvSwitch-type | Ondersteund? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Alle | Dvs | Alle | Dvs | Ja |
| vSphere 6.7UI of hoger, 6.5P03 of hoger | Dvs | VMXNET3 | N-VDS | Ja |
| vSphere 6.7UI of hoger, 6.5P03 of hoger | Dvs | E1000 | N-VDS | [Niet ondersteund per VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI of 6.5P03, NSX-V of versies onder NSX-T2.2, 6.5P03 of hoger | Alle | Alle | N-VDS | [Niet ondersteund per VWware](https://kb.vmware.com/s/article/56991) |

Met betrekking tot de VMware NSX-T 2.3 release:

* De logische schakelaar op de Private Cloud-kant die is uitgerekt tot on-premises via L2VPN kan niet tegelijkertijd worden gerouteerd. De uitgerekte logische schakelaar kan niet worden aangesloten op een logische router.
* L2VPN en routegebaseerde IPSEC VPN's kunnen alleen worden geconfigureerd met API-aanroepen.

Zie [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) voor meer informatie in de VMware-documentatie.

### <a name="sample-l2-vpn-deployment-addressing"></a>Voorbeeld van L2 VPN-implementatie-adressering

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>On-premises netwerk waar de standalone ESG (L2 VPN-client) wordt ingezet

| **Item** | **Waarde** |
|------------|-----------------|
| Netwerknaam | MGMT_NET_VLAN469 |
| Vlan | 469 |
| Cidr| 10.250.0.0/24 |
| IP-adres voor standalone edge-toestel | 10.250.0.111 |
| Ip-adres van standalone edge-toestel NAT | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>On-premises netwerk dat moet worden uitgerekt

| **Item** | **Waarde** |
|------------|-----------------|
| Vlan | 472 |
| Cidr| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Private Cloud IP-schema voor NSX-T Tier0 Router (L2 VPN server)

| **Item** | **Waarde** |
|------------|-----------------|
| Loopback-interface | 192.168.254.254/32 |
| Tunnelinterface | 5.5.5.1/29 |
| Logische schakelaar (uitgerekt) | Stretch_LS |
| Loopback-interface (NAT IP-adres) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Private Cloud-netwerk in kaart te brengen aan het uitgerekte netwerk

| **Item** | **Waarde** |
|------------|-----------------|
| Vlan | 712 |
| Cidr| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Haal de logische router-id op die nodig is voor L2VPN

De volgende stappen laten zien hoe u de logische router-ID van Tier0 DR logische router ophalen voor bijvoorbeeld de IPsec- en L2VPN-services. De logische router-ID is later nodig bij de implementatie van de L2VPN.

1. Meld u aan bij NSX-T Manager https://*nsx-t-manager-ip-adres* en selecteer > **Netwerkrouters** > **Provider-LR** > **Overzicht**. **Networking** Selecteer **Active-Standby**voor **de modus Hoge beschikbaarheid**. Met deze actie wordt een pop-upvenster geopend waarin de Edge VM wordt weergegeven waarop de Tier0-router momenteel actief is.

    ![Actieve stand-by selecteren](media/l2vpn-fetch01.png)

2. Selecteer Randen**van** > **stofknooppunten** **Fabric** > . Noteer het management-IP-adres van de actieve Edge VM1 (Edge VM1) die in de vorige stap is geïdentificeerd.

    ![Notitiebeheer-IP](media/l2vpn-fetch02.png)

3. Open een SSH-sessie naar het ip-adres van het beheer van de Edge VM. Voer ```get logical-router``` de opdracht uit met **gebruikersnaambeheerder** en wachtwoord **CloudSimple 123!**.

    ![logische routeruitvoer krijgen](media/l2vpn-fetch03.png)

4. Als u geen vermelding 'DR-Provider-LR' ziet, voert u de volgende stappen uit.

5. Maak twee logische switches met overlays. Eén logische schakelaar wordt uitgerekt naar on-premises waar de gemigreerde workloads zich bevinden. Een andere logische schakelaar is een dummy schakelaar. Zie Een [logische switch maken](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) in de VMware-documentatie voor instructies.

    ![Logische switch maken](media/l2vpn-fetch04.png)

6. Koppel de dummy-switch aan de Tier1-router met een lokaal IP-adres van de koppeling of een niet-overlappend subnet van on-premises of uw Private Cloud. Zie [Een Downlink-poort toevoegen op een logische router van tier 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) in de VMware-documentatie.

    ![Dummy-schakelaar koppelen](media/l2vpn-fetch05.png)

7. Voer `get logical-router` de opdracht opnieuw uit op de SSH-sessie van de Edge VM. De UUID van de 'DR-Provider-LR' logische router wordt weergegeven. Noteer de UUID, die nodig is bij het configureren van de L2VPN.

    ![logische routeruitvoer krijgen](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Haal de logische-switch-ID op die nodig is voor L2VPN

1. Meld u aan bij [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Selecteer **Schakelswitches** > **voor** > **netwerken**  >  **<\Overzicht van logische schakelaars\>****Overview** > .
3. Noteer de UUID van de stretch logische schakelaar, die nodig is bij het configureren van de L2VPN.

    ![logische routeruitvoer krijgen](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Route- en beveiligingsoverwegingen voor L2VPN

Om een IPsec-routegebaseerde VPN tussen de NSX-T Tier0-router en de standalone NSX Edge-client te maken, moet de loopback-interface van de NSX-T Tier0-router kunnen communiceren met het openbare IP-adres van NSX standalone client on-premises via UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>UDP 500/4500 toestaan voor IPsec

1. [Maak een openbaar IP-adres](public-ips.md) voor de NSX-T Tier0 loopback-interface in de CloudSimple-portal.

2. [Maak een firewalltabel](firewall.md) met stateful regels waarmee UDP 500/ 4500 binnenkomend verkeer mogelijk is en bevestig de firewalltabel aan het NSX-T HostTransport-subnet.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Adverteer de loopback-interface IP aan het underlay-netwerk

1. Maak een null-route voor het loopback-interfacenetwerk. Meld u aan bij NSX-T Manager en selecteer > **Netwerkrouteringsrouters** > **Routers** > **Provider-LR** > **Routing** > **Static Routes**. **Networking** Klik op**toevoegen**. Voer **voor Netwerk**het IP-adres van de loopback-interface in. Klik voor **Volgende hop**op **Toevoegen**, geef 'Null' op voor de volgende hop en houd de standaardwaarde van 1 voor beheerdersafstand.

    ![Statische route toevoegen](media/l2vpn-routing-security01.png)

2. Maak een IP-voorvoegsellijst. Meld u aan bij NSX-T Manager en selecteer**IP-voorvoegsellijsten****voor** >  **Networking** >  > **netwerkrouteringrouteringroutering-** > **routering** > .**Routing** Klik op**toevoegen**. Voer een naam in om de lijst te identificeren. Klik **bij Voorvoegsels**op Twee maal **toevoegen.** Voer in de eerste regel '0.0.0.0/0' in voor **Netwerk** en 'Weigeren' voor **actie.** Selecteer in de tweede regel **Alle** voor **Netwerk** en **Handelingsvergunning** **.**
3. Voeg de IP-voorvoegsellijst toe aan beide BGP-buren (TOR). Als u de IP-voorvoegsellijst aan de BGP-buurman koppelt, wordt voorkomen dat de standaardroute in BGP wordt geadverteerd aan de TOR-switches. Echter, elke andere route die de null route omvat zal adverteren de loopback interface IP-adres aan de TOR-switches.

    ![Lijst met IP-voorvoegsel maken](media/l2vpn-routing-security02.png)

4. Meld u aan bij NSX-T Manager en selecteer **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **BGP** > **Neighbors**. Selecteer de eerste buur. Klik **op** > **Adresfamilies bewerken**. Bewerk voor de IPv4-familie de kolom **Uitfilter** en selecteer de IP-voorvoegsellijst die u hebt gemaakt. Klik op **Opslaan**. Herhaal deze stap voor de tweede buurman.

    ![IP-voorvoegsellijst](media/l2vpn-routing-security03.png) ![toevoegen 1 IP-voorvoegsellijst bijvoegen 2](media/l2vpn-routing-security04.png)

5. Herverdelen van de null statische route in BGP. Als u de loopback-interfaceroute naar de ondervloer wilt adverteren, moet u de null statische route opnieuw distribueren naar BGP. Meld u aan bij NSX-T Manager en selecteer > **Netwerkrouteringsrouters** > **Routers** > **Provider-LR** > **Routing** > **Route Herdistributie** > **Buren**. **Networking** Selecteer **Provider-LR-Route_Redistribution** en klik op **Bewerken**. Schakel het selectievakje **Statisch** in en klik op **Opslaan**.

    ![Null-statische route opnieuw distribueren naar BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Een routegebaseerde VPN configureren op de NSX-T Tier0-router

Gebruik de volgende sjabloon om alle details in te vullen voor het configureren van een routegebaseerde VPN op de NSX-T Tier0-router. De uuids in elke POST-oproep zijn vereist in volgende POST-oproepen. De IP-adressen voor de loopback- en tunnelinterfaces voor L2VPN moeten uniek zijn en niet overlappen met de on-premises of Private Cloud-netwerken.

De IP-adressen die worden gekozen voor loopback- en tunnelinterface die voor L2VPN wordt gebruikt, moeten uniek zijn en niet overlappen met de on-premises of Private Cloud-netwerken. Het loopback-interfacenetwerk moet altijd /32 zijn.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Vervang voor alle volgende API-aanroepen het IP-adres door uw IP-adres van NSX-T Manager. U al deze API-aanroepen uitvoeren `curl` vanuit de POSTMAN-client of met opdrachten.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Schakel de IPSec VPN-service in op de logische router

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Profielen maken: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Profielen maken: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Profielen maken: Tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Een lokaal eindpunt maken

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Een peer endpoint maken

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Een op route gebaseerde VPN-sessie maken

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>L2VPN configureren op NSX-T Tier0-router

Vul na elke POST-oproep de volgende informatie in. De id's zijn vereist in de volgende POST-oproepen.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>De L2VPN-service maken

De uitvoer van de volgende opdracht GET is leeg, omdat de configuratie nog niet is voltooid.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Voor de volgende post-opdracht is de logische router-id de UUID van de eerder verkregen Tier0 DR-logische router.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>De L2VPN-sessie maken

Voor de volgende POST-opdracht is de L2VPN-service-ID de ID die u zojuist hebt verkregen en de IPsec VPN-sessie-ID is de ID die in de vorige sectie is verkregen.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Deze aanroepen creëren een GRE-tunneleindpunt. Voer de volgende opdracht uit om de status te controleren.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Logische poort maken met de opgegeven tunnel-id

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>De peer code voor L2VPN verkrijgen aan de NSX-T kant

De peercode van het NSX-T-eindpunt verkrijgen. De peercode is vereist bij het configureren van het externe eindpunt. De L2VPN <sessie-id> kan worden verkregen uit de vorige sectie. Zie voor meer informatie de [NSX-T 2.3 API Guide](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>De zelfstandige NSX-T-client implementeren (on-premises)

Controleer voordat u deze implementeert of uw on-premises firewallregels inkomend en uitgaand UDP 500/4500-verkeer van/naar het openbare IP-adres van CloudSimple toestaan dat eerder was gereserveerd voor de lusback-interface van de NSX-T T0-router. 

1. [De zelfstandige NSX Edge-client downloaden](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF en Haal de bestanden uit de gedownloade bundel in een map.

    ![Zelfstandige NSX Edge-client downloaden](media/l2vpn-deploy-client01.png)

2. Ga naar de map met alle uitgepakte bestanden. Selecteer alle vmdks (NSX-l2t-client-large.mf en NSX-l2t-client-large.ovf voor grote toestelgrootte of NSX-l2t-client-Xlarge.mf en NSX-l2t-client-Xlarge.ovf voor extra grote toestelgrootte). Klik op **Volgende**.

    ![Sjabloon](media/l2vpn-deploy-client02.png) ![selecteren sjabloon selecteren selecteren](media/l2vpn-deploy-client03.png)

3. Voer een naam in voor de zelfstandige NSX-T-client en klik op **Volgende**.

    ![Sjabloonnaam invoeren](media/l2vpn-deploy-client04.png)

4. Klik zo nodig op **Volgende** om de instellingen van de datastore te bereiken. Selecteer het juiste gegevensarchief voor nsx-t zelfstandige client en klik op **Volgende**.

    ![Gegevensarchief selecteren](media/l2vpn-deploy-client06.png)

5. Selecteer de juiste poortgroepen voor Trunk (Trunk PG), Public (Uplink PG) en HA-interface (Uplink PG) voor de NSX-T standalone client. Klik op **Volgende**.

    ![Poortgroepen selecteren](media/l2vpn-deploy-client07.png)

6. Vul de volgende details in het sjabloonscherm **Aanpassen** en klik op **Volgende:**

    L2T uitbreiden:

    * **Peer-adres**. Voer het IP-adres in dat is gereserveerd op de Azure CloudSimple-portal voor de NSX-T Tier0 Loopback-interface.
    * **Peer Code**. Plak de peercode verkregen uit de laatste stap van de implementatie van l2VPN-server.
    * **Sub Interfaces VLAN (Tunnel ID)**. Voer de VLAN-id in die moet worden uitgerekt. Tussen haakjes () voer u de tunnel-ID in die eerder is geconfigureerd.

    Uplink-interface uitbreiden:

    * **DNS IP-adres**. Voer het on-premises DNS-IP-adres in.
    * **Standaardgateway**.  Voer de standaardgateway van de VLAN in die fungeert als standaardgateway voor deze client.
    * **IP-adres**. Voer het uplink IP-adres van de zelfstandige client in.
    * **Voorvoegsellengte**. Voer de voorvoegsellengte van het uplink VLAN/subnet in.
    * **CLI-beheerder/enable/root-gebruikerswachtwoord**. Stel het wachtwoord in voor het /enable /root-account van de beheerder.

      ![Sjabloon](media/l2vpn-deploy-client08.png)
      ![sjabloon aanpassen sjabloon aanpassen - meer](media/l2vpn-deploy-client09.png)

7. Controleer de instellingen en klik op **Voltooien**.

    ![Volledige configuratie](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Een on-premises sinkpoort configureren

Als een van de VPN-sites geen NSX heeft geïmplementeerd, u een L2 VPN configureren door een standalone NSX Edge op die site te implementeren. Een zelfstandige NSX Edge wordt geïmplementeerd met behulp van een OVF-bestand op een host die niet wordt beheerd door NSX. Hiermee wordt een NSX Edge Services Gateway-toestel geïmplementeerd om te functioneren als een L2 VPN-client.

Als een standalone edge trunk vNIC is aangesloten op een vSphere Distributed Switch, is promiscue modus of een sink port vereist voor L2 VPN-functie. Het gebruik van de promiscue modus kan dubbele pings en dubbele reacties veroorzaken. Gebruik daarom de sinkport-modus in de zelfstandige NSX Edge-configuratie van L2 VPN. Zie de [sinkpoort configureren](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) in de VMware-documentatie.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN en L2VPN-verificatie

Gebruik de volgende opdrachten om IPsec- en L2VPN-sessies te verifiëren vanaf standalone NSX-T Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Gebruik de volgende opdrachten om IPsec- en L2VPN-sessies te verifiëren vanaf de NSX-T Tier0-router.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Gebruik de volgende opdrachten om de sinkport op de ESXi-host te verifiëren waar de NSX-T standalone client VM zich in de on-premises omgeving bevindt.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
