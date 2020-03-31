---
title: Registratiefouten van resourceprovider
description: Beschrijft hoe u registratiefouten van Azure-resourcesprovider oplossen bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273772"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Fouten oplossen voor de registratie van de resourceprovider

In dit artikel worden de fouten beschreven die u tegenkomen bij het gebruik van een resourceprovider die u nog niet eerder in uw abonnement hebt gebruikt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Bij het implementeren van resources ontvangt u mogelijk de volgende foutcode en -bericht:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

U ook een soortgelijk bericht ontvangen waarin staat:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Het foutbericht moet u suggesties geven voor de ondersteunde locaties en API-versies. U uw sjabloon wijzigen in een van de voorgestelde waarden. De meeste providers worden automatisch geregistreerd door de Azure-portal of de command-line interface die u gebruikt, maar niet alle. Als u een bepaalde resourceprovider nog niet eerder hebt gebruikt, moet u deze provider mogelijk registreren.

Of wanneer u automatisch afsluiten voor virtuele machines uitschakelt, ontvangt u mogelijk een foutmelding die vergelijkbaar is met:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Oorzaak

U ontvangt deze fouten om een van deze redenen:

* De vereiste resourceprovider is niet geregistreerd voor uw abonnement
* API-versie die niet wordt ondersteund voor het brontype
* Locatie die niet wordt ondersteund voor het resourcetype
* Voor het automatisch afsluiten van VM's moet de Microsoft.DevTestLab-bronprovider zijn geregistreerd.

## <a name="solution-1---powershell"></a>Oplossing 1 - PowerShell

Gebruik **Get-AzResourceProvider** voor PowerShell om uw registratiestatus te bekijken.

```powershell
Get-AzResourceProvider -ListAvailable
```

Als u een provider wilt registreren, gebruikt u **Register-AzResourceProvider** en geeft u de naam op van de resourceprovider die u wilt registreren.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Gebruik het volgende om de ondersteunde locaties voor een bepaald type resource te gebruiken:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Gebruik het volgende om de ondersteunde API-versies voor een bepaald type resource te gebruiken:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Oplossing 2 - Azure CLI

Als u wilt zien of `az provider list` de provider is geregistreerd, gebruikt u de opdracht.

```azurecli-interactive
az provider list
```

Als u een resourceprovider `az provider register` wilt registreren, gebruikt u de opdracht en geeft u de *naamruimte* op die u wilt registreren.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Gebruik het als u de ondersteunde locaties en API-versies voor een resourcetype wilt bekijken:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Oplossing 3 - Azure-portal

U de registratiestatus bekijken en een naamruimte van een resourceprovider registreren via de portal.

1. Selecteer **alle services**in de portal .

   ![Alle services selecteren](./media/error-register-resource-provider/select-all-services.png)

1. Selecteer **Abonnementen**.

   ![Abonnementen selecteren](./media/error-register-resource-provider/select-subscriptions.png)

1. Selecteer in de lijst met abonnementen het abonnement dat u wilt gebruiken voor het registreren van de resourceprovider.

   ![Abonnement selecteren om resourceprovider te registreren](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Selecteer **Resourceproviders**voor uw abonnement .

   ![Resourceproviders selecteren](./media/error-register-resource-provider/select-resource-provider.png)

1. Bekijk de lijst met resourceproviders en selecteer indien nodig de koppeling **Register** om de resourceprovider te registreren van het type dat u probeert te implementeren.

   ![Resourceproviders aanbieden](./media/error-register-resource-provider/list-resource-providers.png)
