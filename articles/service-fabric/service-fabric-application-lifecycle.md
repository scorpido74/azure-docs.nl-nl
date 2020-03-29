---
title: Levenscyclus van toepassingen in servicestructuur
description: Beschrijft het ontwikkelen, implementeren, testen, upgraden, onderhouden en verwijderen van Service Fabric-toepassingen.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378001"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric-toepassingslevenscyclus
Net als bij andere platforms gaat een toepassing op Azure Service Fabric meestal door de volgende fasen: ontwerp, ontwikkeling, testen, implementatie, upgraden, onderhoud en verwijdering. Service Fabric biedt eersteklas ondersteuning voor de volledige levenscyclus van applicaties van cloudtoepassingen, van ontwikkeling tot implementatie, dagelijks beheer en onderhoud tot uiteindelijke ontmanteling. Het servicemodel maakt het mogelijk verschillende rollen onafhankelijk deel te nemen aan de levenscyclus van de toepassing. In dit artikel vindt u een overzicht van de API's en hoe deze worden gebruikt door de verschillende rollen gedurende de fasen van de levenscyclus van de Service Fabric-toepassing.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Functierollen voor servicemodellen
De rollen van het servicemodel zijn:

* **Serviceontwikkelaar**: Ontwikkelt modulaire en generieke services die kunnen worden hergebruikt en gebruikt in meerdere toepassingen van hetzelfde type of verschillende typen. Een wachtrijservice kan bijvoorbeeld worden gebruikt voor het maken van een ticketing-applicatie (helpdesk) of een e-commerceapplicatie (winkelwagentje).
* **Toepassingsontwikkelaar**: Maakt toepassingen door een verzameling services te integreren om te voldoen aan bepaalde specifieke vereisten of scenario's. Een e-commercewebsite kan bijvoorbeeld 'JSON Stateless Front-End Service', 'Auction Stateful Service' en 'Queue Stateful Service' integreren om een veilingoplossing te bouwen.
* **Toepassingsbeheerder**: Neemt beslissingen over de toepassingsconfiguratie (het invullen van de configuratiesjabloonparameters), implementatie (toewijzing naar beschikbare resources) en de kwaliteit van de service. Een toepassingsbeheerder bepaalt bijvoorbeeld de taalland (Engels voor de Verenigde Staten of Japans voor Japan, bijvoorbeeld) van de toepassing. Een andere geïmplementeerde toepassing kan verschillende instellingen hebben.
* **Operator**: Implementeert toepassingen op basis van de configuratie van de toepassing en vereisten die zijn opgegeven door de toepassingsbeheerder. Een operator voorziet en implementeert de toepassing bijvoorbeeld en zorgt ervoor dat deze wordt uitgevoerd in Azure. Operators bewaken de informatie over de status en prestaties van toepassingen en onderhouden de fysieke infrastructuur indien nodig.

## <a name="develop"></a>Ontwikkelen
1. Een *serviceontwikkelaar* ontwikkelt verschillende soorten services met behulp van het [programmeermodel Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md) of Betrouwbare [Diensten.](service-fabric-reliable-services-introduction.md)
2. Een *serviceontwikkelaar* beschrijft de ontwikkelde servicetypen in een servicemanifestbestand dat bestaat uit een of meer code-, configuratie- en gegevenspakketten.
3. Een *toepassingsontwikkelaar* bouwt vervolgens een toepassing met verschillende servicetypen.
4. Een *toepassingsontwikkelaar* beschrijft het toepassingstype in een toepassingsmanifest door te verwijzen naar de servicemanifesten van de samenstellende services en op de juiste manier verschillende configuratie- en implementatieinstellingen van de samenstellende services te overschrijven en te parameteriseren.

Zie [Aan de slag met betrouwbare actoren](service-fabric-reliable-actors-get-started.md) en ga aan de slag met betrouwbare [services](service-fabric-reliable-services-quick-start.md) voor voorbeelden.

