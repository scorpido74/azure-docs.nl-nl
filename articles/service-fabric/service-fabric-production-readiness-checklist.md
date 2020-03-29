---
title: Checklist voor gereedheid van Azure Service Fabric-productie
description: Maak uw Service Fabric-toepassing en clusterproductie gereed door best practices te volgen.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376797"
---
# <a name="production-readiness-checklist"></a>Controlelijst voor productiegereedheid

Is uw toepassing en cluster klaar om productieverkeer te genereren? Het uitvoeren en testen van uw toepassing en uw cluster betekent niet noodzakelijkerwijs dat het klaar is om in productie te gaan. Houd uw toepassing en cluster soepel door de volgende checklist door te nemen. We raden aan om al deze items af te vinken. Uiteraard u ervoor kiezen om alternatieve oplossingen te gebruiken voor een bepaald regelitem (bijvoorbeeld uw eigen diagnostische frameworks).


## <a name="prerequisites-for-production"></a>Voorwaarden voor de productie
1. Aanbevolen procedures voor Azure Service Fabric: [Application Design](./service-fabric-best-practices-applications.md), [Security](./service-fabric-best-practices-security.md), [Networking,](./service-fabric-best-practices-networking.md) [Capacity planning and scaling](./service-fabric-best-practices-capacity-scaling.md), Infrastructure as [Code](./service-fabric-best-practices-infrastructure-as-code.md)en Monitoring [and Diagnostics](./service-fabric-best-practices-monitoring.md). 
1. De beveiligingsconfiguratie van Betrouwbare actoren implementeren als u het programmeermodel Actoren gebruikt
1. Voor clusters met meer dan 20 cores of 10 knooppunten maakt u een speciaal primair knooppunttype voor systeemservices. Voeg [plaatsingsbeperkingen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) toe om het primaire knooppunttype voor systeemservices te reserveren.
1. Gebruik een D2v2 of hoger SKU voor het primaire knooppunttype. Het wordt aanbevolen om een SKU te kiezen met een capaciteit van ten minste 50 GB harde schijf.
1. Productieclusters moeten [veilig](service-fabric-cluster-security.md)zijn. Zie deze [clustersjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)voor een voorbeeld van het instellen van een beveiligd cluster. Gebruik algemene namen voor certificaten en vermijd het gebruik van zelfondertekende certs.
1. Voeg [resourcebeperkingen toe aan containers en services,](service-fabric-resource-governance.md)zodat ze niet meer dan 75% van de knooppuntresources verbruiken. 
1. Begrijp en stel het [duurzaamheidsniveau in.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Zilver of een hoger duurzaamheidsniveau wordt aanbevolen voor knooppunttypen met stateful workloads. Het primaire knooppunttype moet een duurzaamheidsniveau hebben dat is ingesteld op Zilver of hoger.
1. Begrijp en kies het [betrouwbaarheidsniveau](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) van het knooppunttype. Zilver of hogere betrouwbaarheid wordt aanbevolen.
1. Load en scale test uw workloads om [capaciteitsvereisten](service-fabric-cluster-capacity.md) voor uw cluster te identificeren. 
1. Uw services en applicaties worden gemonitord en applicatielogboeken worden gegenereerd en opgeslagen, met waarschuwingen. Zie bijvoorbeeld [Logboekregistratie toevoegen aan uw Service Fabric-toepassing](service-fabric-how-to-diagnostics-log.md) en [Monitor-containers met Azure Monitor-logboeken.](service-fabric-diagnostics-oms-containers.md)
1. Het cluster wordt bewaakt met waarschuwingen (bijvoorbeeld met [Azure Monitor-logboeken).](service-fabric-diagnostics-event-analysis-oms.md) 
1. De onderliggende infrastructuur voor virtuele machineschaalwordt bewaakt met waarschuwingen (bijvoorbeeld met [Azure Monitor-logboeken](service-fabric-diagnostics-oms-agent.md).
1. Het cluster heeft altijd [primaire en secundaire certificaten](service-fabric-cluster-security-update-certs-azure.md) (zodat u niet wordt vergrendeld).
1. Onderhoud afzonderlijke clusters voor ontwikkeling, fasering en productie. 
1. [Toepassingsupgrades](service-fabric-application-upgrade.md) en [clusterupgrades](service-fabric-tutorial-upgrade-cluster.md) worden eerst getest in ontwikkelings- en faseringsclusters. 
1. Schakel automatische upgrades in productieclusters uit en schakel deze in voor ontwikkelings- en faseringsclusters (rollback indien nodig). 
1. Stel een Herstelpuntdoelstelling (RPO) op voor uw service en stel een [herstelproces voor noodgevallen](service-fabric-disaster-recovery.md) in en test het uit.
1. Plan om uw cluster handmatig of programmatisch te [schalen.](service-fabric-cluster-scaling.md)
1. Plan voor [het patchen van](service-fabric-patch-orchestration-application.md) uw clusterknooppunten. 
1. Maak een CI/CD-pijplijn zo dat uw laatste wijzigingen voortdurend worden getest. Bijvoorbeeld Azure [DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) of [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) gebruiken
1. Test uw ontwikkeling & staging clusters onder belasting met de [Fault Analysis Service](service-fabric-testability-overview.md) en induceer gecontroleerde [chaos.](service-fabric-controlled-chaos.md) 
1. Plan voor [het schalen van](service-fabric-concepts-scalability.md) uw toepassingen. 


Als u het programmeermodel Service Fabric Reliable Services of Reliable Actors gebruikt, moeten de volgende items worden uitgeschakeld:
1. Upgrade toepassingen tijdens lokale ontwikkeling om te controleren of uw `RunAsync` servicecode het annuleringstoken in de methode eert en aangepaste communicatielisteners sluit.
1. Vermijd [veelvoorkomende valkuilen](service-fabric-work-with-reliable-collections.md) bij het gebruik van betrouwbare collecties.
1. Controleer de .NET CLR-geheugenprestatiemeteritems bij het uitvoeren van belastingstests en controleer op hoge percentages garbage collection of groei van op hol geslagen hoop.
1. Onderhoud offline back-ups van [betrouwbare services en betrouwbare actoren](service-fabric-reliable-services-backup-restore.md) en test het restauratieproces.
1. Het aantal typen primaire nodetypevirtuele machines moet idealiter gelijk zijn aan het minimum voor de betrouwbaarheidslaag clusters; voorwaarden om het niveauminimum te overschrijden, omvat: tijdelijk bij het verticaal schalen van de SKU voor virtuele machinevan ten minste primaire nodetypes.

## <a name="optional-best-practices"></a>Optionele aanbevolen procedures

Hoewel bovenstaande lijsten vereisten zijn om in productie te gaan, moeten ook de volgende punten in aanmerking worden genomen:
1. Sluit aan op het [servicefabric-gezondheidsmodel](service-fabric-health-introduction.md) voor het uitbreiden van de ingebouwde gezondheidsevaluatie en -rapportage.
1. Implementeer een aangepaste waakhond die uw toepassing controleert en [de belasting](service-fabric-cluster-resource-manager-metrics.md) van resources rapporteert voor het balanceren [van resources.](service-fabric-cluster-resource-manager-balancing.md) 


## <a name="next-steps"></a>Volgende stappen
* [Een Windows-cluster voor servicestructuur implementeren](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Een Linux-cluster van servicestructuur implementeren](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.
