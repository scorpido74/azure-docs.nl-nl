---
title: Azure Resource Manager-sjablonen
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Gebruik sjablonen van Azure Resource Manager voor het maken en configureren van Azure SQL Database en een met Azure SQL beheerd exemplaar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023875"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure Resource Manager-sjablonen voor Azure SQL Database en een met Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager-sjablonen maken het mogelijk om uw infrastructuur als code te definiëren en uw oplossingen naar de Azure-cloud te implementeren voor uw Azure SQL Database en een met Azure SQL beheerd exemplaar.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure SQL Database.

| |  |
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Met deze Azure Resource Manager-sjabloon maakt u één database in Azure SQL Database en configureert u de firewallregels op serverniveau. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Met deze Azure Resource Manager-sjabloon maakt u een server voor Azure SQL Database. |
| [Elastische pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Met deze sjabloon kunt u een elastische pool implementeren en databases eraan toewijzen. |
| [Failover-groepen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Met deze sjabloon maakt u twee servers, één database en een failover-groep in Azure SQL Database.|
| [Detectie van bedreigingen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Met deze sjabloon kunt u een server en een set databases implementeren met detectie van bedreigingen ingeschakeld, met een e-mailadres voor waarschuwingen voor elke database. Detectie van bedreigingen is een onderdeel van de oplossing SQL Advanced Threat Protection (ATP) en biedt een beveiligingslaag die reageert op mogelijke bedreigingen op servers en databases.|
| [Controle naar Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Met deze sjabloon kunt u een server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een blobopslag. De controlefunctie voor Azure SQL Database houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Controle naar Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Met deze sjabloon kunt u een server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een bestaande Event Hub. Om controlegebeurtenissen te verzenden naar Event Hub, moet u controle-instellingen instellen met `Enabled` `State` en `IsAzureMonitorTargetEnabled` instellen op `true`. Daarnaast moet u de diagnostische instellingen configureren met de categorie `SQLSecurityAuditEvents` voor logboeken op de `master`-database (voor controle op serverniveau). De controlefunctie houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Azure-web-app met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Hiermee kunt u een gratis Azure-web-app en een database in Azure SQL Database maken op het serviceniveau Basic.|
| [Azure-web-app en Redis Cache met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Met deze sjabloon maakt u een web-app, Redis-cache en SQL-database in dezelfde resourcegroep, en maakt u twee verbindingsreeksen in de web-app voor de SQL-database en Redis-cache.|
| [Gegevens importeren uit blob-opslag met ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Met deze Azure Resource Manager-sjabloon maakt u een Azure Data Factory V2 waarmee gegevens uit Azure Blob Storage naar SQL Database worden gekopieerd.|
| [HDInsight-cluster met een SQL-database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Met deze sjabloon kunt u een HDInsight-cluster, een logische SQL-server, een database en twee tabellen maken. Deze sjabloon wordt gebruikt in het [artikel over het gebruik van Sqoop met Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop). |
| [Logische Azure-app die een in SQL opgeslagen procedure volgens een schema uitvoert](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Met deze sjabloon kunt u een logische app maken die een in SQL opgeslagen procedure volgens een schema uitvoert. Eventuele argumenten voor de procedure kunnen in de hoofdsectie van de sjabloon worden geplaatst.|

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor een met Azure SQL beheerd exemplaar.

| |  |
|---|---|
| [Met SQL beheerd exemplaar in een nieuw VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Met deze Azure Resource Manager-sjabloon maakt u een nieuw geconfigureerd Azure-VNet en een met SQL beheerd exemplaar in het VNet. |
| [Netwerkomgeving voor een met SQL beheerd exemplaar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Met deze implementatie maakt u een geconfigureerd virtueel Azure-netwerk met twee subnetten: één subnet dat is gereserveerd voor uw met SQL beheerde exemplaren en een ander subnet voor het opslaan van andere resources (zoals VM's, App Service-omgevingen, enzovoort). Met deze sjabloon maakt u een correct geconfigureerde netwerkomgeving waarin u met SQL beheerde exemplaren kunt implementeren. |
| [Met SQL beheerd exemplaar met P2S-verbinding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `GatewaySubnet`. Het met SQL beheerde exemplaar wordt geïmplementeerd in het subnet ManagedInstance. De virtuele netwerkgateway wordt gemaakt in het subnet `GatewaySubnet` en geconfigureerd voor een punt-naar-site VPN-verbinding. |
| [Met SQL beheerd exemplaar met virtuele machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `Management`. Het met SQL beheerde exemplaar wordt geïmplementeerd in het subnet `ManagedInstance`. Een virtuele machine met de meest recente versie van SQL Server Management Studio (SSMS) wordt geïmplementeerd in het subnet `Management`. |

---
