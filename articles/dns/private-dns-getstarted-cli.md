---
title: Snelstart - Een Azure private DNS-zone maken met de Azure CLI
description: In deze quickstart maakt en test u een privé-DNS-zone en neemt u op in Azure DNS. Dit is een stapsgewijze handleiding voor het maken en beheren van uw eerste privé-DNS-zone en -record met behulp van de Azure CLI.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 34a9858ffbec2360a347b8c765d53551fd2d9fc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76935434"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Snelstart: een Azure private DNS-zone maken met de Azure CLI

Met deze snelle start leidt u door de stappen om uw eerste privé-DNS-zone te maken en op te nemen met de Azure CLI.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Als u een privé-DNS-zone wilt publiceren naar uw virtuele netwerk, geeft u de lijst op met virtuele netwerken die zijn toegestaan om records in de zone om te zetten.  Deze worden *gekoppelde* virtuele netwerken genoemd. Wanneer automatische registratie is ingeschakeld, werkt Azure DNS ook de zonerecords bij wanneer een virtuele machine wordt gemaakt, het IP-adres ervan wijzigt of wordt verwijderd.

In deze snelstart leert u de volgende zaken:

> [!div class="checklist"]
> * Een privé-DNS-zone maken
> * Virtuele testmachines maken
> * Een extra DNS-record maken
> * De privézone testen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Als u dat liever hebt, u deze quickstart voltooien met [Azure PowerShell.](private-dns-getstarted-powershell.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>De resourcegroep maken

Eerst maakt u een resourcegroep die de DNS-zone gaat bevatten: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

In het volgende voorbeeld wordt een virtueel netwerk met de naam **myAzureVNet aandeed.** Vervolgens wordt een DNS-zone met de naam **private.contoso.com** in de **brongroep MyAzureResourceGroup,** wordt de DNS-zone gekoppeld aan het virtuele **MyAzureVnet-netwerk** en wordt automatische registratie mogelijk.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Als u een zone wilt maken alleen voor naamomzetting (geen `-e false` automatische hostnameregistratie), u de parameter gebruiken.

### <a name="list-dns-private-zones"></a>Lijst met privé-DNS-zones weergeven

Gebruik `az network private-dns zone list` als u een opsomming wilt maken van DNS-zones. Zie `az network dns zone list --help` voor help.

Bij het opgeven van de resourcegroep worden alleen de zones in de resourcegroep vermeld:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Als de resourcegroepen worden weggelaten, worden alle zones in het abonnement vermeld:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>De virtuele testmachines maken

Maak nu twee virtuele machines, zodat u uw DNS-privézone kunt testen:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Het duurt enkele minuten voordat dit is voltooid.

## <a name="create-an-additional-dns-record"></a>Een extra DNS-record maken

Gebruik de opdracht `az network private-dns record-set [record type] add-record` om een DNS-record te maken. Voor hulp met bijvoorbeeld het toevoegen van A-records raadpleegt u `az network private-dns record-set A add-record --help`.

 In het volgende voorbeeld wordt een record met de relatieve naam **db** in de DNS-zone **private.contoso.com**, in resourcegroep **MyAzureResourceGroup**. De volledig gekwalificeerde naam van het record is **db.private.contoso.com**. Het recordtype is A, met IP-adres 10.2.0.4.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>DNS-records weergeven

Als u de DNS-records in uw zone wilt weergeven,voert u de volgende opdracht uit:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>De privézone testen

Nu u de naamresolutie testen voor uw **private.contoso.com** privézone.

### <a name="configure-vms-to-allow-inbound-icmp"></a>VM’s configureren voor het toestaan van inkomende ICMP

U kunt de pingopdracht gebruiken voor het testen van naamomzetting. Configureer de firewall dus op beide virtuele machines om inkomende ICMP-pakketten toe te staan.

1. Maak verbinding met myVM01 en open een Windows PowerShell-venster met beheerdersbevoegdheden.
2. Voer de volgende opdracht uit:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Herhaal voor myVM02.

### <a name="ping-the-vms-by-name"></a>De VM's pingen op naam

1. Ping vanuit de myVM02 Windows PowerShell-opdrachtprompt myVM01 met de naam van de automatisch geregistreerde hostnaam:

   ```
   ping myVM01.private.contoso.com
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Ping nu de **db**-naam die u eerder hebt gemaakt:

   ```
   ping db.private.contoso.com
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Alle resources verwijderen

Wanneer u niet langer nodig bent, verwijdert u de resourcegroep **MyAzureResourceGroup** om de bronnen te verwijderen die in deze snelstart zijn gemaakt.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scenario's voor Azure DNS-privézones](private-dns-scenarios.md)

