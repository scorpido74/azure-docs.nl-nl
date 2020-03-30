---
title: ResourcemanagerArchitectuur
description: Een overzicht van en architecturale informatie over de Azure Service Fabric Cluster Resource Manager-service.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551689"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Overzicht van architectuur van clusterresourcebeheer
De Clusterresourcemanager servicestructuur is een centrale service die wordt uitgevoerd in het cluster. Het beheert de gewenste status van de services in het cluster, met name met betrekking tot het verbruik van hulpbronnen en eventuele plaatsingsregels. 

Als u de bronnen in uw cluster wilt beheren, moet de clusterbronbeheerder voor servicestructuur over verschillende gegevens beschikken:

- Welke services bestaan momenteel
- Het huidige (of standaard) resourceverbruik van elke service 
- De resterende clustercapaciteit 
- De capaciteit van de knooppunten in het cluster 
- De hoeveelheid resources die op elk knooppunt wordt verbruikt

Het resourceverbruik van een bepaalde service kan in de loop van de tijd veranderen en services geven meestal om meer dan één type resource. Over verschillende diensten kunnen zowel echte fysieke als fysieke middelen worden gemeten. Services kunnen fysieke statistieken bijhouden, zoals geheugen en schijfverbruik. Meer in het algemeen kunnen services zich begeven om logische statistieken - zaken als 'WorkQueueDepth' of 'TotalRequests'. Zowel logische als fysieke statistieken kunnen in hetzelfde cluster worden gebruikt. Statistieken kunnen worden gedeeld in veel services of specifiek zijn voor een bepaalde service.

## <a name="other-considerations"></a>Andere overwegingen
De eigenaren en exploitanten van het cluster kan verschillen van de service en applicatie auteurs, of op zijn minimaal dezelfde mensen het dragen van verschillende hoeden. Wanneer u uw toepassing ontwikkelt weet u een paar dingen over wat het vereist. U hebt een schatting van de resources die het verbruikt en hoe verschillende services moeten worden geïmplementeerd. De weblaag moet bijvoorbeeld worden uitgevoerd op knooppunten die zijn blootgesteld aan internet, terwijl de databaseservices dat niet mogen doen. Als een ander voorbeeld, de webservices zijn waarschijnlijk beperkt door CPU en netwerk, terwijl de data tier diensten zorg meer over geheugen en schijfverbruik. De persoon die een incident met een livesite voor die service in productie behandelt, of die een upgrade naar de service beheert, heeft echter een andere taak te doen en heeft verschillende tools nodig. 

Zowel het cluster als de services zijn dynamisch:

- Het aantal knooppunten in het cluster kan groeien en krimpen
- Knooppunten van verschillende groottes en typen kunnen komen en gaan
- Services kunnen worden gemaakt, verwijderd en de gewenste toewijzingen en plaatsingsregels wijzigen
- Upgrades of andere beheerbewerkingen kunnen door het cluster rollen bij de toepassing op infrastructuurniveau
- Storingen kunnen op elk moment gebeuren.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Clusterresourcebeheercomponenten en gegevensstroom
De Clusterresourcemanager moet de vereisten van elke service en het verbruik van resources door elk serviceobject binnen deze services bijhouden. De Cluster Resource Manager heeft twee conceptuele onderdelen: agents die op elk knooppunt worden uitgevoerd en een fouttolerante service. De agents op elk knooppunt volgen laadrapporten van services, voegen ze samen en rapporteren ze periodiek. De clusterresourcebeheerservice verzamelt alle informatie van de lokale agents en reageert op basis van de huidige configuratie.

Laten we eens kijken naar het volgende diagram:

<center>

![Resource Balancer-architectuur][Image1]
</center>

Tijdens runtime zijn er veel veranderingen die kunnen gebeuren. Stel dat de hoeveelheid resources die sommige services gebruiken, wordt gewijzigd, sommige services mislukken en sommige knooppunten worden samengevoegd en het cluster verlaten. Alle wijzigingen op een knooppunt worden samengevoegd en periodiek verzonden naar de Cluster Resource Manager-service (1,2) waar ze opnieuw worden samengevoegd, geanalyseerd en opgeslagen. Om de paar seconden die service kijkt naar de wijzigingen en bepaalt of er acties nodig zijn (3). Het kan bijvoorbeeld merken dat sommige lege knooppunten zijn toegevoegd aan het cluster. Als gevolg hiervan besluit het om bepaalde services naar die knooppunten te verplaatsen. De Cluster resource manager kan ook merken dat een bepaald knooppunt is overbelast, of dat bepaalde services zijn mislukt of verwijderd, waardoor bronnen elders vrijte.

Laten we eens kijken naar het volgende diagram en zien wat er daarna gebeurt. Stel dat clusterresourcebeheer bepaalt dat wijzigingen nodig zijn. Het coördineert met andere systeemservices (met name de Failover Manager) om de nodige wijzigingen aan te brengen. Vervolgens worden de benodigde commando's naar de juiste knooppunten gestuurd (4). Stel dat de Resource Manager opmerkte dat Node5 overbelast was en dus besloot om service B te verplaatsen van Knooppunt5 naar Knooppunt4. Aan het einde van de herconfiguratie (5) ziet het cluster er als volgt uit:

<center>

![Resource Balancer-architectuur][Image2]
</center>

## <a name="next-steps"></a>Volgende stappen
- De Clusterresourcemanager heeft veel opties voor het beschrijven van het cluster. Voor meer informatie over hen, check out dit artikel over [het beschrijven van een Service Fabric cluster](./service-fabric-cluster-resource-manager-cluster-description.md)
- De primaire taken van de clusterresourcemanager zijn het opnieuw in evenwicht brengen van het cluster en het afdwingen van plaatsingsregels. Zie [Het balanceren van uw Service Fabric-cluster](./service-fabric-cluster-resource-manager-balancing.md) voor meer informatie over het configureren van dit gedrag

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
