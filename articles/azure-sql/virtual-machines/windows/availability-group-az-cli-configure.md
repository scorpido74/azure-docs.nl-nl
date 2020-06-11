---
title: Een beschikbaarheids groep configureren met behulp van de Azure CLI
description: Gebruik de Azure CLI om het Windows-failovercluster, de beschikbaarheids groep-listener en de interne load balancer op een SQL Server VM in azure te maken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669338"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>De Azure CLI gebruiken om een AlwaysOn-beschikbaarheids groep te configureren voor SQL Server op Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u de [Azure cli](/cli/azure/sql/vm?view=azure-cli-latest/) gebruikt om een Windows-failovercluster te implementeren, SQL Server vm's toe te voegen aan het cluster en de interne Load Balancer en listener te maken voor een always on-beschikbaarheids groep. De implementatie van de AlwaysOn-beschikbaarheids groep wordt nog steeds hand matig uitgevoerd via SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Vereisten
Als u de installatie van een always on-beschikbaarheids groep wilt automatiseren met behulp van de Azure CLI, moet u de volgende vereisten hebben: 
- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resource groep met een domein controller. 
- Een of meer virtuele machines die zijn gekoppeld aan een domein [in azure met SQL Server 2016 (of hoger) Enter prise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) in *dezelfde beschikbaarheidsset of verschillende beschikbaarheids zones* die zijn [geregistreerd bij de resource provider van de SQL-VM](sql-vm-resource-provider-register.md).  
- De [Azure cli](/cli/azure/install-azure-cli). 
- Twee beschik bare (niet gebruikt door een entiteit) IP-adressen. Een voor de interne load balancer. De andere is voor de beschikbaarheids groep-listener binnen hetzelfde subnet als de beschikbaarheids groep. Als u een bestaande load balancer gebruikt, hebt u slechts één beschikbaar IP-adres nodig voor de beschikbaarheids groep-listener. 

## <a name="permissions"></a>Machtigingen
U hebt de volgende account machtigingen nodig voor het configureren van de AlwaysOn-beschikbaarheids groep met behulp van de Azure CLI: 

- Een bestaand domein gebruikers account met een machtiging voor het **maken van computer objecten** in het domein. Een domein beheerders account heeft bijvoorbeeld doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). _Dit account moet ook lid zijn van de lokale groep Administrators op elke virtuele machine om het cluster te maken._
- Het domein gebruikers account dat SQL Server beheert. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Stap 1: een opslag account maken als een cloudwitness
Het cluster heeft een opslag account nodig om te fungeren als de cloudwitness. U kunt elk bestaand opslag account gebruiken of u kunt een nieuw opslag account maken. Als u een bestaand opslag account wilt gebruiken, gaat u verder met de volgende sectie. 

Met het volgende code fragment wordt het opslag account gemaakt: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Mogelijk wordt de fout weer geven `az sql: 'vm' is not in the 'az sql' command group` Als u een verouderde versie van de Azure cli gebruikt. Down load de [nieuwste versie van Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) om deze fout te verhelpen.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Stap 2: meta gegevens van het Windows-failovercluster definiëren
De opdracht groep Azure CLI [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) beheert de meta gegevens van de WSFC-service (Windows Server failover cluster) die als host fungeert voor de beschikbaarheids groep. De meta gegevens van het cluster omvatten de Active Directory domein, cluster accounts, opslag accounts die moeten worden gebruikt als de cloudwitness en SQL Server versie. Gebruik [AZ SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) om de meta gegevens voor WSFC te definiëren, zodat wanneer de eerste SQL Server virtuele machine wordt toegevoegd, het cluster wordt gemaakt zoals gedefinieerd. 

Het volgende code fragment definieert de meta gegevens voor het cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Stap 3: SQL Server Vm's toevoegen aan het cluster
Als u de eerste SQL Server VM toevoegt aan het cluster, wordt het cluster gemaakt. Met de opdracht [AZ SQL VM add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) maakt u het cluster met de naam die u eerder hebt opgegeven, installeert u de cluster functie op de SQL Server-vm's en voegt u deze toe aan het cluster. Met het volgende gebruik van de `az sql vm add-to-group` opdracht voegt u meer SQL Server vm's toe aan het zojuist gemaakte cluster. 

Met het volgende code fragment wordt het cluster gemaakt en wordt de eerste SQL Server VM hieraan toegevoegd: 

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
Gebruik deze opdracht om andere SQL Server Vm's toe te voegen aan het cluster. Wijzig alleen de `-n` para meter voor de naam van de SQL Server-VM. 

