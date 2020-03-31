---
title: Snelstart - Een Azure-privéeindpunt maken met Azure CLI
description: Meer informatie over het privéeindpunt van Azure in deze Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459968"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snelstart: een privéeindpunt maken met Azure CLI
Private Endpoint is de fundamentele bouwsteen voor Private Link in Azure. Hiermee kunnen Azure-resources, zoals virtuele machines (VM's), privé communiceren met Private Link Resources. In deze Quickstart leert u hoe u een VM maakt op een virtueel netwerk, een SQL Database Server met een privéeindpunt met Azure CLI. Vervolgens hebt u toegang tot de VM om de private link-bron (in dit voorbeeld een private Azure SQL Database-server) veilig te openen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource maken, moet u een resourcegroep maken om het virtuele netwerk te hosten. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *Westcentralus* ge:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk met [az-netwerk vnet maken](/cli/azure/network/vnet). In dit voorbeeld wordt een standaard virtueel netwerk met de naam *myVirtualNetwork* gemaakt met één subnet met de naam *mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Subnet privéeindpuntbeleid uitschakelen 
Azure implementeert resources naar een subnet binnen een virtueel netwerk, dus u moet het subnet maken of bijwerken om privéeindpuntnetwerkbeleid uit te schakelen. Update een subnetconfiguratie genaamd *mySubnet* met [az-netwerk vnet subnet update:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>De virtuele machine maken 
Maak een VM met az vm maken. Geef desgevraagd een wachtwoord op dat moet worden gebruikt als aanmeldingsreferenties voor de virtuele machine. In dit voorbeeld wordt een VM met de naam *myVm gemaakt:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Let op het openbare IP-adres van de VM. U gebruikt dit adres om in de volgende stap verbinding te maken met de virtuele machine via internet.

## <a name="create-a-sql-database-server"></a>Een SQL Database Server maken 
Maak een SQL Database Server met de opdracht AZ SQL Server create. Houd er rekening mee dat de naam van uw SQL Server uniek moet zijn in Azure, dus vervang de tijdelijke aanduidingswaarde tussen haakjes door uw eigen unieke waarde: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Houd er rekening mee ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` dat de SQL Server ID vergelijkbaar is met U gebruikt de SQL Server ID in de volgende stap. 

## <a name="create-the-private-endpoint"></a>Het privéeindpunt maken 
Maak een privéeindpunt voor de SQL Database-server in uw virtuele netwerk: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>De private DNS-zone configureren 
Maak een private DNS-zone voor SQL Database-serverdomein en maak een koppeling met het virtuele netwerk. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* via internet:

1. Voer *myVm*in in de zoekbalk van het portaal.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het bestand downloaded.rdp*.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database Server privé openen vanaf de VM

In deze sectie maakt u verbinding met de SQL Database Server van de VM met behulp van het Privéeindpunt.

 1. Open PowerShell in het extern bureaublad van *myVM.*
 2. Voer nslookup  myserver.database.windows.net Je ontvangt een bericht vergelijkbaar met dit: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. SQL Server Management Studio installeren 
 4. Voer in Verbinding maken met de server deze informatie in of selecteer deze: Servertype: Databaseengine selecteren.
 Servernaam: selecteer myserver.database.windows.net gebruikersnaam: voer een gebruikersnaam in die tijdens het maken is opgegeven.
 Wachtwoord: Voer een wachtwoord in dat tijdens het maken is opgegeven.
 Wachtwoord onthouden: Selecteer Ja.
 
 5. Selecteer **Verbinden**.
 6. Blader door **Databases** in het linkermenu.
 7. (Optioneel) Gegevens maken of query's maken uit *mydatabase*
 8. Sluit de verbinding met extern bureaublad met *myVm*.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer dit niet meer nodig is, u de az-groep verwijderen gebruiken om de brongroep en alle resources die deze heeft te verwijderen: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Private Link](private-link-overview.md)
