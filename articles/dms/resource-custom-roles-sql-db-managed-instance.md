---
title: 'Aangepaste rollen: Online SQL Server naar SQL-beheerde instantiemigraties'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het gebruik van de aangepaste rollen voor SQL Server naar Azure SQL Database beheerde instantie online migraties.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254946"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Aangepaste rollen voor SQL Server naar SQL Database beheerde instantie online migraties

Azure Database Migration Service gebruikt een APP-id om te communiceren met Azure Services. De APP-id vereist de rol inzender op abonnementsniveau (die veel bedrijfsbeveiligingsafdelingen niet toestaan) of het maken van aangepaste rollen die de specifieke machtigingen verlenen waarvoor Azure-databaseMigratieservice vereist is. Aangezien er een limiet is van 2.000 aangepaste rollen in Azure Active Directory, u alle machtigingen die specifiek door de APP-id zijn vereist, combineren in een of twee aangepaste rollen en de APP-id de aangepaste rol voor specifieke objecten of resourcegroepen (vs. op abonnementsniveau). Als het aantal aangepaste rollen geen probleem is, u de aangepaste rollen splitsen op resourcetype om drie aangepaste rollen in totaal te maken, zoals hieronder beschreven.

Met de sectie AssignableScopes van de json-tekenreeks voor roldefinitie u bepalen waar de machtigingen worden weergegeven in de gebruikersinterface **Roltoewijzing toevoegen** in de portal. U wilt waarschijnlijk de rol op resourcegroep of zelfs resourceniveau definiëren om te voorkomen dat de gebruikersinterface wordt verrommeld met extra rollen. Houd er rekening mee dat dit de werkelijke roltoewijzing niet uitvoert.

## <a name="minimum-number-of-roles"></a>Minimum aantal rollen

We raden momenteel aan om minimaal twee aangepaste rollen te maken voor de APP-id, een op resourceniveau en de andere op abonnementsniveau.

> [!NOTE]
> De laatste aangepaste rolvereiste kan uiteindelijk worden verwijderd, omdat nieuwe SQL Database-beheerde instantiecode wordt geïmplementeerd in Azure.

**Aangepaste rol voor de APP-id**. Deze rol is vereist voor migratie van Azure Database Migration Service op het niveau van de *resource-* of *resourcegroep* (zie het artikel [Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Aangepaste rol voor de APP ID - abonnement**. Deze rol is vereist voor migratie van Azure Database Migration Service op *abonnementsniveau.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

De hierboven genoemde json moet worden opgeslagen in drie tekstbestanden en u de AzureRM-, AZ PowerShell-cmdlets of Azure CLI gebruiken om de rollen te maken met **AzureRM (New-AzureRM)** of **New-AzRoleDefinition (AZ).**

Zie het artikel [Aangepaste rollen voor Azure-resources voor](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)meer informatie.

Nadat u deze aangepaste rollen hebt gemaakt, moet u roltoewijzingen toevoegen aan gebruikers en APP ID(s) aan de juiste resources of resourcegroepen:

* De rol 'DMS-rol - App-id' moet worden toegekend aan de APP-id die wordt gebruikt voor de migraties, en ook bij de instantie Storage Account, Azure Database Migration Service en SQL Database managed instance resource levels.
* De rol 'DMS-rol - App ID - Sub' moet worden toegekend aan de APP-id op abonnementsniveau (toekenning op de resource- of resourcegroep mislukt). Deze vereiste is tijdelijk totdat een code-update is geïmplementeerd.

## <a name="expanded-number-of-roles"></a>Uitgebreid aantal rollen

Als het aantal aangepaste rollen in uw Azure Active Directory geen probleem is, raden we u aan in totaal drie rollen te maken. U hebt nog steeds de rol 'DMS-rol - App ID – Sub' nodig, maar de rol 'DMS-rol - App ID' hierboven wordt opgesplitst in resourcetype in twee verschillende rollen.

**Aangepaste rol voor de instantie APP-id voor SQL Database beheerd**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Aangepaste rol voor de APP-id voor opslag**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Nieuwe roltoewijzing

Als u een rol wilt toewijzen aan gebruikers/APP-id, opent u de Azure-portal en voert u de volgende stappen uit:

1. Navigeer naar de resourcegroep of -bron (met uitzondering van de rol die moet worden verleend op het abonnement), ga naar **Toegangsbeheer**en blader vervolgens om de aangepaste rollen te vinden die u zojuist hebt gemaakt.

2. Selecteer de juiste rol, selecteer de APP-id en sla de wijzigingen op.

  Uw APP ID(s) wordt nu weergegeven op het tabblad **Toewijzingen van rollen.**

## <a name="next-steps"></a>Volgende stappen

* Bekijk de migratierichtlijnen voor uw scenario in de [Microsoft Database Migration Guide](https://datamigration.microsoft.com/).
