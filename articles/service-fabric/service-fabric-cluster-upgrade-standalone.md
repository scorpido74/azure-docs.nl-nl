---
title: Een zelfstandig Azure Service Fabric-cluster upgraden
description: Meer informatie over het upgraden van de versie of configuratie van een zelfstandig Azure Service Fabric-cluster.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 6da9b4c6890895141ecc419382f05f667614fb31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451826"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Een zelfstandig cluster voor ServiceFabric upgraden en bijwerken

Voor elk modern systeem, het ontwerpen voor upgradability is de sleutel tot het bereiken van succes op lange termijn van uw product. Een zelfstandig cluster azure service fabric is een bron waarvan u eigenaar bent. In dit artikel wordt beschreven wat kan worden bijgewerkt of bijgewerkt.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>De fabricversie beheren die op uw cluster wordt uitgevoerd
Zorg ervoor dat uw cluster altijd een [ondersteunde Service Fabric-versie](service-fabric-versions.md)uitvoert. Wanneer Microsoft de release van een nieuwe versie van Service Fabric aankondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na een minimum van 60 dagen vanaf de datum van de aankondiging. Nieuwe releases worden aangekondigd [op de Service Fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric/). De nieuwe release is beschikbaar om te kiezen op dat punt.

U instellen dat uw cluster automatische fabric-upgrades ontvangt, omdat deze door Microsoft worden uitgebracht of u handmatig een ondersteunde fabricversie selecteren waarop uw cluster moet worden ingeschakeld. Lees Voor meer informatie [de versie Van de Servicefabric die op uw cluster wordt uitgevoerd, bijwerken.](service-fabric-cluster-upgrade-windows-server.md)

## <a name="customize-configuration-settings"></a>Configuratie-instellingen aanpassen

Er kunnen veel verschillende [configuratie-instellingen](service-fabric-cluster-manifest.md) worden ingesteld in het *clusterconfig.json-bestand,* zoals het betrouwbaarheidsniveau van het cluster en de eigenschappen van het knooppunt.  Lees Voor meer informatie [de configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md).  Veel andere, meer geavanceerde, instellingen kunnen ook worden aangepast.  Lees voor meer informatie de [instellingen van de clusterstructuur service](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Knooppunteigenschappen definiëren
Soms wilt u er soms voor zorgen dat bepaalde workloads alleen worden uitgevoerd op bepaalde typen knooppunten in het cluster. Voor sommige workloads kunnen bijvoorbeeld GPU's of SSD's nodig zijn, terwijl andere dat niet mogelijk zijn. Voor elk van de knooppunttypen in een cluster u aangepaste knooppunteigenschappen toevoegen aan clusterknooppunten. Plaatsingsbeperkingen zijn de instructies die zijn gekoppeld aan afzonderlijke services die selecteren voor een of meer knooppunteigenschappen. Plaatsingsbeperkingen bepalen waar services moeten worden uitgevoerd.

Lees [knooppunteigenschappen en plaatsingsbeperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)voor meer informatie over het gebruik van plaatsingsbeperkingen, knooppunteigenschappen en hoe u deze definiëren.
 

## <a name="add-capacity-metrics"></a>Capaciteitsstatistieken toevoegen
Voor elk van de knooppunttypen u aangepaste capaciteitsstatistieken toevoegen die u in uw toepassingen wilt gebruiken om belasting te rapporteren. Raadpleeg de documenten van Service Fabric Cluster Resource Manager Over het [beschrijven van uw cluster](service-fabric-cluster-resource-manager-cluster-description.md) en metrische gegevens [en belasting](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over het gebruik van capaciteitsstatistieken voor het melden van belasting.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Het besturingssysteem in de clusterknooppunten patchen
De patch orchestration-toepassing (POA) is een Service Fabric-toepassing die patching van het besturingssysteem op een Service Fabric-cluster automatiseert zonder downtime. De [Patch Orchestration-toepassing voor Windows](service-fabric-patch-orchestration-application.md) kan op uw cluster worden geïmplementeerd om patches op een georkestreerde manier te installeren, terwijl de services altijd beschikbaar blijven. 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het aanpassen van enkele instellingen voor [clusterfabricen](service-fabric-cluster-fabric-settings.md) voor servicefabric
* Meer informatie over het [schalen van uw cluster in en uit](service-fabric-cluster-scale-up-down.md)
* Meer informatie over [toepassingsupgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
