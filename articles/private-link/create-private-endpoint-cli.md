---
title: 'Snelstartgids: een persoonlijk Azure-eind punt maken met behulp van Azure CLI'
description: Meer informatie over persoonlijk Azure-eind punt in deze Snelstartgids
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 67513c2155e956e005b143c3049abe70a2f126f2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419822"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snelstartgids: een persoonlijk eind punt maken met Azure CLI
Persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), privé communiceren met persoonlijke koppelings bronnen. In deze Quick Start leert u hoe u een virtuele machine maakt in een virtueel netwerk, een SQL Database-Server met een persoonlijk eind punt met behulp van Azure CLI. Vervolgens hebt u toegang tot de virtuele machine en kunt u veilig toegang krijgen tot de persoonlijke koppelings bron (een persoonlijke Azure SQL Database Server in dit voor beeld). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource kunt maken, moet u een resource groep maken om de Virtual Network te hosten. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Een Virtual Network maken
Maak een Virtual Network met [AZ Network vnet Create](/cli/azure/network/vnet). In dit voor beeld wordt een standaard Virtual Network gemaakt met de naam *myVirtualNetwork* met één subnet met de naam *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Beleid voor privé-eind punten van subnet uitschakelen 
Azure implementeert resources in een subnet binnen een virtueel netwerk, dus u moet het subnet maken of bijwerken om beleid voor privé-eindpunt netwerk uit te scha kelen. Een subnet-configuratie met de naam *mySubnet* bijwerken met [AZ Network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>De virtuele machine maken 
Maak een virtuele machine met AZ VM Create. Wanneer u hierom wordt gevraagd, geeft u een wacht woord op dat moet worden gebruikt als aanmeldings referenties voor de virtuele machine. In dit voor beeld wordt een VM gemaakt met de naam *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Noteer het open bare IP-adres van de virtuele machine. U gebruikt dit adres om in de volgende stap verbinding te maken met de virtuele machine via internet.

## <a name="create-a-sql-database-server"></a>Een SQL Database-Server maken 
Maak een SQL Database-Server met de opdracht AZ SQL Server Create. Houd er rekening mee dat de naam van uw SQL Server uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde: 

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

Opmerking de SQL Server-ID lijkt op ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` u de SQL Server-ID in de volgende stap gebruikt. 

## <a name="create-the-private-endpoint"></a>Het persoonlijke eind punt maken 
Maak een persoonlijk eind punt voor de SQL Database-Server in uw Virtual Network: 
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
## <a name="configure-the-private-dns-zone"></a>De Privé-DNS zone configureren 
Maak een Privé-DNS zone voor SQL Database Server domein en maak een koppelings koppeling met de Virtual Network. 
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

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het bestand gedownloade. rdp *.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database Server privé benaderen vanuit de VM

In deze sectie maakt u via het persoonlijke eind punt verbinding met de SQL Database-Server via de VM.

 1. Open Power shell in de Extern bureaublad van *myVM*.
 2. Voer nslookup myserver.database.windows.net in  u een bericht ontvangt dat er ongeveer als volgt uitziet: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. SQL Server Management Studio installeren 
 4. Typ of Selecteer in verbinding maken met server de volgende informatie: server type: Selecteer data base-engine.
 Server naam: Selecteer myserver.database.windows.net gebruikers naam: Voer een gebruikers naam in die tijdens het maken is opgegeven.
 Wacht woord: Voer een wacht woord in dat u hebt opgegeven tijdens het maken.
 Wacht woord onthouden: Selecteer Ja.
 
 5. Selecteer **Verbinden**.
 6. Bladeren door **data bases** vanuit het menu links.
 7. Eventueel Gegevens uit *mydatabase* maken of er een query op uitvoeren
 8. Sluit de verbinding met extern bureau blad met *myVm*.

## <a name="clean-up-resources"></a>Resources opschonen 
U kunt AZ Group Delete gebruiken om de resource groep en alle resources die het heeft, te verwijderen wanneer u deze niet meer nodig hebt: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
