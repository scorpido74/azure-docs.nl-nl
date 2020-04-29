---
title: Azure Firewall toepassings regels met SQL-FQDN configureren
description: In dit artikel leert u hoe u SQL-FQDN-in Azure Firewall toepassings regels kunt configureren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79501494"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Azure Firewall toepassings regels met SQL-FQDN configureren

> [!IMPORTANT]
> Azure Firewall toepassings regels met SQL FQDN-zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

U kunt nu Azure Firewall toepassings regels configureren met SQL-FQDN-waarden. Hierdoor kunt u de toegang tot de virtuele netwerken beperken tot alleen de opgegeven SQL Server-exemplaren.

Met SQL-FQDN-naam kunt u verkeer filteren:

- Van uw VNets naar een Azure SQL Database of een Azure SQL Data Warehouse. Bijvoorbeeld: alleen toegang tot *SQL-server1.database.Windows.net*toestaan.
- Van on-premises naar Azure SQL Managed instances of SQL IaaS die worden uitgevoerd in uw VNets.
- Van spoke-naar-spoke tot Azure SQL Managed instances of SQL IaaS die worden uitgevoerd in uw VNets.

Tijdens de open bare Preview wordt SQL FQDN-filtering alleen ondersteund in de [proxy modus](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (poort 1433). Als u SQL in de standaard omleidings modus gebruikt, kunt u de toegang filteren met behulp van de SQL-service-tag als onderdeel van de [netwerk regels](overview.md#network-traffic-filtering-rules).
Als u niet-standaard poorten gebruikt voor SQL IaaS-verkeer, kunt u die poorten configureren in de toepassings regels van de firewall.

Toepassings regels met SQL FQDN-waarden zijn momenteel in alle regio's beschikbaar via de Azure Portal, Azure CLI, REST en sjablonen.

## <a name="configure-using-azure-cli"></a>Configureren met behulp van Azure CLI

1. Implementeer een [Azure Firewall met behulp van Azure cli](deploy-cli.md).
2. Als u verkeer filtert op Azure SQL Database, SQL Data Warehouse of SQL Managed instance, zorg er dan voor dat de SQL-connectiviteits modus is ingesteld op **proxy**. Zie [Azure SQL-connectiviteits instellingen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)voor meer informatie over het overschakelen naar een andere SQL-connectiviteits modus.

   > [!NOTE]
   > De SQL- *proxy* modus kan leiden tot meer latentie vergeleken met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken met Azure. u kunt de toegang filteren met behulp van de SQL- [service-tag](service-tags.md) in Firewall- [netwerk regels](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Een toepassings regel met SQL FQDN configureren om toegang tot een SQL-Server toe te staan:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configureren met behulp van Azure Portal
1. Implementeer een [Azure Firewall met behulp van Azure cli](deploy-cli.md).
2. Als u verkeer filtert op Azure SQL Database, SQL Data Warehouse of SQL Managed instance, zorg er dan voor dat de SQL-connectiviteits modus is ingesteld op **proxy**. Zie [Azure SQL-connectiviteits instellingen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)voor meer informatie over het overschakelen naar een andere SQL-connectiviteits modus.  

   > [!NOTE]
   > De SQL- *proxy* modus kan leiden tot meer latentie vergeleken met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken met Azure. u kunt de toegang filteren met behulp van de SQL- [service-tag](service-tags.md) in Firewall- [netwerk regels](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Voeg de toepassings regel met het juiste protocol, de poort en de SQL FQDN toe en selecteer vervolgens **Opslaan**.
   ![toepassings regel met SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Toegang tot SQL vanaf een virtuele machine in een VNet waarmee het verkeer via de firewall wordt gefilterd. 
5. Controleer of [Azure firewall logboeken](log-analytics-samples.md) het verkeer mag weer geven.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL database-connectiviteits architectuur](../sql-database/sql-database-connectivity-architecture.md)voor meer informatie over de SQL-proxy en omleidings modi.