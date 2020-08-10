---
title: Toegang verlenen voor het maken van Azure Enter prise-abonnementen
description: Meer informatie over hoe u een gebruiker of Service-Principal kunt gebruiken om op een programmatische manier Azure Enter prise-abonnementen te maken.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: aef9c6781c87ff4e84e46de711308319755e4630
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042065"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Toegang verlenen voor het maken van Azure Enter prise-abonnementen (preview-versie)

Als Azure-klant op [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kunt u een andere gebruiker of Service-Principal toestemming geven om abonnementen te maken die aan uw account worden gefactureerd. In dit artikel leert u hoe u [Azure RBAC (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/role-assignments-portal.md) kunt gebruiken om abonnementen te maken, en hoe u een abonnement kunt controleren. U moet de rol van eigenaar hebben voor het account dat u wilt delen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Toegang verlenen

Als u [abonnementen wilt maken onder een inschrijvings account](programmatically-create-subscription.md), moeten gebruikers de [rol RBAC-eigenaar](../../role-based-access-control/built-in-roles.md#owner) hebben voor dat account. U kunt een gebruiker of een groep gebruikers de rol RBAC-eigenaar verlenen voor een inschrijvings account door de volgende stappen uit te voeren:

1. De object-ID ophalen van het inschrijvings account waaraan u toegang wilt verlenen

    Als u anderen de rol RBAC-eigenaar wilt verlenen voor een inschrijvings account, moet u de eigenaar van het account of een RBAC-eigenaar van het account zijn.

    # <a name="rest"></a>[REST](#tab/rest)

    Aanvraag voor een lijst met alle inschrijvings accounts waartoe u toegang hebt:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure reageert met een lijst met alle inschrijvings accounts waartoe u toegang hebt:

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

    Gebruik de `principalName` eigenschap om het account te identificeren waaraan u de RBAC-eigenaar toegang wilt verlenen. Kopieer de `name` van dat account. Als u bijvoorbeeld de RBAC-eigenaar toegang wilt verlenen tot het SignUpEngineering@contoso.com inschrijvings account, kopieert u deze ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Dit is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId` .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvings accounts weer te geven waartoe u toegang hebt. Selecteer **proberen** om [Azure Cloud shell](https://shell.azure.com/)te openen. Als u de code wilt plakken, klikt u met de rechter muisknop op de shell Vensters en selecteert u **Plakken**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure reageert met een lijst met inschrijvings accounts waartoe u toegang hebt:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Gebruik de `principalName` eigenschap om het account te identificeren waartoe u de RBAC-eigenaar toegang wilt verlenen. Kopieer de `ObjectId` van dat account. Als u bijvoorbeeld de RBAC-eigenaar toegang wilt verlenen tot het SignUpEngineering@contoso.com inschrijvings account, kopieert u deze ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Plak deze object-ID ergens anders zodat u deze in de volgende stap als de kunt gebruiken `enrollmentAccountObjectId` .

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    Gebruik de opdracht [AZ facturering Enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) voor een lijst met alle inschrijvings accounts waartoe u toegang hebt. Selecteer **proberen** om [Azure Cloud shell](https://shell.azure.com/)te openen. Als u de code wilt plakken, klikt u met de rechter muisknop op de shell Vensters en selecteert u **Plakken**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure reageert met een lijst met inschrijvings accounts waartoe u toegang hebt:

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

    Gebruik de `principalName` eigenschap om het account te identificeren waaraan u de RBAC-eigenaar toegang wilt verlenen. Kopieer de `name` van dat account. Als u bijvoorbeeld de RBAC-eigenaar toegang wilt verlenen tot het SignUpEngineering@contoso.com inschrijvings account, kopieert u deze ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Dit is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId` .

1. <a id="userObjectId"></a>De object-ID ophalen van de gebruiker of groep waaraan u de RBAC-eigenaars functie wilt toekennen

    1. Zoek in de Azure Portal op **Azure Active Directory**.
    1. Als u een gebruiker toegang wilt verlenen, klikt u op **gebruikers** in het menu aan de linkerkant. Als u toegang wilt verlenen aan een groep, klikt u op **groepen**.
    1. Selecteer de gebruiker of groep waaraan u de RBAC-eigenaars functie wilt toewijzen.
    1. Als u een gebruiker hebt geselecteerd, vindt u de object-ID op de profiel pagina. Als u een groep hebt geselecteerd, wordt de object-ID op de pagina overzicht weer gegeven. Kopieer de **ObjectID** door te klikken op het pictogram rechts van het tekstvak. Plak dit ergens anders zodat u het in de volgende stap kunt gebruiken als `userObjectId` .

1. De gebruiker of groep de rol RBAC-eigenaar verlenen aan het inschrijvings account

    Ken met de waarden die u in de eerste twee stappen hebt verzameld, de gebruiker of groep de rol RBAC-eigenaar toe aan het inschrijvings account.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Voer de volgende opdracht uit, waarbij ```<enrollmentAccountObjectId>``` u vervangt door de `name` kopie die u in de eerste stap () hebt gekopieerd ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Vervang door ```<userObjectId>``` de object-id die u uit de tweede stap hebt gekopieerd.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Wanneer de rol eigenaar is toegewezen aan het bereik van het inschrijvings account, reageert Azure met informatie van de roltoewijzing:

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Voer de volgende opdracht [AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) uit en vervang ```<enrollmentAccountObjectId>``` door de `ObjectId` verzamelde in de eerste stap ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Vervang door ```<userObjectId>``` de object-id die in de tweede stap is verzameld.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-2)

    Voer de volgende opdracht [AZ Role Assignment Create](../../role-based-access-control/role-assignments-cli.md) uit, waarbij u vervangt door ```<enrollmentAccountObjectId>``` de `name` kopie die u in de eerste stap () hebt gekopieerd ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Vervang door ```<userObjectId>``` de object-id die in de tweede stap is verzameld.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Zodra een gebruiker een RBAC-eigenaar wordt voor uw inschrijvings account, kunnen ze [via programma code abonnementen maken](programmatically-create-subscription.md) . Een abonnement dat is gemaakt door een gemachtigde gebruiker heeft nog steeds de oorspronkelijke eigenaar van het account als service beheerder, maar heeft standaard ook de gedelegeerde gebruiker als een RBAC-eigenaar.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controleren wie abonnementen heeft gemaakt met behulp van activiteiten logboeken

Voor het bijhouden van de abonnementen die zijn gemaakt via deze API, gebruikt u de [API voor Tenant activiteiten logboeken](/rest/api/monitor/tenantactivitylogs). Het is momenteel niet mogelijk Power shell, CLI of Azure Portal te gebruiken om het maken van abonnementen bij te houden.

1. [Verhoog het toegangsniveau](../../role-based-access-control/elevate-access-global-admin.md) als tenantbeheerder van de Azure AD-tenant en wijs vervolgens de rol van Lezer toe aan de controlerende gebruiker voor het bereik `/providers/microsoft.insights/eventtypes/management`. Deze toegang is beschikbaar in de rol [lezer](../../role-based-access-control/built-in-roles.md#reader) , de rol [bewaking van Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) of een [aangepaste rol](../../role-based-access-control/custom-roles.md).
1. Als controle gebruiker roept u de API voor het [Tenant activiteiten logboek](/rest/api/monitor/tenantactivitylogs) op om activiteiten voor het maken van abonnementen weer te geven. Voorbeeld:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Probeer [ARMClient](https://github.com/projectkudu/ARMClient) om deze API eenvoudig aan te roepen vanaf de opdrachtregel.

## <a name="next-steps"></a>Volgende stappen

* Nu de gebruiker of Service-Principal gemachtigd is om een abonnement te maken, kunt u die identiteit gebruiken om [via programma code Azure Enter prise-abonnementen te maken](programmatically-create-subscription.md).
* Zie [voorbeeld code op github](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)voor een voor beeld van het maken van abonnementen met behulp van .net.
* Zie [Azure Resource Manager Overview](overview.md)voor meer informatie over Azure Resource Manager en de api's.
* Zie [uw resources organiseren met Azure-beheer groepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van een groot aantal abonnementen met behulp van beheer groepen
* Voor een uitgebreide best practice richt lijnen voor grote organisaties op het beheer van abonnementen raadpleegt u [Azure Enter prise steigers-prescripted Subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance)
