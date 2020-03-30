---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371724"
---
Aan de volgende eisen moet worden voldaan om met succes een apparaattunnel tot stand te brengen:

* Het apparaat moet een domein aangesloten computer met Windows 10 Enterprise of Education versie 1809 of hoger.
* De tunnel is alleen configureerbaar voor de windows ingebouwde VPN-oplossing en is ingesteld met BEHULPEv2 met computercertificaatauthenticatie.
* Per apparaat kan slechts één apparaattunnel worden geconfigureerd.

1. Installeer clientcertificaten op de Windows [10-client met behulp van het point-to-site VPN-clientartikel.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) Het certificaat moet in de lokale machinewinkel liggen.
1. Maak een VPN-profiel en configureer apparaattunnel in de context van het LOKALE SYSTEEM-account met behulp van [deze instructies.](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)

### <a name="configuration-example-for-device-tunnel"></a>Configuratievoorbeeld voor apparaattunnel

Nadat u de virtuele netwerkgateway hebt geconfigureerd en het clientcertificaat hebt geïnstalleerd in het lokale machinearchief op de Windows 10-client, gebruikt u de volgende voorbeelden om een clientapparaattunnel te configureren:

1. Kopieer de volgende tekst en sla deze op als ***devicecert.ps1***.

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
1. Kopieer de volgende tekst en sla deze op als ***VPNProfile.xml*** in dezelfde map als **devicecert.ps1**. Bewerk de volgende tekst die overeenkomt met uw omgeving.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

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
1. Download **PsExec** van [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) en haal de bestanden naar **C:\PSTools**.
1. Start PowerShell op basis van een cmd-prompt voor beheerders door het volgende uit te voeren:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. Schakel in PowerShell over naar de map waar **devicecert.ps1** en **VPNProfile.xml** zich bevinden en voer de volgende opdracht uit:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Run **rasphone**.

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Zoek naar het **item MachineCertTest** en klik op **Verbinden**.

   ![Verbinding maken](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Als de verbinding slaagt, start u de computer opnieuw op. De tunnel wordt automatisch aangesloten.