## <a name="deploy"></a>Implementeren
1. Een *toepassingsbeheerder* stemt het toepassingstype af op een specifieke toepassing die moet worden geïmplementeerd in een cluster servicestructuur door de juiste parameters van het **element ApplicationType** op te geven in het toepassingsmanifest.
2. Een *operator* uploadt het toepassingspakket naar het clusterimagearchief met behulp van de [ **copyapplicationpackage-methode** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **Copy-ServiceFabricApplicationPackage.** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Het aanvraagpakket bevat het applicatiemanifest en het verzamelen van servicepakketten. Service Fabric implementeert toepassingen uit het toepassingspakket dat is opgeslagen in de afbeeldingsopslag, wat een Azure blob store of de Service Fabric-systeemservice kan zijn.
3. De *operator* voorziet vervolgens het toepassingstype in het doelcluster van het geüploade toepassingspakket met behulp van de [ **provisionApplicationAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)of de bewerking [ **ToepassingREST inhet doelcluster** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Na het inrichten van de toepassing start een *operator* de toepassing met de parameters die door de *toepassingsbeheerder* zijn geleverd met behulp van de [ **createApplicationAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Nieuw-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)of de bewerking [ **ToepassingREST maken.** ](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)
5. Nadat de toepassing is geïmplementeerd, gebruikt een *operator* de [ **CreateServiceAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)de [cmdlet **Nieuw-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)of de bewerking [ **ServiceREST maken** ](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) om nieuwe service-exemplaren voor de toepassing te maken op basis van beschikbare servicetypen.
6. De toepassing wordt nu uitgevoerd in het cluster ServiceFabric.

Zie [Een toepassing](service-fabric-deploy-remove-applications.md) implementeren voor voorbeelden.

## <a name="test"></a>Test
1. Nadat een *serviceontwikkelaar* is geïmplementeerd in het cluster lokale ontwikkeling of een testcluster, voert hij het ingebouwde failovertestscenario uit met behulp van de klassen [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) en [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) of de [cmdlet **Invoke-ServiceFailFailoverTestScenario.** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps) Het failovertestscenario voert een bepaalde service uit via belangrijke overgangen en failovers om ervoor te zorgen dat deze nog steeds beschikbaar is en werkt.
2. De *serviceontwikkelaar* voert vervolgens het ingebouwde chaostestscenario uit met de klassen [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) en [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) of de [cmdlet **Van Invoke-ServiceFabricChaosTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Het scenario voor chaostests veroorzaakt willekeurig meerdere node-, codepakket- en replicafouten in het cluster.
3. De *serviceontwikkelaar* [test service-to-service communicatie](service-fabric-testability-scenarios-service-communication.md) door testscenario's te ontwerpen die primaire replica's rond het cluster verplaatsen.

Zie [Inleiding tot de Fault Analysis Service](service-fabric-testability-overview.md) voor meer informatie.

## <a name="upgrade"></a>Upgraden
1. Een *serviceontwikkelaar* werkt de samenstellende services van de geinstantieerde toepassing bij en/of lost bugs op en biedt een nieuwe versie van het servicemanifest.
2. Een *toepassingsontwikkelaar* overschrijft en parameteriseert de configuratie- en implementatie-instellingen van de consistente services en biedt een nieuwe versie van het toepassingsmanifest. De toepassingsontwikkelaar neemt vervolgens de nieuwe versies van de service manifesten in de toepassing en biedt een nieuwe versie van het toepassingstype in een bijgewerkte toepassingspakket.
3. Een *toepassingsbeheerder* neemt de nieuwe versie van het toepassingstype op in de doeltoepassing door de juiste parameters bij te werken.
4. Een *operator* uploadt het bijgewerkte toepassingspakket naar het clusterimagearchief met de [ **methode CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **Copy-ServiceFabricApplicationPackage.** ](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Het aanvraagpakket bevat het applicatiemanifest en het verzamelen van servicepakketten.
5. Een *operator* voorziet de nieuwe versie van de toepassing in het doelcluster met behulp van de [ **provisionApplicationAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)of de bewerking Rest van de [ **toepassing.** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)
6. Een operator upgradet de doeltoepassing naar de nieuwe versie met behulp van de [ **upgradeApplicationAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de cmdlet Start-ServiceFabricApplicationUpgrade of de bewerking [ **>Upgrade a Application** REST](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).An *operator* upgrades the target application application to the new version using the UpgradeApplicationAsync method, the [ **Start-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), or the Upgrade an Application REST operation .
7. Een *operator* controleert de voortgang van de upgrade met behulp van de [ **GetApplicationUpgradeProgressAsync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)of de [ **progress rest-bewerking Voor upgrade van** ](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1)toepassingen .
8. Indien nodig wijzigt en past de *operator* de parameters van de huidige toepassingsupgrade opnieuw toe met de methode [ **UpdateApplicationUpgradeAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Update-ServiceFabricApplicationUpgrade** ](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)of de [ **update-toepassingsupgradeREST-** bewerking](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Indien nodig rolt de *operator* de huidige toepassingsupgrade terug met de [ **methode RollbackApplicationUpgradeAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Start-ServiceFabricApplicationRollback** ](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)of de restbewerking voor upgrade van de [ **rollback-toepassing** ](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric verbetert de doeltoepassing die in het cluster wordt uitgevoerd zonder de beschikbaarheid van een van de samenstellende services te verliezen.

Zie de [zelfstudie voor de upgrade van toepassingen](service-fabric-application-upgrade-tutorial.md) voor voorbeelden.

## <a name="maintain"></a>Onderhouden
1. Service Fabric-interfaces met de Azure-infrastructuur voor upgrades en patches van het besturingssysteem om de beschikbaarheid van alle toepassingen die in het cluster worden uitgevoerd, te garanderen.
2. Voor upgrades en patches naar het Service Fabric-platform verbetert Service Fabric zichzelf zonder dat de beschikbaarheid van een van de toepassingen die op het cluster worden uitgevoerd, wordt verloren.
3. Een *toepassingsbeheerder* keurt de toevoeging of verwijdering van knooppunten uit een cluster goed na analyse van historische capaciteitsbenuttegegevens en de verwachte toekomstige vraag.
4. Een *operator* voegt knooppunten toe en verwijdert deze zijn opgegeven door de *toepassingsbeheerder.*
5. Wanneer nieuwe knooppunten worden toegevoegd of bestaande knooppunten uit het cluster worden verwijderd, laadt Service Fabric automatisch de lopende toepassingen over alle knooppunten in het cluster om optimale prestaties te bereiken.

## <a name="remove"></a>Verwijderen
1. Een *operator* kan een specifieke instantie van een lopende service in het cluster verwijderen zonder de volledige toepassing te verwijderen met de [ **deleteserviceasync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)de [cmdlet **Remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)of de bewerking Service REST [ **verwijderen** ](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Een *operator* kan ook een toepassingsinstantie en al zijn services verwijderen met behulp van de [ **deleteapplicationasync-methode,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Remove-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)of de bewerking Rest [ **verwijderen** ](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Zodra de toepassing en services zijn gestopt, kan de *operator* het toepassingstype ontrusten met de methode [ **UnprovisionApplicationAsync,** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)de [cmdlet **Unregister-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)of de bewerking [ **Een toepassingsREST niet meer inrichten** ](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Als u de inrichting van het toepassingstype ongedaan maakt, wordt het toepassingspakket niet uit de ImageStore verwijderd. U moet het toepassingspakket handmatig verwijderen.
4. Een *operator* verwijdert het toepassingspakket uit de ImageStore met behulp van de [ **methode RemoveApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **Remove-ServiceFabricApplicationPackage.** ](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps)

Zie [Een toepassing](service-fabric-deploy-remove-applications.md) implementeren voor voorbeelden.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het ontwikkelen, testen en beheren van Service Fabric-toepassingen en -services:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)
* [Toepassingsupgrade](service-fabric-application-upgrade.md)
* [Overzicht van Testabiliteit](service-fabric-testability-overview.md)
