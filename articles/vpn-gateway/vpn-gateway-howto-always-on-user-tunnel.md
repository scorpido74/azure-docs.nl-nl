---
title: Een permanente VPN-gebruikers tunnel configureren
titleSuffix: Azure VPN Gateway
description: In dit artikel wordt beschreven hoe u een always on-VPN-gebruikers tunnel configureert voor uw VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bff2ed48a78bfbae984dea5e5474971817023bc6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729318"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Een AlwaysOn-VPN-gebruikerstunnel configureren

Een nieuwe functie van de Windows 10 VPN-client, altijd aan, is de mogelijkheid om een VPN-verbinding te onderhouden. Met Always on kan het actieve VPN-profiel automatisch verbinding maken en blijven verbonden op basis van triggers, zoals het aanmelden van gebruikers, het wijzigen van de netwerk status of het scherm apparaat actief.

U kunt virtuele Azure-netwerk gateways met Windows 10 altijd on gebruiken om permanente gebruikers tunnels en tunnels voor apparaten te maken met Azure. Dit artikel helpt u bij het configureren van een always on-VPN-gebruikers tunnel.

Altijd op VPN-verbindingen zijn twee typen tunnels:

* **Tunnel van apparaat**: Hiermee maakt u verbinding met opgegeven VPN-servers voordat gebruikers zich aanmelden bij het apparaat. Bij connectiviteits scenario's voor aanmelding en Apparaatbeheer wordt een tunnel gebruikt.

* **Gebruikers tunnel**: maakt alleen verbinding nadat gebruikers zich aanmelden op het apparaat. Door gebruikers tunnels te gebruiken, hebt u toegang tot bedrijfs bronnen via VPN-servers.

Tunnels en gebruikers tunnels van apparaten worden onafhankelijk van hun VPN-profielen gebruikt. Ze kunnen tegelijkertijd worden verbonden en ze kunnen, indien van toepassing, verschillende verificatie methoden en andere VPN-configuratie-instellingen gebruiken.

In de volgende secties configureert u een VPN-gateway en een gebruikers tunnel.

## <a name="step-1-configure-a-vpn-gateway"></a>Stap 1: een VPN-gateway configureren

U configureert de VPN-gateway voor gebruik van IKEv2 en authenticatie op basis van certificaten door de instructies in dit [punt-naar-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) -artikel te volgen.

## <a name="step-2-configure-a-user-tunnel"></a>Stap 2: een gebruikers tunnel configureren

1. Installeer client certificaten op de Windows 10-client, zoals wordt weer gegeven in dit artikel van [punt-naar-site VPN-client](point-to-site-how-to-vpn-client-install-azure-cert.md) . Het certificaat moet zich in het huidige gebruikers archief beslaan.

1. Configureer de always on VPN-client via Power shell, System Center Configuration Manager of intune door de instructies in [Windows 10 client altijd op VPN-verbindingen configureren](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)te volgen.

### <a name="example-configuration-for-the-user-tunnel"></a>Voorbeeld configuratie voor de gebruikers tunnel

Nadat u de virtuele netwerk gateway hebt geconfigureerd en het client certificaat in het archief van de lokale computer op de Windows 10-client hebt geïnstalleerd, configureert u een tunnel voor client apparaten met behulp van de volgende voor beelden:

1. Kopieer de volgende tekst en sla deze op als *usercert. ps1*:

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
1. Kopieer de volgende tekst en sla deze op als *VPNProfile. XML* in dezelfde map als *usercert. ps1*. Bewerk de volgende tekst zodat deze overeenkomt met uw omgeving:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

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
1. Voer PowerShell uit als beheerder.

1. Ga in Power shell naar de map waarin *usercert. ps1* en *VPNProfile. XML* zich bevinden en voer de volgende opdracht uit:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Zoek de vermelding **UserTest** onder **VPN-instellingen**en selecteer vervolgens **verbinding maken**.

1. Als de verbinding is geslaagd, hebt u een always on-gebruikers tunnel geconfigureerd.

## <a name="clean-up-your-resources"></a>Uw resources opschonen

Ga als volgt te werk om het profiel te verwijderen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Verbreek de verbinding en schakel het selectie vakje **automatisch verbinding maken** uit.

![Opschonen](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor informatie over het oplossen van verbindings problemen die zich kunnen voordoen.
