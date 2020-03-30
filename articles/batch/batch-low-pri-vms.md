---
title: Workloads uitvoeren op kosteneffectieve VM's met lage prioriteit - Azure Batch | Microsoft Documenten
description: Meer informatie over het inrichten van VM's met lage prioriteit om de kosten van Azure Batch-workloads te verlagen.
services: batch
author: mscurrell
manager: evansma
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9f4b9ed9254eaf950311dd27d5716c4681707614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053916"
---
# <a name="use-low-priority-vms-with-batch"></a>Virtuele machines met lage prioriteit met Batch gebruiken

Azure Batch biedt virtuele machines met een lage prioriteit (VM's) om de kosten van Batch-workloads te verlagen. Vm's met lage prioriteit maken nieuwe typen Batch-workloads mogelijk doordat een grote hoeveelheid rekenkracht kan worden gebruikt voor zeer lage kosten.
 
Vm's met lage prioriteit profiteren van overtollige capaciteit in Azure. Wanneer u VM's met lage prioriteit opgeeft in uw groepen, kan Azure Batch dit overschot gebruiken, indien beschikbaar.
 
De afweging voor het gebruik van VM's met lage prioriteit is dat deze VM's mogelijk niet beschikbaar zijn om te worden toegewezen of op elk moment kunnen worden uitgesloten, afhankelijk van de beschikbare capaciteit. Om deze reden zijn VM's met lage prioriteit het meest geschikt voor bepaalde soorten workloads. Gebruik VM's met lage prioriteit voor batch- en asynchrone verwerkingsworkloads waarbij de voltooiingstijd van de taak flexibel is en het werk over veel VM's wordt verdeeld.
 
VM's met lage prioriteit worden aangeboden tegen een aanzienlijk lagere prijs in vergelijking met speciale VM's. Zie [Batchprijzen](https://azure.microsoft.com/pricing/details/batch/)voor prijsdetails.

> [!NOTE]
> [Spot VM's](https://azure.microsoft.com/pricing/spot/) zijn nu beschikbaar voor [vm's](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) met één exemplaar en [VM-schaalsets.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot) Spot VM's zijn een evolutie van vm's met lage prioriteit, maar verschillen in die prijs kan variëren en een optionele maximumprijs kan worden ingesteld bij het toewijzen van Spot VM's.
>
> Azure Batch-groepen beginnen binnen enkele maanden na het algemeen beschikbaar stellen van spotVM's te ondersteunen, met nieuwe versies van de [Batch-API's en -hulpprogramma's.](https://docs.microsoft.com/azure/batch/batch-apis-tools) Zodra Spot VM-ondersteuning beschikbaar is, zullen VM's met lage prioriteit worden afgeschaft - ze blijven gedurende ten minste 12 maanden worden ondersteund met behulp van huidige API's en toolversies, zodat er voldoende tijd is voor migratie naar Spot VM's. 
>
> Spot VM's worden niet ondersteund voor [Cloud Service Configuration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) pools. Als u Spot VM's wilt gebruiken, moeten cloudservicegroepen worden gemigreerd naar groepen [virtuele machineconfiguratie.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)


## <a name="use-cases-for-low-priority-vms"></a>Use cases voor VM's met lage prioriteit

Welke workloads kunnen wel en niet worden gebruikt, gezien de kenmerken van vm's met lage prioriteit? Over het algemeen passen batchverwerkingsworkloads goed, omdat taken in veel parallelle taken worden opgesplitst of er veel taken zijn die zijn uitgesplitst en verdeeld over veel VM's.

-   Om het gebruik van overtollige capaciteit in Azure te maximaliseren, kunnen geschikte taken worden uitschalen.

-   Soms vm's kunnen niet beschikbaar zijn of worden vooruitgelopen, wat resulteert in een verminderde capaciteit voor taken en kan leiden tot taakonderbreking en herhalingen. Banen moeten dus flexibel zijn in de tijd die ze kunnen nemen om te draaien.

-   Taken met langere taken kunnen meer worden beïnvloed als ze worden onderbroken. Als langlopende taken controlepunten implementeren om voortgang op te slaan tijdens het uitvoeren, wordt de impact van onderbreking verminderd. Taken met kortere uitvoeringstijden werken meestal het beste met VM's met een lage prioriteit, omdat de impact van onderbreking veel minder is.

-   Langlopende MPI-taken die meerdere VM's gebruiken, zijn niet geschikt om VM's met lage prioriteit te gebruiken, omdat één vm vooraf kan leiden tot het opnieuw uitvoeren van de hele taak.

Enkele voorbeelden van batch processing use cases die goed geschikt zijn voor het gebruik van VM's met lage prioriteit zijn:

-   **Ontwikkeling en testen**: Met name bij de ontwikkeling van grootschalige oplossingen kunnen aanzienlijke besparingen worden gerealiseerd. Alle soorten testen kunnen hiervan profiteren, maar grootschalige belastingtesten en regressietests zijn geweldig gebruik.

-   **Aanvulling van on-demand capaciteit**: VM's met lage prioriteit kunnen worden gebruikt als aanvulling op reguliere speciale VM's - indien beschikbaar, kunnen taken schalen en dus sneller voltooien voor lagere kosten; indien niet beschikbaar, blijft de basislijn van dedicated VM's beschikbaar.

-   **Flexibele taakuitvoeringstijd**: Als er flexibiliteit is in de tijd dat taken moeten worden voltooid, dan kunnen potentiële capaciteitsdalingen worden getolereerd; Echter, met de toevoeging van lage prioriteit VM's taken vaak sneller en voor een lagere kosten.

Batchgroepen kunnen worden geconfigureerd om vm's met een lage prioriteit op een aantal manieren te gebruiken, afhankelijk van de flexibiliteit in de uitvoeringstijd van taken:

-   VM's met lage prioriteit kunnen uitsluitend in een pool worden gebruikt. In dit geval herstelt Batch elke vooringenomen capaciteit indien beschikbaar. Deze configuratie is de goedkoopste manier om taken uit te voeren, omdat alleen VM's met lage prioriteit worden gebruikt.

-   VM's met lage prioriteit kunnen worden gebruikt in combinatie met een vaste basislijn van dedicated VM's. Het vaste aantal speciale VM's zorgt ervoor dat er altijd enige capaciteit is om een taak vooruit te houden.

-   Er kan een dynamische mix van speciale en lage prioriteit VM's, zodat de goedkopere low-priority VM's worden uitsluitend gebruikt wanneer beschikbaar, maar de full-priced dedicated VM's worden opgeschaald wanneer dat nodig is. Deze configuratie houdt een minimale hoeveelheid capaciteit beschikbaar om de taken vooruit te helpen.

## <a name="batch-support-for-low-priority-vms"></a>Batchondersteuning voor VM's met lage prioriteit

Azure Batch biedt verschillende mogelijkheden waarmee u eenvoudig vm's met lage prioriteit gebruiken en profiteren:

-   Batchpools kunnen zowel speciale VM's als VM's met lage prioriteit bevatten. Het nummer van elk type VM kan worden opgegeven wanneer een groep wordt gemaakt of op elk gewenst moment voor een bestaande groep wordt gewijzigd, met behulp van de expliciete bewerking voor het formaat of het gebruik van automatische schaal. Taak- en taakindiening kan ongewijzigd blijven, ongeacht de VM-typen in de groep. U ook een groep configureren om VM's met een lage prioriteit volledig te gebruiken om taken zo goedkoop mogelijk uit te voeren, maar speciale VM's optedraaien als de capaciteit onder een minimumdrempel daalt, om taken draaiende te houden.

-   Batchgroepen zoeken automatisch het doelaantal VM's met lage prioriteit. Als VM's worden voorkomen, probeert Batch de verloren capaciteit te vervangen en terug te keren naar het doel.

-   Wanneer taken worden onderbroken, detecteert batch taken en wordt deze automatisch opnieuw geherplaatst.

-   Vm's met lage prioriteit hebben een afzonderlijk vCPU-quotum dat verschilt van het chassis voor speciale VM's. 
    Het quotum voor VM's met lage prioriteit is hoger dan het quotum voor speciale VM's, omdat VM's met lage prioriteit minder kosten. Zie [Batchservicequota en -limieten voor](batch-quota-limit.md#resource-quotas)meer informatie .    

> [!NOTE]
> VM's met lage prioriteit worden momenteel niet ondersteund voor Batch-accounts die zijn gemaakt in [de gebruikersabonnementsmodus.](batch-api-basics.md#account)
>

## <a name="create-and-update-pools"></a>Groepen maken en bijwerken

Een batchgroep kan zowel speciale als vm's met lage prioriteit bevatten (ook wel compute nodes genoemd). U het doelaantal compute nodes instellen voor zowel speciale als vm's met lage prioriteit. Het doelaantal knooppunten geeft het aantal VM's op dat u in de groep wilt hebben.

Bijvoorbeeld als u een groep wilt maken met Azure-cloudserviceVM's met een doel van 5 speciale VM's en 20 VM's met lage prioriteit:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Ga als volgt te werk om een pool te maken met azure virtuele machines (in dit geval Linux VM's) met een doel van 5 dedicated VM's en 20 VM's met lage prioriteit:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

U het huidige aantal knooppunten voor zowel speciale als vm's met lage prioriteit krijgen:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Poolknooppunten hebben een eigenschap om aan te geven of het knooppunt een speciale of lage prioriteit vm is:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Wanneer een of meer knooppunten in een groep worden voorkomen, retourneert een bewerking met lijstknooppunten op de groep nog steeds die knooppunten. Het huidige aantal knooppunten met lage prioriteit blijft ongewijzigd, maar deze knooppunten hebben hun status ingesteld op de status **Vooraf.I** Batch probeert vervangende VM's te vinden en, indien succesvol, gaan de knooppunten door **De statussen maken** en vervolgens De status **starten** voordat ze beschikbaar worden voor taakuitvoering, net als nieuwe knooppunten.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Een pool met VM's met lage prioriteit schalen

Net als bij pools die uitsluitend uit speciale VM's bestaan, is het mogelijk om een pool met VM's met lage prioriteit te schalen door de methode Formaat wijzigen aan te roepen of met behulp van autoscale.

De bewerking voor het wijzigen van het formaat van de groep neemt een tweede optionele parameter die de waarde van **targetLowPriorityNodes bijwerkt:**

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

De formule voor automatische schaal van de groep ondersteunt VM's met lage prioriteit als volgt:

-   U de waarde van de door de service gedefinieerde variabele **$TargetLowPriorityNodes**krijgen of instellen.

-   U de waarde van de door de service gedefinieerde variabele **$CurrentLowPriorityNodes**krijgen.

-   U de waarde van de door de service gedefinieerde variabele **$PreemptedNodeCount**krijgen. 
    Deze variabele retourneert het aantal knooppunten in de vooraf gaande status en stelt u in staat om het aantal toegewezen knooppunten op of neer te schalen, afhankelijk van het aantal vooraf gaande knooppunten dat niet beschikbaar is.

## <a name="jobs-and-tasks"></a>Taken en taken

Taken en taken vereisen weinig extra configuratie voor knooppunten met lage prioriteit; de enige steun is als volgt:

-   De eigenschap JobManagerTask van een taak heeft een nieuwe eigenschap, **AllowLowPriorityNode.** 
    Wanneer deze eigenschap waar is, kan de taak voor taak taak voor taakbeheer worden gepland op een specifiek of knooppunt met lage prioriteit. Als deze eigenschap onjuist is, wordt de taak taak voor taak taak voor taakbeheer alleen gepland op een speciaal knooppunt.

-   Een [omgevingsvariabele](batch-compute-node-environment-variables.md) is beschikbaar voor een taaktoepassing, zodat deze kan bepalen of deze wordt uitgevoerd op een probleem met een lage prioriteit of een specifiek knooppunt. De omgevingsvariabele is AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Voorrang voor behandeling

VM's kunnen af en toe worden voorrang; wanneer voorrang plaatsvindt, doet Batch het volgende:

-   De voorde inteverwillende VM's hebben hun staat bijgewerkt **naar Preempted**.
-   Als taken werden uitgevoerd op de vooraf gaande node VM's, worden deze taken opnieuw in de wachtrij geplaatst en opnieuw uitgevoerd.
-   De VM wordt effectief verwijderd, wat leidt tot verlies van gegevens die lokaal op de VM zijn opgeslagen.
-   De groep probeert voortdurend het doelaantal beschikbare knooppunten met lage prioriteit te bereiken. Wanneer vervangingscapaciteit wordt gevonden, behouden de knooppunten hun id's, maar worden ze opnieuw geïnitialiseerd, terwijl ze door **statussen maken** en **starten** voordat ze beschikbaar zijn voor taakplanning.
-   Voorrangstellingen zijn beschikbaar als statistiek in de Azure-portal.

## <a name="metrics"></a>Metrische gegevens

Er zijn nieuwe statistieken beschikbaar in de [Azure-portal](https://portal.azure.com) voor knooppunten met een lage prioriteit. Deze statistieken zijn:

- Aantal knooppunts met lage prioriteit
- Kerntelling met lage prioriteit 
- Voorrang Node Count

Ga als bedoeld als het gaat om statistieken in de Azure-portal weer te geven:

1. Navigeer naar uw Batch-account in de portal en bekijk de instellingen voor uw Batch-account.
2. Selecteer **Statistieken** in de sectie **Controle.**
3. Selecteer de statistieken die u wenst in de lijst **Beschikbare statistieken.**

![Statistieken voor knooppunten met lage prioriteit](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht met Batch-functies voor ontwikkelaars](batch-api-basics.md), essentiële informatie voor iedereen die Batch wil gaan gebruiken. Het artikel bevat meer gedetailleerde informatie over de Batch-serviceresources zoals groepen, knooppunten, jobs en taken, en de vele API-functies die u tijdens het maken van de Batch-toepassing kunt gebruiken.
* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Begin met het plannen van de verplaatsing van VM's met lage prioriteit naar vm's met een lage prioriteit. Als u VM's met een lage prioriteit gebruikt met **cloudserviceconfiguratiegroepen,** bent u van plan om naar **configuratiegroepen voor virtuele machines** te gaan.
