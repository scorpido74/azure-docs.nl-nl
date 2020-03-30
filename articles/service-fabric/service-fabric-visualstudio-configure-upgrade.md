---
title: De upgrade van een Service Fabric-toepassing configureren
description: Meer informatie over het configureren van de instellingen voor het upgraden van een Service Fabric-toepassing met Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464073"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>De upgrade van een Service Fabric-toepassing configureren in Visual Studio
Visual Studio-hulpprogramma's voor Azure Service Fabric bieden upgradeondersteuning voor publicatie naar lokale of externe clusters. Er zijn drie scenario's waarin u uw toepassing wilt upgraden naar een nieuwere versie in plaats van de toepassing te vervangen tijdens het testen en foutopsporing:

* Toepassingsgegevens gaan niet verloren tijdens de upgrade.
* De beschikbaarheid blijft hoog, dus er zal geen onderbreking van de service zijn tijdens de upgrade, als er voldoende service-exemplaren zijn verspreid over upgradedomeinen.
* Tests kunnen worden uitgevoerd tegen een toepassing terwijl deze wordt bijgewerkt.

## <a name="parameters-needed-to-upgrade"></a>Parameters die nodig zijn om te upgraden
U kiezen uit twee typen implementaties: regelmatig of upgraden. Een reguliere implementatie wist alle eerdere implementatie-informatie en gegevens op het cluster, terwijl een upgrade-implementatie deze behoudt. Wanneer u een Service Fabric-toepassing in Visual Studio upgradet, moet u parameters voor toepassingsverbetering en beleid voor statuscontrole opgeven. Toepassingsupgradeparameters helpen de upgrade te beheren, terwijl het beleid voor statuscontrole bepaalt of de upgrade is geslaagd. Zie [Service Fabric-toepassingsupgrade: upgradeparameters](service-fabric-application-upgrade-parameters.md) voor meer informatie.

Er zijn drie upgrademodi: *Monitored*, *UnmonitoredAuto*en *UnmonitoredManual*.

* Met een bewaakte upgrade wordt de upgrade- en toepassingsstatuscontrole geautomatiseerd.
* Een upgrade van UnmonitoredAuto automatiseert de upgrade, maar slaat de statuscontrole van de toepassing over.
* Wanneer u een upgrade voor UnmonitoredManual uitvoert, moet u elk upgradedomein handmatig upgraden.

Elke upgrademodus vereist verschillende sets parameters. Zie [Upgradeparameters voor toepassingen](service-fabric-application-upgrade-parameters.md) voor meer informatie over de beschikbare upgradeopties.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Een servicefabric-toepassing upgraden in Visual Studio
Als u de tools Visual Studio Service Fabric gebruikt om een Service Fabric-toepassing te upgraden, u een publicatieproces opgeven als een upgrade in plaats van een gewone implementatie door het selectievakje **De toepassing bijwerken** in te schakelen.

### <a name="to-configure-the-upgrade-parameters"></a>De upgradeparameters configureren
1. Klik op de knop **Instellingen** naast het selectievakje. Het dialoogvenster **Upgradeparameters bewerken** wordt weergegeven. Het dialoogvenster **Upgradeparameters bewerken** ondersteunt de upgrademodi Monitored, UnmonitoredAuto en UnmonitoredManual.
2. Selecteer de upgrademodus die u wilt gebruiken en vul vervolgens het parameterraster in.

    Elke parameter heeft standaardwaarden. De optionele parameter *DefaultServiceTypeHealthPolicy* neemt een hashtabelinvoer. Hier is een voorbeeld van de invoerindeling van de hashtabel voor *DefaultServiceTypeHealthPolicy:*

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* is een andere optionele parameter die een hashtabelinvoer in de volgende indeling neemt:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Hier is een real-life voorbeeld:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Als u de upgrademodus UnmonitoredManual selecteert, moet u handmatig een PowerShell-console starten om het upgradeproces voort te zetten en te voltooien. Raadpleeg [de upgrade van de Service Fabric-toepassing: geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md) om te leren hoe handmatige upgrade werkt.

## <a name="upgrade-an-application-by-using-powershell"></a>Een toepassing upgraden met PowerShell
U PowerShell-cmdlets gebruiken om een Service Fabric-toepassing te upgraden. Zie [Zelfstudie voor upgrade van servicefabric-toepassingen](service-fabric-application-upgrade-tutorial.md) en [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) voor gedetailleerde informatie.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Een statuscontrolebeleid opgeven in het manifestbestand van de toepassing
Elke service in een Service Fabric-toepassing kan zijn eigen parameters voor het gezondheidsbeleid hebben die de standaardwaarden overschrijven. U deze parameterwaarden opgeven in het manifestbestand van de toepassing.

In het volgende voorbeeld ziet u hoe u een uniek statuscontrolebeleid toepast voor elke service in het toepassingsmanifest.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Volgende stappen
Zie [Een toepassing upgraden met Visual Studio](service-fabric-application-upgrade-tutorial.md)voor meer informatie over het upgraden van een toepassing.
