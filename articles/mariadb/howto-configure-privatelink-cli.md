---
title: Private Link - Azure CLI - Azure Database voor MariaDB
description: Meer informatie over het configureren van privékoppeling voor Azure Database voor MariaDB vanuit Azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371695"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Private Link voor Azure Database voor MariaDB maken en beheren met CLI

Een privéeindpunt is de fundamentele bouwsteen voor privékoppelingen in Azure. Hiermee kunnen Azure-resources, zoals Virtuele Machines (VM's), privé communiceren met privékoppelingsbronnen. In dit artikel leert u hoe u de Azure CLI gebruiken om een VM te maken in een Azure Virtual Network en een Azure Database voor MariaDB-server met een Azure-privéeindpunt.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database voor MariaDB prijsniveaus voor algemeen gebruik en geheugengeoptimaliseerd ondersteunt.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding door te nemen, hebt u het volgende nodig:

- Een [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource maken, moet u een resourcegroep maken om het virtuele netwerk te hosten. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *West-Europa* ge:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Een Azure Database for MariaDB-server maken 
Maak een Azure Database voor MariaDB met de opdracht az mariadb server create. Houd er rekening mee dat de naam van uw MariaDB-server uniek moet zijn in Azure, dus vervang de tijdelijke aanduidingswaarde tussen haakjes door uw eigen unieke waarde: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Let op: de MariaDB ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` Server ID is vergelijkbaar met U gebruikt de MariaDB Server ID in de volgende stap. 

## <a name="create-the-private-endpoint"></a>Het privéeindpunt maken 
Maak een privéeindpunt voor de MariaDB-server in uw virtuele netwerk: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>De private DNS-zone configureren 
Maak een private DNS-zone voor MariDB-serverdomein en maak een koppeling met het virtuele netwerk. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> De FQDN in de DNS-instelling voor klanten wordt niet opgelost in het geconfigureerde privé-IP-adres. U moet een DNS-zone instellen voor de geconfigureerde FQDN zoals [hier](../dns/dns-operations-recordsets-portal.md)wordt weergegeven.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM *myVm* via internet:

1. Voer *myVm*in in de zoekbalk van het portaal.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *bestand downloaded.rdp.*

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Toegang tot de MariaDB-server privé vanaf de VM

1. Open PowerShell in het extern bureaublad van *myVM.*

2. Voer  `nslookup mydemoserver.privatelink.mariadb.database.azure.com` in. 

    Je ontvangt een bericht dat vergelijkbaar is met dit:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Test de privékoppelingsverbinding voor de MariaDB-server met behulp van elke beschikbare client. In het onderstaande voorbeeld heb ik [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) gebruikt om de bewerking uit te voeren.

4. Voer in **Nieuwe verbinding**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsnaam| Selecteer de verbindingsnaam van uw keuze.|
    | Hostnaam | Selecteer *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Gebruikersnaam | Voer de *username@servername* gebruikersnaam in die wordt opgegeven tijdens het maken van de MariaDB-server. |
    | Wachtwoord | Voer een wachtwoord in dat is opgegeven tijdens het maken van de MariaDB-server. |
    ||

5. Selecteer **Verbinding testen** of **OK**.

6. (Optioneel) Bladeren door databases vanuit het linkermenu en informatie maken of query's uit de MariaDB-database

8. Sluit de verbinding met extern bureaublad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer dit niet meer nodig is, u de az-groep verwijderen gebruiken om de brongroep en alle resources die deze heeft te verwijderen: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [wat is azure privéeindpunt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
