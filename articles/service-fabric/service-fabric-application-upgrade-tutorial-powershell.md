---
title: Upgrade van servicefabric-app met PowerShell
description: In dit artikel wordt de ervaring doorgenomen van het implementeren van een Service Fabric-toepassing, het wijzigen van de code en het uitrollen van een upgrade met PowerShell.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: b113b5a1042518e3b0d86e53796c5fe49afed418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426781"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade van servicefabric-toepassingen met PowerShell
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

De meest gebruikte en aanbevolen upgrade-benadering is de bewaakte rolling upgrade.  Azure Service Fabric bewaakt de status van de toepassing die wordt bijgewerkt op basis van een set statusbeleid. Zodra een updatedomein (UD) is bijgewerkt, evalueert Service Fabric de status van de toepassing en gaat naar het volgende updatedomein of mislukt de upgrade, afhankelijk van het gezondheidsbeleid.

Een bewaakte toepassingsupgrade kan worden uitgevoerd met behulp van de beheerde of native API's, PowerShell, Azure CLI, Java of REST. Zie Uw toepassing upgraden met Visual Studio voor instructies voor het uitvoeren van een upgrade met [Visual Studio.](service-fabric-application-upgrade-tutorial.md)

Met servicestructuur bewaakte rolling upgrades kan de toepassingsbeheerder het statusevaluatiebeleid configureren dat Service Fabric gebruikt om te bepalen of de toepassing in orde is. Bovendien kan de beheerder de actie configureren die moet worden uitgevoerd wanneer de statusevaluatie mislukt (bijvoorbeeld het uitvoeren van een automatische terugdraaiing.) In deze sectie wordt een bewaakte upgrade uitgevoerd voor een van de SDK-voorbeelden die PowerShell gebruikt. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Stap 1: Het voorbeeld visuele objecten bouwen en implementeren
Bouw en publiceer de toepassing door met de rechtermuisknop te klikken op het toepassingsproject, **VisualObjectsApplication** en de opdracht **Publiceren te** selecteren.  Zie zelfstudie voor upgrade [servicefabric-toepassingen](service-fabric-application-upgrade-tutorial.md)voor meer informatie .  U powershell ook gebruiken om uw toepassing te implementeren.

> [!NOTE]
> Voordat een van de opdrachten Service Fabric in PowerShell kan worden gebruikt, `Connect-ServiceFabricCluster` moet u eerst verbinding maken met het cluster met behulp van de cmdlet. Op dezelfde manier wordt ervan uitgegaan dat het cluster al is ingesteld op uw lokale machine. Zie het artikel over [het instellen van uw Service Fabric-ontwikkelomgeving.](service-fabric-get-started.md)
> 
> 

Nadat u het project in Visual Studio hebt gebouwd, u de [PowerShell-opdracht Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) gebruiken om het toepassingspakket naar de ImageStore te kopiëren. Als u het app-pakket lokaal wilt verifiëren, gebruikt u de cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) De volgende stap is het registreren van de toepassing bij de Runtime van De Service Fabric met behulp van de [cmdlet Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) De volgende stap is het starten van een instantie van de toepassing met behulp van de cmdlet [Nieuw-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)  Deze drie stappen zijn analoog aan het gebruik van het **menu-item Implementeren** in Visual Studio.  Zodra de inrichting is voltooid, moet u het gekopieerde toepassingspakket uit het afbeeldingsarchief opschonen om de verbruikte resources te verminderen.  Als een toepassingstype niet meer nodig is, moet het om dezelfde reden worden niet geregistreerd. Zie [Toepassingen implementeren en verwijderen met PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) voor meer informatie.

U nu Service Fabric Explorer gebruiken [om het cluster en de toepassing weer te geven.](service-fabric-visualizing-your-cluster.md) De toepassing heeft een webservice die kan worden [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) genavigeerd in Internet Explorer door te typen in de adresbalk.  U ziet een aantal zwevende visuele objecten bewegen in het scherm.  Daarnaast u [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) gebruiken om de status van de toepassing te controleren.

## <a name="step-2-update-the-visual-objects-sample"></a>Stap 2: Voorbeeld van visuele objecten bijwerken
U zult merken dat bij de versie die in stap 1 is geïmplementeerd, de visuele objecten niet worden gedraaid. Laten we deze toepassing upgraden naar een toepassing waar de visuele objecten ook draaien.

Selecteer het project VisualObjects.ActorService in de oplossing VisualObjects en open het StatefulVisualObjectActor.cs bestand. Navigeer in dat bestand `MoveObject`naar de `this.State.Move()`methode , `this.State.Move(true)`reageer uit en geef geen commentaar . Met deze wijziging worden de objecten gedraaid nadat de service is bijgewerkt.

We moeten ook het *bestand ServiceManifest.xml* (onder PackageRoot) van het project **VisualObjects.ActorService bijwerken.** Werk het *CodePackage* en de serviceversie bij naar 2.0 en de bijbehorende regels in het bestand *ServiceManifest.xml.*
U de optie *Manifestbestanden van* Visual Studio bewerken gebruiken nadat u met de rechtermuisknop op de oplossing hebt geklikt om het manifestbestand te gewijzigd.

