---
title: Geneste virtualisatie inschakelen in Azure Virtual Machines
description: Geneste virtualisatie inschakelen in Azure Virtual Machines
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 16f5bed5a2342bb1d120d0d3dc853e0bc44376dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033127"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Geneste virtualisatie inschakelen in een Azure VM

Geneste virtualisatie wordt ondersteund in verschillende Azure-families voor virtuele machines. Deze mogelijkheid biedt een grote flexibiliteit bij het ondersteunen van scenario's zoals ontwikkelings-, test-, trainings- en demonstratieomgevingen.   

In dit artikel wordt hyper-v op een Azure VM ingeschakeld en wordt de internetverbinding met die gastvirtuele machine geconfigureerd.

## <a name="create-a-nesting-capable-azure-vm"></a>Een Azure VM met nestingmaken

Maak een nieuwe Windows Server 2016 Azure VM.  Voor snel naslagwerk ondersteunen alle virtuele v3-machines geneste virtualisatie. Voor een volledige lijst met virtuele machineformaten die nesten ondersteunen, raadpleegt u het [artikel Azure Compute Unit](acu.md).

Vergeet niet om een VM-grootte te kiezen die groot genoeg is om de eisen van een gastvirtuele machine te ondersteunen. In dit voorbeeld gebruiken we een Azure VM van D3_v3 grootte. 

