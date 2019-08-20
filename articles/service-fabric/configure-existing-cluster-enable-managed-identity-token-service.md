---
title: 'Azure Service Fabric: een bestaand Azure Service Fabric-cluster configureren om ondersteuning van beheerde identiteiten in te scha kelen | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u een bestaand Azure Service Fabric-cluster configureert om ondersteuning voor beheerde identiteiten in te scha kelen
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 2965376d72cf32c0394d0c493a141bbb1f820ace
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624937"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Een bestaand Azure Service Fabric-cluster configureren om ondersteuning van beheerde identiteiten in te scha kelen (preview-versie)
Als u toegang wilt krijgen tot de functie Managed Identity voor Azure Service Fabric-toepassingen, moet u eerst de **service beheerde identiteits token** inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **beheerde identiteits token service**in te scha kelen.  
> 
> U kunt de Service Fabric versie van een cluster uit de Azure Portal vinden door de cluster bron te openen en de eigenschap **service Fabric versie** te controleren in de sectie Essentials.
> 
> Als het cluster zich in de **hand matige** upgrade modus bevindt, moet u het eerst upgraden naar 6.5.658.9590 of hoger.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>De service beheerde identiteits token inschakelen in een bestaand cluster
Als u de service beheerde identiteits token in een bestaand cluster wilt inschakelen, moet u een cluster upgrade starten die twee wijzigingen specificeert: de beheerde identiteits token service inschakelen en het opnieuw opstarten van elk knoop punt aanvragen. Als u dit wilt doen, voegt u de volgende twee fragmenten toe aan de sjabloon Azure Resource Manager:

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

Om de wijzigingen van kracht te laten worden, moet u ook het upgrade beleid wijzigen om een geforceerde opnieuw op te geven van de Service Fabric runtime op elk knoop punt wanneer de upgrade wordt uitgevoerd via het cluster. Opnieuw opstarten zorgt ervoor dat de nieuw ingeschakelde systeem service wordt gestart en wordt uitgevoerd op elk knoop punt. In het onderstaande `forceRestart` fragment is de essentiële instelling. gebruik uw bestaande waarden voor de overige instellingen.  

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
> Wanneer de upgrade is voltooid, vergeet dan niet om de `forceRestart` instelling terug te draaien om de impact van de volgende upgrades te minimaliseren. 

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

## <a name="related-articles"></a>Gerelateerde artikelen
* [Ondersteuning voor beheerde identiteiten](./concepts-managed-identity.md) bekijken in azure service Fabric

* [Ondersteuning van beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
