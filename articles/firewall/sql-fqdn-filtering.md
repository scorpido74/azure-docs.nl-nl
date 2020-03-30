---
title: Azure Firewall-toepassingsregels configureren met SQL FQDN's
description: In dit artikel vindt u informatie over het configureren van SQL FQDN's in Azure Firewall-toepassingsregels.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501494"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Azure Firewall-toepassingsregels configureren met SQL FQDN's

> [!IMPORTANT]
> Azure Firewall-toepassingsregels met SQL FQDN's staan momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

U azure firewall-toepassingsregels nu configureren met SQL FQDN's. Hiermee u de toegang van uw virtuele netwerken beperken tot alleen de opgegeven SQL-server-exemplaren.

Met SQL FQDN's u verkeer filteren:

- Van uw VNets tot een Azure SQL Database of een Azure SQL Data Warehouse. Bijvoorbeeld: Geef alleen toegang tot *sql-server1.database.windows.net*.
- Van on-premises tot Azure SQL Managed Instances of SQL IaaS die in uw VNets worden uitgevoerd.
- Van spoke-to-spoke tot Azure SQL Managed Instances of SQL IaaS die in uw VNets wordt uitgevoerd.

Tijdens de openbare preview wordt SQL FQDN-filtering alleen ondersteund in [proxy-modus](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (poort 1433). Als u SQL gebruikt in de standaardomleidingsmodus, u toegang filteren met behulp van de SQL-servicetag als onderdeel van [netwerkregels.](overview.md#network-traffic-filtering-rules)
Als u niet-standaardpoorten gebruikt voor SQL IaaS-verkeer, u deze poorten configureren in de regels voor firewalltoepassingen.

Toepassingsregels met SQL FQDN's zijn momenteel beschikbaar in alle regio's via de Azure-portal, Azure CLI, REST en sjablonen.

## <a name="configure-using-azure-cli"></a>Configureren met Azure CLI

1. Een [Azure Firewall implementeren met Azure CLI](deploy-cli.md).
2. Als u verkeer filtert naar Azure SQL Database, SQL Data Warehouse of SQL Managed Instance, moet u ervoor zorgen dat de SQL-verbindingsmodus is ingesteld op **Proxy.** Zie [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)voor meer informatie over het schakelen in de SQL-connectiviteitsmodus.

   > [!NOTE]
   > *SQL-proxymodus* kan resulteren in meer latentie in vergelijking met *omleiding.* Als u de omleidingsmodus wilt blijven gebruiken, de standaardinstelling voor clients die verbinding maken binnen Azure, u de toegang filteren met behulp van de [SQL-servicetag](service-tags.md) in [firewallnetwerkregels.](tutorial-firewall-deploy-portal.md#configure-a-network-rule)

3. Configureer een toepassingsregel met SQL FQDN om toegang tot een SQL-server toe te staan:

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
1. Een [Azure Firewall implementeren met Azure CLI](deploy-cli.md).
2. Als u verkeer filtert naar Azure SQL Database, SQL Data Warehouse of SQL Managed Instance, moet u ervoor zorgen dat de SQL-verbindingsmodus is ingesteld op **Proxy.** Zie [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)voor meer informatie over het schakelen in de SQL-connectiviteitsmodus.  

   > [!NOTE]
   > *SQL-proxymodus* kan resulteren in meer latentie in vergelijking met *omleiding.* Als u de omleidingsmodus wilt blijven gebruiken, de standaardinstelling voor clients die verbinding maken binnen Azure, u de toegang filteren met behulp van de [SQL-servicetag](service-tags.md) in [firewallnetwerkregels.](tutorial-firewall-deploy-portal.md#configure-a-network-rule)
3. Voeg de toepassingsregel toe met het juiste protocol, de poort en SQL FQDN en selecteer **Opslaan**.
   ![toepassingsregel met SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Toegang tot SQL vanaf een virtuele machine in een VNet die het verkeer door de firewall filtert. 
5. Controleer of [Azure Firewall-logboeken](log-analytics-samples.md) laten zien dat het verkeer is toegestaan.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL-databaseconnectiviteitsarchitectuur](../sql-database/sql-database-connectivity-architecture.md)voor meer informatie over SQL-proxy- en omleidingsmodi.