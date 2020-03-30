---
title: Quickstart - Een Azure VMware VM maken op een Private Cloud - Azure VMware-oplossing van CloudSimple
description: Beschrijft hoe u een Azure VMware VM maakt op een CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566145"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Maak Virtuele VMware-machines op uw Private Cloud

Als u virtuele machines wilt maken op uw private cloud, opent u eerst de CloudSimple-portal vanuit de Azure-portal.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Private Cloud wilt maken.
4. Klik **op** de pagina Overzicht op **Ga naar de CloudSimple-portal** om een nieuw browsertabblad voor CloudSimple-portal te openen.  Meld u desgevraagd aan met uw Azure-aanmeldingsreferenties.  

    ![CloudSimple-portal starten](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter web-ui starten

U nu vCenter starten om virtuele machines en beleidsregels in te stellen.

Als u toegang wilt krijgen tot vCenter, start u vanaf de CloudSimple-portal. Klik op de startpagina onder **Algemene taken**op **VSphere-client starten**.  Selecteer de Private Cloud en klik op **VSphere-client starten** in de private cloud.

   ![VSphere-client starten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Een ISO- of vSphere-sjabloon uploaden

  > [!WARNING]
  > Gebruik de vSphere HTML5-client voor ISO-upload.  Het gebruik van een Flash-client kan leiden tot een fout.

1. Verkrijg de ISO- of vSphere-sjabloon die u naar vCenter wilt uploaden om een VM te maken en deze beschikbaar te hebben op uw lokale systeem.
2. Klik in vCenter op het pictogram **Schijf** en selecteer **vsanDatastore**. Klik **op Bestanden** en klik vervolgens op Nieuwe **map**.
    ![vCenter ISO](media/vciso00.png)

3. Maak een map met de titel 'ISO's en sjablonen'.

4. Navigeer naar de ISO-map in ISO's en -sjablonen en klik op **Bestanden uploaden.** Volg de instructies op het scherm om de ISO te uploaden.

## <a name="create-a-virtual-machine-in-vcenter"></a>Een virtuele machine maken in vCenter

1. Klik in vCenter op het pictogram **Hosts en Clusters.**

2. Klik met de rechtermuisknop op **Werkbelasting** en selecteer **Nieuwe virtuele machine**.
    ![Nieuwe VM](media/vcvm01.png)

3. Selecteer **Nieuwe virtuele machine maken** en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm02.png)

4. Geef de machine een naam, selecteer de locatie van de **werkbelastingvm** en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm03.png)

5. Selecteer de **compute resource workload** en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm04.png)

6. Selecteer **vsanDatastore** en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm05.png)

7. Houd de standaard ESXi 6.5 compatibiliteitsselectie en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm06.png)

8. Selecteer het gastbesturingssysteem van de ISO voor de VM die u maakt en klik op **Volgende**.
    ![Nieuwe VM](media/vcvm07.png)

9. Selecteer harde schijf- en netwerkopties. Selecteer **gegevensarchief ISO-bestand**voor nieuw cd/dvd-station .  Als u verkeer van het openbare IP-adres naar deze vm wilt toestaan, selecteert u het netwerk als **vm-1**.
    ![Nieuwe VM](media/vcvm08.png)

10. Er wordt een selectievenster geopend. Selecteer het bestand dat u eerder hebt geüpload naar de map ISO's en sjablonen en klik op **OK**.
    ![Nieuwe VM](media/vcvm10.png)

11. Controleer de instellingen en klik op **OK** om de vm te maken.
    ![Nieuwe VM](media/vcvm11.png)

De VM is nu toegevoegd aan de compute resources voor workloads en is klaar voor gebruik. 
![Nieuwe VM](media/vcvm12.png)

De basisopstelling is nu voltooid. U uw Private Cloud gaan gebruiken, vergelijkbaar met de manier waarop u uw on-premises VM-infrastructuur zou gebruiken.

De volgende secties bevatten optionele informatie over het instellen van DNS- en DHCP-servers voor Private Cloud-workloads en het wijzigen van de standaardnetwerkconfiguratie.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Gebruikers en identiteitsbronnen toevoegen aan vCenter (optioneel)

CloudSimple wijst een standaard vCenter-gebruikersaccount toe met gebruikersnaam. `cloudowner@cloudsimple.local` Er is geen extra accountinstelling vereist om aan de slag te gaan.  CloudSimple kent beheerders normaal gesproken de bevoegdheden toe die ze nodig hebben om normale bewerkingen uit te voeren.  Stel uw actieve directory of Azure AD in als [extra identiteitsbron](set-vcenter-identity.md) in uw private cloud.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Een DNS- en DHCP-server maken (optioneel)

Toepassingen en workloads die worden uitgevoerd in een Private Cloud-omgeving vereisen naamomzetting en DHCP-services voor opzoeking en IP-adrestoewijzing. Een goede DHCP- en DNS-infrastructuur is vereist om deze services te kunnen leveren. U een virtuele machine in vCenter configureren om deze services te leveren in uw Private Cloud-omgeving.

Vereisten

* Een gedistribueerde poortgroep met VLAN geconfigureerd

* Route-instelling naar on-premises of op internet gebaseerde DNS-servers

