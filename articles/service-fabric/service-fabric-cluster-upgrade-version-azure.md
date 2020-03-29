---
title: De Azure Service Fabric-versie van een cluster upgraden
description: Upgrade de servicefabriccode en/of -configuratie waarop een Cluster servicestructuur wordt uitgevoerd, inclusief het instellen van de clusterupdatemodus, het upgraden van certificaten, het toevoegen van toepassingspoorten, het uitvoeren van OS-patches, enzovoort. Wat u verwachten wanneer de upgrades worden uitgevoerd?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: c3ffcbd4296385623ff5e2c1ee001c27598ff3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451811"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Upgrade uitvoeren voor de Service Fabric-versie van een cluster

Voor elk modern systeem, het ontwerpen voor upgradability is de sleutel tot het bereiken van succes op lange termijn van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent, maar die gedeeltelijk door Microsoft wordt beheerd. In dit artikel wordt beschreven hoe u de versie van Service Fabric upgraden die in uw Azure-cluster wordt uitgevoerd.

U instellen dat uw cluster automatische fabric-upgrades ontvangt, omdat deze door Microsoft worden uitgebracht of u een ondersteunde fabricversie selecteren waarop uw cluster moet worden ingeschakeld.

Dit doe je door de clusterconfiguratie 'upgradeMode' in te stellen op de portal of door Resource Manager te gebruiken op het moment van maken of later op een live cluster 

> [!NOTE]
> Zorg ervoor dat uw cluster altijd een ondersteunde fabric-versie draait. Als en wanneer we de release van een nieuwe versie van de service fabric aankondigen, is de vorige versie gemarkeerd voor het einde van de ondersteuning na een minimum van 60 dagen vanaf die datum. De nieuwe releases worden aangekondigd [op de service fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric/). De nieuwe release is beschikbaar om vervolgens te kiezen. 
> 
> 

14 dagen voor het verstrijken van de release wordt uw cluster uitgevoerd, wordt een statusgebeurtenis gegenereerd die uw cluster in een waarschuwingsstatus plaatst. Het cluster blijft in een waarschuwingsstatus totdat u een upgrade uitvoert naar een ondersteunde fabricversie.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>De upgrademodus instellen in de Azure-portal
U het cluster automatisch of handmatig instellen wanneer u het cluster maakt.

![Create_Manualmode][Create_Manualmode]

U het cluster automatisch of handmatig instellen wanneer u zich op een live cluster bevindt, met behulp van de beheerervaring. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgraden naar een nieuwe versie op een cluster die is ingesteld op handmatige modus via portal.
Als u wilt upgraden naar een nieuwe versie, hoeft u alleen de beschikbare versie te selecteren in de vervolgkeuzelijst en op te slaan. De Fabric-upgrade wordt automatisch gestart. Het clusterstatusbeleid (een combinatie van knooppuntstatus en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade nageleefd.

Als niet aan het clusterstatusbeleid is voldaan, wordt de upgrade teruggedraaid. Blader naar beneden in dit document om meer te lezen over het instellen van dit aangepaste gezondheidsbeleid. 

Zodra u de problemen hebt opgelost die hebben geleid tot de terugdraaiing, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>De upgrademodus instellen met een sjabloon Resourcemanager
Voeg de configuratie 'upgradeModus' toe aan de resourcedefinitie van Microsoft.ServiceFabric/clusters en stel de 'clusterCodeVersion' in op een van de ondersteunde fabricversies zoals hieronder wordt weergegeven en implementeer de sjabloon. De geldige waarden voor "upgradeMode" zijn "Manual" of "Automatic"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgraden naar een nieuwe versie op een cluster die is ingesteld op handmatige modus via een Resource Manager-sjabloon.
Wanneer het cluster zich in de handmatige modus bevindt, wijzigt u de 'clusterCodeVersie' in een ondersteunde versie en implementeert u deze. De implementatie van de sjabloon, kicks van de Fabric-upgrade wordt automatisch afgetrapt. Het clusterstatusbeleid (een combinatie van knooppuntstatus en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade nageleefd.

Als niet aan het clusterstatusbeleid is voldaan, wordt de upgrade teruggedraaid.  

Zodra u de problemen hebt opgelost die hebben geleid tot de terugdraaiing, moet u de upgrade opnieuw starten door dezelfde stappen te volgen als voorheen.

## <a name="set-custom-health-polices-for-upgrades"></a>Aangepaste gezondheidspolitie instellen voor upgrades
U aangepaste gezondheidspolitie's opgeven voor een upgrade van de stof. Als u uw cluster hebt ingesteld op automatische fabric-upgrades, wordt dit beleid toegepast op fase [1 van de automatische fabric-upgrades.](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)
Als u uw cluster hebt ingesteld voor handmatige fabric-upgrades, worden deze beleidsregels toegepast telkens wanneer u een nieuwe versie selecteert die het systeem activeert om de fabric-upgrade in uw cluster te starten. Als u het beleid niet overschrijft, worden de standaardinstellingen gebruikt.

U het aangepaste gezondheidsbeleid opgeven of de huidige instellingen onder het hoofd van de stofupgrade bekijken door de geavanceerde upgrade-instellingen te selecteren. Bekijk de volgende afbeelding over hoe. 

![Aangepaste statusbeleidsregels beheren][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Alle beschikbare versies voor alle omgevingen voor een bepaald abonnement weergeven
Voer de volgende opdracht uit en u moet een uitvoer krijgen die vergelijkbaar is met deze.

"supportExpiryUtc" vertelt uw wanneer een bepaalde release verloopt of is verlopen. De laatste release heeft geen geldige datum - het heeft een waarde van "9999-12-31T23:59:59.9999999", wat alleen betekent dat de vervaldatum nog niet is vastgesteld.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

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
