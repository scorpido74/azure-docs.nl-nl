---
title: Veelgestelde vragen over Microsoft Azure Service Fabric
description: Veelgestelde vragen over Service Fabric, waaronder mogelijkheden, use cases en veelvoorkomende scenario's.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254889"
---
# <a name="commonly-asked-service-fabric-questions"></a>Veelgestelde vragen over Service Fabric

Er zijn veelgestelde vragen over wat Service Fabric kan doen en hoe het moet worden gebruikt. Dit document behandelt veel van die veelgestelde vragen en hun antwoorden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Clusterinstelling en -beheer

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hoe kan ik mijn Service Fabric-clustercertificaat terugdraaien?

Voor het terugdraaien van een upgrade naar uw toepassing is detectie van statusfouten vereist voordat het clusterquorum van uw Service Fabric de wijziging uitvoert. vastgelegde wijzigingen kunnen alleen worden doorgestuurd. Escalation engineer's via Customer Support Services, kan nodig zijn om uw cluster te herstellen, als een niet-bewaakte breken certificaat wijziging is ingevoerd.  [De applicatie-upgrade van Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) past [toepassingsupgradeparameters](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)toe en biedt nul belofte voor downtime-upgrade.  Volgens onze aanbevolen applicatie-upgrade modus gecontroleerd, automatische voortgang door middel van update domeinen is gebaseerd op de status controles passeren, automatisch terugrollen als het bijwerken van een standaard service mislukt.
 
Als uw cluster nog steeds gebruikmaakt van de klassieke eigenschap Certificate Thumbprint in uw Resource Manager-sjabloon, wordt u [aangeraden het cluster te wijzigen van duimafdruk van certificaten naar algemene naam](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)om gebruik te maken van moderne functies voor het beheer van geheimen.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kan ik een cluster maken dat meerdere Azure-regio's of mijn eigen datacenters omvat?

Ja. 

De kernservicestructuurclustertechnologie kan worden gebruikt om machines te combineren die overal ter wereld worden uitgevoerd, zolang ze netwerkconnectiviteit met elkaar hebben. Het bouwen en uitvoeren van een dergelijk cluster kan echter ingewikkeld zijn.

