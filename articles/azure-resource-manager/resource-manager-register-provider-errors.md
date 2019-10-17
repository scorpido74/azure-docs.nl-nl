---
title: Registratie fouten van de Azure-resource provider | Microsoft Docs
description: Hierin wordt beschreven hoe u registratie fouten van Azure-resource providers oplost bij het implementeren van resources met Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: fcdcfdfe736f29f18ea2dc240a66fd7fa6bc404b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390261"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Fouten voor de registratie van de resource provider oplossen

In dit artikel worden de fouten beschreven die u kunt tegen komen wanneer u een resource provider gebruikt die u nog niet eerder hebt gebruikt in uw abonnement.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Bij het implementeren van een resource kunnen de volgende fout code en dit bericht worden weer gegeven:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Het is ook mogelijk dat er een vergelijkbaar bericht wordt weer gegeven waarin staat dat:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Het fout bericht geeft u suggesties voor de ondersteunde locaties en API-versies. U kunt uw sjabloon wijzigen in een van de voorgestelde waarden. De meeste providers worden automatisch geregistreerd door de Azure Portal of de opdracht regel interface die u gebruikt, maar niet alle. Als u een bepaalde resource provider nog niet eerder hebt gebruikt, moet u deze provider mogelijk registreren.

Als u automatisch afsluiten uitschakelt voor virtuele machines, wordt er mogelijk een fout bericht met de volgende strekking weer gegeven:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Oorzaak

U ontvangt deze fout berichten om een van de volgende redenen:

* De vereiste resource provider is niet geregistreerd voor uw abonnement
* De API-versie wordt niet ondersteund voor het resource type
* De locatie wordt niet ondersteund voor het resource type
* Als u virtuele machines automatisch wilt afsluiten, moet u de resource provider micro soft. DevTestLab registreren.

## <a name="solution-1---powershell"></a>Oplossing 1-Power shell

Voor Power shell gebruikt u **Get-AzResourceProvider** om de registratie status te bekijken.

```powershell
Get-AzResourceProvider -ListAvailable
```

Als u een provider wilt registreren, gebruikt u **REGI ster-AzResourceProvider** en geeft u de naam op van de resource provider die u wilt registreren.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Als u de ondersteunde locaties voor een bepaald type resource wilt ophalen, gebruikt u:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Als u de ondersteunde API-versies voor een bepaald type resource wilt ophalen, gebruikt u:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Oplossing 2-Azure CLI

Als u wilt controleren of de provider is geregistreerd, gebruikt u de `az provider list` opdracht.

```azurecli-interactive
az provider list
```

Als u een resource provider wilt registreren, gebruikt u de `az provider register` opdracht en geeft u de *naam ruimte* op die u wilt registreren.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Als u de ondersteunde locaties en API-versies voor een resource type wilt zien, gebruikt u:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Oplossing 3-Azure Portal

U kunt de registratie status bekijken en een resource provider-naam ruimte registreren via de portal.

1. Selecteer in de portal **alle services**.

   ![Alle services selecteren](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Selecteer **Abonnementen**.

   ![Abonnementen selecteren](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Selecteer in de lijst met abonnementen het abonnement dat u wilt gebruiken voor de registratie van de resource provider.

   ![Selecteer een abonnement om de resource provider te registreren](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Selecteer **resource providers**voor uw abonnement.

   ![Resource providers selecteren](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Bekijk de lijst met resource providers en selecteer zo nodig de **registratie** koppeling om de resource provider te registreren van het type dat u wilt implementeren.

   ![Resource providers weer geven](./media/resource-manager-register-provider-errors/list-resource-providers.png)
