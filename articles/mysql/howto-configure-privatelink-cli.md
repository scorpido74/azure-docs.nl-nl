---
title: Persoonlijke koppeling-Azure CLI-Azure Database for MySQL
description: Meer informatie over het configureren van een persoonlijke koppeling voor Azure Database for MySQL van Azure CLI
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f83f52f1c1800803c5e1d47f1931f7b13b2c11de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79368006"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Een persoonlijke koppeling voor Azure Database for MySQL maken en beheren met CLI

Een persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunnen Azure-resources, zoals Virtual Machines (Vm's), privé communiceren met persoonlijke koppelings bronnen. In dit artikel leert u hoe u de Azure CLI gebruikt om een virtuele machine te maken in een Azure-Virtual Network en een Azure Database for MySQL-server met een persoonlijk Azure-eind punt.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen ondersteunt.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource kunt maken, moet u een resource groep maken om de Virtual Network te hosten. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *Europa West* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
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

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken 
Maak een Azure Database for MySQL met de opdracht AZ mysql server Create. Houd er rekening mee dat de naam van uw MySQL-server uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde: 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Opmerking de MySQL-Server-ID is ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` vergelijkbaar met de mysql-server-id in de volgende stap. 

## <a name="create-the-private-endpoint"></a>Het persoonlijke eind punt maken 
Maak een persoonlijk eind punt voor de MySQL-server in uw Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>De Privé-DNS zone configureren 
Maak een Privé-DNS zone voor het MySQL-Server domein en maak een koppelings koppeling met de Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> De FQDN in de DNS-instelling van de klant wordt niet omgezet naar het geconfigureerde particuliere IP-adres. U moet een DNS-zone voor de geconfigureerde FQDN instellen, zoals [hier](../dns/dns-operations-recordsets-portal.md)wordt weer gegeven.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het *gedownloade RDP* -bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **meer opties** > selecteren**een ander account gebruiken**om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>De MySQL-server privé openen vanuit de VM

1. Open Power shell in de Extern bureaublad van *myVM*.

2. Voer  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com` in. 

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Test de verbinding van de particuliere verbinding voor de MySQL-server met behulp van een beschik bare client. In het onderstaande voor beeld heb ik [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) gebruikt om de bewerking uit te voeren.


4. In **nieuwe verbinding**voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsnaam| Selecteer de verbindings naam van uw keuze.|
    | Hostnaam | *Mydemoserver.privatelink.mysql.database.Azure.com* selecteren |
    | Gebruikersnaam | Voer de gebruikers *username@servername* naam in, die wordt opgegeven tijdens het maken van de mysql-server. |
    | Wachtwoord | Voer een wacht woord in dat is opgegeven tijdens het maken van de MySQL-server. |
    ||

5. Selecteer Verbinden.

6. Bladeren door data bases vanuit het menu links.

7. Eventueel Gegevens uit de MySQL-data base maken of er query's op uitvoeren.

8. Sluit de verbinding met extern bureau blad met myVm.

## <a name="clean-up-resources"></a>Resources opschonen 
U kunt AZ Group Delete gebruiken om de resource groep en alle resources die het heeft, te verwijderen wanneer u deze niet meer nodig hebt: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Wat is Azure persoonlijk eind punt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) ?
