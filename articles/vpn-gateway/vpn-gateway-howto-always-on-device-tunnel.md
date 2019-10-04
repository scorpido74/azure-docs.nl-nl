---
title: Een always on-VPN-tunnel configureren voor VPN Gateway
description: Stappen voor het configureren van de always on-VPN-tunnel voor VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bc2ec2b952b4f0c6e61fc4953559fa882edfff09
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841145"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Een AlwaysOn-VPN-apparaattunnel configureren

Een van de nieuwe functies van de VPN-client (virtueel particulier netwerk) van Windows 10 is de mogelijkheid om een VPN-verbinding te onderhouden. Always on is een Windows 10-functie waarmee het actieve VPN-profiel automatisch verbinding kan maken en verbonden blijft op basis van triggers, namelijk gebruikers aanmelding, wijziging van de netwerk status of het scherm apparaat actief.

Virtuele Azure-netwerk gateways kunnen worden gebruikt met Windows 10 altijd aan om permanente gebruikers tunnels tot stand te brengen, evenals de tunnels van apparaten met Azure. Dit artikel helpt u bij het configureren van een always ON-VPN-apparaats tunnel.

Altijd op VPN-verbindingen zijn twee typen tunnels:

* De tunnel van het **apparaat** maakt verbinding met opgegeven VPN-servers voordat gebruikers zich aanmelden op het apparaat. Bij connectiviteits scenario's voor aanmelding en Apparaatbeheer wordt de tunnel apparaat gebruikt.

* **Gebruikers tunnel** maakt verbinding alleen nadat een gebruiker zich heeft aangemeld bij het apparaat. Gebruikers tunnel stelt gebruikers in staat om toegang te krijgen tot bedrijfs bronnen via VPN-servers.

Tunnels en gebruikers tunnels van apparaten worden onafhankelijk van hun VPN-profielen gebruikt. Ze kunnen tegelijkertijd worden verbonden en kunnen verschillende verificatie methoden en andere VPN-configuratie-instellingen gebruiken.

## <a name="1-configure-the-gateway"></a>1. De gateway configureren

Configureer de VPN-gateway voor gebruik van IKEv2 en verificatie op basis van certificaten met behulp van dit [punt-naar-site-artikel](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. De tunnel van het apparaat configureren

Aan de volgende vereisten moet worden voldaan om een apparaat tunnel te kunnen instellen:

* Het apparaat moet een computer zijn die lid is van een domein en waarop Windows 10 Enter prise of onderwijs versie 1709 of hoger wordt uitgevoerd.
* De tunnel kan alleen worden geconfigureerd voor de Windows ingebouwde VPN-oplossing en wordt tot stand gebracht met behulp van IKEv2 met authenticatie van computer certificaten. 
* Er kan slechts één tunnel van een apparaat per apparaat worden geconfigureerd.

1. Installeer client certificaten op de Windows 10-client, zoals wordt weer gegeven in dit [artikel van punt-naar-site VPN-client](point-to-site-how-to-vpn-client-install-azure-cert.md). Het certificaat moet zich in het archief van de lokale computer bestaan.
1. Gebruik [deze instructies](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) voor het maken van een VPN-profiel en het configureren van de tunnel in de context van het lokale systeem account.

### <a name="configuration-example-for-device-tunnel"></a>Configuratie voorbeeld voor de tunnel van apparaten

Nadat u de virtuele netwerk gateway hebt geconfigureerd en het client certificaat in het archief van de lokale computer op de Windows 10-client hebt geïnstalleerd, gebruikt u de volgende voor beelden om een tunnel voor client apparaten te configureren.

1. Kopieer de volgende tekst en sla deze op als ***devicecert. ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopieer de volgende tekst en sla deze op als ***VPNProfile. XML*** in dezelfde map als **devicecert. ps1**. Bewerk de volgende tekst zodat deze overeenkomt met uw omgeving.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Down load **PsExec** van [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) en pak de bestanden uit naar **C:\PSTools**.
1. Start Power shell op basis van een opdracht prompt van de beheerder door de volgende opdracht uit te voeren:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![zo](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. Ga in Power shell naar de map waarin **devicecert. ps1** en **VPNProfile. XML** zich bevinden en voer de volgende opdracht uit:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Voer **Rasphone**uit.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Zoek de vermelding **MachineCertTest** en klik op **verbinden**.

   ![Verbinden](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Als de verbinding is geslaagd, start u de computer opnieuw op. De tunnel wordt automatisch verbonden.

## <a name="cleanup"></a>Opschonen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

![Opschonen](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) voor probleem oplossing
