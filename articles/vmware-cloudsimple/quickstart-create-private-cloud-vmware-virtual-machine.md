---
title: 'Snelstartgids: een virtuele VMware-machine maken op de AVS-privécloud'
description: Hierin wordt beschreven hoe u een virtuele VMware-machine maakt op een AVS-Privécloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cbe88afc4f566bad4bacb408346d4dd25a2f6c96
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020060"
---
# <a name="create-vmware-virtual-machines-on-your-avs-private-cloud"></a>Virtuele VMware-machines maken op uw AVS-Privécloud

Als u virtuele machines wilt maken op uw AVS-Privécloud, begint u met het openen van de AVS-Portal vanuit de Azure Portal.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Toegang tot de AVS-Portal

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-Services**.
3. Selecteer de AVS-service waarop u de Privécloud van uw AVS wilt maken.
4. Klik op de pagina **overzicht** op **Ga naar de AVS-Portal** om een nieuw browser tabblad voor de AVS-portal te openen. Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd. 

    ![AVS-Portal starten](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter-Web-UI starten

U kunt nu vCenter starten om virtuele machines en beleids regels in te stellen.

Start vanuit de AVS-Portal om toegang tot vCenter te krijgen. Klik op de start pagina onder **algemene taken**op **VSphere-client starten**. Selecteer de AVS-privécloud en klik vervolgens op **vSphere-client starten** op de AVS-privécloud.

   ![VSphere-client starten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Een ISO-of vSphere-sjabloon uploaden

  > [!WARNING]
  > Voor de ISO-upload gebruikt u de vSphere HTML5-client. Het gebruik van een Flash-client kan een fout veroorzaken.

1. Verkrijg de ISO-of vSphere-sjabloon die u wilt uploaden naar vCenter om een virtuele machine te maken en deze beschikbaar te stellen op uw lokale systeem.
2. Klik in vCenter op het pictogram **schijf** en selecteer **vsanDatastore**. Klik op **bestanden** en klik vervolgens op **nieuwe map**.
    ISO-](media/vciso00.png) ![vCenter

3. Maak een map met de titel ' Iso's en templates '.

4. Ga naar de map Iso's in Iso's en sjablonen en klik op **bestanden uploaden**. Volg de instructies op het scherm om de ISO te uploaden.

## <a name="create-a-virtual-machine-in-vcenter"></a>Een virtuele machine maken in vCenter

1. Klik in vCenter op het pictogram **hosts en clusters** .

2. Klik met de rechter muisknop op **workload** en selecteer **nieuwe virtuele machine**.
    Nieuwe VM-](media/vcvm01.png) ![

3. Selecteer **nieuwe virtuele machine maken** en klik op **volgende**.
    Nieuwe VM-](media/vcvm02.png) ![

4. Geef de machine een naam, selecteer de locatie **van de VM van de werk belasting** en klik op **volgende**.
    Nieuwe VM-](media/vcvm03.png) ![

5. Selecteer de **werk belasting** Compute resource en klik op **volgende**.
    Nieuwe VM-](media/vcvm04.png) ![

6. Selecteer **vsanDatastore** en klik op **volgende**.
    Nieuwe VM-](media/vcvm05.png) ![

7. Behoud de standaard selectie van de ESXi 6,5-compatibiliteit en klik op **volgende**.
    Nieuwe VM-](media/vcvm06.png) ![

8. Selecteer het gast besturingssysteem van de ISO voor de virtuele machine die u maakt en klik op **volgende**.
    Nieuwe VM-](media/vcvm07.png) ![

9. Selecteer de opties voor harde schijf en netwerk. Selecteer **ISO-bestand Data Store**voor nieuwe cd/dvd-stations. Als u verkeer van het open bare IP-adres naar deze VM wilt toestaan, selecteert u het netwerk als **VM-1**.
    Nieuwe VM-](media/vcvm08.png) ![

10. Er wordt een selectie venster geopend. Selecteer het bestand dat u eerder hebt geüpload naar de map Iso's en templates en klik op **OK**.
    Nieuwe VM-](media/vcvm10.png) ![

11. Controleer de instellingen en klik op **OK** om de virtuele machine te maken.
    Nieuwe VM-](media/vcvm11.png) ![

De VM wordt nu toegevoegd aan de werk belasting reken resources en is klaar voor gebruik. 
Nieuwe VM-](media/vcvm12.png) ![

De basis installatie is nu voltooid. U kunt de Privécloud van uw Cloud op dezelfde manier gebruiken als de on-premises VM-infra structuur.

De volgende secties bevatten optionele informatie over het instellen van DNS-en DHCP-servers voor AVS-workloads in de Privécloud en het wijzigen van de standaard-netwerk configuratie.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Gebruikers en identiteits bronnen aan vCenter toevoegen (optioneel)

AVS wijst een standaard-vCenter-gebruikers account toe met gebruikers naam `cloudowner@AVS.local`. U hoeft geen extra account in te stellen om aan de slag te gaan. AVS wijst beheerders normaal gesp roken de bevoegdheden toe die ze nodig hebben om normale bewerkingen uit te voeren. Stel uw on-premises Active Directory of Azure AD in als een [extra id-bron](set-vcenter-identity.md) op uw AVS-privécloud.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Een DNS-en DHCP-server maken (optioneel)

Voor toepassingen en werk belastingen die worden uitgevoerd in een cloud omgeving in de AVS, zijn naam omzetting en DHCP-services vereist voor de toewijzing van lookup-en IP-adressen. U hebt de juiste DHCP-en DNS-infra structuur nodig om deze services te kunnen leveren. U kunt een virtuele machine in vCenter configureren om deze services te bieden in de Privécloud van uw cloud omgeving.

Vereisten

* Een gedistribueerde poort groep waarvoor VLAN is geconfigureerd

* De installatie naar on-premises of op internet gebaseerde DNS-servers routeren

* Virtuele-machine sjabloon of ISO voor het maken van een virtuele machine

De volgende koppelingen bieden richt lijnen voor het instellen van DHCP-en DNS-servers in Linux en Windows.

#### <a name="linux-based-dns-server-setup"></a>Installatie van DNS-server op basis van Linux

Linux biedt verschillende pakketten voor het instellen van DNS-servers. Hier volgt een koppeling naar instructies voor het instellen van een open-source BIND-DNS-server.

[Voor beeld van installatie](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Installatie op basis van Windows

In deze micro soft-onderwerpen wordt beschreven hoe u een Windows-Server als een DNS-server en als een DHCP-server instelt.

[Windows Server als DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server als DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Netwerk configuratie aanpassen (optioneel)

Met de netwerk pagina's in de AVS-Portal kunt u de configuratie voor Firewall tabellen en open bare IP-adressen voor Vm's opgeven.

### <a name="allocate-public-ips"></a>Open bare IP-adressen toewijzen

1. Navigeer naar **netwerk > openbaar IP** in de AVS-Portal.
2. Klik op **openbaar IP-adres toewijzen**.
3. Voer een naam in om de IP-adres vermelding te identificeren.
4. Behoud de standaard locatie.
5. Gebruik de schuif regelaar om de time-out voor inactiviteit indien gewenst te wijzigen.
6. Voer het lokale IP-adres in waarvoor u een openbaar IP-adres wilt toewijzen.
7. Voer desgewenst een bijbehorende DNS-naam in.
8. Klik op **Gereed**.

    ![Openbare IP](media/quick-create-pc-public-ip.png)

De taak voor het toewijzen van het open bare IP-adres begint. U kunt de status van de taak controleren op de pagina **activiteit > taken** . Wanneer de toewijzing is voltooid, wordt de nieuwe vermelding op de open bare IPs-pagina weer gegeven.

De virtuele machine waaraan dit IP-adres moet worden toegewezen, moet worden geconfigureerd met het lokale adres dat hierboven is opgegeven. De procedure voor het configureren van een IP-adres is specifiek voor het besturings systeem van de virtuele machine. Raadpleeg de documentatie voor uw VM-besturings systeem voor de juiste procedure.

#### <a name="example"></a>Voorbeeld

Dit zijn bijvoorbeeld de Details voor Ubuntu 16,04.

Voeg de statische methode toe aan de configuratie van de inet-adres familie in het bestand/etc/network/interfaces. Wijzig de waarden voor adres, netmasker en gateway. Voor dit voor beeld gebruiken we de eth0-interface, het interne IP-adres 192.168.24.10, het gateway adres 192.168.24.1 en netmask 255.255.255.0. De beschik bare subnetgegevens voor uw omgeving vindt u in de welkomst-e-mail.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Schakel de interface hand matig uit.

```
sudo ifdown eth0
```
Schakel de interface hand matig opnieuw in.

```
sudo ifup eth0
```

Standaard wordt al het binnenkomende verkeer van Internet **geweigerd**. Als u een andere poort wilt openen, moet u een [firewall tabel](firewall.md)maken.

Nadat u een intern IP-adres hebt geconfigureerd als het statische IP-adres, controleert u of u het internet kunt bereiken vanuit de VM.

```
ping 8.8.8.8
```
Controleer ook of u de virtuele machine via internet kunt bereiken met behulp van het open bare IP-adres.

Zorg ervoor dat de iptable-regels op de virtuele machine geen poort 80 inkomend blokkeert.
        
```
netstat -an | grep 80
```

Start een http-server die luistert op poort 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

of

```
python3 -m http.server 80
```
Start een browser op uw bureau blad en ga naar poort 80 voor het open bare IP-adres om door de bestanden op uw virtuele machine te bladeren.

### <a name="default-avs-firewall-rules-for-public-ip"></a>Standaard-AVS-firewall regels voor openbaar IP-adres

* VPN-verkeer: al het verkeer tussen (van/naar) de VPN-en alle werkbelasting netwerken en het beheer netwerk is toegestaan.
* Intern verkeer van de Privécloud in de Cloud: alle Oost-West-verkeer tussen (van/naar) werkbelasting netwerken en het beheer netwerk (zie hierboven) is toegestaan.
* Internet verkeer:
  * Al het binnenkomende verkeer van Internet wordt geweigerd aan workload netwerken en het beheer netwerk.
  * Al het uitgaande verkeer naar het Internet vanuit workload netwerken of het beheer netwerk is toegestaan.

U kunt ook de manier wijzigen waarop het verkeer wordt beveiligd, met de functie Firewall regels. Zie [firewall tabellen en-regels instellen](firewall.md)voor meer informatie.

## <a name="install-solutions-optional"></a>Oplossingen installeren (optioneel)

U kunt oplossingen op uw AVS-Privécloud installeren om optimaal gebruik te kunnen maken van uw AVS-privécloud voor persoonlijke Clouds. U kunt back-ups, herstel na nood gevallen, replicatie en andere functies instellen om uw virtuele machines te beveiligen. Voor beelden hiervan zijn VMware Site Recovery Manager (VMware-Veeam) en back-up& replicatie.

Als u een oplossing wilt installeren, moet u voor een beperkte periode extra bevoegdheden aanvragen. Zie [bevoegdheden escaleren](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Volgende stappen

* [VMware-Vm's in azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* [Verbinding maken met een on-premises netwerk met behulp van Azure ExpressRoute](on-premises-connection.md)
* [VPN-gateways instellen op het AVS-netwerk](vpn-gateway.md)
