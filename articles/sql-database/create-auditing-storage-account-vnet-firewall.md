---
title: Controle naar opslagaccount achter VNet en firewall
description: Controle configureren om databasegebeurtenissen te schrijven op een opslagaccount achter virtueel netwerk en firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387627"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Audit schrijven naar een opslagaccount achter VNet en firewall

Auditing voor [Azure SQL Database](sql-database-technical-overview.md) en Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ondersteunt het schrijven van databasegebeurtenissen naar een Azure [Storage-account](../storage/common/storage-account-overview.md) achter een virtueel netwerk en firewall. 

In dit artikel worden twee manieren uitgelegd om Azure SQL Server en Azure-opslagaccount voor deze optie te configureren. De eerste maakt gebruik van de Azure-portal, de tweede maakt gebruik van REST.

### <a name="background"></a>Achtergrond

[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) is de fundamentele bouwsteen voor uw privénetwerk in Azure. Met VNet kunnen veel typen Azure-resources, zoals Virtual Azure Machines (VM), veilig met elkaar, internet en on-premises netwerken communiceren. VNet is vergelijkbaar met een traditioneel netwerk in uw eigen datacenter, maar brengt extra voordelen met zich mee van Azure-infrastructuur zoals schaal, beschikbaarheid en isolatie.

Zie [Wat is Azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over de VNet-concepten, Aanbevolen procedures en nog veel meer.

Zie [Snelstart: Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md)voor meer informatie over het maken van een virtueel netwerk.

## <a name="prerequisites"></a>Vereisten

Voor controle om te schrijven naar een opslagaccount achter een VNet of firewall, zijn de volgende vereisten vereist:

> [!div class="checklist"]
> * Een v2-opslagaccount voor algemene doeleinden. Als u een v1- of blobopslagaccount voor algemene doeleinden hebt, [kunt u upgraden naar een v2-opslagaccount voor algemene doeleinden](../storage/common/storage-account-upgrade.md). Zie [Typen opslagaccounts](../storage/common/storage-account-overview.md#types-of-storage-accounts)voor meer informatie .
> * Het opslagaccount moet zich op hetzelfde abonnement en op dezelfde locatie bevinden als de Azure SQL Database-server. 
> * Het Azure Storage-account vereist `Allow trusted Microsoft services to access this storage account`. Stel dit in op de firewalls voor **opslagaccount en virtuele netwerken.**
> * U moet `Microsoft.Authorization/roleAssignments/write` toestemming hebben voor het geselecteerde opslagaccount. Zie [Azure ingebouwde rollen](../role-based-access-control/built-in-roles.md)voor meer informatie.

## <a name="configure-in-azure-portal"></a>Configureren in Azure Portal

Maak verbinding met [azure portal](https://portal.azure.com) met uw abonnement. Navigeer naar de brongroep en Azure SQL-databaseserver.

1. Klik op **Controle** onder de kop Beveiliging. Selecteer **Op**.

2. Selecteer **Opslag**. Selecteer het opslagaccount waar logboeken worden opgeslagen. Het opslagaccount moet voldoen aan de vereisten die in [Voorwaarden worden](#prerequisites)vermeld.

3. **Opslaggegevens openen** 

  > [!NOTE]
  > Als het geselecteerde opslagaccount zich achter VNet bevindt, ziet u het volgende bericht:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Als u dit bericht niet ziet, zit het opslagaccount niet achter een VNet.

4. Selecteer het aantal dagen voor de bewaartermijn. Klik vervolgens op **OK**. Logboeken die ouder zijn dan de bewaartermijn worden verwijderd.

5. Selecteer **Opslaan** in uw controle-instellingen.

U hebt de audit geconfigureerd om naar een opslagaccount achter een VNet of firewall te schrijven. 

## <a name="configure-with-rest-commands"></a>Configureren met REST-opdrachten

Als alternatief voor het gebruik van de Azure-portal u REST-opdrachten gebruiken om controle te configureren om databasegebeurtenissen te schrijven op een opslagaccount achter een VNet en Firewall. 

De voorbeeldscripts in deze sectie vereisen dat u het script bijwerkt voordat u ze uitvoert. Vervang de volgende waarden in de scripts:

|Voorbeeldwaarde|Voorbeeldbeschrijving|
|:-----|:-----|
|`<subscriptionId>`| Azure-abonnements-ID|
|`<resource group>`| Resourcegroep|
|`<sql database server>`| Naam Azure SQL-databaseserver|
|`<administrator login>`| SQL-databasebeheerdersaccount |
|`<complex password>`| Complex wachtwoord voor het beheerdersaccount|

Ga als lid van het nieuwe BEDRIJF over sql-controle om gebeurtenissen te schrijven naar een opslagaccount achter een VNet of Firewall:

1. Registreer uw Azure SQL Database-server met Azure Active Directory (Azure AD). Gebruik PowerShell of REST API.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Voorbeeldaanvraag

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Aanvraagbody

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Open [Azure Portal](https://portal.azure.com). Ga naar uw opslagaccount. Zoek **Toegangsbeheer (IAM)** en klik op **Roltoewijzing toevoegen**. Wijs **De RBAC-rol opslagblobgegevens-bijdrage rbac** toe aan uw Azure SQL Server die uw Azure SQL-database host die u hebt geregistreerd bij Azure Active Directory (Azure AD) zoals in de vorige stap.

   > [!NOTE]
   > Alleen leden met eigenaarbevoegdheid kunnen deze stap uitvoeren. Raadpleeg [azure-ingebouwde rollen](../role-based-access-control/built-in-roles.md)voor verschillende ingebouwde rollen voor Azure-resources.

3. Configureer [het blob-controlebeleid van Azure SQL-server](/rest/api/sql/server%20auditing%20settings/createorupdate)zonder een *storageAccountAccessKey*op te geven:

   Voorbeeldaanvraag

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Aanvraagbody

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Volgende stappen

- [Gebruik PowerShell om een eindpunt voor virtuele netwerkservices te maken en vervolgens een virtuele netwerkregel voor Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Virtuele netwerkregels: bewerkingen met REST API's](/rest/api/sql/virtualnetworkrules)
- [Eindpunten en regels voor virtuele netwerkservice gebruiken voor databaseservers](sql-database-vnet-service-endpoint-rule-overview.md)
