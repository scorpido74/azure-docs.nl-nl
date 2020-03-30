---
title: Upgrade van servicefabric-toepassingen
description: In dit artikel vindt u een inleiding tot het upgraden van een Service Fabric-toepassing, waaronder het kiezen van upgrademodi en het uitvoeren van gezondheidscontroles.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259043"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade van servicefabric-toepassingen
Een Azure Service Fabric-toepassing is een verzameling services. Tijdens een upgrade vergelijkt Service Fabric het nieuwe [toepassingsmanifest](service-fabric-application-and-service-manifests.md) met de vorige versie en bepaalt welke services in de toepassing updates vereisen. Service Fabric vergelijkt de versienummers in de servicemanifesten met de versienummers in de vorige versie. Als een service niet is gewijzigd, wordt die service niet bijgewerkt.

## <a name="rolling-upgrades-overview"></a>Overzicht van rolling upgrades
In een upgrade met rollende toepassingen wordt de upgrade gefaseerd uitgevoerd. In elke fase wordt de upgrade toegepast op een subset van knooppunten in het cluster, een updatedomein genoemd. Als gevolg hiervan blijft de toepassing beschikbaar tijdens de upgrade. Tijdens de upgrade kan het cluster een mix van de oude en nieuwe versies bevatten.

Om die reden moeten de twee versies compatibel zijn met voorwaarts en achterwaarts. Als ze niet compatibel zijn, is de toepassingsbeheerder verantwoordelijk voor het organiseren van een upgrade in meerdere fasen om de beschikbaarheid te behouden. In een upgrade in meerdere fasen is de eerste stap het upgraden naar een tussenversie van de toepassing die compatibel is met de vorige versie. De tweede stap is het upgraden van de definitieve versie die de compatibiliteit met de pre-update versie breekt, maar is compatibel met de tussenliggende versie.

Updatedomeinen worden opgegeven in het clustermanifest wanneer u het cluster configureert. Updatedomeinen ontvangen geen updates in een bepaalde volgorde. Een updatedomein is een logische eenheid voor implementatie voor een toepassing. Met updatedomeinen kunnen de services tijdens een upgrade op hoge beschikbaarheid blijven.

Niet-rollende upgrades zijn mogelijk als de upgrade wordt toegepast op alle knooppunten in het cluster, wat het geval is wanneer de toepassing slechts één updatedomein heeft. Deze aanpak wordt niet aanbevolen, omdat de service uitvalt en niet beschikbaar is op het moment van de upgrade. Bovendien biedt Azure geen garanties wanneer een cluster is ingesteld met slechts één updatedomein.

Nadat de upgrade is voltooid, blijven alle services en replica's (exemplaren) in dezelfde versie, dat wil zeggen, als de upgrade slaagt, worden ze bijgewerkt naar de nieuwe versie; als de upgrade mislukt en wordt teruggedraaid, zouden ze worden teruggedraaid naar de oude versie.

## <a name="health-checks-during-upgrades"></a>Gezondheidscontroles tijdens upgrades
Voor een upgrade moet het gezondheidsbeleid worden ingesteld (of standaardwaarden kunnen worden gebruikt). Een upgrade wordt succesvol genoemd wanneer alle updatedomeinen binnen de opgegeven time-outs worden bijgewerkt en wanneer alle updatedomeinen als gezond worden beschouwd.  Een domein voor een gezonde update betekent dat het updatedomein alle statuscontroles heeft doorstaan die zijn opgegeven in het gezondheidsbeleid. Een gezondheidsbeleid kan bijvoorbeeld verplichten dat alle services binnen een toepassingsinstantie *gezond*moeten zijn, omdat de gezondheid wordt gedefinieerd door Service Fabric.

Gezondheidsbeleid en -controles tijdens de upgrade door Service Fabric zijn service- en toepassingsagnostisch. Dat wil zeggen, er worden geen servicespecifieke tests uitgevoerd.  Uw service heeft bijvoorbeeld een doorvoervereiste, maar Service Fabric heeft niet de informatie om de doorvoer te controleren. Raadpleeg de [gezondheidsartikelen](service-fabric-health-introduction.md) voor de controles die worden uitgevoerd. De controles die tijdens een upgrade plaatsvinden, omvatten tests voor de vraag of het toepassingspakket correct is gekopieerd, of de instantie is gestart, enzovoort.

De toepassingsstatus is een aggregatie van de onderliggende entiteiten van de toepassing. Kortom, Service Fabric evalueert de status van de toepassing via de status die wordt gerapporteerd over de toepassing. Het evalueert ook de gezondheid van alle diensten voor de toepassing op deze manier. Service Fabric evalueert verder de status van de toepassingsservices door de gezondheid van hun kinderen, zoals de servicereplica, te aggregeren. Zodra het beleid voor toepassingsstatus is voldaan, kan de upgrade doorgaan. Als het gezondheidsbeleid wordt geschonden, mislukt de upgrade van de toepassing.

