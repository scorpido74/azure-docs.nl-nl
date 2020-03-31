---
title: Ondersteuningsbeleid voor Azure Kubernetes Service (AKS)
description: Meer informatie over aks-ondersteuningsbeleid (Azure Kubernetes Service), gedeelde verantwoordelijkheid en functies in preview (of alfa of bèta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593577"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Ondersteuningsbeleid voor Azure Kubernetes-service

In dit artikel vindt u informatie over het beleid en de beperkingen voor azure Kubernetes Service (AKS). Het artikel beschrijft ook het beheer van werknemersknooppunt, beheerde beheervlakonderdelen, opensourcecomponenten van derden en beveiligings- of patchbeheer.

## <a name="service-updates-and-releases"></a>Service-updates en -releases

* Zie [AKS-releasenotes voor release-informatie](https://github.com/Azure/AKS/releases).
* Zie [AKS preview-functies en gerelateerde projecten](https://github.com/Azure/AKS/blob/master/previews.md)voor informatie over functies in preview.

## <a name="managed-features-in-aks"></a>Beheerde functies in AKS

Basisinfrastructuur as a service (IaaS) cloudcomponenten, zoals reken- of netwerkcomponenten, geven gebruikers toegang tot besturingselementen op laag niveau en aanpassingsopties. AKS biedt daarentegen een kant-en-klare Kubernetes-implementatie die klanten de gemeenschappelijke set configuraties en mogelijkheden biedt die ze nodig hebben. AKS-klanten hebben beperkte aanpassings-, implementatie- en andere opties. Deze klanten hoeven zich geen zorgen te maken over kubernetes-clusters of beheren deze niet rechtstreeks.

Met AKS krijgt de klant een volledig beheerd *controlevliegtuig.* Het besturingsvlak bevat alle componenten en services die de klant moet bedienen en biedt Kubernetes-clusters aan eindgebruikers. Alle Kubernetes-componenten worden onderhouden en beheerd door Microsoft.

Microsoft beheert en bewaakt de volgende onderdelen via het bedieningsvenster:

* Kubelet- of Kubernetes API-servers
* Etcd of een compatibele key-value store, die Quality of Service (QoS), schaalbaarheid en runtime biedt
* DNS-services (bijvoorbeeld kube-dns of CoreDNS)
* Kubernetes proxy of netwerken

AKS is geen volledig beheerde clusteroplossing. Sommige onderdelen, zoals werknemersknooppunten, hebben *gedeelde verantwoordelijkheid,* waarbij gebruikers moeten helpen bij het onderhouden van het AKS-cluster. Gebruikersinvoer is bijvoorbeeld vereist om een besturingssysteem (OS) beveiligingspatch (worker node) toe te passen.

De services worden *beheerd* in de zin dat Microsoft en het AKS-team implementeren, werken en verantwoordelijk zijn voor de beschikbaarheid en functionaliteit van de service. Klanten kunnen deze beheerde onderdelen niet wijzigen. Microsoft beperkt de aanpassing om een consistente en schaalbare gebruikerservaring te garanderen. Zie [AKS Engine](https://github.com/Azure/aks-engine)voor een volledig aanpasbare oplossing.

> [!NOTE]
> AKS-werkknooppunten worden in de Azure-portal weergegeven als reguliere Azure IaaS-resources. Maar deze virtuele machines worden geïmplementeerd in een aangepaste\\Azure-brongroep (vooraf vastgelegd met MC *). Het is mogelijk om AKS-werkknooppunten te wijzigen. U bijvoorbeeld Secure Shell (SSH) gebruiken om AKS-werkknooppunten te wijzigen op de manier waarop u normale virtuele machines wijzigt (u de basis-os-afbeelding echter niet wijzigen en wijzigingen blijven mogelijk bestaan door een update of reboot) en u andere Azure-bronnen aan AKS koppelen werknemersknooppunten. Maar wanneer u wijzigingen *aanbrengt op het niveau van bandbeheer en -aanpassing,* kan het AKS-cluster niet meer te ondersteunen zijn. Vermijd het wijzigen van werknemersknooppunten, tenzij Microsoft Support u ertoe aangeeft wijzigingen aan te brengen.

Als u niet-ondersteunde bewerkingen uitgeeft zoals hierboven gedefinieerd, zoals uit de toewijzing van de band van alle agentknooppunten, wordt het cluster niet ondersteund. AKS behoudt zich het recht voor om besturingsvlakken te archiveren die zijn geconfigureerd in de ondersteuningsrichtlijnen voor langere perioden die gelijk zijn aan en langer dan 30 dagen. AKS onderhoudt back-ups van cluster-e-ups en kan het cluster gemakkelijk opnieuw toewijzen. Deze herverdeling kan worden gestart door elke PUT-bewerking die het cluster weer in ondersteuning brengt, zoals een upgrade of schaal naar actieve agentknooppunten.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

Wanneer een cluster wordt gemaakt, definieert de klant de Kubernetes-worker-knooppunten die AKS maakt. Klantworkloads worden uitgevoerd op deze knooppunten. Klanten bezitten en kunnen de werknemersknooppunten bekijken of wijzigen.

Omdat klantclusterknooppunten privécode uitvoeren en gevoelige gegevens opslaan, heeft Microsoft Support slechts op beperkte manieren toegang tot deze gegevens. Microsoft Support kan zich niet aanmelden bij, opdrachten uitvoeren of logboeken voor deze knooppunten bekijken zonder uitdrukkelijke toestemming of hulp van de klant.

Omdat werknemersknooppunten gevoelig zijn, houdt Microsoft er alles aan om hun achtergrondbeheer te beperken. In veel gevallen blijft uw werkbelasting worden uitgevoerd, zelfs als de Kubernetes-hoofdknooppunten, enz., en andere door Microsoft beheerde componenten mislukken. Onzorgvuldig gewijzigde werknemersknooppunten kunnen gegevens- en workloads verliezen en kan het cluster onondersteunbaar maken.

## <a name="aks-support-coverage"></a>DEKKING AKS-ondersteuning

Microsoft biedt technische ondersteuning voor het volgende:

* Connectiviteit met alle Kubernetes-componenten die de Kubernetes-service biedt en ondersteunt, zoals de API-server.
* Beheer, uptime, QoS en bewerkingen van Kubernetes control plane services (Kubernetes master nodes, API server, etcd en kube-dns, bijvoorbeeld).
* Etcd. Ondersteuning omvat geautomatiseerde, transparante back-ups van alle geëtste gegevens om de 30 minuten voor rampenplanning en clusterstatusherstel. Deze back-ups zijn niet direct beschikbaar voor klanten of gebruikers. Ze zorgen voor betrouwbaarheid en consistentie van gegevens.
* Eventuele integratiepunten in het Azure-stuurprogramma voor de cloudprovider voor Kubernetes. Deze omvatten integraties in andere Azure-services, zoals load balancers, permanente volumes of netwerken (Kubernetes en Azure CNI).
* Vragen of problemen met betrekking tot het aanpassen van besturingsvlakcomponenten zoals de Kubernetes API-server, etcd en kube-dns.
* Problemen met netwerken, zoals Azure CNI, kubenet of andere problemen met netwerktoegang en functionaliteit. Problemen kunnen onder meer DNS-resolutie, pakketverlies, routering, enzovoort. Microsoft ondersteunt verschillende netwerkscenario's:
  * Kubenet (basic) en advanced networking (Azure CNI) binnen het cluster en de bijbehorende componenten
  * Connectiviteit met andere Azure-services en -toepassingen
  * Invallende controllers en invallende of load balancerconfiguraties
  * Netwerkprestaties en latentie

Microsoft biedt geen technische ondersteuning voor het volgende:

* Vragen over het gebruik van Kubernetes. Microsoft Support geeft bijvoorbeeld geen advies over het maken van aangepaste ingress-controllers, het gebruik van toepassingsworkloads of het toepassen van softwarepakketten of hulpprogramma's van derden of open-source software.
  > [!NOTE]
  > Microsoft Support kan advies geven over AKS-clusterfunctionaliteit, -aanpassing en -afstemming (bijvoorbeeld problemen en procedures voor Kubernetes-bewerkingen).
* Open-source projecten van derden die niet worden geleverd als onderdeel van het Kubernetes-besturingsvlak of worden geïmplementeerd met AKS-clusters. Deze projecten kunnen zijn istio, Helm, gezant, of anderen.
  > [!NOTE]
  > Microsoft kan de beste ondersteuning bieden voor open-source projecten van derden, zoals Helm en Kured. Waar de opensourcetool van derden integreert met de Kubernetes Azure-cloudprovider of andere AKS-specifieke bugs, ondersteunt Microsoft voorbeelden en toepassingen uit Microsoft-documentatie.
* Closed-source software van derden. Deze software kan beveiligingsscantools en netwerkapparaten of software bevatten.
* Problemen met multicloud- of multivendor-build-outs. Microsoft biedt bijvoorbeeld geen ondersteuning voor problemen met betrekking tot het uitvoeren van een federatieve multipublic cloudleverancieroplossing.
* Andere netwerkaanpassingen dan die in de [AKS-documentatie](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft ondersteunt problemen en bugs met betrekking tot netwerkbeveiligingsgroepen (NSG's). Microsoft Support kan bijvoorbeeld vragen beantwoorden over een NSG-storing of een onverwacht NSG- of load balancer-gedrag.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS-ondersteuningsdekking voor werknemersknooppunten

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft-verantwoordelijkheden voor AKS-werkknooppunten

Microsoft en klanten delen de verantwoordelijkheid voor Kubernetes worker nodes waar:

* De basis-OS-afbeelding heeft toevoegingen vereist (zoals monitoring- en netwerkagents).
* De werknemersknooppunten ontvangen automatisch OS-patches.
* Problemen met de Componenten van kubernetes-besturingsvlakdie op de werknemersknooppunten worden uitgevoerd, worden automatisch verholpen. Onderdelen zijn onder andere:
  * Kube-proxy
  * Netwerktunnels die communicatiepaden bieden naar de hoofdcomponenten van Kubernetes
  * Kubelet
  * Docker of Moby daemon

> [!NOTE]
> Als een onderdeel van een besturingsvlak op een werkknooppunt niet operationeel is, moet het AKS-team mogelijk afzonderlijke onderdelen of het hele werkknooppunt opnieuw opstarten. Deze rebootbewerkingen zijn geautomatiseerd en bieden automatische herstel voor veelvoorkomende problemen. Deze reboots vinden alleen plaats op _knooppuntniveau_ en niet op het cluster, tenzij er een noodonderhoud of -storing is.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Klantverantwoordelijkheden voor AKS-werkknooppunten

Microsoft start werknemersknooppunten niet automatisch opnieuw op om patches op OS-niveau toe te passen. Hoewel OS-patches worden geleverd aan de werknemersknooppunten, is de *klant* verantwoordelijk voor het opnieuw opstarten van de werknemersknooppunten om de wijzigingen toe te passen. Gedeelde bibliotheken, daemons zoals solid-state hybride aandrijving (SSHD) en andere componenten op het niveau van het systeem of besturingssysteem worden automatisch gepatcht.

Klanten zijn verantwoordelijk voor het uitvoeren van Kubernetes-upgrades. Ze kunnen upgrades uitvoeren via het Azure-configuratiescherm of het Azure CLI. Dit geldt voor updates die beveiligings- of functionaliteitsverbeteringen aan Kubernetes bevatten.

> [!NOTE]
> Omdat AKS een *beheerde service*is, zijn de einddoelen het verwijderen van de verantwoordelijkheid voor patches, updates en logboekverzamelingen om het servicebeheer completer en hands-off te maken. Naarmate de capaciteit van de service voor end-to-end beheer toeneemt, kunnen toekomstige releases bepaalde functies weglaten (bijvoorbeeld opnieuw opstarten van knooppunt's en automatisch patchen).

### <a name="security-issues-and-patching"></a>Beveiligingsproblemen en patchen

Als er een beveiligingslek wordt gevonden in een of meer onderdelen van AKS, patcht het AKS-team alle betrokken clusters om het probleem te beperken. Als alternatief zal het team gebruikers upgradebegeleiding geven.

Voor werknemersknooppunten die van invloed zijn op een beveiligingslek, als er een patch met nul downtime beschikbaar is, past het AKS-team die patch toe en stelt het gebruikers op de hoogte van de wijziging.

Wanneer een beveiligingspatch opnieuw moet worden opgestart, stelt Microsoft klanten op de hoogte van deze vereiste. De klant is verantwoordelijk voor het opnieuw opstarten of bijwerken om de clusterpatch te krijgen. Als gebruikers de patches niet toepassen volgens AKS-richtlijnen, blijft hun cluster kwetsbaar voor het beveiligingsprobleem.

### <a name="node-maintenance-and-access"></a>Onderhoud en toegang tot knooppunt

Werknemersknooppunten zijn een gedeelde verantwoordelijkheid en zijn eigendom van klanten. Hierdoor hebben klanten de mogelijkheid om zich aan te melden bij hun werknemersknooppunten en mogelijk schadelijke wijzigingen aan te brengen, zoals kernelupdates en het installeren of verwijderen van pakketten.

Als klanten destructieve wijzigingen aanbrengen of ervoor zorgen dat onderdelen van het controlevlak offline gaan of niet-functioneel worden, detecteert AKS deze fout en herstelt het werknemersknooppunt automatisch naar de vorige werkstatus.

Hoewel klanten zich kunnen aanmelden bij en werknemersknooppunten kunnen wijzigen, wordt dit afgeraden omdat wijzigingen een cluster niet te ondersteunen kunnen maken.

## <a name="network-ports-access-and-nsgs"></a>Netwerkpoorten, toegang en NSGs

Als managed service heeft AKS specifieke netwerk- en connectiviteitsvereisten. Deze eisen zijn minder flexibel dan de vereisten voor normale IaaS-componenten. In AKS kunnen bewerkingen zoals het aanpassen van NSG-regels, het blokkeren van een specifieke poort (bijvoorbeeld met behulp van firewallregels die uitgaande poort 443 blokkeren) en het whitelisten van URL's uw cluster onschendbaar maken.

> [!NOTE]
> Op dit moment u met AKS het uitgaande verkeer van uw cluster niet volledig vergrendelen. Als u de lijst met URL's en poorten wilt beheren die uw cluster kan gebruiken voor uitgaand verkeer, zie [limietverkeer](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Niet-ondersteunde alpha- en bètaKubernetes-functies

AKS ondersteunt alleen stabiele functies binnen het upstream Kubernetes-project. Tenzij anders gedocumenteerd, ondersteunt AKS geen alfa- en bètafuncties die beschikbaar zijn in het upstream Kubernetes-project.

In twee scenario's kunnen alfa- of bètafuncties worden uitgerold voordat ze algemeen beschikbaar zijn:

* Klanten hebben een ontmoeting gehad met de AKS-product-, ondersteunings- of engineeringteams en zijn gevraagd om deze nieuwe functies uit te proberen.
* Deze functies zijn [ingeschakeld door een functievlag.](https://github.com/Azure/AKS/blob/master/previews.md) Klanten moeten zich expliciet aanmelden om deze functies te gebruiken.

## <a name="preview-features-or-feature-flags"></a>Voorbeeld van functies of functievlaggen bekijken

Voor functies en functionaliteit waarvoor uitgebreide tests en feedback van gebruikers nodig zijn, brengt Microsoft nieuwe preview-functies of functies achter een functievlag uit. Beschouw deze functies als prerelease- of bètafuncties.

Preview-functies of functies voor functies zijn niet bedoeld voor productie. Voortdurende veranderingen in API's en gedrag, bugfixes en andere wijzigingen kunnen resulteren in instabiele clusters en downtime.

Functies in openbare preview vallen onder 'best effort' ondersteuning omdat deze functies in preview zijn en niet bedoeld zijn voor productie en alleen tijdens kantooruren worden ondersteund door de technische ondersteuningsteams van DE AKS. Zie voor meer informatie:

* [Veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Preview-functies worden van kracht op *Azure-abonnementsniveau.* Installeer geen preview-functies op een productieabonnement. Bij een productieabonnement kunnen preview-functies het standaard-API-gedrag wijzigen en van invloed zijn op reguliere bewerkingen.

## <a name="upstream-bugs-and-issues"></a>Upstream bugs en problemen

Gezien de snelheid van ontwikkeling in het upstream Kubernetes-project ontstaan er steevast bugs. Sommige van deze bugs kunnen niet worden gepatcht of gewerkt rond binnen het AKS-systeem. In plaats daarvan vereisen bugfixes grotere patches voor upstream-projecten (zoals Kubernetes, node- of worker-besturingssystemen en kernels). Voor onderdelen waarvan Microsoft eigenaar is (zoals de Azure-cloudprovider), zijn AKS- en Azure-personeel toegewijd aan het oplossen van problemen stroomopwaarts in de community.

Wanneer een probleem met de technische ondersteuning wordt veroorzaakt door een of meer upstreambugs, zullen AKS-ondersteunings- en engineeringteams:

* Identificeer en koppel de upstream bugs aan ondersteunende details om uit te leggen waarom dit probleem van invloed is op uw cluster of workload. Klanten ontvangen links naar de vereiste repositories, zodat ze de problemen kunnen bekijken en kunnen zien wanneer een nieuwe release oplossingen biedt.
* Zorg voor mogelijke oplossingen of oplossingen. Als het probleem kan worden verholpen, wordt een [bekend probleem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) ingediend in de AKS-repository. De bekende indiening van het probleem legt uit:
  * Het probleem, inclusief links naar upstream bugs.
  * De tijdelijke oplossing en details over een upgrade of een andere volharding van de oplossing.
  * Ruwe tijdlijnen voor de opname van het probleem, gebaseerd op de upstream release cadans.
