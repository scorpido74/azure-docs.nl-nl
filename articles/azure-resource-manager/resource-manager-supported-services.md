---
title: Azure-resource providers en resource typen | Microsoft Docs
description: Hierin worden de resource providers beschreven die ondersteuning bieden voor Resource Manager, hun schema's en beschik bare API-versies en de regio's die de resources kunnen hosten.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 2cbc8843d41b760c52b9ca5ccfb6d940bd454136
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164808"
---
# <a name="azure-resource-providers-and-types"></a>Azure-resource providers en-typen

Wanneer u resources implementeert, moet u regel matig informatie over de resource providers en typen ophalen. Als u bijvoorbeeld sleutels en geheimen wilt opslaan, werkt u met de resource provider micro soft.-kluis. Deze resource provider biedt een resource type genaamd kluizen voor het maken van de sleutel kluis.

De naam van een resourcetype heeft deze syntaxis: **{resourceprovider}/{resourcetype}** . Het resourcetype voor een sleutelkluis is **Microsoft.keyvault\vaults**.

In dit artikel leert u het volgende:

* Alle resource providers in azure weer geven
* De registratie status van een resource provider controleren
* Een resource provider registreren
* Resource typen voor een resource provider weer geven
* Geldige locaties voor een resource type weer geven
* Geldige API-versies voor een resource type weer geven

U kunt deze stappen uitvoeren via de Azure Portal, Azure PowerShell of Azure CLI.

Zie [resource providers voor Azure-Services](azure-services-resource-providers.md)voor een lijst waarin resource providers worden toegewezen aan Azure-Services.

## <a name="azure-portal"></a>Azure Portal

Als u alle resource providers en de registratie status voor uw abonnement wilt weer geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**.

    ![abonnementen selecteren](./media/resource-manager-supported-services/select-subscriptions.png)
3. Voer in het vak **alle services** het **abonnement**in en selecteer vervolgens **abonnementen**.
4. Selecteer het abonnement dat u wilt weer geven in de lijst met abonnementen.
5. **Resource providers** selecteren en de lijst met beschik bare resource providers weer geven.

    ![resource providers weer geven](./media/resource-manager-supported-services/show-resource-providers.png)

6. Als u een resource provider registreert, wordt uw abonnement geconfigureerd om te werken met de resource provider. Het bereik voor de registratie is altijd het abonnement. Standaard worden veel resource providers automatisch geregistreerd. Het is echter mogelijk dat u bepaalde resource providers hand matig moet registreren. Als u een resource provider wilt registreren, moet u gemachtigd zijn om `/register/action` de bewerking voor de resource provider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Als u een resource provider wilt registreren, selecteert u **registreren**. In de vorige scherm afbeelding is de koppeling **registreren** gemarkeerd voor **micro soft. blauw druk**.

    U kunt de registratie van een resource provider niet ongedaan maken wanneer u nog steeds resource typen van die resource provider in uw abonnement hebt.

Informatie weer geven voor een bepaalde resource provider:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**.

    ![Alle services selecteren](./media/resource-manager-supported-services/more-services.png)

3. In het vak **alle services** voert u **resource Explorer**in en selecteert u vervolgens **resource Explorer**.
4. Vouw **providers** uit door de pijl-rechts te selecteren.

    ![Providers selecteren](./media/resource-manager-supported-services/select-providers.png)

5. Vouw een resource provider en resource type uit die u wilt weer geven.

    ![Resource type selecteren](./media/resource-manager-supported-services/select-resource-type.png)

6. Resource Manager wordt in alle regio's ondersteund, maar de resources die u implementeert, worden mogelijk niet ondersteund in alle regio's. Daarnaast kunnen er beperkingen gelden voor uw abonnement, waardoor u bepaalde regio's die ondersteuning bieden voor de resource niet kunt gebruiken. In resource Explorer worden geldige locaties voor het bron type weer gegeven.

    ![Locaties weer geven](./media/resource-manager-supported-services/show-locations.png)

