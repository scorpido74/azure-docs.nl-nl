---
title: Een Azure Service Fabric-cluster upgraden
description: 'Meer informatie over het upgraden van de versie of configuratie van een Azure Service Fabric-cluster: clusterupdatemodus instellen, certificaten upgraden, toepassingspoorten toevoegen, OS-patches uitvoeren en wat u verwachten wanneer de upgrades worden uitgevoerd.'
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: 6897854820339fc78dd9083c82147dce95ab68b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258653"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Een Azure Service Fabric-cluster upgraden en bijwerken

Voor elk modern systeem, het ontwerpen voor upgradability is de sleutel tot het bereiken van succes op lange termijn van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent, maar die gedeeltelijk door Microsoft wordt beheerd. In dit artikel wordt beschreven wat automatisch wordt beheerd en wat u zelf configureren.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>De fabricversie beheren die op uw cluster wordt uitgevoerd

Zorg ervoor dat uw cluster altijd een [ondersteunde fabric-versie](service-fabric-versions.md)uitvoert. Elke keer dat Microsoft de release van een nieuwe versie van Service Fabric aankondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na een minimum van 60 dagen vanaf die datum. Nieuwe releases worden aangekondigd op de [Service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dagen voor het verstrijken van de release wordt uw cluster uitgevoerd, wordt een statusgebeurtenis gegenereerd die uw cluster in een waarschuwingsstatus plaatst. Het cluster blijft in een waarschuwingsstatus totdat u een upgrade uitvoert naar een ondersteunde fabricversie.

U instellen dat uw cluster automatische fabric-upgrades ontvangt, omdat deze door Microsoft worden uitgebracht of u een ondersteunde fabricversie selecteren waarop uw cluster moet worden ingeschakeld.  Lees voor meer informatie [de servicefabric-versie van uw cluster](service-fabric-cluster-upgrade-version-azure.md)bijwerken.

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Het upgradegedrag van stoffen tijdens automatische upgrades

Microsoft onderhoudt de fabriccode en -configuratie die in een Azure-cluster wordt uitgevoerd. We voeren automatisch bewaakte upgrades uit naar de software op een zo nodig basis. Deze upgrades kunnen code, configuratie of beide zijn. Om ervoor te zorgen dat uw toepassing geen of minimale impact ondervindt als gevolg van deze upgrades, worden upgrades uitgevoerd in de volgende fasen:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Een upgrade wordt uitgevoerd met alle clusterstatusbeleidsregels

Tijdens deze fase gaan de upgrades één upgradedomein tegelijk door en blijven de toepassingen die in het cluster werden uitgevoerd zonder downtime. Het clusterstatusbeleid (voor de status van knooppunt en de toepassingsstatus) wordt tijdens de upgrade nageleefd.

Als niet aan het clusterstatusbeleid is voldaan, wordt de upgrade teruggedraaid en wordt er een e-mail verzonden naar de eigenaar van het abonnement. De e-mail bevat de volgende informatie:

* Melding dat we een clusterupgrade moesten terugdraaien.
* Voorgestelde corrigerende acties, indien van toepassing.
* Het aantal dagen *(n)* totdat we fase 2 uitvoeren.

We proberen dezelfde upgrade nog een paar keer uit te voeren voor het geval upgrades om infrastructuurredenen mislukten. Na de *n* dagen vanaf de datum waarop de e-mail is verzonden, gaan we door naar fase 2.

Als aan het clusterstatusbeleid is voldaan, wordt de upgrade als geslaagd beschouwd en gemarkeerd als voltooid. Dit kan gebeuren tijdens de eerste upgrade of een van de upgrade herhalingen in deze fase. Er is geen e-mailbevestiging van een geslaagde run. Dit is om te voorkomen dat u te veel e-mails stuurt; het ontvangen van een e-mail moet worden gezien als een uitzondering op normaal. We verwachten dat de meeste clusterupgrades zullen slagen zonder dat dit gevolgen heeft voor de beschikbaarheid van uw toepassing.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Een upgrade wordt uitgevoerd door alleen standaardstatusbeleid te gebruiken

Het gezondheidsbeleid in deze fase is zo ingesteld dat het aantal toepassingen dat aan het begin van de upgrade in orde was, voor de duur van het upgradeproces gelijk blijft. Net als in fase 1 gaan de Fase 2-upgrades één upgradedomein tegelijk uit en blijven de toepassingen die in het cluster werden uitgevoerd zonder downtime. Het clusterstatusbeleid (een combinatie van knooppuntstatus en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt gedurende de duur van de upgrade nageleefd.

Als niet aan het in feite geldende clusterstatusbeleid is voldaan, wordt de upgrade teruggedraaid. Vervolgens wordt een e-mail naar de eigenaar van het abonnement gestuurd. De e-mail bevat de volgende informatie:

* Melding dat we een clusterupgrade moesten terugdraaien.
* Voorgestelde corrigerende acties, indien van toepassing.
* Het aantal dagen (n) tot we fase 3 uitvoeren.

We proberen dezelfde upgrade nog een paar keer uit te voeren voor het geval upgrades om infrastructuurredenen mislukten. Een herinneringse-mail wordt een paar dagen voor het begin van de dag verzonden. Na de n dagen vanaf de datum waarop de e-mail is verzonden, gaan we door naar fase 3. De e-mails die we u in fase 2 sturen, moeten serieus worden genomen en er moeten herstelmaatregelen worden genomen.

Als aan het clusterstatusbeleid is voldaan, wordt de upgrade als geslaagd beschouwd en gemarkeerd als voltooid. Dit kan gebeuren tijdens de eerste upgrade of een van de upgrade herhalingen in deze fase. Er is geen e-mailbevestiging van een geslaagde run.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Een upgrade wordt uitgevoerd met behulp van agressief gezondheidsbeleid

Deze gezondheidsbeleid in deze fase zijn gericht op de voltooiing van de upgrade in plaats van de gezondheid van de toepassingen. Weinig cluster upgrades eindigen in deze fase. Als uw cluster deze fase haalt, is de kans groot dat uw toepassing ongezond wordt en/of de beschikbaarheid verliest.

Net als bij de andere twee fasen, fase 3 upgrades gaan een upgrade domein op een moment.

Als niet aan het clusterstatusbeleid is voldaan, wordt de upgrade teruggedraaid. We proberen dezelfde upgrade nog een paar keer uit te voeren voor het geval upgrades om infrastructuurredenen mislukten. Daarna wordt het cluster vastgemaakt, zodat het geen ondersteuning en/of upgrades meer ontvangt.

Een e-mail met deze informatie wordt verzonden naar de eigenaar van het abonnement, samen met de corrigerende acties. We verwachten niet dat clusters in een toestand komen waarin fase 3 is mislukt.

Als aan het clusterstatusbeleid is voldaan, wordt de upgrade als geslaagd beschouwd en gemarkeerd als voltooid. Dit kan gebeuren tijdens de eerste upgrade of een van de upgrade herhalingen in deze fase. Er is geen e-mailbevestiging van een geslaagde run.

## <a name="manage-certificates"></a>Certificaten beheren

Service Fabric maakt gebruik van [X.509-servercertificaten](service-fabric-cluster-security.md) die u opgeeft wanneer u een cluster maakt om de communicatie tussen clusterknooppunten te beveiligen en clients te verifiëren. U certificaten voor het cluster en de client toevoegen, bijwerken of verwijderen in de [Azure-portal](https://portal.azure.com) of met PowerShell/Azure CLI.  Lees certificaten [toevoegen of verwijderen](service-fabric-cluster-security-update-certs-azure.md) voor meer informatie

## <a name="open-application-ports"></a>Toepassingspoorten openen

U toepassingspoorten wijzigen door de resourceeigenschappen Load Balancer te wijzigen die zijn gekoppeld aan het knooppunttype. U de Azure-portal gebruiken of U PowerShell/Azure CLI gebruiken. Lees [Toepassingspoorten openen voor een cluster voor](create-load-balancer-rule.md)meer informatie.

## <a name="define-node-properties"></a>Knooppunteigenschappen definiëren

Soms wilt u er soms voor zorgen dat bepaalde workloads alleen worden uitgevoerd op bepaalde typen knooppunten in het cluster. Voor sommige workloads kunnen bijvoorbeeld GPU's of SSD's nodig zijn, terwijl andere dat niet mogelijk zijn. Voor elk van de knooppunttypen in een cluster u aangepaste knooppunteigenschappen toevoegen aan clusterknooppunten. Plaatsingsbeperkingen zijn de instructies die zijn gekoppeld aan afzonderlijke services die selecteren voor een of meer knooppunteigenschappen. Plaatsingsbeperkingen bepalen waar services moeten worden uitgevoerd.

Lees [knooppunteigenschappen en plaatsingsbeperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)voor meer informatie over het gebruik van plaatsingsbeperkingen, knooppunteigenschappen en hoe u deze definiëren.

## <a name="add-capacity-metrics"></a>Capaciteitsstatistieken toevoegen

Voor elk van de knooppunttypen u aangepaste capaciteitsstatistieken toevoegen die u in uw toepassingen wilt gebruiken om belasting te rapporteren. Raadpleeg de documenten van Service Fabric Cluster Resource Manager Over het [beschrijven van uw cluster](service-fabric-cluster-resource-manager-cluster-description.md) en metrische gegevens [en belasting](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over het gebruik van capaciteitsstatistieken voor het melden van belasting.

## <a name="set-health-policies-for-automatic-upgrades"></a>Gezondheidsbeleid instellen voor automatische upgrades

U aangepaste gezondheidsbeleidsregels opgeven voor de upgrade van de stof. Als u uw cluster hebt ingesteld op automatische fabric-upgrades, wordt dit beleid toegepast op fase 1 van de automatische fabric-upgrades.
Als u uw cluster hebt ingesteld voor handmatige fabric-upgrades, worden deze beleidsregels toegepast telkens wanneer u een nieuwe versie selecteert die het systeem activeert om de fabric-upgrade in uw cluster te starten. Als u het beleid niet overschrijft, worden de standaardinstellingen gebruikt.

U het aangepaste gezondheidsbeleid opgeven of de huidige instellingen onder het hoofd van de stofupgrade bekijken door de geavanceerde upgrade-instellingen te selecteren. Bekijk de volgende afbeelding over hoe.

![Aangepaste statusbeleidsregels beheren][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Fabric-instellingen voor uw cluster aanpassen

Veel verschillende configuratie-instellingen kunnen worden aangepast op een cluster, zoals het betrouwbaarheidsniveau van het cluster en knooppunteigenschappen. Lees voor meer informatie de [instellingen van de clusterstructuur service](service-fabric-cluster-fabric-settings.md).

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
