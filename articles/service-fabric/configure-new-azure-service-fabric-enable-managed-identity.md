---
title: Azure Service Fabric-een nieuw Azure Service Fabric-cluster implementeren met ondersteuning voor beheerde identiteit | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een nieuw Service Fabric-cluster maakt met beheerde identiteit ingeschakeld
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cb32251c2507f3def7f5e830af15828ff2f04c29
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640724"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Een nieuw Azure Service Fabric-cluster maken met ondersteuning voor beheerde identiteiten (preview-versie)

Als u toegang wilt krijgen tot de functie Managed Identity voor Azure Service Fabric-toepassingen, moet u eerst de service beheerde identiteits token inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService** naast andere systeem services.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **beheerde identiteits token service**in te scha kelen.  

## <a name="enable-the-managed-identity-token-service"></a>De service beheerde identiteits token inschakelen 
U kunt het volgende code fragment in een Azure Resource Manager sjabloon gebruiken om de service beheerde identiteits token in te scha kelen tijdens het maken van het cluster:

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

Als de implementatie mislukt met dit bericht, betekent dit dat het cluster niet op de vereiste Service Fabric versie is (de mini maal ondersteunde runtime is 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```


## <a name="related-articles"></a>Gerelateerde artikelen
* [Ondersteuning voor beheerde identiteiten](./concepts-managed-identity.md) bekijken in azure service Fabric

* [Ondersteuning van beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)