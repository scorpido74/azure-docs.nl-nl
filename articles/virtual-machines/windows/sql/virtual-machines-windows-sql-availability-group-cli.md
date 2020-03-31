---
title: Een beschikbaarheidsgroep configureren (Azure CLI)
description: Gebruik de Azure CLI om het Windows-failovercluster, de listener van de beschikbaarheidsgroep en de interne load balancer op een SQL Server VM in Azure te maken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022371"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>De Azure CLI gebruiken om een groep Always On-beschikbaarheid voor SQL Server op een Azure VM te configureren
In dit artikel wordt beschreven hoe u de [Azure CLI kunt](/cli/azure/sql/vm?view=azure-cli-latest/) gebruiken om een Windows-failovercluster te implementeren, SQL Server VM's aan het cluster toe te voegen en de interne load balancer en listener te maken voor een groep beschikbaarheid always on. De implementatie van de groep Always On beschikbaarheid gebeurt nog steeds handmatig via SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Vereisten
Als u de installatie van een beschikbaarheidsgroep Always On wilt automatiseren met de Azure CLI, moet u de volgende vereisten hebben: 
- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resourcegroep met een domeincontroller. 
- Een of meer met een domein verbonden [VM's in Azure met SQL Server 2016 (of hoger) Enterprise-editie](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) in *dezelfde beschikbaarheidsset of verschillende beschikbaarheidszones* die zijn [geregistreerd bij de SQL VM-resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- De [Azure CLI](/cli/azure/install-azure-cli). 
- Twee beschikbare (niet gebruikt door een entiteit) IP-adressen. Een daarvan is voor de interne load balancer. De andere is voor de listener van de beschikbaarheidsgroep binnen hetzelfde subnet als de beschikbaarheidsgroep. Als u een bestaande load balancer gebruikt, hebt u slechts één beschikbaar IP-adres nodig voor de listener van de beschikbaarheidsgroep. 

## <a name="permissions"></a>Machtigingen
U hebt de volgende accountmachtigingen nodig om de groep Beschikbaarheid always on te configureren met de Azure CLI: 

- Een bestaand domeingebruikersaccount met de machtiging **Computerobject maken** in het domein. Een domeinbeheerdersaccount heeft bijvoorbeeld doorgaans voldoende toestemming account@domain.com(bijvoorbeeld: ). _Dit account moet ook deel uitmaken van de lokale beheerdersgroep op elke vm om het cluster te maken._
- Het domeingebruikersaccount dat de SQL Server-service beheert. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Stap 1: Een opslagaccount maken als cloudgetuige
Het cluster heeft een opslagaccount nodig om als cloudgetuige op te treden. U elk bestaand opslagaccount gebruiken of u een nieuw opslagaccount maken. Als u een bestaand opslagaccount wilt gebruiken, gaat u verder naar de volgende sectie. 

In het volgende codefragment wordt het opslagaccount gemaakt: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Mogelijk ziet u `az sql: 'vm' is not in the 'az sql' command group` de fout als u een verouderde versie van de Azure CLI gebruikt. Download de [nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) om deze fout te verkomen.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Stap 2: Windows-failoverclustermetagegevens definiëren
De [commanditeergroep Azure](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) CLI az sql vm beheert de metagegevens van de WSFC-service (Failover Cluster) van Windows Server die de beschikbaarheidsgroep host. Clustermetagegevens omvatten het Active Directory-domein, clusteraccounts, opslagaccounts die moeten worden gebruikt als cloudgetuige en SQL Server-versie. Gebruik [de az sql vm-groep maken](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) om de metagegevens voor WSFC te definiëren, zodat wanneer de eerste SQL Server VM wordt toegevoegd, het cluster wordt gemaakt zoals gedefinieerd. 

In het volgende codefragment worden de metagegevens voor het cluster gedefinieerd:
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Stap 3: SQL Server VM's toevoegen aan het cluster
Als u de eerste SQL Server VM aan het cluster toevoegt, wordt het cluster gesmaakt. De opdracht [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) maakt het cluster met de eerder opgegeven naam, installeert de clusterrol op de SQL Server VM's en voegt deze toe aan het cluster. Volgende toepassingen `az sql vm add-to-group` van de opdracht voegen meer SQL Server VM's toe aan het nieuw gemaakte cluster. 

In het volgende codefragment wordt het cluster gemaakt en wordt de eerste SQL Server VM toegevoegd: 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Gebruik deze opdracht om andere SQL Server VM's aan het cluster toe te voegen. Wijzig alleen `-n` de parameter voor de SQL Server VM-naam. 

## <a name="step-4-create-the-availability-group"></a>Stap 4: De beschikbaarheidsgroep maken
Maak handmatig de beschikbaarheidsgroep zoals u dat normaal zou doen, met SQL [Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Maak op dit moment *geen* listener omdat dit in de volgende secties via de Azure CLI wordt gedaan.  

## <a name="step-5-create-the-internal-load-balancer"></a>Stap 5: De interne load balancer maken

De groeplistener Always On beschikbaarheid vereist een interne instantie van Azure Load Balancer. De interne load balancer biedt een "zwevend" IP-adres voor de authenticiteitsgroep beschikbaarheid dat een snellere failover en heraansluiting mogelijk maakt. Als de SQL Server VM's in een beschikbaarheidsgroep deel uitmaken van dezelfde beschikbaarheidsset, u een Basic load balancer gebruiken. Anders moet u een standaard load balancer gebruiken.  

> [!NOTE]
> De interne load balancer moet zich in hetzelfde virtuele netwerk bevinden als de SQL Server VM-exemplaren. 

In het volgende codefragment wordt de interne load balancer gemaakt:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> De openbare IP-bron voor elke SQL Server VM moet een standaard SKU hebben die compatibel is met de standaardloadbalancer. Als u de SKU van de openbare IP-bron van uw VM wilt bepalen, gaat u naar **Resourcegroep,** selecteert u uw **bron voor openbaar IP-adres** voor de gewenste SQL Server VM en zoekt u de waarde onder **SKU** in het deelvenster **Overzicht.**  

## <a name="step-6-create-the-availability-group-listener"></a>Stap 6: De listener van de beschikbaarheidsgroep maken
Nadat u de beschikbaarheidsgroep handmatig hebt gemaakt, u de listener maken met behulp van [az sql vm ag-listener.](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create) 

De *subnetbron-id* is `/subnets/<subnetname>` de waarde van de bron-ID van de virtuele netwerkbron. Ga als u de subnetbron-id identificeren:
   1. Ga naar uw resourcegroep in de [Azure-portal.](https://portal.azure.com) 
   1. Selecteer de virtuele netwerkbron. 
   1. Selecteer **Eigenschappen** in het deelvenster **Instellingen.** 
   1. Identificeer de bron-ID voor het `/subnets/<subnetname>` virtuele netwerk en sluit u aan bij het einde ervan om de subnetbron-id te maken. Bijvoorbeeld:
      - Uw virtuele netwerkbron-ID is:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Uw subnetnaam is:`default`
      - Daarom is uw subnetbron-ID:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


In het volgende codefragment wordt de listener van de beschikbaarheidsgroep gekanalen:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Het aantal replica's in een beschikbaarheidsgroep wijzigen
Er is een extra laag van complexiteit wanneer u een beschikbaarheidsgroep implementeert naar SQL Server VM's die in Azure worden gehost. De resourceprovider en de virtuele machinegroep beheren nu de resources. Als zodanig is er een extra stap om de metagegevens van de listener bij te werken met informatie over de SQL Server VM's wanneer u replica's toevoegt of verwijdert in de beschikbaarheidsgroep. Wanneer u het aantal replica's in de beschikbaarheidsgroep wijzigt, moet u ook de opdracht [az sql vm-group ag-listener-update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) gebruiken om de listener bij te werken met de metagegevens van de SQL Server VM's. 


### <a name="add-a-replica"></a>Een replica toevoegen

Ga als lid van het volgende over een nieuwe replica naar de beschikbaarheidsgroep:

1. Voeg de SQL Server VM toe aan het cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Gebruik SQL Server Management Studio om de SQL Server-instantie toe te voegen als een replica binnen de beschikbaarheidsgroep.
1. Voeg de SQL Server VM-metagegevens toe aan de listener:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Een replica verwijderen

Ga als lid van het werk om een replica uit de beschikbaarheidsgroep te verwijderen:

1. Verwijder de replica uit de beschikbaarheidsgroep met SQL Server Management Studio. 
1. Verwijder de SQL Server VM-metagegevens uit de listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Verwijder de SQL Server VM uit het cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>De listener voor beschikbaarheidsgroep verwijderen
Als u later de authenticiteit van de beschikbaarheidsgroep moet verwijderen die is geconfigureerd met de Azure CLI, moet u de SQL VM-bronprovider doorlopen. Omdat de listener is geregistreerd via de SQL VM-resourceprovider, is het verwijderen via SQL Server Management Studio onvoldoende. 

De beste methode is om deze te verwijderen via de SQL VM-resourceprovider met behulp van het volgende codefragment in de Azure CLI. Hiermee worden de metagegevens van de beschikbaarheidsgroeplistener verwijderd uit de SQL VM-bronprovider. Het verwijdert ook fysiek de luisteraar uit de beschikbaarheidsgroep. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server VM's](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen voor SQL Server VM's](virtual-machines-windows-sql-server-iaas-faq.md)
* [Releasenotes voor SQL Server VM's](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Schakelen tussen licentiemodellen voor een SQL Server VM](virtual-machines-windows-sql-ahb.md)
* [Overzicht van beschikbaarheidsgroepen altijd op &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuratie van een serverexemplaar voor beschikbaarheidsgroepen always op &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Beheer van een beschikbaarheidsgroep &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Bewaken van beschikbaarheidsgroepen &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Overzicht van Transact-SQL-instructies voor beschikbaarheidsgroepen altijd op &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Overzicht van PowerShell-cmdlets voor beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
