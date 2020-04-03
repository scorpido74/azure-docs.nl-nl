---
title: Concepten - Kubernetes basics for Azure Kubernetes Services (AKS)
description: Leer de basiscluster- en workloadcomponenten van Kubernetes en hoe deze zich verhouden tot functies in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 019c886aba1c8fe34211e73e4d960b14e79303b9
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617445"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes kernconcepten voor Azure Kubernetes Service (AKS)

Naarmate toepassingsontwikkeling zich verplaatst naar een containergebaseerde aanpak, is het belangrijk om resources te orkestreren en te beheren. Kubernetes is het toonaangevende platform dat de mogelijkheid biedt om betrouwbare planning van fouttolerante toepassingsworkloads te bieden. Azure Kubernetes Service (AKS) is een beheerd Kubernetes-aanbod dat de implementatie en beheer van containertoepassingen verder vereenvoudigt.

Dit artikel introduceert de belangrijkste Kubernetes-infrastructuurcomponenten zoals het *besturingsvlak,* *knooppunten*en *knooppuntgroepen*. Workloadresources zoals *pods,* *implementaties*en *sets* worden ook geïntroduceerd, samen met het groeperen van resources in *naamruimten*.

## <a name="what-is-kubernetes"></a>Wat is Kubernetes?

Kubernetes is een snel evoluerend platform dat containergebaseerde applicaties en de bijbehorende netwerk- en opslagcomponenten beheert. De focus ligt op de toepassingsworkloads, niet op de onderliggende infrastructuurcomponenten. Kubernetes biedt een declaratieve benadering van implementaties, ondersteund door een robuuste set API's voor beheerbewerkingen.

U moderne, draagbare, op microservices gebaseerde toepassingen bouwen en uitvoeren die profiteren van Kubernetes die de beschikbaarheid van deze toepassingscomponenten orkestreert en beheert. Kubernetes ondersteunt zowel stateloze als stateful applicaties naarmate teams vooruitgang boeken door de adoptie van op microservices gebaseerde applicaties.

Als open platform u met Kubernetes uw toepassingen bouwen met uw favoriete programmeertaal, besturingssysteem, bibliotheken of berichtenbus. Bestaande tools voor continue integratie en continue levering (CI/CD) kunnen worden geïntegreerd met Kubernetes om releases te plannen en te implementeren.

Azure Kubernetes Service (AKS) biedt een beheerde Kubernetes-service die de complexiteit voor implementatie- en kernbeheertaken vermindert, inclusief het coördineren van upgrades. Het AKS-besturingsvlak wordt beheerd door het Azure-platform en u betaalt alleen voor de AKS-knooppunten waarop uw toepassingen worden uitgevoerd. AKS is gebouwd op de top van de open-source Azure Kubernetes Service Engine[(aks-engine).][aks-engine]

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-clusterarchitectuur

Een Kubernetes-cluster is verdeeld in twee componenten:

- *Controlevlakknooppunten* bieden de belangrijkste Kubernetes-services en orkestratie van toepassingsworkloads.
- *Met knooppunten* worden de toepassingsworkloads uitgevoerd.