## <a name="step-4-create-the-availability-group"></a>Stap 4: de beschikbaarheids groep maken
Maak de beschikbaarheids groep hand matig zoals u dat gewoonlijk zou doen met behulp van [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [Power shell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Maak op dit moment *geen* listener, omdat dit wordt gedaan via de Azure cli in de volgende secties.  

## <a name="step-5-create-the-internal-load-balancer"></a>Stap 5: de interne load balancer maken

Voor de always on-beschikbaarheids groep-listener is een intern exemplaar van Azure Load Balancer vereist. De interne load balancer biedt een ' zwevend ' IP-adres voor de beschikbaarheids groep-listener die snellere failover en opnieuw verbinden mogelijk maakt. Als de SQL Server Vm's in een beschikbaarheids groep deel uitmaken van dezelfde beschikbaarheidsset, kunt u een basis load balancer gebruiken. Anders moet u een standaard load balancer gebruiken.  

> [!NOTE]
> De interne load balancer moeten zich in hetzelfde virtuele netwerk bevindt als de VM-exemplaren van SQL Server. 

Met het volgende code fragment maakt u de interne load balancer:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> De open bare IP-resource voor elke SQL Server virtuele machine moet een standaard-SKU hebben om compatibel te zijn met de standaard load balancer. Als u de SKU van de open bare IP-resource van de virtuele machine wilt bepalen, gaat u naar de **resource groep**, selecteert u de resource voor het **open bare IP-adres** voor de gewenste SQL Server VM en zoekt u de waarde onder **SKU** in het deel venster **overzicht** .  

## <a name="step-6-create-the-availability-group-listener"></a>Stap 6: de listener voor de beschikbaarheids groep maken
Nadat u de beschikbaarheids groep hand matig hebt gemaakt, kunt u de listener maken met behulp van [AZ SQL VM AG-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

De *resource-id* van het subnet is de waarde van `/subnets/<subnetname>` toegevoegd aan de resource-id van de virtuele netwerk resource. De resource-ID van het subnet identificeren:
   1. Ga naar de resource groep in de [Azure Portal](https://portal.azure.com). 
   1. Selecteer de bron van het virtuele netwerk. 
   1. Selecteer **Eigenschappen** in het deel venster **instellingen** . 
   1. Identificeer de bron-ID voor het virtuele netwerk en voeg deze toe aan `/subnets/<subnetname>` het einde om de resource-id van het subnet te maken. Bijvoorbeeld:
      - De resource-ID van uw virtuele netwerk is:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - De naam van het subnet is:`default`
      - De ID van uw subnet-bron is daarom:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Met het volgende code fragment wordt de beschikbaarheids groep-listener gemaakt:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Het aantal replica's in een beschikbaarheids groep wijzigen
Er is een toegevoegde laag complexiteit wanneer u een beschikbaarheids groep implementeert voor SQL Server Vm's die worden gehost in Azure. De resource provider en de virtuele-machine groep beheren nu de resources. Als u replica's toevoegt aan of verwijdert uit de beschikbaarheids groep, is er een extra stap voor het bijwerken van de meta gegevens van de listener met informatie over de SQL Server Vm's. Wanneer u het aantal replica's in de beschikbaarheids groep wijzigt, moet u ook de opdracht [AZ SQL VM Group AG-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) gebruiken om de listener bij te werken met de meta gegevens van de SQL Server vm's. 


### <a name="add-a-replica"></a>Een replica toevoegen

Een nieuwe replica toevoegen aan de beschikbaarheids groep:

1. Voeg de SQL Server virtuele machine toe aan het cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Gebruik SQL Server Management Studio om het SQL Server exemplaar toe te voegen als een replica in de beschikbaarheids groep.
1. Voeg de SQL Server VM-meta gegevens toe aan de listener:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Een replica verwijderen

Een replica verwijderen uit de beschikbaarheids groep:

1. Verwijder de replica uit de beschikbaarheids groep met behulp van SQL Server Management Studio. 
1. De meta gegevens van de SQL Server virtuele machine uit de listener verwijderen:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Verwijder de SQL Server-VM uit het cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>De listener voor de beschikbaarheids groep verwijderen
Als u de beschikbaarheids groep die u later hebt geconfigureerd met de Azure CLI wilt verwijderen, moet u de resource provider van de SQL-VM door lopen. Omdat de listener is geregistreerd via de resource provider van de SQL-VM, kunt u deze alleen verwijderen via SQL Server Management Studio onvoldoende. 

U kunt de methode het beste verwijderen via de resource provider van de SQL-VM met behulp van het volgende code fragment in de Azure CLI. Hiermee verwijdert u de meta gegevens van de beschikbaarheids groep van de resource provider van de SQL-VM. Ook wordt de listener fysiek uit de beschikbaarheids groep verwijderd. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server Vm's](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server Vm's](frequently-asked-questions-faq.md)
* [Release opmerkingen voor SQL Server Vm's](../../database/doc-changes-updates-release-notes.md)
* [Licentie modellen voor een SQL Server VM overschakelen](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Overzicht van AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuratie van een Server exemplaar voor Always on-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Beheer van een beschikbaarheids groep &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Bewaking van beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Overzicht van Transact-SQL-instructies voor AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Overzicht van Power shell-cmdlets voor AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
