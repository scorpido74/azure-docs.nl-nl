---
title: Een Point-to-Site (P2S) VPN op Windows configureren voor gebruik met Azure Files | Microsoft Documenten
description: Een Point-to-Site (P2S) VPN configureren op Windows voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273474"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Een Point-to-Site (P2S) VPN op Windows configureren voor gebruik met Azure Files
U een Point-to-Site (P2S) VPN-verbinding gebruiken om uw Azure-bestandsshares vanaf buiten Azure over SMB te monteren, zonder poort 445 te openen. Een Point-to-Site VPN-verbinding is een VPN-verbinding tussen Azure en een individuele client. Als u een P2S VPN-verbinding met Azure Files wilt gebruiken, moet een P2S VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die verbinding moeten maken met uw Azure-bestandsshares vanuit uw on-premises netwerk, u een Site-to-Site (S2S) VPN-verbinding gebruiken in plaats van een Point-to-Site-verbinding voor elke client. Zie [Een site-to-site VPN configureren voor gebruik met Azure Files voor](storage-files-configure-s2s-vpn.md)meer informatie.

We raden u ten zeerste aan [om netwerkoverwegingen voor directe toegang tot azure-bestanden te](storage-files-networking-overview.md) lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure-bestanden.

In het artikel worden de stappen beschreven om een Point-to-Site VPN op Windows (Windows-client en Windows Server) te configureren om Azure-bestandsshares direct on-premises te monteren. Als u Azure File Sync-verkeer wilt routeren via een VPN, raadpleegt u [de proxy- en firewall-instellingen voor Azure File Sync configureren.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de Azure PowerShell-module. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps) en uw besturingssysteem selecteren voor meer informatie over het installeren van de Azure PowerShell-module. Als u de Azure CLI liever op Windows gebruikt, u de onderstaande instructies echter wel voor Azure PowerShell weergeven.

- Een Azure-bestandsshare dat u on-premises wilt monteren. Azure-bestandsshares worden geïmplementeerd in opslagaccounts, die beheerconstructies zijn die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestandsshares en opslagaccounts in [Een Azure-bestandsshare maken.](storage-how-to-create-file-share.md)

- Een privéeindpunt voor het opslagaccount met het Azure-bestandsaandeel dat u on-premises wilt monteren. Zie [Eindpunten voor Azure Files-netwerk configureren](storage-files-networking-endpoints.md?tabs=azure-powershell)voor meer informatie over het maken van een privéeindpunt. 

## <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren
Als u uw Azure-bestandsshare en andere Azure-bronnen via een Point-to-Site VPN on-premises wilt openen, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Windows-machine en dit virtuele Azure-netwerk.

Met de volgende PowerShell wordt een Virtueel Azure-netwerk gemaakt met drie subnetten: een voor het serviceeindpunt van uw opslagaccount, een voor het privéeindpunt van uw opslagaccount, dat vereist is om toegang te krijgen tot het opslagaccount on-premises zonder aangepaste routering te maken voor het openbare IP-adres van het opslagaccount dat kan veranderen, en een voor het privé-netwerkgateway van uw virtuele netwerk die de VPN-service biedt. 

Vergeet niet `<region>` `<resource-group>`om `<desired-vnet-name>` te vervangen, , en met de juiste waarden voor uw omgeving.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Rootcertificaat maken voor VPN-verificatie
Om vpn-verbindingen van uw on-premises Windows-machines te verifiëren om toegang te krijgen tot uw virtuele netwerk, moet u twee certificaten maken: een rootcertificaat, dat wordt verstrekt aan de gateway voor virtuele machines, en een clientcertificaat, dat wordt ondertekend met het rootcertificaat. Met de volgende PowerShell wordt het basiscertificaat gemaakt. het clientcertificaat wordt gemaakt nadat de azure virtual network gateway is gemaakt met informatie van de gateway. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerkgateway implementeren
De Azure virtual network gateway is de service waarmee uw on-premises Windows-machines verbinding maken. Het implementeren van deze service vereist twee basiscomponenten: een openbaar IP dat de gateway naar uw clients identificeert, waar ze zich ook in de wereld bevinden en een rootcertificaat dat u eerder hebt gemaakt en dat wordt gebruikt om uw klanten te verifiëren.

Vergeet niet `<desired-vpn-name-here>` om te vervangen door de naam die u wilt voor deze bronnen.

> [!Note]  
> Het implementeren van de Azure virtual network gateway kan tot 45 minuten duren. Terwijl deze bron wordt geïmplementeerd, wordt dit PowerShell-script geblokkeerd voordat de implementatie moet worden voltooid. Dit is normaal gedrag.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Clientcertificaat maken
Het clientcertificaat wordt gemaakt met de URI van de virtuele netwerkgateway. Dit certificaat is ondertekend met het rootcertificaat dat u eerder hebt gemaakt.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>De VPN-client configureren
De Azure virtual network gateway maakt een downloadbaar pakket met configuratiebestanden die nodig zijn om de VPN-verbinding op uw on-premises Windows-machine te initialiseren. We configureren de VPN-verbinding met de [Always On VPN-functie](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) van Windows 10/Windows Server 2016+. Dit pakket bevat ook uitvoerbare pakketten die de verouderde Windows VPN-client configureren, indien gewenst. Deze handleiding maakt gebruik van Always On VPN in plaats van de oudere Windows VPN-client, omdat de Always On VPN-client eindgebruikers in staat stelt verbinding te maken/los te koppelen van de Azure VPN zonder beheerdersmachtigingen voor hun machine. 

In het volgende script wordt het clientcertificaat geïnstalleerd dat vereist is voor verificatie tegen de virtuele netwerkgateway, download t/m het VPN-pakket. Vergeet niet `<computer1>` `<computer2>` om te vervangen en met de gewenste computers. U dit script op zoveel machines uitvoeren als u `$sessions` wenst door meer PowerShell-sessies aan de array toe te voegen. Uw gebruiksaccount moet een beheerder zijn op elk van deze machines. Als een van deze machines de lokale machine is waarvan u het script uitvoert, moet u het script uitvoeren vanuit een verhoogde PowerShell-sessie. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure-bestandsshare monteren
Nu u uw Point-to-Site VPN hebt ingesteld, u deze gebruiken om de Azure-bestandsshare te monteren op de computers die u via PowerShell instelt. In het volgende voorbeeld wordt het aandeel gemonteerd, de hoofdmap van het aandeel vermeld om te bewijzen dat het aandeel daadwerkelijk is gemonteerd en het aandeel wordt ontkoppeld. Helaas is het niet mogelijk om het aandeel hardnekkig te monteren over PowerShell remoting. Zie [Een Azure-bestandsshare gebruiken met Windows](storage-how-to-use-files-windows.md)als u voortdurend wilt monteren. 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Zie ook
- [Netwerkoverwegingen voor directe azure-toegang tot bestandsdelen](storage-files-networking-overview.md)
- [Een Point-to-Site (P2S) VPN op Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Een Site-to-Site (S2S) VPN configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)
