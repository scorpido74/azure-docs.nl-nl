---
title: Programmatisch Azure Enter prise-abonnementen maken | Microsoft Docs
description: Meer informatie over het programmatisch maken van extra Azure Enter prise-of Enterprise Dev/Test-abonnementen.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 755eabe97508b403205ff04a8d2d35feee314eb9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258927"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmatisch Azure Enter prise-abonnementen maken (preview)

Als Azure-klant op [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kunt u via programma code EA-(MS-AZR-0017P) en EA dev/test-abonnementen (MS-AZR-0148P) maken. In dit artikel leert u hoe u via programma code abonnementen kunt maken met behulp van Azure Resource Manager.

Wanneer u een Azure-abonnement maakt op basis van deze API, wordt dat abonnement geregeld op basis van de overeenkomst waaronder u Microsoft Azure Services van micro soft of een geautoriseerde wederverkoper hebt verkregen. Zie [Microsoft Azure juridische informatie](https://azure.microsoft.com/support/legal/)voor meer informatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U moet beschikken over een rol van eigenaar op het inschrijvings account waarvoor u abonnementen wilt maken. Er zijn twee manieren om deze rollen te verkrijgen:

* De registratie beheerder kan [u een account eigenaar maken](https://ea.azure.com/helpdocs/addNewAccount) (aanmelden vereist), zodat u een eigenaar van het inschrijvings account kunt worden. Volg de instructies in de e-mail met de uitnodiging die u ontvangt om hand matig een eerste abonnement te maken. Bevestig het eigendom van het account en maak hand matig een eerste EA-abonnement voordat u verdergaat met de volgende stap. Het is niet voldoende om het account toe te voegen aan de inschrijving.

* Een bestaande eigenaar van het inschrijvings account kan [u toegang verlenen](grant-access-to-create-subscription.md). En als u een Service-Principal wilt gebruiken om het EA-abonnement te maken, moet u [die service-principal de mogelijkheid geven om abonnementen te maken](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Accounts waartoe u toegang hebt, zoeken

Nadat u bent toegevoegd aan een Azure EA-inschrijving als een account eigenaar, gebruikt Azure de account-naar-inschrijvings relatie om te bepalen waar de abonnements kosten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden in rekening gebracht voor de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden door geven over het inschrijvings account en de gebruikers-principals voor de eigenaar van het abonnement. 

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *basismap*van het account, dat wil zeggen de directory waarin abonnementen standaard worden gemaakt.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvings account SignUpEngineering@contoso.com , kopieert ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```u dit. Deze id is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `ObjectId` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvings account SignUpEngineering@contoso.com , kopieert ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```u dit. Plak deze object-ID ergens anders zodat u deze in de volgende stap als de `enrollmentAccountObjectId`kunt gebruiken.

## <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

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

Gebruik de `principalName` eigenschap om het account te identificeren waarnaar u wilt dat abonnementen worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvings account SignUpEngineering@contoso.com , kopieert ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```u dit. Deze id is de object-ID van het inschrijvings account. Plak deze waarde ergens anders zodat u deze in de volgende stap kunt gebruiken als `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvings account

In het volgende voor beeld wordt een abonnement met de naam *ontwikkel team* gemaakt in het inschrijvings account dat u in de vorige stap hebt geselecteerd. Het abonnements aanbod is *MS-AZR-0017P* (Regular micro soft Enterprise Agreement). Daarnaast worden optioneel twee gebruikers toegevoegd als RBAC-eigen aars voor het abonnement.

# <a name="resttabrest"></a>[REST](#tab/rest)

Voer de volgende aanvraag uit en `<enrollmentAccountObjectId>` Vervang deze `name` door de gekopieerde van de```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```eerste stap (). Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

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

| Element naam  | Vereist | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `offerType`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `owners`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |

In het antwoord krijgt u een `subscriptionOperation` object terug voor bewaking. Wanneer het maken van het abonnement is voltooid `subscriptionOperation` , retourneert het object `subscriptionLink` een object met de abonnements-id.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer eerst deze preview-module door uit `Install-Module Az.Subscription -AllowPrerelease`te voeren. Om ervoor te `-AllowPrerelease` zorgen dat u werkt, installeert u een recente versie van PowerShellGet van de [PowerShellGet-module ophalen](/powershell/gallery/installing-psget).

Voer de opdracht [New-AzSubscription](/powershell/module/az.subscription) hieronder uit, `<enrollmentAccountObjectId>` waarbij u `ObjectId` vervangt door de verzamelde in```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```de eerste stap (). Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Element naam  | Vereist | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Ja       | Tekenreeks | De object-ID van het inschrijvings account dat voor het abonnement wordt gemaakt en wordt gefactureerd. Deze waarde is een GUID die u krijgt van `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `OwnerSignInName`    | Nee       | Tekenreeks | Het e-mail adres van elke gebruiker die u wilt toevoegen als een RBAC-eigenaar van het abonnement wanneer het wordt gemaakt. U kunt deze para meter gebruiken in `OwnerObjectId`plaats van.|
| `OwnerApplicationId` | Nee       | Tekenreeks | De toepassings-ID van elke service-principal die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt. U kunt deze para meter gebruiken in `OwnerObjectId`plaats van. Wanneer u deze para meter gebruikt, moet de Service-Principal [Lees toegang hebben tot de map](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Zie [New-AzSubscription](/powershell/module/az.subscription)voor een volledige lijst met alle para meters.

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst deze preview-extensie door uit `az extension add --name subscription`te voeren.

Voer de opdracht [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) hieronder uit, `<enrollmentAccountObjectId>` waarbij u `name` vervangt door de kopie die u in```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```de eerste stap () hebt gekopieerd. Als u eigen aars wilt opgeven, leert u [hoe u gebruikers object-id's kunt ophalen](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Element naam  | Vereist | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nee      | Tekenreeks | De weergave naam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, zoals "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Ja      | Tekenreeks | De aanbieding van het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (productie gebruik) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) moeten worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Ja       | Tekenreeks | De object-ID van het inschrijvings account dat voor het abonnement wordt gemaakt en wordt gefactureerd. Deze waarde is een GUID die u krijgt van `az billing enrollment-account list`. |
| `owner-object-id`      | Nee       | Tekenreeks | De object-ID van een gebruiker die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `owner-upn`    | Nee       | Tekenreeks | Het e-mail adres van elke gebruiker die u wilt toevoegen als een RBAC-eigenaar van het abonnement wanneer het wordt gemaakt. U kunt deze para meter gebruiken in `owner-object-id`plaats van.|
| `owner-spn` | Nee       | Tekenreeks | De toepassings-ID van elke service-principal die u wilt toevoegen als een RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt. U kunt deze para meter gebruiken in `owner-object-id`plaats van. Wanneer u deze para meter gebruikt, moet de Service-Principal [Lees toegang hebben tot de map](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Zie [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)voor een volledige lijst met alle para meters.

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van de API voor het maken van Azure Enter prise-abonnementen

- Alleen Azure Enter prise-abonnementen kunnen worden gemaakt met deze API.
- Er is een limiet van 200 abonnementen per inschrijvings account. Daarna kunnen meer abonnementen voor het account alleen worden gemaakt via het account centrum. Als u meer abonnementen via de API wilt maken, maakt u een nieuw inschrijvings account.
- Gebruikers die geen eigenaar zijn van accounts, maar die zijn toegevoegd aan een inschrijvings account via RBAC, kunnen geen abonnementen maken met het account centrum.
- U kunt de Tenant niet selecteren voor het abonnement dat u wilt maken in. Het abonnement wordt altijd gemaakt in de thuis Tenant van de eigenaar van het account. Zie de Tenant van een [abonnement wijzigen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)als u het abonnement wilt verplaatsen naar een andere Tenant.

## <a name="next-steps"></a>Volgende stappen

* Zie [voorbeeld code op github](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)voor een voor beeld van het maken van abonnementen met behulp van .net.
* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [toegang verlenen voor het maken van Azure Enter prise-abonnementen (preview)](grant-access-to-create-subscription.md)voor meer informatie.
* Zie [uw resources organiseren met Azure-beheer groepen](management-groups-overview.md) voor meer informatie over het beheren van een groot aantal abonnementen met behulp van beheer groepen
