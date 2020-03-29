---
title: Op rollen gebaseerd toegangsbeheer in Azure Cosmos DB
description: Ontdek hoe Azure Cosmos DB databasebescherming biedt met RBAC (Active directory integration).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445091"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Op rollen gebaseerd toegangsbeheer in Azure Cosmos DB

Azure Cosmos DB biedt ingebouwde role-based access control (RBAC) voor veelvoorkomende beheerscenario's in Azure Cosmos DB. Een persoon die een profiel heeft in Azure Active Directory, kan deze RBAC-rollen toewijzen aan gebruikers, groepen, serviceprincipals of beheerde identiteiten om toegang te verlenen of te weigeren tot bronnen en bewerkingen op Azure Cosmos DB-bronnen. Roltoewijzingen zijn alleen toegankelijk voor beheervlak, waaronder toegang tot Azure Cosmos-accounts, databases, containers en aanbiedingen (doorvoer).

## <a name="built-in-roles"></a>Ingebouwde rollen

Hieronder volgen de ingebouwde rollen die worden ondersteund door Azure Cosmos DB:

|**Ingebouwde rol**  |**Beschrijving**  |
|---------|---------|
|[DocumentDB-accountbijdrager](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan Azure Cosmos DB-accounts beheren.|
|[Cosmos DB-accountlezer](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan Azure Cosmos DB-accountgegevens lezen.|
|[Cosmos Backup Operator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Kan herstelaanvraag indienen voor een Azure Cosmos-database of een container.|
|[Cosmos DB-operator](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kan Azure Cosmos-accounts, databases en containers inrichten, maar hebben geen toegang tot de sleutels die nodig zijn om toegang te krijgen tot de gegevens.|

> [!IMPORTANT]
> RBAC-ondersteuning in Azure Cosmos DB is alleen van toepassing op beheervlakbewerkingen. Gegevensvlakbewerkingen worden beveiligd met hoofdsleutels of resourcetokens. Zie [Beveiligde toegang tot gegevens in Azure Cosmos DB](secure-access-to-data.md) voor meer informatie

## <a name="identity-and-access-management-iam"></a>Identiteits- en toegangsbeheer (IAM)

Het deelvenster **Toegangsbeheer (IAM)** in de Azure-portal wordt gebruikt om op rollen gebaseerdtoegangsbeheer op Azure Cosmos-bronnen te configureren. De rollen worden toegepast op gebruikers, groepen, serviceprincipals en beheerde identiteiten in Active Directory. U ingebouwde rollen of aangepaste rollen voor personen en groepen gebruiken. In de volgende schermafbeelding wordt RBAC (Active Directory-integratie) weergegeven met behulp van toegangsbeheer (IAM) in de Azure-portal:

![Toegangsbeheer (IAM) in de Azure-portal - databasebeveiliging aantonen](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Aangepaste rollen

Naast de ingebouwde rollen kunnen gebruikers ook [aangepaste rollen](../role-based-access-control/custom-roles.md) in Azure maken en deze rollen toepassen op serviceprincipals voor alle abonnementen binnen hun Active Directory-tenant. Aangepaste rollen bieden gebruikers een manier om RBAC-roldefinities te maken met een aangepaste set resourceproviderbewerkingen. Voor meer informatie over welke bewerkingen beschikbaar zijn voor het bouwen van aangepaste rollen voor Azure Cosmos DB, zie [de beheerbewerkingen van Azure Cosmos DB-bronprovider](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Wijzigingen van Cosmos SDK voorkomen

De Cosmos-resourceprovider kan worden vergrendeld om wijzigingen in resources te voorkomen, waaronder Cosmos-account, databases, containers en doorvoer van elke client die verbinding maakt via accountsleutels (d.w.z. toepassingen die verbinding maken via Cosmos SDK). Wanneer u deze instelt, moeten wijzigingen in een resource afkomstig zijn van een gebruiker met de juiste RBAC-rol en -referenties. Deze mogelijkheid is `disableKeyBasedMetadataWriteAccess` ingesteld met eigenschapswaarde in de cosmos-resourceprovider. Hieronder vindt u een voorbeeld van een Azure Resource Manager-sjabloon met deze eigenschapsinstelling.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Wat is rbac (role-based access control) voor Azure-resources](../role-based-access-control/overview.md)
- [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB-resourceprovider-bewerkingen](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
