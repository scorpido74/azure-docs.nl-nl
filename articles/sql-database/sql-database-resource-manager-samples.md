---
title: Azure Resource Manager-sjablonen
description: Gebruik sjablonen van Azure Resource Manager voor het maken en configureren van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79214006"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure Resource Manager-sjablonen voor Azure SQL Database

Azure Resource Manager-sjablonen maken het mogelijk om uw infrastructuur als code te definiëren en uw oplossingen naar de Azure-cloud te implementeren.

## <a name="single-database--elastic-pool"></a>[Eén data base & elastische pool](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure SQL Database.

| |  |
|---|---|
| [Eén data base](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Met deze Azure Resource Manager-sjabloon maakt u één Azure SQL-database met een logische server en configureert u de firewallregels. |
| [Logische server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Met deze Azure Resource Manager-sjabloon maakt u een logische server voor Azure SQL Database. |
| [Elastische pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Met deze sjabloon kunt u een nieuwe elastische pool implementeren met de nieuwe gekoppelde SQL-server en de nieuwe SQL-databases om eraan toe te wijzen. |
| [Failover-groepen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Met deze sjabloon maakt u twee logische Azure SQL-servers, een SQL-database en een failover-groep.|
| [Detectie van bedreigingen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Met deze sjabloon kunt u een logische Azure SQL-server en een set Azure SQL-databases implementeren met detectie van bedreigingen ingeschakeld, met een e-mailadres voor waarschuwingen voor elke database. Detectie van bedreigingen is een onderdeel van de oplossing SQL Advanced Threat Protection (ATP) en biedt een beveiligingslaag die reageert op mogelijke bedreigingen op SQL-servers en databases.|
| [Controle naar Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Met deze sjabloon kunt u een logische Azure SQL-server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een blob-opslag. De controlefunctie voor Azure SQL Database houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Controle naar Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Met deze sjabloon kunt u een Azure SQL-server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een bestaande Event Hub. Als u controle gebeurtenissen naar Event hub wilt verzenden, stelt u de controle `Enabled` `State` -instellingen `IsAzureMonitorTargetEnabled` in `true`op en stelt u in op als. Configureer ook de diagnostische instellingen `SQLSecurityAuditEvents` met de categorie logboek `master` op de data base (voor het controleren van het niveau). De controlefunctie voor Azure SQL Database en SQL Data Warehouse houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Azure-web-app met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Hiermee kunt u een gratis Azure-web-app en SQL-database maken op het serviceniveau Basic.|
| [Azure-web-app en Redis Cache met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Met deze sjabloon maakt u een web-app, Redis-cache en SQL-database in dezelfde resourcegroep, en maakt u twee verbindingsreeksen in de web-app voor de SQL-database en Redis-cache.|
| [Gegevens importeren uit blob-opslag met ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Met deze Azure Resource Manager-sjabloon maakt u een Azure Data Factory V2 waarmee gegevens uit Azure Blob Storage naar SQL Database worden gekopieerd.|
| [HDInsight-cluster met een SQL-database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Met deze sjabloon kunt u een HDInsight-cluster, een SQL Database-server, een SQL-database en twee tabellen maken. Deze sjabloon wordt gebruikt in het [artikel over het gebruik van Sqoop met Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop). |
| [Logische Azure-app die een in SQL opgeslagen procedure volgens een schema uitvoert](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Met deze sjabloon kunt u een logische app maken die een in SQL opgeslagen procedure volgens een schema uitvoert. Eventuele argumenten voor de procedure kunnen in de hoofdsectie van de sjabloon worden geplaatst.|

## <a name="managed-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure SQL Database - Beheerd exemplaar.

| |  |
|---|---|
| [Beheerd exemplaar in een nieuw VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Met deze Azure Resource Manager-sjabloon maakt u een nieuw geconfigureerd Azure-VNet en een beheerd exemplaar in het VNet. |
| [Netwerkomgeving voor beheerd exemplaar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Met deze implementatie maakt u een geconfigureerd virtueel Azure-netwerk met twee subnetten: één subnet dat is gereserveerd voor uw beheerde exemplaren en een ander subnet voor het opslaan van andere resources (zoals VM's, App Service-omgevingen, enzovoort). Met deze sjabloon maakt u een correct geconfigureerde netwerkomgeving waarin u beheerde exemplaren kunt implementeren. |
| [Beheerd exemplaar met P2S-verbinding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `GatewaySubnet`. Het beheerde exemplaar wordt geïmplementeerd in het subnet ManagedInstance. De virtuele netwerkgateway wordt gemaakt in het subnet `GatewaySubnet` en geconfigureerd voor een punt-naar-site VPN-verbinding. |
| [Beheerd exemplaar met virtuele machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `Management`. Het beheerde exemplaar wordt geïmplementeerd in het subnet `ManagedInstance`. Een virtuele machine met de meest recente versie van SQL Server Management Studio (SSMS) wordt geïmplementeerd in het subnet `Management`. |

---
