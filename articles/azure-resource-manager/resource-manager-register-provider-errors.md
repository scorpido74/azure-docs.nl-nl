---
title: Registratie fouten van resource provider
description: Hierin wordt beschreven hoe u registratie fouten van Azure-resource providers oplost bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: 96595bab9d0db189911cac4fc1b42c722c2c1515
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150506"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Los fouten voor de registratie van de resourceprovider

Dit artikel beschrijft de fouten die optreden kunnen bij het gebruik van een resourceprovider die u hebt nog niet eerder in uw abonnement gebruikt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Als u resources implementeert, krijgt u mogelijk de volgende code en het bericht:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Of een soortgelijk bericht waarin wordt vermeld:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Het foutbericht geeft suggesties voor de ondersteunde locaties en API-versies. U kunt de sjabloon wijzigen in een van de voorgestelde waarden. De meeste providers zijn geregistreerde automatisch door de Azure-portal of de opdrachtregelinterface die u gebruikt, maar niet alle. Als u een bepaalde resourceprovider voordat u dit nog niet hebt gebruikt, moet u wellicht dat de provider registreren.

Als u automatisch afsluiten uitschakelt voor virtuele machines, wordt er mogelijk een fout bericht met de volgende strekking weer gegeven:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Oorzaak

U ontvangt deze fout berichten om een van de volgende redenen:

* De vereiste resource provider is niet geregistreerd voor uw abonnement
* API-versie niet ondersteund voor het resourcetype
* Locatie niet ondersteund voor het resourcetype
* Als u virtuele machines automatisch wilt afsluiten, moet u de resource provider micro soft. DevTestLab registreren.

## <a name="solution-1---powershell"></a>Oplossing 1: PowerShell

Voor Power shell gebruikt u **Get-AzResourceProvider** om de registratie status te bekijken.

```powershell
Get-AzResourceProvider -ListAvailable
```

Als u een provider wilt registreren, gebruikt u **REGI ster-AzResourceProvider** en geeft u de naam op van de resource provider die u wilt registreren.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Voor de ondersteunde locaties voor een bepaald type resource, gebruikt u:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Voor de ondersteunde API-versies voor een bepaald type resource, gebruikt u:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Oplossing 2 - Azure CLI

Als u wilt zien of de provider is geregistreerd, gebruikt u de `az provider list` opdracht.

```azurecli-interactive
az provider list
```

Gebruik voor het registreren van een resourceprovider de `az provider register` opdracht en geeft u de *naamruimte* om u te registreren.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Als u wilt zien van de ondersteunde locaties en API-versies voor een resourcetype, gebruikt u:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Oplossing 3 - Azure portal

U kunt de status van inschrijving en registreren van een naamruimte van de resource provider via de portal.

1. Selecteer in de portal **alle services**.

   ![Alle services selecteren](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Selecteer **Abonnementen**.

   ![Abonnementen selecteren](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Selecteer het abonnement dat u gebruiken wilt voor het registreren van de resourceprovider in de lijst met abonnementen.

   ![Abonnement selecteren om resourceprovider te registreren](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Voor uw abonnement en selecteer **resourceproviders**.

   ![Resourceproviders selecteren](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Bekijk de lijst met resourceproviders en selecteer indien nodig, de **registreren** koppeling voor het registreren van de resourceprovider van het type dat u probeert te implementeren.

   ![Lijst met resourceproviders](./media/resource-manager-register-provider-errors/list-resource-providers.png)
