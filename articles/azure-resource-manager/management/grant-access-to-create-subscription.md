---
title: Toegang verlenen voor het maken van Azure Enterprise-abonnementen
description: Meer informatie over hoe u een gebruiker of serviceprincipal de mogelijkheid geeft om programmatisch Azure Enterprise-abonnementen te maken.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478875"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Toegang verlenen voor het maken van Azure Enterprise-abonnementen (voorbeeld)

Als Azure-klant op [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)u een andere gebruiker of serviceprincipal toestemming geven om abonnementen te maken die op uw account zijn gefactureerd. In dit artikel leert u hoe u [RBAC (Role-Based Access Control)](../../active-directory/role-based-access-control-configure.md) gebruiken om de mogelijkheid te delen om abonnementen te maken en hoe u het maken van abonnementen controleren. Je moet de rol Eigenaar hebben op het account dat je wilt delen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Toegang verlenen

Als [u abonnementen wilt maken onder een inschrijvingsaccount,](programmatically-create-subscription.md)moeten gebruikers de rol [RBAC-eigenaar](../../role-based-access-control/built-in-roles.md#owner) in dat account hebben. U een gebruiker of een groep gebruikers de Rol RBAC-eigenaar voor een inschrijvingsaccount verlenen door de volgende stappen te volgen:

1. De object-id opvragen van het inschrijvingsaccount waartoe u toegang wilt verlenen

    Als u anderen de rol Van RBAC-eigenaar op een inschrijvingsaccount wilt verlenen, moet u de accounteigenaar of een RBAC-eigenaar van het account zijn.

    # <a name="rest"></a>[Rest](#tab/rest)

    Verzoek om een lijst van alle inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure reageert met een lijst met alle inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Gebruik `principalName` de eigenschap om het account te identificeren waartoe Ups- eigenaar RBAC-eigenaar toegang wilt verlenen. Kopieer `name` de van die rekening. Als u bijvoorbeeld RBAC Owner toegang wilt SignUpEngineering@contoso.com verlenen tot het ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inschrijvingsaccount, kopieert u . Dit is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvingsaccounts waartoe u toegang hebt, weer te geven. Selecteer **Probeer deze** om Azure Cloud Shell te [openen.](https://shell.azure.com/) Als u de code wilt plakken, klikt u met de rechtermuisknop op de shell-vensters en selecteert u **Plakken**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Gebruik `principalName` de eigenschap om het account te identificeren waartoe Ups- eigenaar RBAC-eigenaar toegang wilt verlenen. Kopieer `ObjectId` de van die rekening. Als u bijvoorbeeld RBAC Owner toegang wilt SignUpEngineering@contoso.com verlenen tot het ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inschrijvingsaccount, kopieert u . Plak deze object-id ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als de .

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    Gebruik de [opdracht lijst met az-factureringsinschrijvingen](https://aka.ms/EASubCreationPublicPreviewCLI) om alle inschrijvingsaccounts waartoe u toegang hebt, weer te geven. Selecteer **Probeer deze** om Azure Cloud Shell te [openen.](https://shell.azure.com/) Als u de code wilt plakken, klikt u met de rechtermuisknop op de shell-vensters en selecteert u **Plakken**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Gebruik `principalName` de eigenschap om het account te identificeren waartoe Ups- eigenaar RBAC-eigenaar toegang wilt verlenen. Kopieer `name` de van die rekening. Als u bijvoorbeeld RBAC Owner toegang wilt SignUpEngineering@contoso.com verlenen tot het ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inschrijvingsaccount, kopieert u . Dit is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als .

1. <a id="userObjectId"></a>Object-id opvragen van de gebruiker of groep waaraan u de rol RBAC-eigenaar wilt geven

    1. Zoek in de Azure-portal in **Azure Active Directory**.
    1. Als u een gebruiker toegang wilt verlenen, klikt u op **Gebruikers** in het menu aan de linkerkant. Als u toegang tot een groep wilt verlenen, klikt u op **Groepen**.
    1. Selecteer de gebruiker of groep waaraan u de rol RBAC-eigenaar wilt geven.
    1. Als u een gebruiker hebt geselecteerd, vindt u de object-id op de profielpagina. Als u een groep hebt geselecteerd, bevindt de object-id zich op de pagina Overzicht. Kopieer de **ObjectID** door op het pictogram rechts van het tekstvak te klikken. Plak dit ergens zodat u het `userObjectId`gebruiken in de volgende stap als.

1. De gebruiker of groep de Rol Van RBAC-eigenaar op het inschrijvingsaccount verlenen

    Als u de waarden gebruikt die u in de eerste twee stappen hebt verzameld, verleent u de gebruiker of groepeer de rol RBAC-eigenaar op het inschrijvingsaccount.

    # <a name="rest"></a>[Rest](#tab/rest-2)

    Voer de volgende opdracht ```<enrollmentAccountObjectId>``` uit, vervangen door de `name` ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```u die u in de eerste stap hebt gekopieerd ( ). Vervang ```<userObjectId>``` door de object-id die u uit de tweede stap hebt gekopieerd.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Wanneer de rol Eigenaar is toegewezen aan het bereik van het inschrijvingsaccount, reageert Azure met informatie over de roltoewijzing:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Voer de volgende opdracht [Nieuw-AzRoleAssignment uit,](../../active-directory/role-based-access-control-manage-access-powershell.md) vervangen door ```<enrollmentAccountObjectId>``` de `ObjectId` verzamelde in de eerste stap (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Vervang ```<userObjectId>``` door de object-ID die in de tweede stap is verzameld.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-2)

    Voer de volgende opdracht [voor het maken van az-rollen uit](../../active-directory/role-based-access-control-manage-access-azure-cli.md) en vervang ```<enrollmentAccountObjectId>``` de `name` opdracht die u in de eerste stap hebt gekopieerd (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Vervang ```<userObjectId>``` door de object-ID die in de tweede stap is verzameld.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Zodra een gebruiker een RBAC-eigenaar voor uw inschrijvingsaccount wordt, kan hij [er programmatisch abonnementen](programmatically-create-subscription.md) onder maken. Een abonnement dat is gemaakt door een gedelegeerde gebruiker heeft nog steeds de oorspronkelijke accounteigenaar als servicebeheerder, maar heeft ook standaard de gedelegeerde gebruiker als RBAC-eigenaar.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controleren wie abonnementen heeft gemaakt met behulp van activiteitslogboeken

Als u de abonnementen wilt bijhouden die via deze API zijn gemaakt, gebruikt u de [API voor tenantactiviteitslogboek](/rest/api/monitor/tenantactivitylogs). Het is momenteel niet mogelijk om PowerShell, CLI of Azure-portal te gebruiken om het maken van abonnementen bij te houden.

1. [Verhoog het toegangsniveau](../../active-directory/role-based-access-control-tenant-admin-access.md) als tenantbeheerder van de Azure AD-tenant en wijs vervolgens de rol van Lezer toe aan de controlerende gebruiker voor het bereik `/providers/microsoft.insights/eventtypes/management`.
1. Roep als controlegebruiker de [API voor het logboek tenantactiviteit](/rest/api/monitor/tenantactivitylogs) aan om activiteiten voor het maken van abonnementen te bekijken. Voorbeeld:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Probeer [ARMClient](https://github.com/projectkudu/ARMClient) om deze API eenvoudig aan te roepen vanaf de opdrachtregel.

## <a name="next-steps"></a>Volgende stappen

* Nu de gebruiker of serviceprincipal toestemming heeft om een abonnement te maken, u die identiteit gebruiken om [programmatisch Azure Enterprise-abonnementen te maken.](programmatically-create-subscription.md)
* Zie [voorbeeldcode op GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)voor een voorbeeld van het maken van abonnementen met .NET.
* Zie overzicht azure [resource manager](overview.md)voor meer informatie over Azure Resource Manager en de API's.
* Zie [Uw resources ordenen met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met beheergroepen
* Zie [Azure enterprise scaffold - prescriptief abonnementsbeheer](/azure/architecture/cloud-adoption-guide/subscription-governance) voor grote organisaties voor grote organisaties over abonnementsbeheer voor grote organisaties voor het beheren van abonnementen.
