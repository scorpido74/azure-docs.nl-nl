---
title: Beheerde identiteitsondersteuning configureren in een bestaand cluster van Servicefabric
description: Zo u ondersteuning voor beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 73c890e960f26b8e0e3fa924d9ff6b7a4cd4a4dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415694"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Beheerde identiteitsondersteuning configureren in een bestaand cluster van Servicefabric

Als u [Beheerde identiteiten wilt gebruiken voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in uw Service Fabric-toepassingen, schakelt u eerst de Managed Identity Token *Service* in op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric-toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangstokens namens hen. Zodra de service is ingeschakeld, u deze zien in Service Fabric Explorer onder de sectie **Systeem** in het linkerdeelvenster, uitgevoerd onder de naam **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **Managed Identity Token Service**in te schakelen.  
>
> U de Service Fabric-versie van een cluster vinden vanuit de Azure-portal door de clusterbron te openen en de eigenschap **Service Fabric-versie** in de sectie **Essentials te** controleren.
>
> Als het cluster de **handmatige** upgrademodus heeft, moet u het eerst upgraden naar 6.5.658.9590 of hoger.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>*Managed Identity Token Service* inschakelen in een bestaand cluster

Als u de Managed Identity Token Service in een bestaand cluster wilt inschakelen, moet u een clusterupgrade starten met twee wijzigingen: (1) De Managed Identity Token Service inschakelen en (2) het aanvragen van een herstart van elk knooppunt. Voeg eerst het volgende fragment toe met de sjabloon cluster Azure Resource Manager:

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

Als u wilt dat de wijzigingen van kracht worden, moet u ook het upgradebeleid wijzigen om een krachtige herstart van de runtime van de ServiceFabric op elk knooppunt op te geven naarmate de upgrade door het cluster vordert. Deze herstart zorgt ervoor dat de nieuw ingeschakelde systeemservice wordt gestart en op elk knooppunt wordt uitgevoerd. In het onderstaande fragment `forceRestart` is de essentiële instelling om opnieuw opstarten mogelijk te maken. Gebruik voor de overige parameters onderstaande waarden of gebruik bestaande aangepaste waarden die al zijn opgegeven voor de clusterbron. Aangepaste instellingen voor fabric-upgradebeleid ('upgradeBeschrijving') kunnen worden bekeken vanuit Azure Portal door de optie 'Fabric-upgrades' te selecteren op de bron van de ServiceFabric of resources.azure.com. Standaardopties voor het upgradebeleid ('upgradeBeschrijving') zijn niet zichtbaar vanaf powershell of resources.azure.com. Zie [ClusterUpgradePolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) voor meer informatie.  

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
> Vergeet na de succesvolle voltooiing van de upgrade `forceRestart` niet om de instelling terug te draaien, om de impact van latere upgrades te minimaliseren. 

## <a name="errors-and-troubleshooting"></a>Fouten en probleemoplossing

Als de implementatie mislukt met het volgende bericht, betekent dit dat het cluster niet wordt uitgevoerd op een voldoende servicefabric-versie:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een beheerde identiteit met systeemtoegewezen](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Gebruik maken van de beheerde identiteit van een Service Fabric-toepassing van servicecode](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-bronnen](./how-to-grant-access-other-resources.md)