## <a name="upgrade-modes"></a>Upgrademodi
De modus die we aanbevelen voor applicatie-upgrade is de bewaakte modus, de veelgebruikte modus. De bewaakte modus voert de upgrade uit op één updatedomein en als alle statuscontroles verstrijken (volgens het opgegeven beleid), gaat u automatisch door naar het volgende updatedomein.  Als statuscontroles mislukken en/of time-outs zijn bereikt, wordt de upgrade teruggedraaid voor het updatedomein of wordt de modus gewijzigd in niet-bewaakte handleiding. U de upgrade zo configureren dat u een van deze twee modi kiest voor mislukte upgrades. 

De niet-bewaakte handmatige modus heeft handmatige interventie nodig na elke upgrade op een updatedomein, om de upgrade op het volgende updatedomein te starten. Er worden geen gezondheidscontroles van servicefabric uitgevoerd. De beheerder voert de status- of statuscontroles uit voordat de upgrade in het volgende updatedomein wordt gestart.

## <a name="upgrade-default-services"></a>Standaardservices bijwerken
Sommige standaardserviceparameters die in het toepassingsmanifest zijn [gedefinieerd,](service-fabric-application-and-service-manifests.md) kunnen ook worden bijgewerkt als onderdeel van een toepassingsupgrade. Alleen de serviceparameters die ondersteuning bieden die wordt gewijzigd via [Update-ServiceFabricService,](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) kunnen worden gewijzigd als onderdeel van een upgrade. Het gedrag van het wijzigen van standaardservices tijdens de upgrade van toepassingen is als volgt:

1. Standaardservices in het nieuwe toepassingsmanifest die nog niet in het cluster bestaan, worden gemaakt.
2. Standaardservices die in zowel de vorige als nieuwe toepassingsmanifesten bestaan, worden bijgewerkt. De parameters van de standaardservice in het nieuwe toepassingsmanifest overschrijven de parameters van de bestaande service. De toepassingsupgrade wordt automatisch teruggedraaid als het bijwerken van een standaardservice mislukt.
3. Standaardservices die niet in het nieuwe toepassingsmanifest voorkomen, worden verwijderd als ze in het cluster voorkomen. **Houd er rekening mee dat het verwijderen van een standaardservice ertoe leidt dat alle status van die service wordt verwijderd en niet ongedaan kan worden gemaakt.**

Wanneer een toepassingsupgrade wordt teruggedraaid, worden standaardserviceparameters teruggezet naar hun oude waarden voordat de upgrade is gestart, maar kunnen verwijderde services niet opnieuw worden gemaakt met hun oude status.

> [!TIP]
> De instelling [voor clusterconfig enableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) moet *de* regels 2) en 3) hierboven inschakelen (standaardservice-update en verwijdering). Deze functie wordt vanaf Service Fabric versie 5.5 ondersteund.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Meerdere toepassingen upgraden met HTTPS-eindpunten
U moet oppassen dat **u dezelfde poort** niet gebruikt voor verschillende exemplaren van dezelfde toepassing wanneer u HTTP**S gebruikt.** De reden hiervoor is dat Service Fabric het cert niet kan upgraden voor een van de toepassingsinstanties. Bijvoorbeeld, als toepassing 1 of toepassing 2 beide willen hun cert 1 upgraden naar cert 2. Wanneer de upgrade plaatsvindt, heeft Service Fabric mogelijk de cert 1-registratie met http.sys opgeschoond, ook al gebruikt de andere toepassing deze nog steeds. Om dit te voorkomen detecteert Service Fabric dat er al een andere toepassingsinstantie is geregistreerd op de poort met het certificaat (vanwege http.sys) en mislukt de bewerking.

Service Fabric ondersteunt daarom geen upgrade van twee verschillende services die **dezelfde poort** gebruiken in verschillende toepassingsinstanties. Met andere woorden, u niet hetzelfde certificaat gebruiken op verschillende services op dezelfde poort. Als u een gedeeld certificaat op dezelfde poort moet hebben, moet u ervoor zorgen dat de services op verschillende machines worden geplaatst met plaatsingsbeperkingen. Of overweeg om servicefabricdynamische poorten te gebruiken indien mogelijk voor elke service in elk toepassingsexemplaar. 

Als u een upgrade ziet mislukken met https, wordt een foutwaarschuwing weergegeven met de tekst 'De Windows HTTP Server API ondersteunt geen meerdere certificaten voor toepassingen die een poort delen'.

## <a name="application-upgrade-flowchart"></a>Stroomdiagram voor toepassingsupgrade
Het stroomdiagram na deze alinea kan u helpen inzicht te krijgen in het upgradeproces van een Service Fabric-toepassing. In de stroom wordt met name beschreven hoe de time-outs, waaronder *HealthCheckStableDuration*, *HealthCheckRetryTimeout*en *UpgradeHealthCheckInterval,* helpen bepalen wanneer de upgrade in één updatedomein als een succes of een fout wordt beschouwd.

![Het upgradeproces voor een Service Fabric-toepassing][image]

## <a name="next-steps"></a>Volgende stappen
[Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.

[Het upgraden van uw toepassing met PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

Bepaal hoe uw toepassing wordt bijgewerkt met [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Maak uw toepassingsupgrades compatibel door te leren hoe [u Data Serialization kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
