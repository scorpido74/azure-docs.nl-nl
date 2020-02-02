---
title: Ondersteuning voor beheerde identiteiten configureren in een bestaand Service Fabric cluster
description: U kunt als volgt ondersteuning voor beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934949"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Ondersteuning voor beheerde identiteiten configureren in een bestaand Service Fabric cluster (preview-versie)

Als u [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in uw service Fabric-toepassingen wilt gebruiken, moet u eerst de *service beheerde identiteits token* inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **beheerde identiteits token service**in te scha kelen.  
>
> U kunt de Service Fabric versie van een cluster uit de Azure Portal vinden door de cluster bron te openen en de eigenschap **service Fabric versie** te controleren in de sectie **Essentials** .
>
> Als het cluster zich in de **hand matige** upgrade modus bevindt, moet u het eerst upgraden naar 6.5.658.9590 of hoger.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>*Beheerde identiteits token service* inschakelen in een bestaand cluster

Als u de service beheerde identiteits token in een bestaand cluster wilt inschakelen, moet u een cluster upgrade initiëren die twee wijzigingen specificeert: (1) het inschakelen van de beheerde identiteits token service, en (2) het aanvragen van het opnieuw opstarten van elk knoop punt. Voeg eerst het volgende fragment toe uw cluster Azure Resource Manager sjabloon:

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

Om de wijzigingen van kracht te laten worden, moet u ook het upgrade beleid wijzigen om een geforceerde opnieuw op te geven van de Service Fabric runtime op elk knoop punt wanneer de upgrade wordt uitgevoerd via het cluster. Opnieuw opstarten zorgt ervoor dat de nieuw ingeschakelde systeem service wordt gestart en wordt uitgevoerd op elk knoop punt. In het onderstaande code fragment is `forceRestart` de essentiële instelling. Gebruik uw bestaande waarden voor de overige instellingen.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Wanneer de upgrade is voltooid, moet u niet verg eten de `forceRestart`-instelling terug te zetten om de impact van de volgende upgrades te minimaliseren. 

## <a name="errors-and-troubleshooting"></a>Fouten en probleem oplossing

Als de implementatie mislukt met het volgende bericht, betekent dit dat het cluster niet wordt uitgevoerd op een hoge voldoende Service Fabric-versie:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)