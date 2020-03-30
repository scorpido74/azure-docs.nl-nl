---
title: Overzicht van standalone servicefabricclusters
description: Service Fabric-clusters draaien op Windows Server en Linux, wat betekent dat u Service Fabric-toepassingen overal implementeren en hosten waar u Windows Server of Linux uitvoeren.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465640"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Overzicht van zelfstandige clusters van servicefabric

Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster, wordt een clusterknooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knooppunten. Als u nieuwe knooppunten aan het cluster toevoegt, brengt Service Fabric de replica's en instanties van de servicepartitie opnieuw in evenwicht in het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de discussie over de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, u het aantal knooppunten in het cluster verminderen. Service Fabric brengt de partitiereplica's en instanties opnieuw in evenwicht in het verminderde aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt.

Een knooppunttype definieert de grootte, het aantal en de eigenschappen voor een set knooppunten in het cluster. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Knooppunttypen worden gebruikt voor het definiëren van rollen voor een set clusterknooppunten, zoals 'front-end' of 'back-end'. Uw cluster kan meer dan één knooppunttype hebben, maar voor productieclusters moet het primaire knooppunttype ten minste vijf VM's bevatten (of ten minste drie VM's voor testclusters). [Service Fabric-systeemservices](service-fabric-technical-overview.md#system-services) worden op de knooppunten van het primaire knooppunttype geplaatst.

Het proces voor het maken van een Service Fabric-cluster on-premises is vergelijkbaar met het proces van het maken van een cluster op elke cloud van uw keuze met een set VM's. De eerste stappen voor het inrichten van de VM's worden geregeld door de cloudprovider of on-premises omgeving die u gebruikt. Zodra u een set VM's hebt met netwerkconnectiviteit tussen deze apparaten, zijn de stappen om het Service Fabric-pakket in te stellen, de clusterinstellingen te bewerken en de clustercreatie- en beheerscripts uit te voeren identiek. Dit zorgt ervoor dat uw kennis en ervaring met het beheren van Service Fabric-clusters overdraagbaar is wanneer u ervoor kiest om nieuwe hostingomgevingen te targeten.

## <a name="cluster-security"></a>Clusterbeveiliging
Een cluster van ServiceFabric is een bron waarvan u eigenaar bent.  Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voorkomen dat onbevoegde gebruikers er verbinding mee maken. Een beveiligd cluster is vooral belangrijk wanneer u productieworkloads op het cluster uitvoert.

### <a name="node-to-node-security"></a>Beveiliging van knooppunt tot knooppunt
Beveiliging van knooppunten beveiligt de communicatie tussen de VM's of computers in een cluster. Dit beveiligingsscenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deelnemen aan hostingtoepassingen en -services in het cluster. Service Fabric gebruikt X.509-certificaten om een cluster te beveiligen en beveiligingsfuncties voor toepassingen te bieden.  Er is een clustercertificaat vereist om clusterverkeer te beveiligen en cluster- en serververificatie te bieden.  Zelfondertekende certificaten kunnen worden gebruikt voor testclusters, maar een certificaat van een vertrouwde certificaatautoriteit moet worden gebruikt om productieclusters te beveiligen.

Windows-beveiliging kan ook worden ingeschakeld voor een windows-standalone cluster. Als u Windows Server 2012 R2 en Windows Active Directory hebt, raden we u aan Windows-beveiliging te gebruiken met groepsbeheerserviceaccounts. Gebruik anders Windows-beveiliging met Windows-accounts.

Lees [de beveiliging van knooppunt tot knooppunt voor](service-fabric-cluster-security.md#node-to-node-security) meer informatie

### <a name="client-to-node-security"></a>Client-to-node beveiliging
Client-to-node beveiliging verifieert clients en helpt de communicatie tussen een client en afzonderlijke knooppunten in het cluster te beveiligen. Dit type beveiliging zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients worden uniek geïdentificeerd door middel van hun X.509 certificaat beveiligingsreferenties. Een willekeurig aantal optionele clientcertificaten kan worden gebruikt om beheerders- of gebruikersclients met het cluster te verifiëren.

Naast clientcertificaten kan Azure Active Directory ook worden geconfigureerd om clients met het cluster te verifiëren.

Lees voor meer informatie [client-to-nodebeveiliging](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>RBAC (op rollen gebaseerd toegangsbeheer)
Service Fabric ondersteunt ook toegangscontrole om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster veiliger te maken. Er worden twee typen toegangsbeheer ondersteund voor clients die verbinding maken met een cluster: de rol Administrator en de rol Gebruiker.  

Lees voor meer informatie [RBAC (Role-Based Access Control).](service-fabric-cluster-security.md#role-based-access-control-rbac)

## <a name="scaling"></a>Schalen

Toepassingseisen veranderen in de loop van de tijd. Mogelijk moet u de clusterresources vergroten om te voldoen aan de toegenomen toepassingsworkload of het netwerkverkeer of clusterbronnen verlagen wanneer de vraag daalt. Nadat u een cluster servicestructuur hebt gemaakt, u het cluster horizontaal schalen (het aantal knooppunten wijzigen) of verticaal wijzigen (de bronnen van de knooppunten wijzigen). U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd. Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

Lees [Zelfstandige clusters schalen](service-fabric-cluster-scaling-standalone.md)voor meer informatie.

## <a name="upgrading"></a>Upgraden

Een standalone cluster is een bron die u volledig bezit. U bent verantwoordelijk voor het patchen van het onderliggende besturingssysteem en het initiëren van stofupgrades. U instellen dat uw cluster automatische runtime-upgrades ontvangt, wanneer Microsoft een nieuwe versie uitbrengt, of ervoor kiezen om een ondersteunde runtime-versie te selecteren die u wilt. Naast fabric-upgrades u ook het besturingssysteem patchen en clusterconfiguratie bijwerken, zoals certificaten of toepassingspoorten. 

Lees Voor meer informatie [Zelfstandige clusters upgraden.](service-fabric-cluster-upgrade-standalone.md)

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
U clusters maken op VM's of computers waarop deze besturingssystemen worden uitgevoerd (Linux wordt nog niet ondersteund):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Volgende stappen
Lees meer over [het beveiligen,](service-fabric-cluster-security.md) [schalen](service-fabric-cluster-scaling-standalone.md)en [upgraden van](service-fabric-cluster-upgrade-standalone.md) zelfstandige clusters.

Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).
