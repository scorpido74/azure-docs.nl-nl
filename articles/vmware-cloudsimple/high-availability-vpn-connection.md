---
title: Azure VMware Solution by CloudSimple - Configureer hoge beschikbaarheid van on-premises naar CloudSimple VPN-gateway
description: Beschrijft hoe u een verbinding met hoge beschikbaarheid configureert vanuit uw on-premises omgeving naar een CloudSimple VPN-gateway die is ingeschakeld voor hoge beschikbaarheid
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025262"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Een verbinding met hoge beschikbaarheid configureren van on-premises naar CloudSimple VPN-gateway

Netwerkbeheerders kunnen een iPsec-vpn-verbinding met hoge beschikbaarheid configureren van hun on-premises omgeving naar een CloudSimple VPN-gateway.

Deze handleiding bevat stappen om een on-premises firewall te configureren voor een IPsec Site-to-Site VPN-verbinding met hoge beschikbaarheid. De gedetailleerde stappen zijn specifiek voor het type on-premises firewall. Als voorbeelden geeft deze handleiding stappen weer voor twee soorten firewalls: Cisco ASA en Palo Alto Networks.

## <a name="before-you-begin"></a>Voordat u begint

Voltooi de volgende taken voordat u de on-premises firewall configureert.

1. Controleer of uw organisatie de vereiste knooppunten heeft [ingericht](create-nodes.md) en ten minste één CloudSimple Private Cloud heeft gemaakt.
2. [Configureer een Site-to-Site VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises netwerk en uw CloudSimple Private Cloud.

Zie [VPN-gateways overzicht](cloudsimple-vpn-gateways.md) voor ondersteunde fase 1 en fase 2 voorstellen.

## <a name="configure-on-premises-cisco-asa-firewall"></a>On-premises Cisco ASA-firewall configureren

De instructies in deze sectie zijn van toepassing op Cisco ASA versie 8.4 en hoger. In het configuratievoorbeeld wordt Cisco Adaptive Security Appliance Software Version 9.10 geïmplementeerd en geconfigureerd in IKEv1-modus.

Als u de Site-to-Site VPN wilt laten werken, moet u UDP 500/4500 en ESP (IP-protocol 50) toestaan vanuit de Primaire en Secundaire Openbare IP (Peer IP) cloudsimple op de externe interface van de on-premises Cisco ASA VPN-gateway.

### <a name="1-configure-phase-1-ikev1"></a>1. Fase 1 configureren (IKEv1)

Voer de volgende CLI-opdracht in de Cisco ASA-firewall in om fase 1 (IKEv1) op de buiteninterface in te schakelen.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Maak een IKEv1-beleid

Maak een IKEv1-beleid dat de algoritmen en methoden definieert die moeten worden gebruikt voor hashing, authenticatie, Diffie-Hellman-groep, levensduur en versleuteling.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Een tunnelgroep maken

Maak een tunnelgroep onder de IPsec-kenmerken. Configureer het peer IP-adres en de vooraf gedeelde tunnelsleutel, die u instelt bij [het configureren van uw Site-to-Site VPN-gateway.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Fase 2 (IPsec) configureren

Als u fase 2 (IPsec) wilt configureren, maakt u een toegangscontrolelijst (ACL) waarin het verkeer wordt gedefinieerd dat moet worden versleuteld en getunneld. In het volgende voorbeeld is het verkeer van belang afkomstig van de tunnel die afkomstig is van het lokale subnet (10.16.1.0/24) naar het remote subnet private cloud (192.168.0.0/24). De ACL kan meerdere vermeldingen bevatten als er meerdere subnetten tussen de sites zijn.

In Cisco ASA-versies 8.4 en hoger kunnen objecten of objectgroepen worden gemaakt die dienen als containers voor de netwerken, subnetten, host-IP-adressen of meerdere objecten. Maak een object voor de lokale en een object voor de externe subnetten en gebruik ze voor de crypto ACL en de NAT-instructies.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Een on-premises lokaal subnet definiëren als object

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Het externe subnet CloudSimple definiëren als object

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Een toegangslijst configureren voor het verkeer van belang

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. De transformatieset configureren

Configureer de transformatieset (TS), ```ikev1```waarbij het trefwoord moet worden betrokken. De versleutelings- en hash-kenmerken die in de TS zijn opgegeven, moeten overeenkomen met de parameters die worden vermeld in [standaardconfiguratie voor CloudSimple VPN-gateways.](cloudsimple-vpn-gateways.md)

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. De cryptokaart configureren

Configureer de cryptokaart, die deze componenten bevat:

* Peer IP-adres
* Gedefinieerde ACL die het verkeer van belang bevat
* Transformatieset

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. De cryptokaart toepassen

Pas de cryptokaart toe op de externe interface:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Bevestig de toepasselijke NAT-regels

Het volgende is de NAT-regel die wordt gebruikt. Zorg ervoor dat het VPN-verkeer niet wordt onderworpen aan een andere NAT-regel.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Voorbeeld van IPsec Site-to-Site VPN gevestigde uitvoer van Cisco ASA

Fase 1-uitvoer:

![Fase 1-uitvoer voor Cisco ASA-firewall](media/ha-vpn-connection-cisco-phase1.png)

Fase 2-output:

![Fase 2-uitvoer voor Cisco ASA-firewall](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>On-premises Palo Alto Networks-firewall configureren

De instructies in deze sectie zijn van toepassing op Palo Alto Networks versie 7.1 en hoger. In dit configuratievoorbeeld wordt Palo Alto Networks VM-Series Software Version 8.1.0 geïmplementeerd en geconfigureerd in IKEv1-modus.

Als u de Site-to-Site VPN wilt laten werken, moet u UDP 500/4500 en ESP (IP-protocol 50) toestaan vanuit de Primaire en Secundaire Openbare IP (Peer IP) cloudsimple op de externe interface van de on-premises Palo Alto Networks-gateway.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Primaire en secundaire tunnelinterfaces maken

Meld u aan bij de Palo Alto-firewall, selecteer > **Netwerkinterfaces** > **Tunnel** >  **Network****toevoegen,** configureer de volgende velden en klik op **OK**.

* Interfacenaam. Het eerste veld wordt automatisch gevuld met trefwoord 'tunnel'. Voer in het aangrenzende veld een willekeurig getal in tussen 1 en 9999. Deze interface zal worden gebruikt als een primaire tunnelinterface om site-to-site verkeer tussen het on-premises datacenter en de Private Cloud te vervoeren.
* Commentaar. Voer opmerkingen in voor eenvoudige identificatie van het doel van de tunnel
* Netflow Profiel. Gebruik de standaardinstelling.
* Config. Interface toewijzen aan: virtuele router: **standaard selecteren**. 
        Beveiligingszone: selecteer de zone voor vertrouwd LAN-verkeer. In dit voorbeeld is de naam van de zone voor LAN-verkeer 'Vertrouwen'.
* IPv4. Klik **op Toevoegen** en voeg een niet-overlappend ongebruikt /32 ip-adres toe in uw omgeving, dat wordt toegewezen aan de primaire tunnelinterface en wordt gebruikt voor het bewaken van de tunnels (later uitgelegd).

Omdat deze configuratie is voor een VPN met hoge beschikbaarheid, zijn twee tunnelinterfaces vereist: een primaire en een secundaire. Herhaal de vorige stappen om de secundaire tunnelinterface te maken. Selecteer een andere tunnel-ID en een ander ongebruikt ip-adres van 32.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Het instellen van statische routes voor Private Cloud-subnetten die via de Site-to-Site VPN kunnen worden bereikt

Routes zijn nodig om de on-premises subnetten te bereiken voor cloudsimple private cloud subnetten.

 > Selecteer >  > **Static Routes** >  **OK** **Netwerkvirtuele****routers***standaard*statische routes**toevoegen,** configureer de volgende velden en klik op OK .

* Naam. Voer een naam in om het doel van de route eenvoudig te kunnen identificeren.
* Bestemming. Geef de CloudSimple private cloud-subnetten op die vanaf on-premises via S2S-tunnelinterfaces moeten worden bereikt
* Interface. Selecteer de primaire tunnelinterface die in stap-1(sectie-2) is gemaakt in de vervolgkeuzelijst. In dit voorbeeld is het tunnel.20.
* Volgende Hop. Selecteer **Geen**.
* Afstand admin. Gebruik de standaardinstelling.
* Metrische. Voer een waarde in van 1 tot 65535. De sleutel is om lagere metrische gegevens in te voeren voor de route die overeenkomt met de primaire tunnelinterface in vergelijking met de route overeenkomstige secundaire tunnelinterface waardoor de voormalige route de voorkeur heeft. Als tunnel.20 een metrische waarde heeft van 20 in tegenstelling tot een metrische waarde van 30 voor tunnel.30, heeft tunnel.20 de voorkeur.
* Routetabel. Gebruik de standaardinstelling.
* BFD Profiel. Gebruik de standaardinstelling.
* Padbewaking. Schakel dit selectievakje niet in.

Herhaal de vorige stappen om een andere route te maken voor Private Cloud-subnetten om te gebruiken als secundaire/back-uproute via de secundaire tunnelinterface. Selecteer deze keer een andere tunnel-ID en een hogere statistiek dan voor de primaire route.

### <a name="3-define-the-cryptographic-profile"></a>3. Definieer het cryptografische profiel

Definieer een cryptografisch profiel dat de protocollen en algoritmen voor identificatie, authenticatie en versleuteling opgeeft die moeten worden gebruikt voor het instellen van VPN-tunnels in IKEv1 Fase 1.

Selecteer **Netwerkprofielen** > **Expand Network Profiles** > **uitvouwen IKE Crypto** > **Toevoegen,** configureer de volgende velden en klik op **OK**.

* Naam. Voer een naam in van het IKE-cryptoprofiel.
* DH Groep. Klik **op Toevoegen** en selecteer de juiste DH-groep.
* Versleuteling. Klik **op Toevoegen** en selecteer de juiste versleutelingsmethode.
* Verificatie. Klik **op Toevoegen** en selecteer de juiste verificatiemethode.
* Sleutellevensduur. Gebruik de standaardinstelling.
* IKEv2 Authenticatie Meerdere. Gebruik de standaardinstelling.

### <a name="4-define-ike-gateways"></a>4. IKE-gateways definiëren

Definieer IKE-gateways om de communicatie tussen de peers over elk uiteinde van de VPN-tunnel tot stand te brengen.

Selecteer > **Netwerkuitbreidnetwerkprofielen** > **IKE-gateways** > **Toevoegen,** configureer de volgende velden en klik op **OK**. **Network**

Tabblad Algemeen:

* Naam. Voer de naam in voor de IKE-gateway om te worden peered met de primaire CloudSimple VPN-peer.
* Versie. Selecteer **ALLEEN IKEv1-modus**.
* Adrestype. Selecteer **IPv4**.
* Interface. Selecteer de openbare of externe interface.
* Lokaal IP-adres. Gebruik de standaardinstelling.
* Peer IP-adrestype. Selecteer **IP**.
* Peer-adres. Voer het primaire IP-adres van CloudSimple VPN-peer in.
* Verificatie. Selecteer **Vooraf gedeelde sleutel**.
* Vooraf gedeelde sleutel / Bevestig vooraf gedeelde sleutel. Voer de vooraf gedeelde sleutel in die overeenkomt met de CloudSimple VPN-gatewaysleutel.
* Lokale identificatie. Voer het openbare IP-adres van de on-premises Palo Alto-firewall in.
* Peer Identificatie. Voer het primaire IP-adres van CloudSimple VPN-peer in.

Tabblad Geavanceerde opties:

* Passieve modus inschakelen. Schakel dit selectievakje niet in.
* Inschakelen NAT Traversal. Laat ongecontroleerd als de on-premises Palo Alto firewall zich niet achter een NAT-apparaat bevindt. Schakel anders het selectievakje in.

IKEv1:

* Exchange-modus. Selecteer **hoofdhoofd**.
* IKE crypto profiel. Selecteer het IKE Crypto-profiel dat u eerder hebt gemaakt. Laat het vak Fragmentatie inschakelen onaangevinkt.
* Dode peer detectie. Laat het vak je niet aanvinken.

Herhaal de vorige stappen om de secundaire IKE-gateway te maken.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IpSEC-cryptoprofielen definiëren

Selecteer >  >  **Network** > **NETWERKprofielen IPSEC-cryptotoevoegen****uit vergroten,** configureer de volgende velden en klik op **OK**.**Add**

* Naam. Voer een naam in voor het IPsec-cryptoprofiel.
* IPsec Protocol. Selecteer **ESP**.
* Versleuteling. Klik **op Toevoegen** en selecteer de juiste versleutelingsmethode.
* Verificatie. Klik **op Toevoegen** en selecteer de juiste verificatiemethode.
* DH Groep. Selecteer **geen pfs**.
* Levensduur. Stel in als 30 minuten.
* Inschakelen. Laat het vak je niet aanvinken.

Herhaal de vorige stappen om een ander IPsec-cryptoprofiel te maken, dat wordt gebruikt als de secundaire CloudSimple VPN-peer. Hetzelfde IPSEC Crypto-profiel kan ook worden gebruikt voor zowel de primaire als secundaire IPSec-tunnels (zie de volgende procedure).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Monitorprofielen definiëren voor tunnelbewaking

Selecteer > **Netwerkuitbreidnetwerkprofielen** > **Monitor** > **Toevoegen,** configureer de volgende velden en klik op **OK**. **Network**

* Naam. Voer een naam in van het monitorprofiel dat moet worden gebruikt voor tunnelbewaking voor proactieve reactie op de storing.
* Actie. Selecteer **Fail over**.
* Interval. Voer de waarde **3**in .
* Drempel. Voer de waarde **7**in .

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Het opzetten van primaire en secundaire IPsec-tunnels.

Selecteer **Add Network** > **IPsec** > **Tunnels,** configureer de volgende velden en klik op **OK**.

Tabblad Algemeen:

* Naam. Voer een naam in voor de primaire IPSEC-tunnel die moet worden gekeken met primaire CloudSimple VPN-peer.
* Tunnel Interface. Selecteer de primaire tunnelinterface.
* Type. Gebruik de standaardinstelling.
* Adrestype. Selecteer **IPv4**.
* IKE Gateway. Selecteer de primaire IKE-gateway.
* IPsec Crypto Profiel. Selecteer het primaire IPsec-profiel. Selecteer **Geavanceerde opties weergeven**.
* Replay-beveiliging inschakelen. Gebruik de standaardinstelling.
* Tos-header kopiëren. Laat het vak je niet aanvinken.
* Tunnel Monitor. Schakel het vakje in.
* Bestemming IP. Voer een IP-adres in dat behoort tot het Subnet CloudSimple Private Cloud dat is toegestaan via de Site-to-Site-verbinding. Zorg ervoor dat de tunnelinterfaces (zoals tunnel.20 - 10.64.5.2/32 en tunnel.30 - 10.64.6.2/32) op Palo Alto het IP-adres van CloudSimple Private Cloud via de Site-to-Site VPN kunnen bereiken. Zie de volgende configuratie voor proxy-id's.
* Profiel. Selecteer het monitorprofiel.

Tabblad Proxy-instellingen: klik op **IPv4** > Het volgende**toevoegen** en configureren:

* Proxy-id. Voer een naam in voor het interessante verkeer. Er kunnen meerdere Proxy ID's worden uitgevoerd in een IPsec tunnel.
* Lokale. Geef de on-premises lokale subnetten op die mogen communiceren met Private Cloud-subnetten via de Site-to-Site VPN.
* Externe. Geef de externe subnetten voor de private cloud op die mogen communiceren met de lokale subnetten.
* Protocol. Selecteer **een .**

Herhaal de vorige stappen om een andere IPsec-tunnel te maken die u gebruiken voor de secundaire CloudSimple VPN-peer.

## <a name="references"></a>Verwijzingen

NAT configureren op Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500-serie configuratiegids</a>

Ondersteunde IKEv1- en IKEv2-attributen op Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco ASA-serie CLI-configuratiehandleiding</a>

IPsec Site-to-Site VPN configureren op Cisco ASA met versie 8.4 en hoger:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">IKEv1 IPsec-site-to-site-tunnels configureren met de ASDM of CLI op de ASA</a>

Cisco Adaptive Security Appliance virtual (ASAv) configureren op Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco Adaptive Security Virtual Appliance (ASAv) quickstart Gids</a>

Site-to-Site VPN configureren met proxy-instellingen op Palo Alto:

[Site-to-Site VPN instellen](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Tunnelmonitor instellen:

[Tunnelbewaking instellen](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE-gateway- of IPsec-tunnelbewerkingen:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Een IKE-gateway- of IPsec-tunnel in-- of uitschakelen, vernieuwen of opnieuw starten</a>
