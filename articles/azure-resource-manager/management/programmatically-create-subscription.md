---
title: Programmatisch Azure-abonnementen maken
description: Meer informatie over het programmatisch maken van extra Azure-abonnementen.
author: anuragdalmia
ms.topic: conceptual
ms.date: 07/09/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 7fac201de2fd623058eb5771e194ae697f879ee8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224159"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programmatisch Azure-abonnementen maken (preview)

Azure-klanten met een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [micro soft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) of [micro soft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) -facturerings account kunnen abonnementen maken via een programma. In dit artikel leert u hoe u via programma code abonnementen kunt maken met behulp van Azure Resource Manager.

Wanneer u een Azure-abonnement programmatisch maakt, wordt dat abonnement geregeld op basis van de overeenkomst waaronder u Azure-Services van micro soft of een geautoriseerde wederverkoper hebt verkregen. Zie [Microsoft Azure juridische informatie](https://azure.microsoft.com/support/legal/)voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Abonnementen maken voor een EA-facturerings account

Gebruik de informatie in de volgende secties om EA-abonnementen te maken.

### <a name="prerequisites"></a>Vereisten

U moet een rol van eigenaar hebben voor een inschrijvings account om een abonnement te maken. Er zijn twee manieren om de rol te verkrijgen:

* De Enter prise-beheerder van uw inschrijving kan [u een account eigenaar maken](https://ea.azure.com/helpdocs/addNewAccount) (aanmelden vereist) waarmee u eigenaar van het inschrijvings account is.

* Een bestaande eigenaar van het inschrijvings account kan [u toegang verlenen](grant-access-to-create-subscription.md). En als u een Service-Principal wilt gebruiken om een EA-abonnement te maken, moet u [die service-principal de mogelijkheid geven om abonnementen te maken](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Accounts waartoe u toegang hebt, zoeken

Wanneer u bent toegevoegd aan een inschrijvings account dat is gekoppeld aan een account eigenaar, gebruikt Azure de account-naar-inschrijvings relatie om te bepalen waar de abonnements kosten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden gefactureerd aan de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden door geven over het inschrijvings account en de gebruikers-principals voor de eigenaar van het abonnement.

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *basismap*van het account, dat wil zeggen de directory waarin abonnementen standaard worden gemaakt.

### <a name="rest"></a>[REST](#tab/rest)

Aanvraag voor een lijst met alle inschrijvings accounts waartoe u toegang hebt:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

De API-respons geeft een lijst van alle inschrijvings accounts waartoe u toegang hebt:

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

Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het SignUpEngineering@contoso.com inschrijvings account, kopieert u dit ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Deze id is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId` .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Open [Azure Cloud shell](https://shell.azure.com/) en selecteer Power shell.

Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvings accounts weer te geven waartoe u toegang hebt.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure reageert met een lijst met inschrijvings accounts waartoe u toegang hebt:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `ObjectId` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het SignUpEngineering@contoso.com inschrijvings account, kopieert u dit ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Plak deze object-ID ergens anders zodat u deze in de volgende stap als de kunt gebruiken `enrollmentAccountObjectId` .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de opdracht [AZ facturering Enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) voor een lijst met alle inschrijvings accounts waartoe u toegang hebt.

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

Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het SignUpEngineering@contoso.com inschrijvings account, kopieert u dit ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Deze id is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId` .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvings account

In het volgende voor beeld wordt een abonnement met de naam *ontwikkel team* gemaakt in het inschrijvings account dat u in de vorige stap hebt geselecteerd. Het abonnements aanbod is *MS-AZR-0017P* (Regular micro soft Enterprise Agreement). Daarnaast worden optioneel twee gebruikers toegevoegd als RBAC-eigen aars voor het abonnement.

### <a name="rest"></a>[REST](#tab/rest)

Voer de volgende aanvraag uit en vervang `<enrollmentAccountObjectId>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `offerType`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `owners`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |

Als onderdeel van de koptekst wordt in het antwoord `Location` een URL weer gegeven die u kunt opvragen voor de status van de bewerking voor het maken van het abonnement. Wanneer het maken van het abonnement is voltooid, retourneert een GET on `Location` -URL een `subscriptionLink` object met de abonnements-id. Raadpleeg API-documentatie voor [abonnementen](https://docs.microsoft.com/rest/api/subscription/) voor meer informatie

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer eerst deze preview-module door uit te voeren `Install-Module Az.Subscription -AllowPrerelease` . Om ervoor te zorgen dat u `-AllowPrerelease` werkt, installeert u een recente versie van PowerShellGet van de [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget).

Voer de opdracht [New-AzSubscription](/powershell/module/az.subscription) hieronder uit, waarbij u vervangt `<enrollmentAccountObjectId>` door de `ObjectId` verzamelde in de eerste stap ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Ja       | Tekenreeks | De object-ID van het inschrijvings account dat voor het abonnement wordt gemaakt en wordt gefactureerd. Deze waarde is een GUID die u krijgt van `Get-AzEnrollmentAccount` . |
| `OwnerObjectId`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `OwnerSignInName`    | Nee       | Tekenreeks | Het e-mail adres van elke gebruiker die u wilt toevoegen als een RBAC-eigenaar van het abonnement wanneer het wordt gemaakt. U kunt deze para meter gebruiken in plaats van `OwnerObjectId` .|
| `OwnerApplicationId` | Nee       | Tekenreeks | De toepassings-ID van elke service-principal die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt. U kunt deze para meter gebruiken in plaats van `OwnerObjectId` . Wanneer u deze para meter gebruikt, moet de Service-Principal [Lees toegang hebben tot de map](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Zie [New-AzSubscription](/powershell/module/az.subscription)voor een volledige lijst met alle para meters.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installeer eerst deze preview-extensie door uit te voeren `az extension add --name subscription` .

Voer de opdracht [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) hieronder uit, waarbij `<enrollmentAccountObjectId>` u vervangt door de kopie die `name` u in de eerste stap () hebt gekopieerd ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Ja       | Tekenreeks | De object-ID van het inschrijvings account dat voor het abonnement wordt gemaakt en wordt gefactureerd. Deze waarde is een GUID die u krijgt van `az billing enrollment-account list` . |
| `owner-object-id`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `owner-upn`    | Nee       | Tekenreeks | Het e-mail adres van elke gebruiker die u wilt toevoegen als een RBAC-eigenaar van het abonnement wanneer het wordt gemaakt. U kunt deze para meter gebruiken in plaats van `owner-object-id` .|
| `owner-spn` | Nee       | Tekenreeks | De toepassings-ID van elke service-principal die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt. U kunt deze para meter gebruiken in plaats van `owner-object-id` . Wanneer u deze para meter gebruikt, moet de Service-Principal [Lees toegang hebben tot de map](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Zie [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)voor een volledige lijst met alle para meters.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van de API voor het maken van Azure Enter prise-abonnementen

- Alleen Azure Enter prise-abonnementen kunnen worden gemaakt met deze API.
- Er is een limiet van 2000 abonnementen per inschrijvings account. Daarna kunnen meer abonnementen voor het account alleen worden gemaakt in de Azure Portal. Als u meer abonnementen via de API wilt maken, maakt u een nieuw inschrijvings account.
- Gebruikers die geen eigenaar zijn van accounts, maar die zijn toegevoegd aan een inschrijvings account via RBAC, kunnen geen abonnementen maken in de Azure Portal.
- U kunt de Tenant niet selecteren voor het abonnement dat u wilt maken in. Het abonnement wordt altijd gemaakt in de thuis Tenant van de eigenaar van het account. Zie de Tenant van een [abonnement wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)als u het abonnement wilt verplaatsen naar een andere Tenant.


## <a name="create-subscriptions-for-an-mca-account"></a>Abonnementen maken voor een MCA-account

### <a name="prerequisites"></a>Vereisten

U moet een rol voor eigenaar, bijdrager of Azure-abonnement hebben op een factuur sectie of eigenaar of rol Inzender voor een facturerings profiel of een facturerings account om abonnementen te maken. Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

In het voor beeld hieronder wordt REST-Api's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Facturerings accounts zoeken waartoe u toegang hebt

Voer de onderstaande aanvraag in om alle facturerings accounts weer te geven.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
De API-respons bevat een lijst met de facturerings accounts waartoe u toegang hebt.

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
Gebruik de `displayName` eigenschap om het facturerings account te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat de agreeementType van het account *MicrosoftCustomerAgreement*is. Kopieer de `name` account.  Als u bijvoorbeeld een abonnement voor het `Contoso` facturerings account wilt maken, kopieert u dit `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Factuur secties zoeken om abonnementen te maken

De kosten voor uw abonnement worden weer gegeven op een gedeelte van de factuur van een facturerings profiel. Gebruik de volgende API om de lijst met factuur secties en facturerings profielen op te halen waarvoor u gemachtigd bent om Azure-abonnementen te maken.

Voer de volgende aanvraag uit en vervang `<billingAccountName>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
In de API-respons worden alle gedeelten van de factuur en de facturerings profielen vermeld waarop u toegang hebt tot het maken van abonnementen:

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

Gebruik de `invoiceSectionDisplayName` eigenschap om de factuur sectie te identificeren waarvoor u abonnementen wilt maken. Kopieer de `invoiceSectionId` en `billingProfileId` een van de `skuId` voor de factuur sectie. Als u bijvoorbeeld een abonnement van het `Microsoft Azure plan` gedeelte type voor factuur wilt maken `Development` , kopieert u, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` en `0001` . Plak deze waarden ergens anders, zodat u ze in de volgende stap kunt gebruiken.

### <a name="create-a-subscription-for-an-invoice-section"></a>Een abonnement voor een factuur sectie maken

In het volgende voor beeld wordt een abonnement met de naam *dev team* van het type *Microsoft Azure plan* gemaakt voor de sectie *Development* invoice. Het abonnement wordt gefactureerd aan het facturerings profiel *van de contoso Finance* en wordt weer gegeven in de sectie *ontwikkeling* van de factuur.

Voer de volgende aanvraag uit en vervang deze door `<invoiceSectionId>` de `invoiceSectionId` gekopieerde uit de tweede stap ( ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX``` ). U moet de `billingProfileId` en `skuId` gekopieerde uit de tweede stap door geven in de aanvraag parameters van de API. Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Ja      | Tekenreeks | De weergave naam van het abonnement.|
| `billingProfileId`   | Ja      | Tekenreeks | De ID van het facturerings profiel dat wordt gefactureerd voor de kosten van het abonnement.  |
| `skuId` | Ja      | Tekenreeks | De SKU-ID die het type van het Azure-abonnement bepaalt. |
| `owners`      | Nee       | Tekenreeks | De object-ID van elke gebruiker of service-principal die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `costCenter` | Nee      | Tekenreeks | Het kosten centrum dat aan het abonnement is gekoppeld. Het wordt weer gegeven in het CSV-bestand voor gebruik. |
| `managementGroupId` | Nee      | Tekenreeks | De ID van de beheer groep waaraan het abonnement wordt toegevoegd. Zie [beheergroepen-List API](/rest/api/resources/managementgroups/list)als u de lijst met beheer groepen wilt ophalen. Gebruik de ID van een beheer groep van de API. |

In het antwoord krijgt u een object terug `subscriptionCreationResult` voor bewaking. Wanneer het maken van het abonnement is voltooid, `subscriptionCreationResult` retourneert het object een `subscriptionLink` object met de abonnements-id.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Abonnementen maken voor een MPA-facturerings account

### <a name="prerequisites"></a>Vereisten

U moet een rol van globale beheerder of beheerder hebben in het account van de Cloud solution provider van uw organisatie om een abonnement te maken voor uw facturerings account. Zie [Partner Center: gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie.

In het voor beeld hieronder wordt REST-Api's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>De facturerings accounts zoeken waartoe u toegang hebt

Voer de onderstaande aanvraag in om alle accounts weer te geven waartoe u toegang hebt.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
De API-antwoord lijst met de facturerings accounts.

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
Gebruik de `displayName` eigenschap om het facturerings account te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat de agreeementType van het account *MicrosoftPartnerAgreement*is. Kopieer de `name` voor het account. Als u bijvoorbeeld een abonnement voor het `Contoso` facturerings account wilt maken, kopieert u dit `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="find-customers-that-have-azure-plans"></a>Klanten zoeken die Azure-abonnementen hebben

Maak de volgende aanvraag, vervangen `<billingAccountName>` door de `name` kopie van de eerste stap ( ```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) om alle klanten in het facturerings account weer te geven voor wie u Azure-abonnementen kunt maken.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
De API-reactie vermeldt de klanten in het facturerings account met Azure-abonnementen. U kunt abonnementen maken voor deze klanten.

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

Gebruik de `displayName` eigenschap om de klant te identificeren waarvoor u abonnementen wilt maken. Kopieer de `id` voor de klant. Als u bijvoorbeeld een abonnement voor wilt maken `Fabrikam toys` , kopieert u dit `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Plak deze waarde ergens in de volgende stappen om deze te gebruiken.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optioneel voor indirecte providers: de wederverkopers voor een klant ophalen

Als u een indirecte provider bent in het CSP-model met twee lagen, kunt u een wederverkoper opgeven tijdens het maken van abonnementen voor klanten.

Maak de volgende aanvraag, vervangen `<customerId>` door de `id` kopie van de tweede stap ( ```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ) om alle wederverkopers weer te geven die beschikbaar zijn voor een klant.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
In het API-antwoord worden de wederverkopers voor de klant vermeld:

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
Gebruik de `description` eigenschap om de wederverkoper te identificeren die wordt gekoppeld aan het abonnement. Kopieer het `resellerId` voor de reseller. Als u bijvoorbeeld wilt koppelen `Wingtip` , kopieert u deze `3xxxxx` . Plak deze waarde ergens zodat u deze kunt gebruiken tijdens de volgende stap.

### <a name="create-a-subscription-for-a-customer"></a>Een abonnement voor een klant maken

In het volgende voor beeld wordt een abonnement met de naam *ontwikkel team* voor *fabrikam Toys* gemaakt en wordt *Wingtip* Reseller aan het abonnement gekoppeld. T

Voer de volgende aanvraag uit en vervang deze door `<customerId>` de `id` gekopieerde uit de tweede stap ( ```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Geef de optionele *resellerId* gekopieerd van de tweede stap in de aanvraag parameters van de API.

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
| `displayName` | Ja      | Tekenreeks | De weergave naam van het abonnement.|
| `skuId` | Ja      | Tekenreeks | De SKU-ID van het Azure-abonnement. *0001* gebruiken voor abonnementen van het type Microsoft Azure plan |
| `resellerId`      | Nee       | Tekenreeks | De MPN-ID van de wederverkoper die wordt gekoppeld aan het abonnement.  |

In het antwoord krijgt u een object terug `subscriptionCreationResult` voor bewaking. Wanneer het maken van het abonnement is voltooid, `subscriptionCreationResult` retourneert het object een `subscriptionLink` object met de abonnements-id.

## <a name="next-steps"></a>Volgende stappen

* Zie [voorbeeld code op github](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)voor een voor beeld van het maken van Enterprise Agreement een EA-abonnement met behulp van .net.
* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [toegang verlenen voor het maken van Azure Enter prise-abonnementen (preview)](grant-access-to-create-subscription.md)voor meer informatie.
* Zie [uw resources organiseren met Azure-beheer groepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van een groot aantal abonnementen met behulp van beheer groepen
