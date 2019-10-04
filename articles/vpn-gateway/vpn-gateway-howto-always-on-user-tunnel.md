---
title: Een always on-VPN-tunnel configureren voor VPN Gateway
description: Stappen voor het configureren van de permanente gebruikers-VPN-tunnel voor VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846470"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Een always on-VPN-gebruikers tunnel configureren

Een van de nieuwe functies van de VPN-client (virtueel particulier netwerk) van Windows 10 is de mogelijkheid om een VPN-verbinding te onderhouden. Always on is een Windows 10-functie waarmee het actieve VPN-profiel automatisch verbinding kan maken en verbonden blijft op basis van triggers, namelijk gebruikers aanmelding, wijziging van de netwerk status of het scherm apparaat actief.

Virtuele Azure-netwerk gateways kunnen worden gebruikt met Windows 10 altijd aan om permanente gebruikers tunnels tot stand te brengen, evenals de tunnels van apparaten met Azure. Dit artikel helpt u bij het configureren van een always on-VPN-gebruikers tunnel.

Altijd op VPN-verbindingen zijn twee typen tunnels:

* De tunnel van het **apparaat** maakt verbinding met opgegeven VPN-servers voordat gebruikers zich aanmelden op het apparaat. Bij connectiviteits scenario's voor aanmelding en Apparaatbeheer wordt de tunnel apparaat gebruikt.

* **Gebruikers tunnel** maakt verbinding alleen nadat een gebruiker zich heeft aangemeld bij het apparaat. Gebruikers tunnel stelt gebruikers in staat om toegang te krijgen tot bedrijfs bronnen via VPN-servers.

Tunnels en gebruikers tunnels van apparaten worden onafhankelijk van hun VPN-profielen gebruikt. Ze kunnen tegelijkertijd worden verbonden en kunnen verschillende verificatie methoden en andere VPN-configuratie-instellingen gebruiken.

## <a name="1-configure-the-gateway"></a>1. De gateway configureren

Configureer de VPN-gateway voor gebruik van IKEv2 en verificatie op basis van certificaten met behulp van dit [punt-naar-site-artikel](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. De gebruikers tunnel configureren

1. Installeer client certificaten op de Windows 10-client, zoals wordt weer gegeven in dit [artikel van punt-naar-site VPN-client](point-to-site-how-to-vpn-client-install-azure-cert.md). Het certificaat moet zich in het huidige gebruikers archief beslaan
2. Configureer de always on VPN-client via Power shell, SCCM of intune met behulp van [deze instructies](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Configuratie voorbeeld voor gebruikers tunnel

Nadat u de virtuele netwerk gateway hebt geconfigureerd en het client certificaat in het archief van de lokale computer op de Windows 10-client hebt geïnstalleerd, gebruikt u de volgende voor beelden om een tunnel voor client apparaten te configureren.

1. Kopieer de volgende tekst en sla deze op als ***usercert. ps1***.

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
1. Kopieer de volgende tekst en sla deze op als ***VPNProfile. XML*** in dezelfde map als **usercert. ps1**. Bewerk de volgende tekst zodat deze overeenkomt met uw omgeving.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Voer Power shell uit als Administrator.

1. Ga in Power shell naar de map waarin **usercert. ps1** en **VPNProfile. XML** zich bevinden en voer de volgende opdracht uit:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Kijk onder VPN-instellingen.

1. Zoek de vermelding **UserTest** en klik op **verbinden**.

1. Als de verbinding is geslaagd, hebt u een permanente gebruikers tunnel geconfigureerd.

## <a name="cleanup"></a>Opschonen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

1. Verbreek de verbinding en schakel automatisch verbinding maken uit

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Opschonen](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) voor probleem oplossing