* Sjabloon voor virtuele machines of ISO om een virtuele machine te maken

De volgende koppelingen bieden richtlijnen voor het instellen van DHCP- en DNS-servers op Linux en Windows.

#### <a name="linux-based-dns-server-setup"></a>Dns-serversetup op Linux

Linux biedt verschillende pakketten voor het opzetten van DNS-servers.  Hier volgt een koppeling naar instructies voor het instellen van een open-source BIND DNS-server.

[Voorbeeldinstelling](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows-installatie

In deze Microsoft-onderwerpen wordt beschreven hoe u een Windows-server instelt als DNS-server en als DHCP-server.

[Windows Server als DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server als DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Netwerkconfiguratie aanpassen (optioneel)

Met de netwerkpagina's in de CloudSimple-portal u de configuratie opgeven voor firewalltabellen en openbare IP-adressen voor VM's.

### <a name="allocate-public-ips"></a>Openbare IP's toewijzen

1. Navigeer naar **Network > Public IP** in de CloudSimple-portal.
2. Klik **op Openbaar IP toewijzen**.
3. Voer een naam in om de IP-adresvermelding te identificeren.
4. Houd de standaardlocatie.
5. Gebruik de schuifregelaar om de gewenste time-out te wijzigen.
6. Voer het lokale IP-adres in waarvoor u een openbaar IP-adres wilt toewijzen.
7. Voer desgewenst een bijbehorende DNS-naam in.
8. Klik op **Gereed**.

    ![Openbare IP](media/quick-create-pc-public-ip.png)

De taak van het toewijzen van het openbare IP-adres begint. U de status van de taak controleren op de pagina **Activiteit > Taken.** Wanneer de toewijzing is voltooid, wordt de nieuwe vermelding weergegeven op de pagina Openbare IP's.

De VM waaraan dit IP-adres moet worden toegewezen, moet worden geconfigureerd met het hierboven opgegeven lokale adres. De procedure voor het configureren van een IP-adres is specifiek voor het VM-besturingssysteem. Raadpleeg de documentatie voor uw VM-besturingssysteem voor de juiste procedure.

#### <a name="example"></a>Voorbeeld

Hier zijn bijvoorbeeld de details voor Ubuntu 16.04.

Voeg de statische methode toe aan de inet-adresfamilieconfiguratie in het bestand /etc/netwerk/interfaces. Wijzig de adres-, netmasker- en gatewaywaarden. Voor dit voorbeeld gebruiken we de eth0-interface, intern IP-adres 192.168.24.10, gatewayadres 192.168.24.1 en netmask 255.255.255.0. Voor uw omgeving wordt de beschikbare subnetinformatie verstrekt in de welkomste-mail.

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
Schakel de interface handmatig uit.

```
sudo ifdown eth0
```
Schakel de interface opnieuw handmatig in.

```
sudo ifup eth0
```

Standaard wordt al het binnenkomende verkeer van het internet **geweigerd.** Als u een andere poort wilt openen, maakt u een [firewalltabel.](firewall.md)

Nadat u een intern IP-adres hebt geconfigureerd als het statische IP-adres, controleert u of u het internet vanuit de VM bereiken.

```
ping 8.8.8.8
```
Controleer ook of u de vm vanaf het internet bereiken met behulp van het openbare IP-adres.

Zorg ervoor dat iptable-regels op de VM poort 80-binnenkomenniet blokkeren.
        
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
Start een browser op uw bureaublad en wijs deze naar poort 80 voor het openbare IP-adres om door de bestanden op uw vm te bladeren.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standaard CloudSimple-firewallregels voor openbare IP

* VPN-verkeer: Al het verkeer tussen (van/naar) de VPN en alle workloadnetwerken en het beheernetwerk is toegestaan.
* Privé cloud intern verkeer: Al het oost-west verkeer tussen (van/naar) workloadnetwerken en het beheernetwerk (hierboven weergegeven) is toegestaan.
* Internetverkeer:
  * Al het binnenkomende verkeer van het internet wordt geweigerd aan workloadnetwerken en het beheernetwerk.
  * Al het uitgaande verkeer naar het internet van workloadnetwerken of het beheernetwerk is toegestaan.

U ook de manier wijzigen waarop uw verkeer is beveiligd, met behulp van de functie Firewallregels. Zie [Firewalltabellen en -regels instellen voor](firewall.md)meer informatie.

## <a name="install-solutions-optional"></a>Oplossingen installeren (optioneel)

U oplossingen installeren op uw CloudSimple Private Cloud om optimaal te profiteren van uw Private Cloud vCenter-omgeving. U back-ups, noodherstel, replicatie en andere functies instellen om uw virtuele machines te beschermen. Voorbeelden hiervan zijn VMware Site Recovery Manager (VMware SRM) en Veeam Backup & Replicatie.

Als u een oplossing wilt installeren, moet u voor een beperkte periode aanvullende bevoegdheden aanvragen. Zie [Bevoegdheden escaleren](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* [Verbinding maken met on-premises netwerk via Azure ExpressRoute](on-premises-connection.md)
* [VPN-gateways instellen op cloudeenvoudig-netwerk](vpn-gateway.md)
