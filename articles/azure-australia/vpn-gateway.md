---
title: Azure-VPN Gateway in azure Australië
description: Implementatie van VPN Gateway in azure Australië zodat deze voldoet aan de ISM en de bescherming van de Australische overheids instanties effectief te beschermen
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575436"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure-VPN Gateway in azure Australië

Een essentiële service met een open bare Cloud is de beveiligde verbinding van Cloud bronnen en-services met bestaande on-premises systemen. De service die deze mogelijkheid biedt in azure, is Azure VPN Gateway. In dit artikel vindt u een overzicht van de belangrijkste punten waarmee u rekening moet houden wanneer u een VPN-gateway configureert om te voldoen aan de [besturings elementen van het gegevens beveiligings handboek](https://acsc.gov.au/infosec/ism/)(ASD) van het Australische signalen (ISM).

Een VPN-gateway wordt gebruikt voor het verzenden van versleuteld verkeer tussen een virtueel netwerk in Azure en een ander netwerk. Er zijn drie scenario's die worden verholpen door VPN-gateways:

- Site-naar-site (S2S)
- Punt-naar-site (P2S)
- Netwerk-naar-netwerk

Dit artikel richt zich op S2S VPN-gateways. In diagram 1 ziet u een voor beeld van een configuratie van een S2S VPN-gateway.

![VPN-gateway met multi-site-verbindingen](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1: Azure S2S VPN Gateway*

## <a name="key-design-considerations"></a>Belangrijkste overwegingen bij het ontwerpen

Er zijn drie netwerk opties waarmee u Azure kunt verbinden met Australische overheids klanten:

- DIAPICTOGRAM
- Azure ExpressRoute
- Openbaar Internet

De gebruikers handleiding van het Australische Cyber Security Center [voor Azure](https://servicetrust.microsoft.com/viewpage/Australia) adviseert dat VPN gateway (of een equivalente, beschermde gecertificeerde service van derden) wordt gebruikt in combi natie met de drie netwerk opties. Deze aanbeveling is ervoor te zorgen dat de verbindingen voldoen aan de ISM-controles voor versleuteling en integriteit.

### <a name="encryption-and-integrity"></a>Versleuteling en integriteit

Standaard onderhandelt de VPN de algoritmen voor versleuteling en integriteit tijdens de verbinding tot stand brengen als onderdeel van de IKE-Handshakes. Tijdens de IKE-handshake is de configuratie en volg orde van voor keur afhankelijk van of de VPN-gateway de initiator of de responder is. Deze aanwijzing wordt beheerd via het VPN-apparaat. De uiteindelijke configuratie van de verbinding wordt bepaald door de configuratie van het VPN-apparaat. Zie [over VPN-services](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)voor meer informatie over gevalideerde VPN-apparaten en hun configuratie.

VPN-gateways kunnen versleuteling en integriteit beheren door een aangepast IPsec/IKE-beleid te configureren voor de verbinding.

### <a name="resource-operations"></a>Resource bewerkingen

VPN-gateways maken een verbinding tussen Azure-en niet-Azure-omgevingen via het open bare Internet. De ISM heeft besturings elementen die betrekking hebben op de expliciete autorisatie van verbindingen. Het is standaard mogelijk om VPN-gateways te gebruiken om niet-geautoriseerde tunnels te maken in beveiligde omgevingen. Het is essentieel dat organisaties gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) van Azure om te bepalen wie VPN-gateways en hun verbindingen kan maken en wijzigen. Azure heeft geen ingebouwde rol voor het beheren van VPN-gateways, dus er is een aangepaste rol vereist.

De toegang tot de rol van Inzender, Inzender en netwerk bijdrage wordt nauw keurig gecontroleerd. We raden u ook aan Azure Active Directory Privileged Identity Management te gebruiken voor gedetailleerdere toegangs controle.

### <a name="high-availability"></a>Hoge beschikbaarheid

Azure VPN-gateways kunnen meerdere verbindingen hebben en meerdere on-premises VPN-apparaten ondersteunen in dezelfde on-premises omgeving. Zie diagram 1.

Virtuele netwerken in azure kunnen meerdere VPN-gateways hebben die kunnen worden geïmplementeerd in onafhankelijke, actief-passieve of actieve-actieve configuraties.

U wordt aangeraden alle VPN-gateways te implementeren in een [configuratie met hoge Beschik baarheid](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable). Een voor beeld: twee on-premises VPN-apparaten die zijn verbonden met twee VPN-gateways in de modus actief-passief of actief/actief. Zie diagram 2.

![Redundante VPN-gateway verbindingen](media/dual-redundancy.png)

*Diagram 2: actieve en actieve VPN-gateways en twee VPN-apparaten*

### <a name="forced-tunneling"></a>Geforceerde tunneling

Geforceerde tunneling omleidt of forceert alle Internet verkeer via de VPN-gateway naar de on-premises omgeving voor inspectie en controle. Zonder geforceerde tunneling wordt het Internet-gebonden verkeer van Vm's in azure door de Azure-netwerk infrastructuur rechtstreeks naar het open bare Internet gepasseerd zonder de optie om het verkeer te controleren of te controleren. Geforceerde tunneling is van cruciaal belang wanneer een organisatie een beveiligde Internet gateway (SIG) voor een omgeving moet gebruiken.

## <a name="detailed-configuration"></a>Gedetailleerde configuratie

### <a name="service-attributes"></a>Service kenmerken

VPN-gateways voor S2S-verbindingen die zijn geconfigureerd voor de Australische overheid, moeten de volgende kenmerken hebben:

|Kenmerk | Pas|
|--- | --- |
|Gateway type | VPN|
|

De kenmerk instellingen die vereist zijn om te voldoen aan de ISM-besturings elementen voor beveiligde zijn:

|Kenmerk | Pas|
|--- |---|
|vpnType |RouteBased|
|Verbinding/vpnClientProtocols | IkeV2|
|

Azure VPN-gateways ondersteunen een bereik van cryptografische algoritmen van de IPsec-en IKE-protocol standaarden. Het standaard beleid stelt maximale compatibiliteit met een breed scala aan VPN-apparaten van derden in. Als gevolg hiervan is het mogelijk dat er tijdens de IKE-handshake een niet-compatibele configuratie wordt onderhandeld. We raden u ten zeerste aan om [aangepaste IPSec/IKE-beleids](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) parameters toe te passen op verbinding in VPN-gateways om ervoor te zorgen dat de verbindingen voldoen aan de ISM-controles voor on-premises omgevings verbindingen met Azure. De sleutel kenmerken worden weer gegeven in de volgende tabel.

|Kenmerk|Wordt|Pas|
|---|---|---|
|saLifeTimeSeconds|< 14400 sec.|> 300 seconden|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

Voor dhGroup en pfsGroup in de vorige tabel hebben ECP256 en ECP384 de voor keur, zelfs als andere instellingen kunnen worden gebruikt.

### <a name="related-services"></a>Verwante services

Wanneer u een Azure VPN-gateway ontwerpt en configureert, moet er ook een aantal gerelateerde services bestaan en worden geconfigureerd.

|Service | Actie vereist|
|--- | ---|
|Virtueel netwerk | VPN-gateways zijn gekoppeld aan een virtueel netwerk. Maak een virtueel netwerk voordat u een nieuwe VPN-gateway maakt.|
|Openbaar IP-adres | S2S VPN-gateways hebben een openbaar IP-adres nodig om verbinding te maken tussen het on-premises VPN-apparaat en de VPN-gateway. Maak een openbaar IP-adres voordat u een S2S VPN-gateway maakt.|
|Subnet | Maak een subnet van het virtuele netwerk voor de VPN-gateway.|
|

## <a name="implementation-steps-using-powershell"></a>Implementaties tappen met behulp van Power shell

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

1. Maak een aangepaste rol. Een voor beeld is virtualNetworkGateway contributor. Maak een rol die moet worden toegewezen aan gebruikers die VPN-gateways kunnen maken en wijzigen. De aangepaste rol moet de volgende bewerkingen toestaan:

   Micro soft. Network/virtualNetworkGateways/*  
   Micro soft. Network/Connections/*  
   Micro soft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Micro soft. Network/publicIPAddresses/*  
   Micro soft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Voeg de aangepaste rol toe aan gebruikers die VPN-gateways en verbindingen met on-premises omgevingen mogen maken en beheren.

### <a name="create-a-vpn-gateway"></a>Een VPN-gateway maken

Bij deze stappen wordt ervan uitgegaan dat u al een virtueel netwerk hebt gemaakt.

1. Maak een nieuw openbaar IP-adres.
2. Maak een VPN-gateway-subnet.
3. Een IP-configuratie bestand voor een VPN-gateway maken.
4. Een VPN-gateway maken.
5. Maak een lokale netwerk gateway voor het on-premises VPN-apparaat.
6. Maak een IPsec-beleid. Bij deze stap wordt ervan uitgegaan dat u aangepaste IPsec/IKE-beleids regels gebruikt.
7. Maak een verbinding tussen de VPN-gateway en een lokale netwerk gateway met behulp van het IPsec-beleid.

### <a name="enforce-tunneling"></a>Tunneling afdwingen

Als geforceerde tunneling is vereist, moet u voordat u de VPN-gateway maakt, het volgende doen:

1. Maak een route tabel en routerings regels.
2. Een route tabel koppelen aan de juiste subnetten.

Nadat u de VPN-gateway hebt gemaakt:

- Stel GatewayDefaultSite in op de on-premises omgeving op de VPN-gateway.

### <a name="example-powershell-script"></a>Voor beeld Power shell-script

Een voor beeld van een Power shell-script dat wordt gebruikt voor het maken van een aangepast IPsec/IKE-beleid voldoet aan ISM-controles voor Australische beveiligde beveiligings classificatie.

Hierbij wordt ervan uitgegaan dat het virtuele netwerk, de VPN-gateway en de lokale gateways bestaan.

#### <a name="create-an-ipsecike-policy"></a>Een IPsec/IKE-beleid maken

Met het volgende voorbeeld script maakt u een IPsec/IKE-beleid met de volgende algoritmen en para meters:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, levens duur van SA 14.400 seconden en 102.400.000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Een S2S-VPN-verbinding maken met het aangepaste IPsec/IKE-beleid

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel heeft betrekking op de specifieke configuratie van VPN Gateway om te voldoen aan de vereisten die zijn opgegeven in de Information Security-hand leiding voor het beveiligen van beveiligde gegevens van de Australische overheid tijdens de overdracht. Zie voor de stappen voor het configureren van uw VPN-gateway:

- [Overzicht van virtuele Azure-netwerk gateway](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Wat is VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met behulp van Power shell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Een VPN-gateway maken en beheren](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)