7. De API-versie komt overeen met een versie van REST API bewerkingen die door de resource provider worden vrijgegeven. Als resource provider nieuwe functies maakt, wordt een nieuwe versie van de REST API vrijgegeven. In resource Explorer worden geldige API-versies voor het bron type weer gegeven.

    ![API-versies weer geven](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u alle resource providers in Azure en de registratie status voor uw abonnement wilt weer geven, gebruikt u:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Hiermee worden resultaten geretourneerd die vergelijkbaar zijn met:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Als u een resource provider registreert, wordt uw abonnement geconfigureerd om te werken met de resource provider. Het bereik voor de registratie is altijd het abonnement. Standaard worden veel resource providers automatisch geregistreerd. Het is echter mogelijk dat u bepaalde resource providers hand matig moet registreren. Als u een resource provider wilt registreren, moet u gemachtigd zijn om `/register/action` de bewerking voor de resource provider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Hiermee worden resultaten geretourneerd die vergelijkbaar zijn met:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

U kunt de registratie van een resource provider niet ongedaan maken wanneer u nog steeds resource typen van die resource provider in uw abonnement hebt.

Als u informatie wilt weer geven voor een bepaalde resource provider, gebruikt u:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Hiermee worden resultaten geretourneerd die vergelijkbaar zijn met:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Als u de resource typen voor een resource provider wilt zien, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Hiermee wordt het volgende geretourneerd:

```powershell
batchAccounts
operations
locations
locations/quotas
```

De API-versie komt overeen met een versie van REST API bewerkingen die door de resource provider worden vrijgegeven. Als resource provider nieuwe functies maakt, wordt een nieuwe versie van de REST API vrijgegeven.

Als u de beschik bare API-versies voor een resource type wilt ophalen, gebruikt u:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Hiermee wordt het volgende geretourneerd:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager wordt in alle regio's ondersteund, maar de resources die u implementeert, worden mogelijk niet ondersteund in alle regio's. Daarnaast kunnen er beperkingen gelden voor uw abonnement, waardoor u bepaalde regio's die ondersteuning bieden voor de resource niet kunt gebruiken.

Als u de ondersteunde locaties voor een resource type wilt ophalen, gebruikt u.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Hiermee wordt het volgende geretourneerd:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure-CLI

Als u alle resource providers in Azure en de registratie status voor uw abonnement wilt weer geven, gebruikt u:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Hiermee worden resultaten geretourneerd die vergelijkbaar zijn met:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Als u een resource provider registreert, wordt uw abonnement geconfigureerd om te werken met de resource provider. Het bereik voor de registratie is altijd het abonnement. Standaard worden veel resource providers automatisch geregistreerd. Het is echter mogelijk dat u bepaalde resource providers hand matig moet registreren. Als u een resource provider wilt registreren, moet u gemachtigd zijn om `/register/action` de bewerking voor de resource provider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar.

```azurecli
az provider register --namespace Microsoft.Batch
```

Retourneert een bericht dat de registratie is doorlopend.

U kunt de registratie van een resource provider niet ongedaan maken wanneer u nog steeds resource typen van die resource provider in uw abonnement hebt.

Als u informatie wilt weer geven voor een bepaalde resource provider, gebruikt u:

```azurecli
az provider show --namespace Microsoft.Batch
```

Hiermee worden resultaten geretourneerd die vergelijkbaar zijn met:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Als u de resource typen voor een resource provider wilt zien, gebruikt u:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Hiermee wordt het volgende geretourneerd:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

De API-versie komt overeen met een versie van REST API bewerkingen die door de resource provider worden vrijgegeven. Als resource provider nieuwe functies maakt, wordt een nieuwe versie van de REST API vrijgegeven.

Als u de beschik bare API-versies voor een resource type wilt ophalen, gebruikt u:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Hiermee wordt het volgende geretourneerd:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager wordt in alle regio's ondersteund, maar de resources die u implementeert, worden mogelijk niet ondersteund in alle regio's. Daarnaast kunnen er beperkingen gelden voor uw abonnement, waardoor u bepaalde regio's die ondersteuning bieden voor de resource niet kunt gebruiken.

Als u de ondersteunde locaties voor een resource type wilt ophalen, gebruikt u.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Hiermee wordt het volgende geretourneerd:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager sjablonen ontwerpen](resource-group-authoring-templates.md)voor meer informatie over het maken van Resource Manager-sjablonen. 
* Zie [sjabloon verwijzing voor informatie](/azure/templates/)over het weer geven van de sjabloon schema's van de resource provider.
* Zie [resource providers voor Azure-Services](azure-services-resource-providers.md)voor een lijst waarin resource providers worden toegewezen aan Azure-Services.
* Zie [Azure rest API](/rest/api/)om de bewerkingen voor een resource provider weer te geven.
