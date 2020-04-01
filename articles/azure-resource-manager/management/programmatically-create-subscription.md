---
title: Programmatisch Azure-abonnementen maken
description: Meer informatie over het programmatisch maken van aanvullende Azure-abonnementen.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460395"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programmatisch Azure-abonnementen maken (voorbeeld)

Azure-klanten met een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) of [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) kunnen programmatisch abonnementen maken. In dit artikel leert u hoe u programmatisch abonnementen maakt met Azure Resource Manager.

Wanneer u een Azure-abonnement programmatisch maakt, wordt dat abonnement geregeld door de overeenkomst waaronder u Azure-services hebt verkregen van Microsoft of een geautoriseerde wederverkoper. Zie [Microsoft Azure Legal Information voor](https://azure.microsoft.com/support/legal/)meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Abonnementen maken voor een EA-factureringsaccount

Gebruik de informatie in de volgende secties om EA-abonnementen te maken.

### <a name="prerequisites"></a>Vereisten

Je moet een rol eigenaar hebben in een inschrijvingsaccount om een abonnement te maken. Er zijn twee manieren om de rol te krijgen:

* De Enterprise Administrator van uw inschrijving kan [u een accounteigenaar maken](https://ea.azure.com/helpdocs/addNewAccount) (aanmelden vereist) waardoor u eigenaar bent van het inschrijvingsaccount.

* Een bestaande eigenaar van het inschrijvingsaccount kan [u toegang verlenen.](grant-access-to-create-subscription.md) Als u een serviceprincipal wilt gebruiken om een EA-abonnement te maken, moet u [die serviceprincipal ook de mogelijkheid bieden om abonnementen te maken.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Accounts zoeken waartoe u toegang hebt

Nadat u bent toegevoegd aan een inschrijvingsaccount dat is gekoppeld aan een accounteigenaar, gebruikt Azure de relatie tussen account en inschrijving om te bepalen waar de abonnementskosten moeten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden gefactureerd op de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden doorgeven aan het inschrijvingsaccount en de gebruikersprincipals om eigenaar te zijn van het abonnement.

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *thuismap*van de eigenaar van het account, de map waarin abonnementen standaard zijn gemaakt.

### <a name="rest"></a>[REST](#tab/rest)

Verzoek om een lijst van alle inschrijvingsaccounts waartoe u toegang hebt:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Het API-antwoord bevat alle inschrijvingsaccounts waartoe u toegang hebt:

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

Gebruik `principalName` de eigenschap om te bepalen aan welke account u abonnementen moet factureren. Kopieer `name` de van die rekening. Als u bijvoorbeeld abonnementen wilt maken SignUpEngineering@contoso.com onder het inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Deze id is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Open [Azure Cloud Shell](https://shell.azure.com/) en selecteer PowerShell.

Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvingsaccounts waartoe u toegang hebt, weer te geven.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Gebruik `principalName` de eigenschap om te bepalen aan welke account u abonnementen moet factureren. Kopieer `ObjectId` de van die rekening. Als u bijvoorbeeld abonnementen wilt maken SignUpEngineering@contoso.com onder het inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Plak deze object-id ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als de .

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht lijst met az-factureringsinschrijvingen](https://aka.ms/EASubCreationPublicPreviewCLI) om alle inschrijvingsaccounts waartoe u toegang hebt, weer te geven.

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

Gebruik `principalName` de eigenschap om te bepalen aan welke account u abonnementen moet factureren. Kopieer `name` de van die rekening. Als u bijvoorbeeld abonnementen wilt maken SignUpEngineering@contoso.com onder het inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Deze id is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze kunt `enrollmentAccountObjectId`gebruiken in de volgende stap als .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvingsaccount

In het volgende voorbeeld wordt een abonnement met de naam *Dev-teamabonnement* gemaakt in het inschrijvingsaccount dat in de vorige stap is geselecteerd. Het abonnementsaanbod is *MS-AZR-0017P* (reguliere Microsoft Enterprise Agreement). Het voegt ook optioneel twee gebruikers toe als RBAC-eigenaren voor het abonnement.

### <a name="rest"></a>[REST](#tab/rest)

Voer de volgende aanvraag uit en vervang `<enrollmentAccountObjectId>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Als u eigenaren wilt opgeven, leest u hoe u [gebruikersobject-id's krijgen.](grant-access-to-create-subscription.md#userObjectId)

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als dit niet is opgegeven, is deze ingesteld op de naam van de aanbieding, zoals 'Microsoft Azure Enterprise'.                                 |
| `offerType`   | Ja      | Tekenreeks | Het aanbod van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productiegebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, moet worden [ingeschakeld via de EA-portal).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Nee       | Tekenreeks | De object-id van elke gebruiker die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer deze is gemaakt.  |

In het antwoord krijg `subscriptionOperation` je een object terug voor controle. Wanneer de aanmaak van `subscriptionOperation` het abonnement `subscriptionLink` is voltooid, retourneert het object een object met de abonnements-id.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer eerst deze previewmodule `Install-Module Az.Subscription -AllowPrerelease`door . Installeer een `-AllowPrerelease` recente versie van PowerShellGet van [Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget)om er zeker van te zijn dat het werkt.

Voer de opdracht [Nieuw-AzAbonnement](/powershell/module/az.subscription) `<enrollmentAccountObjectId>` hieronder `ObjectId` uit en vervang```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```de verzamelde in de eerste stap ( ). Als u eigenaren wilt opgeven, leest u hoe u [gebruikersobject-id's krijgen.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als dit niet is opgegeven, is deze ingesteld op de naam van de aanbieding, zoals 'Microsoft Azure Enterprise'.                                 |
| `OfferType`   | Ja      | Tekenreeks | Het aanbod van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productiegebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, moet worden [ingeschakeld via de EA-portal).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Ja       | Tekenreeks | De object-id van het inschrijvingsaccount waarop het abonnement is gemaakt en aan u wordt gefactureerd. Deze waarde is een GUID `Get-AzEnrollmentAccount`die u krijgt van . |
| `OwnerObjectId`      | Nee       | Tekenreeks | De object-id van elke gebruiker die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer deze is gemaakt.  |
| `OwnerSignInName`    | Nee       | Tekenreeks | Het e-mailadres van elke gebruiker die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer het wordt gemaakt. U deze parameter `OwnerObjectId`gebruiken in plaats van.|
| `OwnerApplicationId` | Nee       | Tekenreeks | De toepassings-ID van een serviceprincipal die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer het wordt gemaakt. U deze parameter `OwnerObjectId`gebruiken in plaats van. Bij het gebruik van deze parameter moet de serviceprincipal [leestoegang hebben tot de map.](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)|

Zie [Nieuw-AzAbonnement](/powershell/module/az.subscription)voor een volledige lijst met alle parameters.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst deze voorbeeldextensie `az extension add --name subscription`door het uitvoeren van .

Voer de opdracht [AZ-account](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` hieronder `name` aan en vervang de```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```opdracht die u in de eerste stap hebt gekopieerd ( ). Als u eigenaren wilt opgeven, leest u hoe u [gebruikersobject-id's krijgen.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als dit niet is opgegeven, is deze ingesteld op de naam van de aanbieding, zoals 'Microsoft Azure Enterprise'.                                 |
| `offer-type`   | Ja      | Tekenreeks | Het aanbod van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productiegebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, moet worden [ingeschakeld via de EA-portal).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Ja       | Tekenreeks | De object-id van het inschrijvingsaccount waarop het abonnement is gemaakt en aan u wordt gefactureerd. Deze waarde is een GUID `az billing enrollment-account list`die u krijgt van . |
| `owner-object-id`      | Nee       | Tekenreeks | De object-id van elke gebruiker die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer deze is gemaakt.  |
| `owner-upn`    | Nee       | Tekenreeks | Het e-mailadres van elke gebruiker die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer het wordt gemaakt. U deze parameter `owner-object-id`gebruiken in plaats van.|
| `owner-spn` | Nee       | Tekenreeks | De toepassings-ID van een serviceprincipal die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer het wordt gemaakt. U deze parameter `owner-object-id`gebruiken in plaats van. Bij het gebruik van deze parameter moet de serviceprincipal [leestoegang hebben tot de map.](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)|

Zie [az-account maken](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)om een volledige lijst met alle parameters te bekijken.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van Api voor het maken van Azure Enterprise-abonnementen

- Alleen Azure Enterprise-abonnementen kunnen met deze API worden gemaakt.
- Er is een limiet van 500 abonnementen per inschrijvingsaccount. Daarna kunnen meer abonnementen voor het account alleen worden gemaakt in de Azure-portal. Als u meer abonnementen wilt maken via de API, maakt u een ander inschrijvingsaccount.
- Gebruikers die geen accounteigenaren zijn, maar via RBAC aan een inschrijvingsaccount zijn toegevoegd, kunnen geen abonnementen maken in de Azure-portal.
- U de tenant niet selecteren voor het abonnement waarin u wilt worden gemaakt. Het abonnement wordt altijd gemaakt in de woninghuurder van de accounteigenaar. Zie [Abonnementstenant wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)als u het abonnement naar een andere tenant wilt verplaatsen.


## <a name="create-subscriptions-for-an-mca-account"></a>Abonnementen maken voor een MCA-account

### <a name="prerequisites"></a>Vereisten

U moet een rol voor eigenaar, bijdrager of Azure-abonnementsmaker hebben in een factuursectie of eigenaar of bijdrager rol in een factureringsprofiel of een factureringsaccount om abonnementen te maken. Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

In het onderstaande voorbeeld worden REST-API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Factureringsaccounts zoeken waartoe u toegang hebt

Doe onderstaand verzoek om alle factureringsaccounts op te sommen.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Het API-antwoord bevat de factureringsaccounts waartoe u toegang hebt.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Gebruik `displayName` de eigenschap om het factureringsaccount te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat het overeenkomstementType van het account *MicrosoftCustomerAgreement*is. Kopieer `name` de rekening.  Als u bijvoorbeeld een abonnement voor `Contoso` het factureringsaccount wilt `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`maken, kopieert u . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Factuursecties zoeken om abonnementen te maken

De kosten voor uw abonnement worden weergegeven op een gedeelte van de factuur van een factureringsprofiel. Gebruik de volgende API om de lijst met factuursecties en factureringsprofielen op te halen waarop u toestemming hebt om Azure-abonnementen te maken.

Voer de volgende aanvraag uit en vervang `<billingAccountName>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
De API-respons bevat alle factuursecties en hun factureringsprofielen waarop u toegang hebt om abonnementen te maken:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Gebruik `invoiceSectionDisplayName` de eigenschap om de factuursectie te identificeren waarvoor u abonnementen wilt maken. Kopieer `invoiceSectionId`de `billingProfileId` , en `skuId` een van de voor de factuur sectie. Als u bijvoorbeeld een abonnement op `Microsoft Azure plan` het `Development` type voor de `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`factuursectie wilt maken, kopieert `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` u en `0001`kopieert u . Plak deze waarden ergens zodat u ze in de volgende stap gebruiken.

### <a name="create-a-subscription-for-an-invoice-section"></a>Een abonnement maken voor een factuursectie

In het volgende voorbeeld wordt een abonnement gemaakt met de naam *Dev Team-abonnement* van het type *Microsoft Azure Plan* voor de sectie *Ontwikkelingsfactuur.* Het abonnement wordt gefactureerd in het factureringsprofiel van *contoso finance* en wordt weergegeven in het gedeelte *Ontwikkeling* van de factuur.

Doe het volgende verzoek, `<invoiceSectionId>` `invoiceSectionId` vervangen door de gekopieerde uit de tweede stap (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). U moet de `billingProfileId` en `skuId` gekopieerde en gekopieerde van de tweede stap in de aanvraagparameters van de API passeren. Als u eigenaren wilt opgeven, leest u hoe u [gebruikersobject-id's krijgen.](grant-access-to-create-subscription.md#userObjectId)

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Tekenreeks | De weergavenaam van het abonnement.|
| `billingProfileId`   | Ja      | Tekenreeks | De id van het factureringsprofiel dat in rekening wordt gebracht voor de kosten van het abonnement.  |
| `skuId` | Ja      | Tekenreeks | De sku-id die het type Azure-abonnement bepaalt. |
| `owners`      | Nee       | Tekenreeks | De object-id van een gebruiker of serviceprincipal die u als RBAC-eigenaar op het abonnement wilt toevoegen wanneer het wordt gemaakt.  |
| `costCenter` | Nee      | Tekenreeks | De kostenplaats die aan het abonnement is gekoppeld. Het verschijnt in het csv-bestand voor gebruik. |
| `managementGroupId` | Nee      | Tekenreeks | De ID van de beheergroep waaraan het abonnement zal worden toegevoegd. Zie [Beheergroepen - Api lijst .](/rest/api/resources/managementgroups/list) Gebruik de ID van een beheergroep uit de API. |

In het antwoord krijg `subscriptionCreationResult` je een object terug voor controle. Wanneer de aanmaak van `subscriptionCreationResult` het abonnement `subscriptionLink` is voltooid, retourneert het object een object met de abonnements-id.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Abonnementen maken voor een MPA-factureringsaccount

### <a name="prerequisites"></a>Vereisten

U moet een rol van globale beheerder of beheerder hebben in het provideraccount van uw organisatie voor cloudoplossingen om een abonnement voor uw factureringsaccount te maken. Zie [Partnercentrum - Gebruikersrollen en machtigingen toewijzen](https://docs.microsoft.com/partner-center/permissions-overview)voor meer informatie.

In het onderstaande voorbeeld worden REST-API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>De factureringsaccounts zoeken waartoe u toegang hebt

Doe onderstaand verzoek om alle factureringsaccounts weer te geven waar toe je toegang hebt.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
In de API-respons worden de factureringsaccounts weergegeven.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Gebruik `displayName` de eigenschap om het factureringsaccount te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat het overeenkomstementType van het account *MicrosoftPartnerAgreement*is. Kopieer `name` de voor rekening. Als u bijvoorbeeld een abonnement voor `Contoso` het factureringsaccount wilt `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`maken, kopieert u . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="find-customers-that-have-azure-plans"></a>Klanten zoeken met Azure-abonnementen

Doe het volgende verzoek, `<billingAccountName>` `name` vervangen door de gekopieerde uit de eerste stap ( )```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```om alle klanten in het factureringsaccount te vermelden voor wie u Azure-abonnementen maken.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Het API-antwoord geeft een overzicht van de klanten in het factureringsaccount met Azure-abonnementen. U abonnementen maken voor deze klanten.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Gebruik `displayName` de eigenschap om de klant te identificeren waarvoor u abonnementen wilt maken. Kopieer `id` de voor de klant. Als u bijvoorbeeld een abonnement wilt `Fabrikam toys`maken voor `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`, kopieert u . Plak deze waarde ergens om deze te gebruiken in de volgende stappen.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optioneel voor indirecte providers: haal de resellers voor een klant

Als u een indirecte provider bent in het CSP-model met twee lagen, u een reseller opgeven terwijl u abonnementen voor klanten maakt.

Doe het volgende verzoek, `<customerId>` `id` vervangen door de gekopieerde uit de tweede stap ( )```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```om alle resellers die beschikbaar zijn voor een klant lijst.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
De API-respons bevat de resellers voor de klant:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Gebruik `description` de eigenschap om de reseller te identificeren die aan het abonnement is gekoppeld. Kopieer `resellerId` de voor de reseller. Als u bijvoorbeeld wilt `Wingtip`koppelen, kopieert `3xxxxx`u . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="create-a-subscription-for-a-customer"></a>Een abonnement voor een klant maken

In het volgende voorbeeld wordt een abonnement met de naam *Dev Team-abonnement* voor *Fabrikam-speelgoed* gemaakt en *wordt Wingtip-reseller* aan het abonnement verbonden. T

Doe het volgende verzoek, `<customerId>` `id` vervangen door de gekopieerde uit de tweede stap (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Geef de optionele *resellerId* door die is gekopieerd vanaf de tweede stap in de aanvraagparameters van de API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Tekenreeks | De weergavenaam van het abonnement.|
| `skuId` | Ja      | Tekenreeks | De sku-id van het Azure-abonnement. *0001* gebruiken voor abonnementen van het type Microsoft Azure-abonnement |
| `resellerId`      | Nee       | Tekenreeks | De MPN-id van de reseller die aan het abonnement wordt gekoppeld.  |

In het antwoord krijg `subscriptionCreationResult` je een object terug voor controle. Wanneer de aanmaak van `subscriptionCreationResult` het abonnement `subscriptionLink` is voltooid, retourneert het object een object met de abonnements-id.

## <a name="next-steps"></a>Volgende stappen

* Zie [voorbeeldcode op GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)voor een voorbeeld voor het maken van een EA-abonnement (Enterprise Agreement) met .NET.
* Nu u een abonnement hebt gemaakt, u die mogelijkheid toekennen aan andere gebruikers en serviceprincipals. Zie [Toegang verlenen om Azure Enterprise-abonnementen te maken (voorbeeld) voor](grant-access-to-create-subscription.md)meer informatie.
* Zie [Uw resources ordenen met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met beheergroepen