U de regionale beschikbaarheid van Dv3 of Ev3-serie virtuele machines [hier](https://azure.microsoft.com/regions/services/)bekijken.

>[!NOTE]
>
>Zie [Windows VM's maken en beheren met de Azure PowerShell-module](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm) voor gedetailleerde instructies voor het maken van een nieuwe virtuele machine.
    
## <a name="connect-to-your-azure-vm"></a>Verbinding maken met uw Azure VM

Maak via een extern bureaublad verbinding met de virtuele machine.

1. Klik op de knop **Verbinden** in de eigenschappen van de virtuele machine. Er wordt een Remote Desktop Protocol-bestand (.rdp) gemaakt en gedownload.

2. Open het gedownloade RDP-bestand om verbinding met de VM te maken. Als u hierom wordt gevraagd, klikt u op **Verbinden**. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) uit de Mac App Store.

3. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine en klik vervolgens op **OK**.

4. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om door te gaan met de verbinding.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>De functie Hyper-V inschakelen op de Azure VM
U deze instellingen handmatig configureren of we hebben een PowerShell-script geleverd om de configuratie te automatiseren.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Optie 1: Een PowerShell-script gebruiken om geneste virtualisatie te configureren
Een PowerShell-script om geneste virtualisatie op een Windows Server 2016-host in te schakelen, is beschikbaar op [GitHub.](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested) Het script controleert de vereisten en configureert vervolgens geneste virtualisatie op de Azure VM. Een herstart van de Azure VM is noodzakelijk om de configuratie te voltooien. Dit script kan werken in andere omgevingen, maar is niet gegarandeerd. Bekijk het Azure-blogbericht met een live videodemonstratie over geneste virtualisatie die op Azure wordt uitgevoerd! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Optie 2: Geneste virtualisatie handmatig configureren

1. Open PowerShell op de Azure VM als beheerder. 

2. Schakel de Hyper-V-functie en beheertools in.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Met deze opdracht wordt de Azure VM opnieuw gestart. U verliest uw RDP-verbinding tijdens het herstartproces.
    
3. Nadat de Azure VM opnieuw is opgestart, maakt u opnieuw verbinding met uw VM via RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Internetconnectiviteit instellen voor de virtuele gastmachine
Maak een nieuwe virtuele netwerkadapter voor de virtuele gastmachine en configureer een NAT-gateway om internetverbinding mogelijk te maken.

### <a name="create-a-nat-virtual-network-switch"></a>Een virtuele NAT-netwerkswitch maken

1. Open PowerShell op de Azure VM als beheerder.
   
2. Maak een interne schakelaar.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Bekijk de eigenschappen van de switch en noteer de ifIndex voor de nieuwe adapter.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter (NetAdapter)](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Let op de "ifIndex" voor de virtuele schakelaar die u zojuist hebt gemaakt.
    
4. Maak een IP-adres voor de NAT-gateway.
    
Om de gateway te configureren, hebt u informatie over uw netwerk nodig:    
  * IPAddress - Het IP-adres van nat gateway geeft het IPv4- of IPv6-adres op dat moet worden gebruikt als het standaardgatewayadres voor het virtuele netwerksubnet. De generieke vorm is a.b.c.1 (bijvoorbeeld "192.168.0.1"). Hoewel de uiteindelijke positie niet hoeft te worden .1, is het meestal (op basis van voorvoegsel lengte). Meestal moet u gebruik maken van een RFC 1918 prive-netwerk adres ruimte. 
  * VoorvoegselLengte - De subnetvoorvoegingslengte definieert de lokale subnetgrootte (subnetmasker). De subnetvoorvoegsellengte is een gehele waarde tussen 0 en 32. 0 zou de kaart van het hele internet, 32 zou alleen toestaan dat een toegewezen IP. Gemeenschappelijke waarden variëren van 24 tot 12, afhankelijk van het aantal IP's dat aan de NAT moet worden gekoppeld. Een gemeenschappelijke voorfixlengte is 24 - dit is een subnetmasker van 255.255.255.0.
  * InterfaceIndex - **ifIndex** is de interface-index van de virtuele switch die in de vorige stap is gemaakt. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Het NAT-netwerk maken

Om de gateway te configureren, moet u informatie verstrekken over het netwerk en nat gateway:
  * Naam - Dit is de naam van het NAT-netwerk. 
  * InternalIPInterfaceAddressPrefix - Het NAT-subnetvoorvoegsel beschrijft zowel het IP-voorvoegsel van de NAT-gateway als het NAT-subnetvoorvoegsellengte van bovenaf. De generieke vorm is a.b.c.0/NAT Subnet Prefix Length. 

Maak in PowerShell een nieuw NAT-netwerk.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>De virtuele gastmachine maken

>[!IMPORTANT] 
>
>De Azure-gastagent wordt niet ondersteund op geneste VM's en kan problemen veroorzaken op zowel de host als de geneste VM's. Installeer de Azure-agent niet op geneste VM's en gebruik geen afbeelding voor het maken van de geneste VM's waarop de Azure-gastagent al is geïnstalleerd.

1. Open Hyper-V Manager en maak een nieuwe virtuele machine. Configureer de virtuele machine om het nieuwe interne netwerk te gebruiken dat u hebt gemaakt.
    
    ![NetwerkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installeer een besturingssysteem op de virtuele gastmachine.
    
    >[!NOTE] 
    >
    >U hebt installatiemedia nodig om een besturingssysteem op de VM te installeren. In dit geval gebruiken we Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Een IP-adres toewijzen aan de virtuele gastmachine

U een IP-adres toewijzen aan de virtuele computer van de gast door handmatig een statisch IP-adres in te stellen op de virtuele gastmachine of DHCP op de Azure VM te configureren om het IP-adres dynamisch toe te wijzen.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Optie 1: DHCP configureren om dynamisch een IP-adres toe te wijzen aan de virtuele gastmachine
Volg de onderstaande stappen om DHCP op de virtuele hostmachine te configureren voor dynamische adrestoewijzing.

#### <a name="install-dchp-server-on-the-azure-vm"></a>DCHP-server installeren op de Azure VM

1. Serverbeheer openen. Klik op het dashboard op **Rollen en onderdelen toevoegen**. De wizard Functies en onderdelen toevoegen wordt weergegeven.
  
2. Klik in de wizard op **Volgende** tot de pagina Serverrollen.
  
3. Klik hierop om het **selectievakje DHCP-server** in te schakelen, klik op **Onderdelen toevoegen**en klik vervolgens op **Volgende** totdat u de wizard hebt voltooid.
  
4. Klik **op Installeren**.

#### <a name="configure-a-new-dhcp-scope"></a>Een nieuw DHCP-bereik configureren

1. Dhcp-beheer openen.
  
2. Vouw in het navigatiedeelvenster de servernaam uit, klik met de rechtermuisknop op **IPv4**en klik op **Nieuwe scope**. De wizard Nieuw bereik wordt weergegeven en klikt op **Volgende**.
  
3. Voer een naam en beschrijving in voor het bereik en klik op **Volgende**.
  
4. Definieer een IP-bereik voor uw DCHP-server (bijvoorbeeld 192.168.0.100 tot 192.168.0.200).
  
5. Klik **op Volgende** tot de standaardgatewaypagina. Voer het IP-adres in dat u eerder hebt gemaakt (bijvoorbeeld 192.168.0.1) als standaardgateway en klik op **Toevoegen**.
  
6. Klik **op Volgende** totdat de wizard is voltooid en alle standaardwaarden achterlaat en vervolgens op **Voltooien**klikt.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Optie 2: Handmatig een statisch IP-adres instellen op de virtuele gastmachine
Als u DHCP niet hebt geconfigureerd om dynamisch een IP-adres toe te wijzen aan de virtuele gastmachine, voert u deze stappen uit om een statisch IP-adres in te stellen.

1. Open PowerShell op de Azure VM als beheerder.

2. Klik met de rechtermuisknop op de virtuele computer van de gast en klik op Verbinden.

3. Meld u aan bij de virtuele computer van de gast.

4. Open het netwerk- en deelcentrum op de virtuele gastmachine.

5. Configureer de netwerkadapter voor een adres binnen het bereik van het NAT-netwerk dat u in de vorige sectie hebt gemaakt.

In dit voorbeeld gebruikt u een adres in het bereik van 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Test connectiviteit in gast virtuele machine

Open uw browser in de virtuele gastmachine en navigeer naar een webpagina.
    ![GuestVM (GuestVM)](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Intranetconnectiviteit instellen voor de virtuele gastmachine

Raadpleeg [dit document](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)voor instructies over het inschakelen van transparante connectiviteit tussen vm's voor gasten en Azure-vm's.
