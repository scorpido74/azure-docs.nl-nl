---
title: Een Point-to-Site (P2S) VPN op Linux configureren voor gebruik met Azure Files | Microsoft Documenten
description: Een Point-to-Site (P2S) VPN configureren op Linux voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061054"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Een Point-to-Site (P2S) VPN op Linux configureren voor gebruik met Azure Files
U een Point-to-Site (P2S) VPN-verbinding gebruiken om uw Azure-bestandsshares vanaf buiten Azure over SMB te monteren, zonder poort 445 te openen. Een Point-to-Site VPN-verbinding is een VPN-verbinding tussen Azure en een individuele client. Als u een P2S VPN-verbinding met Azure Files wilt gebruiken, moet een P2S VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die verbinding moeten maken met uw Azure-bestandsshares vanuit uw on-premises netwerk, u een Site-to-Site (S2S) VPN-verbinding gebruiken in plaats van een Point-to-Site-verbinding voor elke client. Zie [Een site-to-site VPN configureren voor gebruik met Azure Files voor](storage-files-configure-s2s-vpn.md)meer informatie.

We raden u ten zeerste aan [het netwerkoverzicht azure files](storage-files-networking-overview.md) te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure Files.

In het artikel worden de stappen beschreven om een Point-to-Site VPN op Linux te configureren om Azure-bestandsshares direct on-premises te monteren. Als u Azure File Sync-verkeer wilt routeren via een VPN, raadpleegt u [de proxy- en firewall-instellingen voor Azure File Sync configureren.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de Azure CLI. Zie [Azure PowerShell CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) en uw besturingssysteem selecteren voor meer informatie over het installeren van de Azure CLI. Als u de Azure PowerShell-module liever op Linux gebruikt, u, maar de onderstaande instructies worden gepresenteerd voor Azure CLI.

- Een Azure-bestandsshare dat u on-premises wilt monteren. Azure-bestandsshares worden geïmplementeerd in opslagaccounts, die beheerconstructies zijn die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestandsshares en opslagaccounts in [Een Azure-bestandsshare maken.](storage-how-to-create-file-share.md)

- Een privéeindpunt voor het opslagaccount met het Azure-bestandsaandeel dat u on-premises wilt monteren. Zie [Eindpunten voor Azure Files-netwerk configureren](storage-files-networking-endpoints.md?tabs=azure-cli)voor meer informatie over het maken van een privéeindpunt. 

## <a name="install-required-software"></a>Vereiste software installeren
De Azure virtual network gateway kan VPN-verbindingen bieden via verschillende VPN-protocollen, waaronder IPsec en OpenVPN. Deze gids laat zien hoe ipsec te gebruiken en maakt gebruik van de strongSwan pakket om de ondersteuning op Linux. 

> Geverifieerd met Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren 
Als u uw Azure-bestandsshare en andere Azure-bronnen via een Point-to-Site VPN on-premises wilt openen, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Linux-machine en dit virtuele Azure-netwerk.

Met het volgende script wordt een Virtueel Azure-netwerk gemaakt met drie subnetten: een voor het serviceeindpunt van uw opslagaccount, één voor het privéeindpunt van uw opslagaccount, dat vereist is om on-premises toegang te krijgen tot het opslagaccount zonder aangepaste routering voor het openbare IP van het opslagaccount dat kan veranderen, en een voor uw virtuele netwerkgateway die de VPN-service biedt. 

Vergeet niet `<region>` `<resource-group>`om `<desired-vnet-name>` te vervangen, , en met de juiste waarden voor uw omgeving.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Certificaten maken voor VPN-verificatie
Om vpn-verbindingen van uw on-premises Linux-machines te verifiëren om toegang te krijgen tot uw virtuele netwerk, moet u twee certificaten maken: een rootcertificaat, dat wordt verstrekt aan de gateway van de virtuele machine en een clientcertificaat, dat ondertekend met het basiscertificaat. In het volgende script worden de vereiste certificaten gemaakt.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerkgateway implementeren
De Azure virtual network gateway is de service waarmee uw on-premises Linux-machines verbinding maken. Het implementeren van deze service vereist twee basiscomponenten: een openbaar IP dat de gateway naar uw clients identificeert, waar ze zich ook in de wereld bevinden en een rootcertificaat dat u eerder hebt gemaakt en dat wordt gebruikt om uw klanten te verifiëren.

Vergeet niet `<desired-vpn-name-here>` om te vervangen door de naam die u wilt voor deze bronnen.

> [!Note]  
> Het implementeren van de Azure virtual network gateway kan tot 45 minuten duren. Terwijl deze bron wordt geïmplementeerd, wordt dit scriptscript voor bash geblokkeerd voordat de implementatie moet worden voltooid. Dit is normaal gedrag.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>De VPN-client configureren
De Azure virtual network gateway maakt een downloadbaar pakket met configuratiebestanden die nodig zijn om de VPN-verbinding op uw on-premises Linux-machine te initialiseren. In het volgende script worden de certificaten die `ipsec.conf` u hebt gemaakt op de juiste plek geplaatst en wordt het bestand geconfigureerd met de juiste waarden uit het configuratiebestand in het downloadbare pakket.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure-bestandsshare monteren
Nu u uw Point-to-Site VPN hebt ingesteld, u uw Azure-bestandsshare instellen. In het volgende voorbeeld wordt het aandeel niet-voortdurend gemonteerd. Zie [Een Azure-bestandsshare gebruiken met Linux](storage-how-to-use-files-linux.md)als u voortdurend wilt monteren. 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files-netwerken](storage-files-networking-overview.md)
- [Een Point-to-Site (P2S) VPN op Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een Site-to-Site (S2S) VPN configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)