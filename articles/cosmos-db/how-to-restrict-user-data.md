---
title: Gebruikerstoegang tot gegevensbewerkingen beperken alleen met Azure Cosmos DB
description: Meer informatie over het beperken van de toegang tot gegevensbewerkingen alleen met Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980372"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Gebruikerstoegang beperken tot alleen gegevensbewerkingen

In Azure Cosmos DB zijn er twee manieren om uw interacties met de databaseservice te verifiëren:
- uw Azure Active Directory-identiteit gebruiken wanneer u interactie hebt met de Azure-portal,
- azure cosmos [DB-sleutels](secure-access-to-data.md#master-keys) of [resourcetokens](secure-access-to-data.md#resource-tokens) gebruiken bij het uitgeven van oproepen van API's en SDK's.

Elke verificatiemethode geeft toegang tot verschillende reeksen ![bewerkingen, met enige overlap: splitsing van bewerkingen per verificatietype](./media/how-to-restrict-user-data/operations.png)

In sommige scenario's u sommige gebruikers van uw organisatie beperken om alleen gegevensbewerkingen (dat zijn CRUD-aanvragen en query's) uit te voeren. Dit is meestal het geval voor ontwikkelaars die geen resources hoeven te maken of verwijderen of de ingerichte doorvoer van de containers waaraan ze werken, wijzigen.

U de toegang beperken door de volgende stappen toe te passen:
1. Een aangepaste Azure Active Directory-rol maken voor de gebruikers die u de toegang wilt beperken. De aangepaste Active Directory-rol moet een fijnkorrelig toegangsniveau hebben voor bewerkingen met behulp van [de gedetailleerde acties van](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Het niet toestaan van de uitvoering van niet-gegevensbewerkingen met sleutels. U dit bereiken door deze bewerkingen alleen te beperken tot Azure Resource Manager-aanroepen.

In de volgende secties van dit artikel ziet u hoe u deze stappen uitvoert.

> [!NOTE]
> Als u de opdrachten in de volgende secties wilt uitvoeren, moet u Azure PowerShell Module 3.0.0 of hoger installeren, evenals de [Azure-eigenaarrol](../role-based-access-control/built-in-roles.md#owner) op het abonnement dat u probeert te wijzigen.

Vervang in de PowerShell-scripts in de volgende secties de volgende tijdelijke aanduidingen met waarden die specifiek zijn voor uw omgeving:
- `$MySubscriptionId`- De abonnements-ID die het Azure Cosmos-account bevat, waar u de machtigingen wilt beperken. Bijvoorbeeld: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- De brongroep met het Azure Cosmos-account. Bijvoorbeeld: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- De naam van uw Azure Cosmos-account. Bijvoorbeeld: `mycosmosdbsaccount`.
- `$MyUserName`- Deusername@domainlogin ( ) van de gebruiker voor wie u de toegang wilt beperken. Bijvoorbeeld: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>selecteer uw Azure-abonnement

Voor Azure PowerShell-opdrachten moet u inloggen en het abonnement selecteren om de opdrachten uit te voeren:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>De aangepaste Azure Active Directory-rol maken

In het volgende script wordt een Azure Active Directory-roltoewijzing gemaakt met alleen-sleutelstoegang voor Azure Cosmos-accounts. De rol is gebaseerd op [aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md) en [Granulaire acties voor Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Deze rollen en acties `Microsoft.DocumentDB` maken deel uit van de naamruimte van Azure Active Directory.

1. Maak eerst een JSON-document met de `AzureCosmosKeyOnlyAccess.json` volgende inhoud:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Voer de volgende opdrachten uit om de toewijzing van de rol te maken en deze aan de gebruiker toe te wijzen:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>De uitvoering van niet-gegevensbewerkingen verbieden

Met de volgende opdrachten wordt de mogelijkheid verwijderd om toetsen te gebruiken om:
- bronnen maken, wijzigen of verwijderen
- containerinstellingen bijwerken (inclusief indexeringsbeleid, doorvoer, enz.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het op rollen gebaseerde toegangscontrole](role-based-access-control.md) van Cosmos DB
- Krijg een overzicht van [veilige toegang tot gegevens in Cosmos DB](secure-access-to-data.md)
