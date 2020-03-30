---
title: Beheerde identiteitsondersteuning configureren voor een nieuw cluster van Servicefabric
description: Zo u ondersteuning voor beheerde identiteiten inschakelen in een nieuw Azure Service Fabric-cluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930465"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Beheerde identiteitsondersteuning configureren voor een nieuw cluster van Servicefabric (voorbeeld)

Als u [Beheerde identiteiten wilt gebruiken voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in uw Service Fabric-toepassingen, schakelt u eerst de Managed Identity Token *Service* in op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric-toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangstokens namens hen. Zodra de service is ingeschakeld, u deze zien in Service Fabric Explorer onder de sectie **Systeem** in het linkerdeelvenster, uitgevoerd onder de naam **fabric:/System/ManagedIdentityTokenService** naast andere systeemservices.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **Managed Identity Token Service**in te schakelen.  

## <a name="enable-the-managed-identity-token-service"></a>De beheerde identiteitstokenservice inschakelen

Als u de Managed Identity Token Service wilt inschakelen op het moment van het maken van een cluster, voegt u het volgende fragment toe aan de sjabloon Azure Resource Manager van het cluster:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Fouten

Als de implementatie mislukt met dit bericht, betekent dit dat het cluster zich niet op de vereiste Service Fabric-versie bevindt (de minimale ondersteunde runtime is 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Verwante artikelen

* [Beheerde identiteitsondersteuning controleren](./concepts-managed-identity.md) in Azure Service Fabric

* [Beheerde identiteitsondersteuning inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Service Fabric-toepassing implementeren met een beheerde identiteit met systeemtoegewezen](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Gebruik maken van de beheerde identiteit van een Service Fabric-toepassing van servicecode](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-bronnen](./how-to-grant-access-other-resources.md)