![Kubernetes-besturingsvlak en knooppuntcomponenten](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Controlevliegtuig

Wanneer u een AKS-cluster maakt, wordt automatisch een besturingsvlak gemaakt en geconfigureerd. Dit besturingsvlak wordt geleverd als een beheerde Azure-bron die is geabstraheerd van de gebruiker. Er zijn geen kosten verbonden aan het besturingsvlak, alleen voor de knooppunten die deel uitmaken van het AKS-cluster.

Het besturingsvlak bevat de volgende basiscomponenten van Kubernetes:

- *kube-apiserver* - De API-server is hoe de onderliggende Kubernetes API's worden blootgesteld. Deze component biedt de interactie voor `kubectl` beheertools, zoals of het Kubernetes-dashboard.
- *etcd* - Om de status van uw Kubernetes cluster en configuratie te behouden, is de zeer beschikbare *etcd* een belangrijke waardeopslag binnen Kubernetes.
- *kube-scheduler* - Wanneer u toepassingen maakt of schaalt, bepaalt de scheduler welke knooppunten de werkbelasting kunnen uitvoeren en start deze.
- *kube-controller-manager* - Controller Manager houdt toezicht op een aantal kleinere controllers die acties uitvoeren, zoals het repliceren van pods en het verwerken van knooppuntbewerkingen.

AKS biedt een controlevlak met één tenant, met een speciale API-server, Scheduler, enz. U definieert het aantal en de grootte van de knooppunten en het Azure-platform configureert de beveiligde communicatie tussen het besturingsvlak en knooppunten. Interactie met het besturingsvlak vindt plaats `kubectl` via Kubernetes API's, zoals of het Kubernetes-dashboard.

Dit beheerde controlevlak betekent dat u geen componenten hoeft te configureren zoals een zeer beschikbare *etcd-winkel,* maar het betekent ook dat u het besturingsvlak niet rechtstreeks openen. Upgrades naar Kubernetes worden georkestreerd via de Azure CLI- of Azure-portal, waarmee het besturingsvlak en vervolgens de knooppunten worden ge-overgezet. Als u mogelijke problemen wilt oplossen, u de logboeken van het besturingsvlak bekijken via Azure Monitor-logboeken.

Als u het besturingsvlak op een bepaalde manier moet configureren of er directe toegang tot nodig hebt, u uw eigen Kubernetes-cluster implementeren met [aks-engine.][aks-engine]

Zie [Aanbevolen procedures voor clusterbeveiliging en upgrades in AKS voor][operator-best-practices-cluster-security]bijbehorende aanbevolen procedures.

## <a name="nodes-and-node-pools"></a>Knooppunten en knooppuntgroepen

Als u uw toepassingen en ondersteunende services wilt uitvoeren, hebt u een *Kubernetes-knooppunt*nodig. Een AKS-cluster heeft een of meer knooppunten, een virtuele Azure-machine (VM) waarop de componenten van het Kubernetes-knooppunt en de runtime van de container worden uitgevoerd:

- Het `kubelet` is de Kubernetes-agent die de orchestration-aanvragen van het controlevlak verwerkt en de planning van het uitvoeren van de gevraagde containers.
- Virtuele netwerken worden afgehandeld door de *kube-proxy* op elk knooppunt. De proxy leidt het netwerkverkeer en beheert IP-adressering voor services en pods.
- De *runtime van* de container is de component waarmee containerized toepassingen kunnen worden uitgevoerd en kunnen communiceren met extra bronnen, zoals het virtuele netwerk en de opslag. In AKS wordt Moby gebruikt als de containerruntime.

![Azure virtuele machine en ondersteunende bronnen voor een Kubernetes-knooppunt](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

De Azure VM-grootte voor uw knooppunten bepaalt hoeveel CPU's, hoeveel geheugen en de beschikbare grootte en het type opslag (zoals krachtige SSD of gewone hdd). Als u op een behoefte aan toepassingen verwacht waarvoor grote hoeveelheden CPU en geheugen of krachtige opslag nodig zijn, plant u de grootte van het knooppunt dienovereenkomstig. U ook het aantal knooppunten in uw AKS-cluster uitschalen om aan de vraag te voldoen.

In AKS is de VM-afbeelding voor de knooppunten in uw cluster momenteel gebaseerd op Ubuntu Linux of Windows Server 2019. Wanneer u een AKS-cluster maakt of het aantal knooppunten schaalt, maakt het Azure-platform het gevraagde aantal VM's en configureert het deze. Er is geen handmatige configuratie voor u uit te voeren. Agentknooppunten worden gefactureerd als standaard virtuele machines, dus eventuele kortingen die u hebt op de VM-grootte die u gebruikt (inclusief [Azure-reserveringen)][reservation-discounts]worden automatisch toegepast.

Als u een ander hostbesturingssysteem, containerruntime of aangepaste pakketten moet gebruiken, u uw eigen Kubernetes-cluster implementeren met [aks-engine.][aks-engine] De `aks-engine` upstream releases functies en biedt configuratie-opties voordat ze officieel worden ondersteund in AKS clusters. Als u bijvoorbeeld een andere containerruntime dan Moby wilt `aks-engine` gebruiken, u een Kubernetes-cluster configureren en implementeren dat aan uw huidige behoeften voldoet.

### <a name="resource-reservations"></a>Resourcereserveringen

Knooppuntbronnen worden door AKS gebruikt om het knooppunt te laten functioneren als onderdeel van uw cluster. Dit kan leiden tot een discrepantie tussen de totale resources van uw knooppunt en de resources die kunnen worden toegewezen wanneer deze worden gebruikt in AKS. Dit is belangrijk om op te merken bij het instellen van aanvragen en limieten voor geïmplementeerde pods van de gebruiker.

Ga als lid op zoek naar de toewijzingsresources van een knooppunt:
```kubectl
kubectl describe node [NODE_NAME]

```

Om de prestaties en functionaliteit van knooppunten te behouden, worden resources op elk knooppunt door AKS gereserveerd. Naarmate een knooppunt groter wordt in resources, wordt de resourcereservering groter als gevolg van een hogere hoeveelheid geïmplementeerde gebruikerspods die beheer nodig hebben.

>[!NOTE]
> Als u AKS-add-ons zoals Container Insights (OMS) gebruikt, worden extra knooppuntbronnen verbruikt.

- **CPU** - gereserveerde CPU is afhankelijk van knooppunttype en clusterconfiguratie, wat kan leiden tot minder toewijsbare CPU vanwege het uitvoeren van extra functies

| CPU-cores op host | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-gereserveerd (millicores)|60|100|140|180|260|420|740|

- **Geheugen** - geheugen dat door AKS wordt gebruikt, bevat de som van twee waarden.

1. De kubelet daemon is geïnstalleerd op alle Kubernetes agent nodes om het maken en beëindigen van containers te beheren. Standaard op AKS heeft deze daemon de volgende uitzettingsregel: *memory.available<750Mi*, wat betekent dat een knooppunt altijd ten minste 750 Mi moet zijn toewijsbaar te allen tijde.  Wanneer een host onder die drempel van het beschikbare geheugen ligt, beëindigt de kubelet een van de lopende pods om geheugen op de hostmachine vrij te maken en te beschermen. Dit is een reactieve actie zodra het beschikbare geheugen afneemt tot boven de 750Mi-drempelwaarde.

2. De tweede waarde is een regressieve snelheid van geheugenreserveringen voor de kubelet daemon om goed te functioneren (kube-gereserveerd).
    - 25% van de eerste 4 GB geheugen
    - 20% van de volgende 4 GB geheugen (tot 8 GB)
    - 10% van de volgende 8 GB geheugen (tot 16 GB)
    - 6% van de volgende 112 GB geheugen (tot 128 GB)
    - 2% van elk geheugen boven 128 GB

De bovenstaande regels voor geheugen- en CPU-toewijzing worden gebruikt om agentknooppunten gezond te houden, waaronder enkele hostingsysteempods die essentieel zijn voor de clusterstatus. Deze toewijzingsregels zorgen er ook voor dat het knooppunt minder toewijsbaar geheugen en CPU rapporteert dan het zou zijn als het geen deel uitmaakte van een Kubernetes-cluster. De bovenstaande resourcereserveringen kunnen niet worden gewijzigd.

Als een knooppunt bijvoorbeeld 7 GB biedt, wordt 34% van het geheugen niet toegewezen bovenop de 750Mi-harde uitzettingsdrempel.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Naast reserveringen voor Kubernetes zelf, reserveert het onderliggende node OS ook een hoeveelheid CPU- en geheugenbronnen om OS-functies te behouden.

Zie [Aanbevolen procedures voor basisplannerfuncties in AKS voor][operator-best-practices-scheduler]bijbehorende aanbevolen procedures.

### <a name="node-pools"></a>Knooppuntzwembaden

Knooppunten van dezelfde configuratie worden gegroepeerd in *knooppuntgroepen.* Een Kubernetes-cluster bevat een of meer knooppuntgroepen. Het beginaantal knooppunten en grootte worden gedefinieerd wanneer u een AKS-cluster maakt, waarmee een *standaardknooppuntgroep*wordt gemaakt. Deze standaardknooppuntgroep in AKS bevat de onderliggende VM's waarop de agentknooppunten worden uitgevoerd.

> [!NOTE]
> Als u ervoor wilt zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee (twee) knooppunten uitvoeren in de standaardknooppuntgroep.

Wanneer u een AKS-cluster schaalt of upgradet, wordt de actie uitgevoerd tegen de standaardknooppuntgroep. U er ook voor kiezen om een specifieke knooppuntgroep te schalen of te upgraden. Voor upgradebewerkingen worden lopende containers gepland op andere knooppunten in de knooppuntgroep totdat alle knooppunten zijn bijgewerkt.

Zie [Meerdere knooppuntgroepen maken en beheren voor een cluster in AKS voor][use-multiple-node-pools]meer informatie over het gebruik van meerdere knooppuntgroepen in AKS.

### <a name="node-selectors"></a>Knooppuntselecties

In een AKS-cluster dat meerdere knooppuntgroepen bevat, moet u de Kubernetes Scheduler mogelijk vertellen welke knooppuntgroep u moet gebruiken voor een bepaalde bron. Invallencontrollers mogen bijvoorbeeld niet worden uitgevoerd op Windows Server-knooppunten (momenteel in preview in AKS). Met knooppuntselecties u verschillende parameters definiëren, zoals het knooppuntbesturingssysteem, om te bepalen waar een pod moet worden gepland.

Het volgende basisvoorbeeld plant een NGINX-instantie op een Linux-knooppunt met behulp van de node selector *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Zie [Aanbevolen procedures voor geavanceerde plannerfuncties in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het bepalen waar pods zijn gepland.

## <a name="pods"></a>Peulen

Kubernetes gebruikt *pods* om een instantie van uw toepassing uit te voeren. Een pod vertegenwoordigt één exemplaar van uw toepassing. Pods hebben meestal een 1:1-toewijzing met een container, hoewel er geavanceerde scenario's zijn waarin een pod meerdere containers kan bevatten. Deze capsules met meerdere containers worden samen gepland op hetzelfde knooppunt en kunnen containers gerelateerde resources delen.

Wanneer u een pod maakt, u *resourceaanvragen* definiëren om een bepaalde hoeveelheid CPU- of geheugenbronnen op te vragen. De Kubernetes Scheduler probeert de pods te plannen om te worden uitgevoerd op een knooppunt met beschikbare resources om aan de aanvraag te voldoen. U ook maximale resourcelimieten opgeven die voorkomen dat een bepaalde pod te veel rekenkracht van het onderliggende knooppunt verbruikt. Een aanbevolen toepassing is het opnemen van resourcelimieten voor alle pods om de Kubernetes Scheduler te helpen begrijpen welke resources nodig en zijn toegestaan.

Zie [Kubernetes pods][kubernetes-pods] en [Kubernetes pod lifecycle][kubernetes-pod-lifecycle]voor meer informatie.

Een pod is een logische bron, maar de container(en) zijn waar de toepassingsworkloads worden uitgevoerd. Pods zijn meestal kortstondige, beschikbare resources en individueel geplande pods missen enkele van de hoge beschikbaarheids- en redundantiefuncties die Kubernetes biedt. In plaats daarvan worden pods meestal geïmplementeerd en beheerd door *Kubernetes-controllers,* zoals de implementatiecontroller.

## <a name="deployments-and-yaml-manifests"></a>Implementaties en YAML-manifesten

Een *implementatie* vertegenwoordigt een of meer identieke pods, beheerd door de Kubernetes Deployment Controller. Een implementatie definieert het aantal *replica's* (pods) dat moet worden gemaakt en de Kubernetes Scheduler zorgt ervoor dat als pods of knooppunten problemen ondervinden, er extra pods worden gepland op gezonde knooppunten.

U implementaties bijwerken om de configuratie van pods, gebruikte containerafbeelding of bijbehorende opslag te wijzigen. De implementatiecontroller voert een bepaald aantal replica's uit en beëindigt deze, maakt replica's van de nieuwe implementatiedefinitie en zet het proces voort totdat alle replica's in de implementatie zijn bijgewerkt.

De meeste statusloze toepassingen in AKS moeten het implementatiemodel gebruiken in plaats van afzonderlijke pods te plannen. Kubernetes kan de status en status van implementaties controleren om ervoor te zorgen dat het vereiste aantal replica's binnen het cluster wordt uitgevoerd. Wanneer u alleen afzonderlijke pods plant, worden de pods niet opnieuw gestart als ze een probleem tegenkomen en worden ze niet opnieuw gepland op gezonde knooppunten als hun huidige knooppunt een probleem ondervindt.

Als een toepassing vereist dat een quorum van instanties altijd beschikbaar is voor managementbeslissingen, wilt u niet dat een updateproces die mogelijkheid verstoort. *Pod-onderbrekingsbudgetten* kunnen worden gebruikt om te bepalen hoeveel replica's in een implementatie kunnen worden verwijderd tijdens een update- of knooppuntupgrade. Als u bijvoorbeeld *5* replica's in uw implementatie hebt, u een podonderbreking van *4* definiëren om slechts één replica tegelijk te laten verwijderen/opnieuw plannen. Net als bij pod resource limieten, een beste praktijk is het definiëren van pod onderbreking budgetten op toepassingen die een minimum aantal replica's vereisen om altijd aanwezig te zijn.

Implementaties worden meestal gemaakt `kubectl create` en `kubectl apply`beheerd met of . Als u een implementatie wilt maken, definieert u een manifestbestand in de yaml-indeling (YAML Ain't Markup Language). In het volgende voorbeeld wordt een basisimplementatie van de NGINX-webserver mogelijk. De implementatie geeft *3* replica's aan die moeten worden gemaakt en dat poort *80* op de container moet worden geopend. Resourceaanvragen en -limieten worden ook gedefinieerd voor CPU en geheugen.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Complexere toepassingen kunnen worden gemaakt door ook diensten zoals load balancers op te neem in het YAML-manifest.

Zie [Kubernetes-implementaties][kubernetes-deployments]voor meer informatie.

### <a name="package-management-with-helm"></a>Package management met Helm

Een gemeenschappelijke benadering van het beheren van applicaties in Kubernetes is met [Helm][helm]. U bestaande openbare *Helm-diagrammen* bouwen en gebruiken die een verpakte versie van toepassingscode en Kubernetes YAML-manifesten bevatten om resources te implementeren. Deze Helmgrafieken kunnen lokaal worden opgeslagen, of vaak in een externe opslagplaats, zoals een [repo van Azure Container Registry Helm][acr-helm].

Als u Helm wilt gebruiken, wordt een servercomponent met de naam *Tiller* geïnstalleerd in uw Kubernetes-cluster. De Tiller beheert de installatie van grafieken binnen het cluster. De Helm-client zelf is lokaal geïnstalleerd op uw computer of kan worden gebruikt in de [Azure Cloud Shell.][azure-cloud-shell] U met de client naar Helm-diagrammen zoeken of maken en deze vervolgens installeren in uw Kubernetes-cluster.

![Helm bevat een clientcomponent en een Tiller-component aan de serverzijde die bronnen in het Kubernetes-cluster maakt](media/concepts-clusters-workloads/use-helm.png)

Zie [Toepassingen met Helm installeren in Azure Kubernetes Service (AKS)][aks-helm]voor meer informatie.

## <a name="statefulsets-and-daemonsets"></a>StatefulSets en DaemonSets

De implementatiecontroller gebruikt de Kubernetes Scheduler om een bepaald aantal replica's uit te voeren op een beschikbaar knooppunt met beschikbare resources. Deze benadering van het gebruik van implementaties kan voldoende zijn voor stateloze toepassingen, maar niet voor toepassingen waarvoor een permanente naamgevingsconventie of -opslag vereist is. Voor toepassingen waarvoor een replica moet bestaan op elk knooppunt of geselecteerde knooppunten binnen een cluster, kijkt de implementatiecontroller niet naar de manier waarop replica's over de knooppunten worden verdeeld.

Er zijn twee Kubernetes-bronnen waarmee u dit soort toepassingen beheren:

- *StatefulSets* - De status van toepassingen behouden buiten een afzonderlijke podlevenscyclus, zoals opslag.
- *DaemonSets* - Zorg voor een lopende instantie op elk knooppunt, vroeg in het Kubernetes bootstrap-proces.

### <a name="statefulsets"></a>StatefulSets

Moderne applicatie-ontwikkeling is vaak gericht op stateless toepassingen, maar *StatefulSets* kunnen worden gebruikt voor stateful toepassingen, zoals toepassingen die databasecomponenten bevatten. Een StatefulSet is vergelijkbaar met een implementatie in die waarin een of meer identieke pods worden gemaakt en beheerd. Replica's in een StatefulSet volgen een sierlijke, sequentiële benadering van implementatie, schaal, upgrades en beëindigingen. Met een StatefulSet (zoals replica's worden verplaatst) blijven de naamgevingsconventie, netwerknamen en opslag bestaan.

U definieert de toepassing in `kind: StatefulSet`YAML-indeling met behulp van , en de StatefulSet Controller verwerkt vervolgens de implementatie en het beheer van de vereiste replica's. Gegevens worden geschreven naar permanente opslag, geleverd door Azure Managed Disks of Azure Files. Bij StatefulSets blijft de onderliggende permanente opslag behouden, zelfs wanneer de StatefulSet wordt verwijderd.

Zie [Kubernetes StatefulSets voor][kubernetes-statefulsets]meer informatie.

Replica's in een StatefulSet zijn gepland en worden uitgevoerd in elk beschikbaar knooppunt in een AKS-cluster. Als u ervoor moet zorgen dat ten minste één pod in uw set op een knooppunt wordt uitgevoerd, u in plaats daarvan een DaemonSet gebruiken.

### <a name="daemonsets"></a>DaemonSets

Voor specifieke behoeften voor logboekverzameling of -bewaking moet u mogelijk een bepaalde pod uitvoeren op alle of geselecteerde knooppunten. Een *DaemonSet* wordt opnieuw gebruikt om een of meer identieke pods te implementeren, maar de DaemonSet-controller zorgt ervoor dat elk knooppunt een instantie van de pod uitvoert.

De DaemonSet-controller kan pods op knooppunten al vroeg in het clusteropstartproces plannen voordat de standaard Kubernetes-planner is gestart. Deze mogelijkheid zorgt ervoor dat de pods in een DaemonSet worden gestart voordat traditionele pods in een implementatie of StatefulSet zijn gepland.

Net als StatefulSets wordt een DaemonSet gedefinieerd als onderdeel `kind: DaemonSet`van een YAML-definitie met behulp van .

Zie [Kubernetes DaemonSets voor][kubernetes-daemonset]meer informatie.

> [!NOTE]
> Als daemonsets de [add-on virtuele knooppunten](virtual-nodes-cli.md#enable-virtual-nodes-addon)gebruiken, worden geen pods gemaakt op het virtuele knooppunt.

## <a name="namespaces"></a>Naamruimten

Kubernetes-bronnen, zoals pods en implementaties, zijn logisch gegroepeerd in een *naamruimte.* Deze groeperingen bieden een manier om een AKS-cluster logisch te verdelen en de toegang te beperken om resources te maken, weer te geven of te beheren. U bijvoorbeeld naamruimten maken voor afzonderlijke bedrijfsgroepen. Gebruikers kunnen alleen communiceren met bronnen binnen hun toegewezen naamruimten.

![Kubernetes-naamruimten om bronnen en toepassingen logisch te verdelen](media/concepts-clusters-workloads/namespaces.png)

Wanneer u een AKS-cluster maakt, zijn de volgende naamruimten beschikbaar:

- *standaard* - Deze naamruimte is waar pods en implementaties standaard worden gemaakt wanneer er geen wordt opgegeven. In kleinere omgevingen u toepassingen rechtstreeks implementeren in de standaardnaamruimte zonder extra logische scheidingen te maken. Wanneer u interactie hebt met de `kubectl get pods`Kubernetes API, zoals met , wordt de standaardnaamruimte gebruikt wanneer er geen is opgegeven.
- *kube-systeem* - Deze naamruimte is waar kernbronnen bestaan, zoals netwerkfuncties zoals DNS en proxy, of het Kubernetes-dashboard. U implementeert doorgaans uw eigen toepassingen niet in deze naamruimte.
- *kube-public* - Deze naamruimte wordt meestal niet gebruikt, maar kan worden gebruikt om resources zichtbaar te maken in het hele cluster en kan door elke gebruiker worden bekeken.

Zie [Kubernetes namespaces voor][kubernetes-namespaces]meer informatie.

## <a name="next-steps"></a>Volgende stappen

Dit artikel behandelt enkele van de belangrijkste Kubernetes-componenten en hoe deze van toepassing zijn op AKS-clusters. Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS toegang en identiteit][aks-concepts-identity]
- [Kubernetes / AKS-beveiliging][aks-concepts-security]
- [Kubernetes / AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes / AKS-opslag][aks-concepts-storage]
- [Kubernetes / AKS-schaal][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