Nadat de wijzigingen zijn aangebracht, moet het manifest er als volgt uitzien (gemarkeerde gedeelten tonen de wijzigingen):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu wordt het bestand *ApplicationManifest.xml* (gevonden onder het **visualobjects-project** onder de oplossing **VisualObjects)** bijgewerkt naar versie 2.0 van het **visualobjects.ActorService-project.** Bovendien wordt de toepassingsversie bijgewerkt naar 2.0.0.0 van 1.0.0.0. Het *toepassingsmanifest.xml* moet er als volgt uitzien:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Bouw nu het project op door alleen het **ActorService-project** te selecteren en vervolgens met de rechtermuisknop te klikken en de optie **Bouwen** in Visual Studio te selecteren. Als u **Alle opnieuw opbouwen**selecteert, moet u de versies voor alle projecten bijwerken, omdat de code zou zijn gewijzigd. Laten we vervolgens de bijgewerkte toepassing verpakken door met de rechtermuisknop op ***VisualObjectsApplication***te klikken, het menu Servicefabric te selecteren en **Pakket**te kiezen. Met deze actie wordt een toepassingspakket dat kan worden geïmplementeerd.  Uw bijgewerkte toepassing is klaar om te worden geïmplementeerd.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Stap 3: Beslissen over gezondheidsbeleid en upgradeparameters
Maak uzelf vertrouwd met de [upgradeparameters van](service-fabric-application-upgrade-parameters.md) de toepassing en het [upgradeproces](service-fabric-application-upgrade.md) om een goed inzicht te krijgen in de verschillende upgradeparameters, time-outs en het toegepaste gezondheidscriterium. Voor deze walkthrough is het beoordelingscriterium voor servicestatus ingesteld op de standaardwaarden (en aanbevolen) waarden, wat betekent dat alle services en instanties na de upgrade *in orde* moeten zijn.  

Laten we echter de *HealthCheckStableDuration* verhogen tot 180 seconden (zodat de services ten minste 120 seconden gezond zijn voordat de upgrade doorgaat naar het volgende updatedomein).  Laten we ook instellen dat de *UpgradeDomainTimeout* 1200 seconden is en de *UpgradeTimeout* 3000 seconden.

Tot slot, laten we ook de *UpgradeFailureAction* intestellen op terugdraaien. Voor deze optie moet Service Fabric de toepassing terugdraaien naar de vorige versie als deze problemen ondervindt tijdens de upgrade. Bij het starten van de upgrade (in stap 4) worden dus de volgende parameters opgegeven:

FailureAction = Terugdraaien

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Stap 4: Toepassing voorbereiden op upgrade
Nu is de applicatie is gebouwd en klaar om te worden opgewaardeerd. Als u een PowerShell-venster opent als beheerder en [Get-ServiceFabricApplication typt,](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)moet dit u laten weten dat het toepassingstype 1.0.0.0 van **VisualObjects** is dat is geïmplementeerd.  

Het toepassingspakket wordt opgeslagen onder het volgende relatieve pad waar u de Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug hebt ongecomprimeerd.* U moet een map "Pakket" vinden in die map, waar het toepassingspakket is opgeslagen. Controleer de tijdstempels om ervoor te zorgen dat het de nieuwste build is (het kan nodig zijn om de paden ook op de juiste manier te wijzigen).

Laten we nu het bijgewerkte toepassingspakket kopiëren naar de Service Fabric ImageStore (waar de toepassingspakketten worden opgeslagen door Service Fabric). De parameter *ApplicationPackagePathInImageStore* informeert Service Fabric waar het toepassingspakket kan worden gevonden. We hebben de bijgewerkte toepassing\_in 'VisualObjects V2' met de volgende opdracht geplaatst (mogelijk moet u paden opnieuw op de juiste manier wijzigen).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

De volgende stap is het registreren van deze toepassing bij Service Fabric, die kan worden uitgevoerd met de opdracht [Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Als de vorige opdracht niet slaagt, is het waarschijnlijk dat u een heropbouw van alle services nodig hebt. Zoals vermeld in stap 2, moet u mogelijk ook uw WebService-versie bijwerken.

Het wordt aanbevolen om het aanvraagpakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassingspakketten uit de afbeeldingswinkel verwijderde, worden systeembronnen vrijgemaakt.  Het bijhouden van ongebruikte toepassingspakketten verbruikt schijfopslag en leidt tot problemen met de prestaties van toepassingen.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Stap 5: De upgrade van de toepassing starten
Nu zijn we helemaal klaar om de upgrade van de toepassing te starten met de opdracht [Start-ServiceFabricApplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


De naam van de toepassing is hetzelfde als in het bestand *ApplicationManifest.xml.* Service Fabric gebruikt deze naam om te bepalen welke toepassing wordt bijgewerkt. Als u de time-outs te kort instelt, u een foutbericht tegenkomen waarin het probleem wordt vermeld. Raadpleeg de sectie probleemoplossing of verhoog de time-outs.

Nu, naarmate de upgrade van de toepassing vordert, u deze controleren met Behulp van Service Fabric Explorer of met de opdracht [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

In een paar minuten moet de status die u hebt gekregen met de vorige PowerShell-opdracht, aangeven dat alle updatedomeinen zijn bijgewerkt (voltooid). En je moet vinden dat de visuele objecten in uw browser venster zijn begonnen met draaien!

U proberen te upgraden van versie 2 naar versie 3, of van versie 2 naar versie 1 als een oefening. De overgang van versie 2 naar versie 1 wordt ook beschouwd als een upgrade. Speel met time-outs en gezondheidsbeleid om jezelf ermee vertrouwd te maken. Wanneer u wordt geïmplementeerd in een Azure-cluster, moeten de parameters op de juiste manier worden ingesteld. Het is goed om de time-outs conservatief in te stellen.

## <a name="next-steps"></a>Volgende stappen
[Het upgraden van uw toepassing met Visual Studio](service-fabric-application-upgrade-tutorial.md) leidt u door een applicatie-upgrade met Visual Studio.

Bepaal hoe uw toepassing wordt bijgewerkt met behulp van [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Maak uw toepassingsupgrades compatibel door te leren hoe [u gegevensserialisatie kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)

