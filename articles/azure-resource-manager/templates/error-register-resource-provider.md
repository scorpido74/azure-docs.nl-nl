---
title: Registratie fouten van resource provider
description: Hierin wordt beschreven hoe u registratie fouten van Azure-resource providers oplost bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273772"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Los fouten voor de registratie van de resourceprovider

Dit artikel beschrijft de fouten die optreden kunnen bij het gebruik van een resourceprovider die u hebt nog niet eerder in uw abonnement gebruikt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

Als u wilt controleren of de provider is geregistreerd, gebruikt u de opdracht `az provider list`.

```azurecli-interactive
az provider list
```

Als u een resource provider wilt registreren, gebruikt u de opdracht `az provider register` en geeft u de *naam ruimte* op die u wilt registreren.

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

   ![Alle services selecteren](./media/error-register-resource-provider/select-all-services.png)

1. Selecteer **Abonnementen**.

   ![Abonnementen selecteren](./media/error-register-resource-provider/select-subscriptions.png)

1. Selecteer het abonnement dat u gebruiken wilt voor het registreren van de resourceprovider in de lijst met abonnementen.

   ![Abonnement selecteren om resourceprovider te registreren](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Selecteer **resource providers**voor uw abonnement.

   ![Resourceproviders selecteren](./media/error-register-resource-provider/select-resource-provider.png)

1. Bekijk de lijst met resource providers en selecteer zo nodig de **registratie** koppeling om de resource provider te registreren van het type dat u wilt implementeren.

   ![Lijst met resourceproviders](./media/error-register-resource-provider/list-resource-providers.png)