Als u geïnteresseerd bent in dit scenario, raden we u aan om contact op te nemen via de [Service Fabric GitHub Issues List](https://github.com/azure/service-fabric-issues) of via uw ondersteuningsmedewerker om aanvullende richtlijnen te verkrijgen. Het Service Fabric-team werkt aan extra duidelijkheid, begeleiding en aanbevelingen voor dit scenario. 

Enkele dingen om te overwegen: 

1. De clusterbron Service Fabric in Azure is regionaal, evenals de virtuele machineschaalsets waarop het cluster is gebouwd. Dit betekent dat u in het geval van een regionale storing de mogelijkheid verliezen om het cluster te beheren via Azure Resource Manager of de Azure-portal. Dit kan gebeuren, ook al blijft het cluster actief en u er rechtstreeks mee communiceren. Bovendien biedt Azure vandaag de dag niet de mogelijkheid om één virtueel netwerk te hebben dat bruikbaar is voor verschillende regio's. Dit betekent dat voor een cluster met meerdere regio's in Azure [openbare IP-adressen voor elke VM in de VM-schaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) of [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md)vereist zijn. Deze netwerkkeuzes hebben verschillende gevolgen voor de kosten, prestaties en tot op zekere hoogte het ontwerp van toepassingen, dus een zorgvuldige analyse en planning is vereist voordat u zo'n omgeving opstaat.
2. Het onderhoud, beheer en de bewaking van deze machines kunnen ingewikkeld worden, vooral wanneer deze over _verschillende typen_ omgevingen worden verdeeld, zoals tussen verschillende cloudproviders of tussen on-premises resources en Azure. Er moet voor worden gezorgd dat upgrades, monitoring, beheer en diagnostiek worden begrepen voor zowel het cluster als de toepassingen voordat productieworkloads in een dergelijke omgeving worden uitgevoerd. Als u al ervaring hebt met het oplossen van deze problemen in Azure of in uw eigen datacenters, is het waarschijnlijk dat dezelfde oplossingen kunnen worden toegepast bij het uitbouwen of uitvoeren van uw Service Fabric-cluster. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Ontvangen Service Fabric-knooppunten automatisch OS-updates?

U [de functie Automatic Machine Scale Set Automatic OS Image Update](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) Generally Available vandaag nog gebruiken.

Voor clusters die NIET in Azure worden uitgevoerd, hebben we [een toepassing geleverd](service-fabric-patch-orchestration-application.md) om de besturingssystemen onder uw Service Fabric-knooppunten te patchen.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kan ik grote virtuele machineschaalsets gebruiken in mijn SF-cluster? 

**Kort antwoord** - Nee. 

**Long Answer** - Hoewel de grote virtuele machine schaal sets u een virtuele machine schaal ingesteld tot 1000 VM exemplaren schalen, het doet dit door het gebruik van Placement Groups (PG's). Foutdomeinen (FD's) en upgradedomeinen (UD's) zijn alleen consistent binnen een plaatsingsgroep Servicefabric gebruikt FD's en UD's om plaatsingsbeslissingen te nemen van uw servicereplica's/Service-exemplaren. Aangezien de FD's en UD's alleen binnen een plaatsingsgroep vergelijkbaar zijn, kan SF deze niet gebruiken. Als VM1 in PG1 bijvoorbeeld een topologie van FD=0 heeft en VM9 in PG2 een topologie van FD=4 heeft, betekent dit niet dat VM1 en VM2 zich op twee verschillende hardwareracks bevinden, vandaar dat SF de FD-waarden in dit geval niet kan gebruiken om plaatsingsbeslissingen te nemen.

Er zijn andere problemen met grote virtuele machine schaal sets op dit moment, zoals het ontbreken van level-4 Load balancing ondersteuning. Zie voor [meer informatie over grote schaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Wat is de minimale grootte van een Service Fabric-cluster? Waarom kan het niet kleiner zijn?

De minimale ondersteunde grootte voor een Service Fabric-cluster met productieworkloads is vijf knooppunten. Voor dev-scenario's ondersteunen we één knooppunt (geoptimaliseerd voor snelle ontwikkelervaring in Visual Studio) en vijf knooppuntclusters.

We vereisen dat een productiecluster ten minste 5 knooppunten heeft vanwege de volgende drie redenen:
1. Zelfs wanneer er geen gebruikersservices worden uitgevoerd, wordt in een Cluster ServiceFabric een reeks stateful systeemservices uitgevoerd, waaronder de naamgevingsservice en de failovermanagerservice. Deze systeemdiensten zijn essentieel om het cluster operationeel te laten blijven.
2. We plaatsen altijd een replica van een service per knooppunt, dus clustergrootte is de bovengrens voor het aantal replica's dat een service (eigenlijk een partitie) kan hebben.
3. Aangezien een clusterupgrade ten minste één knooppunt naar beneden zal brengen, willen we een buffer van ten minste één knooppunt hebben, daarom willen we dat een productiecluster ten minste twee knooppunten *heeft naast* het absolute minimum. Het absolute minimum is de quorumgrootte van een systeemservice, zoals hieronder wordt uitgelegd.  

We willen dat het cluster beschikbaar is in het licht van gelijktijdige uitval van twee knooppunten. Om een Service Fabric-cluster beschikbaar te laten zijn, moeten de systeemservices beschikbaar zijn. Stateful systeemservices zoals naamgevingsservice en failovermanagerservice, die bijhouden welke services zijn geïmplementeerd in het cluster en waar ze momenteel worden gehost, zijn afhankelijk van een sterke consistentie. Die sterke consistentie hangt op zijn beurt af van de mogelijkheid om een *quorum* te verkrijgen voor een bepaalde update van de status van die services, waarbij een quorum een strikte meerderheid van de replica's (N/2 +1) voor een bepaalde dienst vertegenwoordigt. Dus als we veerkrachtig willen zijn tegen gelijktijdig verlies van twee knooppunten (dus gelijktijdig verlies van twee replica's van een systeemservice), moeten we ClusterSize - QuorumSize >= 2 hebben, waardoor de minimale grootte vijf moet zijn. Om dat te zien, overweeg dan dat het cluster N-knooppunten heeft en er N-replica's van een systeemservice zijn - één op elk knooppunt. De quorumgrootte voor een systeemservice is (N/2 + 1). De bovenstaande ongelijkheid ziet eruit als N - (N/2 + 1) >= 2. Er zijn twee gevallen te overwegen: wanneer N is zelfs en wanneer N is vreemd. Als N gelijk is,\*zeg N = 2 m waar m\*>=\*1, lijkt de ongelijkheid op 2 m - (2 m/2 + 1) >= 2 of m >= 3. Het minimum voor N is 6 en dat wordt bereikt wanneer m = 3. Aan de andere kant, als N vreemd\*is, zeg N = 2 m+1\*waar m >\*= 1, ziet de ongelijkheid eruit als\*2 m+1 - ( (2 m+1)/2 + 1 ) >= 2 of 2 m+1 - (m+1) >= 2 of m >= 2. Het minimum voor N is 5 en dat wordt bereikt wanneer m = 2. Van alle waarden van N die voldoen aan de ongelijkheid clustergrootte - QuorumSize >= 2, is het minimum daarom 5.

Let op, in het bovenstaande argument hebben we aangenomen dat elk knooppunt een replica van een systeemservice heeft, dus de quorumgrootte wordt berekend op basis van het aantal knooppunten in het cluster. Door *TargetReplicaSetSize* te wijzigen, kunnen we echter de quorumgrootte kleiner maken dan (N/2+1), wat de indruk kan geven dat we een cluster kleiner dan 5 knooppunten kunnen hebben en nog steeds 2 extra knooppunten boven de quorumgrootte hebben. In een cluster met 4 node, als we bijvoorbeeld de TargetReplicaSetSize op 3 instellen, is de quorumgrootte op basis van TargetReplicaSetSize (3/2 + 1) of 2, dus hebben we ClusterGrootte - QuorumSize = 4-2 >= 2. We kunnen echter niet garanderen dat de systeemservice op of boven het quorum zal zijn als we tegelijkertijd een paar knooppunten verliezen, het kan zijn dat de twee knooppunten die we verloren twee replica's hostten, dus de systeemservice gaat in quorumverlies (met slechts één replica over) en zal niet meer beschikbaar zijn.

Met die achtergrond, laten we een aantal mogelijke clusterconfiguraties onderzoeken:

**Eén knooppunt:** deze optie biedt geen hoge beschikbaarheid, omdat het verlies van het enkele knooppunt om welke reden dan ook het verlies van het hele cluster betekent.

**Twee knooppunten:** een quorum voor een service die wordt geïmplementeerd op twee knooppunten (N = 2) is 2 (2/2 + 1 = 2). Wanneer een enkele replica verloren gaat, is het onmogelijk om een quorum te maken. Aangezien het uitvoeren van een service-upgrade het tijdelijk afbreken van een replica vereist, is dit geen nuttige configuratie.

**Drie knooppunten**: met drie knooppunten (N=3) is de vereiste om een quorum te maken nog steeds twee knooppunten (3/2 + 1 = 2). Dit betekent dat u een individueel knooppunt verliezen en het quorum behouden, maar het gelijktijdig uitvallen van twee knooppunten zal de systeemservices naar quorumverlies leiden en ervoor zorgen dat het cluster niet meer beschikbaar is.

**Vier knooppunten**: met vier knooppunten (N=4) is de vereiste om een quorum te maken drie knooppunten (4/2 + 1 = 3). Dit betekent dat u een individueel knooppunt verliezen en het quorum behouden, maar het gelijktijdig uitvallen van twee knooppunten zal de systeemservices naar quorumverlies leiden en ervoor zorgen dat het cluster niet meer beschikbaar is.

**Vijf knooppunten**: met vijf knooppunten (N=5) is de vereiste om een quorum te maken nog steeds drie knooppunten (5/2 + 1 = 3). Dit betekent dat u twee knooppunten tegelijk verliezen en het quorum voor de systeemservices behouden.

Voor productieworkloads moet u bestand zijn tegen gelijktijdige uitval van ten minste twee knooppunten (bijvoorbeeld een vanwege clusterupgrade, één vanwege andere redenen), dus er zijn vijf knooppunten vereist.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kan ik mijn cluster 's nachts/in het weekend uitschakelen om kosten te besparen?

In het algemeen niet. Service fabric slaat status op lokale, kortstondige schijven op, wat betekent dat als de virtuele machine naar een andere host wordt verplaatst, de gegevens er niet mee worden verplaatst. Bij normaal gebruik is dat geen probleem, omdat het nieuwe knooppunt wordt bijgewerkt door andere knooppunten. Als u echter alle knooppunten stopt en ze later opnieuw start, is er een aanzienlijke kans dat de meeste knooppunten op nieuwe hosts beginnen en het systeem niet kunnen herstellen.

Als u clusters wilt maken voor het testen van uw toepassing voordat deze wordt geïmplementeerd, raden we u aan deze clusters dynamisch te maken als onderdeel van uw [pijplijn voor continue integratie/continue implementatie.](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hoe upgrade ik mijn besturingssysteem (bijvoorbeeld van Windows Server 2012 naar Windows Server 2016)?

Terwijl we werken aan een verbeterde ervaring, bent u vandaag verantwoordelijk voor de upgrade. U moet de OS-afbeelding op de virtuele machines van het cluster één VM tegelijk upgraden. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan ik gekoppelde gegevensschijven versleutelen in een clusterknooppunttype (virtuele machineschaalset)?
Ja.  Zie [Een cluster maken met gekoppelde gegevensschijven](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) en Azure Disk Encryption for Virtual Machine Scale Sets [voor](../virtual-machine-scale-sets/disk-encryption-overview.md)meer informatie.

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan ik VM's met een lage prioriteit gebruiken in een clusterknooppunttype (virtuele machineschaalset)?
Nee. VM's met lage prioriteit worden niet ondersteund. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Wat zijn de mappen en processen die ik moet uitsluiten bij het uitvoeren van een anti-virusprogramma in mijn cluster?

| **Antivirus uitgesloten mappen** |
| --- |
| Programmabestanden\Microsoft Service Fabric |
| FabricDataRoot (van clusterconfiguratie) |
| FabricLogRoot (van clusterconfiguratie) |

| **Antivirus uitgesloten processen** |
| --- |
| Fabric.exe (Fabric.exe) |
| FabricHost.exe (FabricHost.exe) |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe (FabricGateway.exe) |
| FabricDCA.exe (FabricDCA.exe) |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe (FabricRM.exe) |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Hoe kan mijn toepassing zich verifiëren naar KeyVault om geheimen te krijgen?
Het volgende zijn middelen voor uw toepassing om referenties te verkrijgen voor het verifiëren van KeyVault:

A. Tijdens uw build/packing-taak voor toepassingen u een certificaat in het gegevenspakket van uw SF-app opnemen en dit gebruiken om u te authenticeren naar KeyVault.
B. Voor virtuele machinescale set MSI ingeschakeld hosts, u een eenvoudige PowerShell SetupEntryPoint voor uw SF app om een access token te krijgen [van de MSI eindpunt,](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)en vervolgens [halen uw geheimen uit KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Toepassingsontwerp

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Wat is de beste manier om gegevens op te vragen over partities van een betrouwbare verzameling?

Betrouwbare verzamelingen worden meestal [verdeeld](service-fabric-concepts-partitioning.md) om uitschalen mogelijk te maken voor betere prestaties en doorvoer. Dat betekent dat de toestand voor een bepaalde service kan worden verspreid over tientallen of honderden machines. Als u bewerkingen wilt uitvoeren via die volledige gegevensset, hebt u een aantal opties:

- Maak een service die alle partities van een andere service opvraagt om de vereiste gegevens in te trekken.
- Maak een service die gegevens kan ontvangen van alle partities van een andere service.
- Duw regelmatig gegevens van elke service naar een extern archief. Deze benadering is alleen geschikt als de query's die u uitvoert geen deel uitmaken van uw core business-logica, omdat de gegevens van de externe winkel verouderd zijn.
- U ook gegevens opslaan die query's in alle records rechtstreeks in een gegevensarchief moeten ondersteunen in plaats van in een betrouwbare verzameling. Dit elimineert het probleem met verouderde gegevens, maar maakt het niet mogelijk de voordelen van betrouwbare collecties te benutten.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Wat is de beste manier om gegevens op te vragen bij mijn acteurs?

Acteurs zijn ontworpen om onafhankelijke eenheden van staat en compute, dus het wordt niet aanbevolen om brede query's van actor staat uit te voeren tijdens runtime. Als u een query in de volledige set van actor staat, moet u overwegen:

- Uw actorservices vervangen door stateful betrouwbare services, zodat het aantal netwerkaanvragen om alle gegevens te verzamelen, van het aantal actoren tot het aantal partities in uw service.
- Het ontwerpen van uw acteurs om periodiek hun status naar een externe winkel te duwen voor eenvoudiger query's. Zoals hierboven is deze aanpak alleen levensvatbaar als de query's die u uitvoert niet nodig zijn voor uw runtime-gedrag.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Hoeveel gegevens kan ik opslaan in een betrouwbare verzameling?

Betrouwbare services worden meestal verdeeld, dus het bedrag dat u opslaan, wordt alleen beperkt door het aantal machines dat u in het cluster hebt en de hoeveelheid geheugen die beschikbaar is op die machines.

Stel bijvoorbeeld dat u een betrouwbare verzameling hebt in een service met 100 partities en 3 replica's, waarbij objecten worden opgeslagen die gemiddeld 1 kb groot zijn. Stel nu dat je een 10 machine cluster met 16GB geheugen per machine. Voor eenvoud en conservatief te zijn, ervan uitgaan dat het besturingssysteem en het systeem diensten, de Service Fabric runtime, en uw diensten verbruiken 6GB van dat, waardoor 10GB beschikbaar per machine, of 100 GB voor het cluster.

Met in het achterhoofd dat elk object drie keer moet worden opgeslagen (een primaire en twee replica's), zou je voldoende geheugen voor ongeveer 35 miljoen objecten in uw collectie bij het werken op volle capaciteit. We raden echter aan om bestand te zijn tegen het gelijktijdige verlies van een foutdomein en een upgradedomein, dat ongeveer 1/3 van de capaciteit vertegenwoordigt, en het aantal zou verminderen tot ongeveer 23 miljoen.

Houd er rekening mee dat deze berekening ook van uitgegaan is:

- Dat de verdeling van gegevens over de partities grofweg uniform is of dat u belastingsstatistieken rapporteert aan clusterbronbeheer. Standaard laadt Service Fabric het saldo op basis van het aantal replica's. In het voorgaande voorbeeld zou dat 10 primaire replica's en 20 secundaire replica's op elk knooppunt in het cluster plaatsen. Dat werkt goed voor de belasting die gelijkmatig is verdeeld over de partities. Als de belasting niet gelijk is, moet u de belasting rapporteren, zodat Resource Manager kleinere replica's samen kan verpakken en grotere replica's meer geheugen op een afzonderlijk knooppunt kan laten verbruiken.

- Dat de betrouwbare service in kwestie de enige opslagstatus in het cluster is. Aangezien u meerdere services implementeren in een cluster, moet u rekening houden met de resources die elk moet worden uitgevoerd en de status ervan moet beheren.

- Dat het cluster zelf niet groeit of krimpt. Als u meer machines toevoegt, brengt Service Fabric uw replica's opnieuw in evenwicht om de extra capaciteit te benutten totdat het aantal machines het aantal partities in uw service overtreft, omdat een afzonderlijke replica geen machines kan overspannen. Als u daarentegen de grootte van het cluster vermindert door machines te verwijderen, worden uw replica's strakker verpakt en hebben ze minder totale capaciteit.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Hoeveel gegevens kan ik opslaan in een acteur?

Net als bij betrouwbare services wordt de hoeveelheid gegevens die u in een actorservice opslaan, alleen beperkt door de totale schijfruimte en het geheugen dat beschikbaar is voor de knooppunten in uw cluster. Echter, individuele actoren zijn het meest effectief wanneer ze worden gebruikt om een kleine hoeveelheid van de staat en de bijbehorende bedrijfslogica in kapselen. Als algemene regel moet een individuele actor staat hebben die wordt gemeten in kilobytes.

## <a name="other-questions"></a>Andere vragen

### <a name="how-does-service-fabric-relate-to-containers"></a>Hoe verhoudt Service Fabric zich tot containers?

Containers bieden een eenvoudige manier om diensten en hun afhankelijkheden te verpakken, zodat ze consistent in alle omgevingen werken en op een geïsoleerde manier op één machine kunnen werken. Service Fabric biedt een manier om services te implementeren en te beheren, inclusief [services die zijn verpakt in een container.](service-fabric-containers-overview.md)

### <a name="are-you-planning-to-open-source-service-fabric"></a>Bent u van plan om open-source Service Fabric?

We hebben open-source delen van Service Fabric[(betrouwbare diensten kader,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [betrouwbare actoren kader,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [ASP.NET Core integratie bibliotheken,](https://github.com/Azure/service-fabric-aspnetcore) [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), en Service [Fabric CLI](https://github.com/Azure/service-fabric-cli)) op GitHub en accepteren bijdragen van de gemeenschap aan deze projecten. 

We [hebben onlangs aangekondigd](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) dat we van plan zijn om open-source de Service Fabric runtime. Op dit punt hebben we de [Service Fabric repo](https://github.com/Microsoft/service-fabric/) op GitHub met Linux build en test tools, wat betekent dat je de repo klonen, Service Fabric voor Linux bouwen, basistests uitvoeren, problemen openen en pull-verzoeken indienen. We werken er hard aan om de Windows-buildomgeving ook te laten migreren, samen met een complete CI-omgeving.

Volg de [Service Fabric-blog](https://blogs.msdn.microsoft.com/azureservicefabric/) voor meer details als ze worden aangekondigd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [de belangrijkste servicefabric-concepten](service-fabric-technical-overview.md) en [-aanbevolen procedures](service-fabric-best-practices-overview.md)
