---
title: Ondersteunings beleid voor Azure Kubernetes service (AKS)
description: Meer informatie over het ondersteunings beleid voor Azure Kubernetes service (AKS), de gedeelde verantwoordelijkheid en functies die in Preview zijn (of alpha of Beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 8c1736fcd4356e0778c3b36301b4490764efab76
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240887"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Ondersteunings beleid voor de Azure Kubernetes-service

In dit artikel vindt u informatie over het technische ondersteunings beleid en de beperkingen voor Azure Kubernetes service (AKS). Het artikel bevat ook informatie over het beheer van knoop punten, beheerde beheer vlak onderdelen, open source-onderdelen van derden en beveiligings-of patch beheer.

## <a name="service-updates-and-releases"></a>Service-updates en-releases

* Zie [AKS Release Notes](https://github.com/Azure/AKS/releases)(Engelstalig) voor informatie over de release.
* Zie [AKS preview-functies en gerelateerde projecten](https://github.com/Azure/AKS/blob/master/previews.md)voor meer informatie over de functies van de preview-versie.

## <a name="managed-features-in-aks"></a>Beheerde functies in AKS

IaaS-Cloud onderdelen (Infrastructure as a Service), zoals Compute-of netwerk onderdelen, bieden gebruikers toegang tot besturings elementen op laag niveau en aanpassings opties. AKS biedt daarentegen een kant-en-klare Kubernetes-implementatie waarmee klanten beschikken over de gemeen schappelijke set configuraties en mogelijkheden. AKS-klanten hebben beperkte aanpassing, implementatie en andere opties. Deze klanten hoeven zich geen zorgen te maken over Kubernetes-clusters of ze rechtstreeks te beheren.

Met AKS krijgt de klant een volledig beheerd *besturings vlak*. Het besturings vlak bevat alle onderdelen en services die de klant moet uitvoeren en Kubernetes-clusters aan eind gebruikers kunnen bieden. Alle Kubernetes-onderdelen worden onderhouden en beheerd door micro soft.

Micro soft beheert en bewaakt de volgende onderdelen via het deel venster beheer:

* Kubelet-of Kubernetes-API-servers
* Etcd of een compatibele sleutel-value Store, waarmee u Quality of Service (QoS), schaal baarheid en runtime kunt bieden
* DNS-services (bijvoorbeeld uitvoeren-DNS of CoreDNS)
* Kubernetes-proxy of-netwerken

AKS is geen volledig beheerde cluster oplossing. Sommige onderdelen, zoals worker-knoop punten, hebben *gedeelde verantwoordelijkheid*, waar gebruikers het AKS-cluster moeten onderhouden. Gebruikers invoer is vereist, bijvoorbeeld om een beveiligings patch van een werk knooppunt besturings systeem (OS) toe te passen.

De services worden *beheerd* in de zin dat micro soft en het AKS-team implementeert, werkt en verantwoordelijk is voor de beschik baarheid en functionaliteit van de service. Klanten kunnen deze beheerde onderdelen niet wijzigen. Micro soft beperkt de aanpassing tot een consistente en schaal bare gebruikers ervaring. Zie de [AKS-engine](https://github.com/Azure/aks-engine)voor een volledig aanpas bare oplossing.

> [!NOTE]
> AKS worker-knoop punten worden weer gegeven in de Azure Portal als normale Azure IaaS-resources. Deze virtuele machines worden echter geïmplementeerd in een aangepaste Azure-resource groep (voorafgegaan door MC\\*). Het is mogelijk om AKS worker-knoop punten te wijzigen. U kunt bijvoorbeeld Secure Shell (SSH) gebruiken om AKS worker-knoop punten te wijzigen op de manier waarop u normale virtuele machines wijzigt (u kunt de basis installatie kopie van het besturings systeem niet wijzigen, en wijzigingen worden mogelijk niet persistent gemaakt via een update of opnieuw opstarten) en u kunt andere Azure-resources koppelen aan AKS worker-knoop punten. Maar wanneer u wijzigingen aanbrengt *buiten-band beheer en-aanpassing,* kan het AKS-cluster niet meer worden ondersteund. Vermijd het wijzigen van worker-knoop punten tenzij Microsoft Ondersteuning u wijzigingen aanbrengt.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

Wanneer een cluster wordt gemaakt, definieert de klant de Kubernetes worker-knoop punten die AKS maakt. Werk belastingen voor klanten worden op deze knoop punten uitgevoerd. Klanten zijn eigenaar en kunnen de worker-knoop punten weer geven of wijzigen.

Omdat de cluster knooppunten van de klant persoonlijke code uitvoeren en gevoelige gegevens opslaan, kunnen Microsoft Ondersteuning ze op een beperkte manier benaderen. Microsoft Ondersteuning kunt zich niet aanmelden bij, opdrachten uitvoeren in of Logboeken voor deze knoop punten zonder uitdrukkelijke toestemming of ondersteuning van de klant weer geven.

Omdat worker-knoop punten gevoelig zijn, neemt micro soft er goed aan om het beheer van de achtergrond te beperken. In veel gevallen blijft uw workload actief, zelfs als de Kubernetes-hoofd knooppunten, etcd en andere door micro soft beheerde onderdelen mislukken. Onvoorzichtige gewijzigde werk knooppunten kunnen verlies van gegevens en workloads veroorzaken, en kunnen het cluster niet-ondersteund genereren.

## <a name="aks-support-coverage"></a>AKS-ondersteunings dekking

Micro soft biedt technische ondersteuning voor het volgende:

* Connectiviteit met alle Kubernetes-onderdelen die de Kubernetes-service biedt en ondersteunt, zoals de API-server.
* Beheer, uptime, QoS en bewerkingen van Kubernetes Control vlak-Services (bijvoorbeeld Kubernetes-hoofd knooppunten, API-server, etcd en uitvoeren-DNS).
* Etcd. Ondersteuning omvat geautomatiseerde, transparante back-ups van alle etcd-gegevens om de 30 minuten voor nood planning en herstel van de cluster status. Deze back-ups zijn niet rechtstreeks beschikbaar voor klanten of gebruikers. Ze garanderen de betrouw baarheid en consistentie van gegevens.
* Alle integratie punten in het stuur programma van de Azure-Cloud provider voor Kubernetes. Dit omvat integraties in andere Azure-Services, zoals load balancers, permanente volumes of netwerken (Kubernetes en Azure CNI).
* Vragen of problemen met het aanpassen van beheer vlak onderdelen, zoals de Kubernetes API-server, etcd en uitvoeren-DNS.
* Problemen met netwerken, zoals Azure CNI, kubenet of andere problemen met netwerk toegang en-functionaliteit. Problemen zijn onder andere DNS-omzetting, pakket verlies, route ring, enzovoort. Micro soft biedt ondersteuning voor verschillende netwerk scenario's:
  * Kubenet (Basic) en Advanced Network (Azure CNI) binnen het cluster en de bijbehorende onderdelen
  * Connectiviteit met andere Azure-Services en-toepassingen
  * Inkomend controllers en ingangs-of load balancer configuraties
  * Netwerk prestaties en latentie

Micro soft biedt geen technische ondersteuning voor het volgende:

* Vragen over het gebruik van Kubernetes. Microsoft Ondersteuning biedt bijvoorbeeld geen advies over het maken van aangepaste inkomende controllers, het gebruiken van werk belastingen voor toepassingen of het Toep assen van software pakketten of hulpprogram ma's van derden of open source.
  > [!NOTE]
  > Microsoft Ondersteuning kunt advies geven over de functionaliteit, aanpassing en fijnafstelling van AKS-clusters (bijvoorbeeld Kubernetes-bewerkingen en procedures).
* Open-source projecten van derden die niet worden meegeleverd als onderdeel van het Kubernetes-besturings vlak of worden geïmplementeerd met AKS-clusters. Deze projecten kunnen Istio, helm, Envoy of anderen bevatten.
  > [!NOTE]
  > Micro soft kan de beste ondersteuning bieden voor open-source projecten van derden, zoals helm en Kured. Wanneer het open source-hulp programma van derden is geïntegreerd met de Kubernetes Azure-Cloud provider of andere AKS, ondersteunt micro soft de voor beelden en toepassingen van micro soft-documentatie.
* Afgesloten bron software van derden. Deze software kan beveiligings scan Programma's en netwerk apparaten of-software omvatten.
* Problemen met Build-outs met multiclouds of multivendors. Micro soft biedt bijvoorbeeld geen ondersteuning voor problemen die betrekking hebben op het uitvoeren van een federatieve oplossing voor de leverancier van de Cloud.
* Andere netwerk aanpassingen dan die vermeld in de [AKS-documentatie](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Micro soft biedt ondersteuning voor problemen en fouten die betrekking hebben op netwerk beveiligings groepen (Nsg's). Microsoft Ondersteuning kunt bijvoorbeeld vragen beantwoorden over een NSG-fout bij het bijwerken of een onverwachte NSG of load balancer gedrag.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS-ondersteunings dekking voor worker-knoop punten

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Micro soft-verantwoordelijkheden voor AKS worker-knoop punten

Micro soft en klanten delen de verantwoordelijkheid voor Kubernetes-worker-knoop punten waar:

* De basis installatie kopie van het besturings systeem heeft vereiste toevoegingen (zoals controle-en netwerk agents).
* De worker-knoop punten ontvangen automatisch OS-patches.
* Problemen met de Kubernetes-besturings vlak onderdelen die worden uitgevoerd op de worker-knoop punten, worden automatisch hersteld. Onderdelen zijn onder andere:
  * Kube-proxy
  * Netwerk tunnels die communicatie paden naar de hoofd onderdelen van de Kubernetes bieden
  * Kubelet
  * Docker-of Moby-daemon

> [!NOTE]
> Als een besturings element van een werk knooppunt niet operationeel is, moet het AKS-team mogelijk afzonderlijke onderdelen of het hele worker-knoop punt opnieuw opstarten. Deze bewerkingen voor opnieuw opstarten worden geautomatiseerd en bieden automatisch herstel voor veelvoorkomende problemen. Deze opnieuw opstarten vindt alleen plaats op _knooppunt_ niveau en niet op het cluster, tenzij dit nood onderhoud of onderbreking is.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Verantwoordelijkheden van de klant voor AKS worker-knoop punten

Micro soft start werk knooppunten niet automatisch opnieuw op om patches op besturingssysteem niveau toe te passen. Hoewel besturingssysteem patches worden geleverd aan de worker-knoop punten, is de *klant* verantwoordelijk voor het opnieuw opstarten van de worker-knoop punten om de wijzigingen toe te passen. Gedeelde bibliotheken, daemons zoals een ' Solid-State ' hybride station (SSHD) en andere onderdelen op het niveau van het systeem of het besturings systeem worden automatisch geïnstalleerd.

Klanten zijn verantwoordelijk voor het uitvoeren van Kubernetes-upgrades. Ze kunnen upgrades uitvoeren via het configuratie scherm van Azure of de Azure CLI. Dit geldt voor updates die verbeteringen in de beveiliging of functionaliteit van Kubernetes bevatten.

> [!NOTE]
> Omdat AKS een *beheerde service*is, bevatten de eind doelen van het proces de verantwoordelijkheid voor patches, updates en logboek verzameling, zodat het Service beheer meer volledig en hand kan worden uitgevoerd. Omdat de capaciteit van de service voor end-to-end-beheer toeneemt, kunnen toekomstige releases sommige functies achterwege laten (bijvoorbeeld het opnieuw opstarten van knoop punten en automatische patching).

### <a name="security-issues-and-patching"></a>Beveiligings problemen en patches

Als er een beveiligings fout wordt gevonden in een of meer onderdelen van AKS, worden alle betrokken clusters door het AKS-team bijgewerkt om het probleem te verhelpen. Het team geeft ook hulp bij het upgraden van gebruikers.

Voor werk knooppunten waarop een beveiligings fout van toepassing is, geldt dat als er een patch voor nul uitval beschikbaar is, deze patch wordt toegepast en gebruikers van de wijziging op de hoogte worden gebracht.

Wanneer een beveiligings patch vereist dat het werk knooppunt opnieuw wordt opgestart, stelt micro soft klanten op de hoogte van deze vereiste. De klant is verantwoordelijk voor het opnieuw opstarten of bijwerken van de cluster patch. Als gebruikers de patches niet op basis van AKS-richt lijnen Toep assen, blijft het cluster kwetsbaar voor het beveiligings probleem.

### <a name="node-maintenance-and-access"></a>Beheer van knoop punten en toegang

Worker-knoop punten zijn een gedeelde verantwoordelijkheid en zijn het eigendom van klanten. Hierdoor kunnen klanten zich aanmelden bij hun worker-knoop punten en mogelijk schadelijke wijzigingen aanbrengen, zoals kernel-updates en het installeren of verwijderen van pakketten.

Als klanten destructieve wijzigingen aanbrengen of besturings vlak onderdelen laten weggaan of niet functioneren, wordt deze fout door AKS gedetecteerd en wordt het worker-knoop punt automatisch teruggezet naar de vorige werk status.

Hoewel klanten zich kunnen aanmelden bij en wijzigen van worker-knoop punten, wordt dit afgeraden omdat wijzigingen een cluster niet kunnen ondersteunen.

## <a name="network-ports-access-and-nsgs"></a>Netwerk poorten, Access en Nsg's

Als beheerde service heeft AKS specifieke netwerk-en connectiviteits vereisten. Deze vereisten zijn minder flexibel dan vereisten voor normale IaaS-onderdelen. In AKS, zoals het aanpassen van NSG-regels, het blok keren van een specifieke poort (bijvoorbeeld met firewall regels die de uitgaande poort 443 blok keren) en white list-Url's kan uw cluster niet meer worden ondersteund.

> [!NOTE]
> Op dit moment kunt u geen uitgaand verkeer van het cluster volledig vergren delen met AKS. Zie limiet voor uitgaand [verkeer beperken](limit-egress-traffic.md)voor het beheren van de lijst met url's en poorten die door uw cluster kunnen worden gebruikt voor het uitgaande verkeer.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Niet-ondersteunde alpha-en bèta Kubernetes-functies

AKS ondersteunt alleen stabiele functies in het upstream Kubernetes-project. Tenzij anders gedocumenteerd, ondersteunt AKS geen alpha-en bèta functies die beschikbaar zijn in het upstream Kubernetes-project.

In twee scenario's kunnen alpha-of bèta-functies worden uitgesteld voordat ze algemeen beschikbaar zijn:

* Klanten hebben voldaan aan het AKS-product, de ondersteunings-of engineering teams en zijn gevraagd deze nieuwe functies uit te proberen.
* Deze functies zijn [ingeschakeld door een functie vlag](https://github.com/Azure/AKS/blob/master/previews.md). Klanten moeten zich expliciet aanmelden om deze functies te gebruiken.

## <a name="preview-features-or-feature-flags"></a>Preview-functies of functie vlaggen

Voor functies en functionaliteit waarvoor uitgebreide test-en gebruikers feedback zijn vereist, brengt micro soft nieuwe preview-functies of-onderdelen achter een functie vlag uit. Overweeg deze functies als voorlopige versie of bèta-functies.

Preview-functies of functies van functie vlaggen zijn niet bedoeld voor productie. Voortdurende wijzigingen in Api's en gedrag, fout oplossingen en andere wijzigingen kunnen leiden tot onstabiele clusters en uitval tijd.

Functies in de open bare preview vallen onder de ' Best effort ' ondersteuning, aangezien deze functies in de preview-versie zijn en niet bedoeld voor productie en alleen worden ondersteund door de AKS technische ondersteunings teams tijdens kantoor uren. Ga voor meer informatie naar:

* [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Preview-functies worden van kracht op het niveau van het Azure- *abonnement* . Installeer geen preview-functies op een productie abonnement. In een productie abonnement kunnen preview-functies het standaard API-gedrag wijzigen en de normale bewerkingen beïnvloeden.

## <a name="upstream-bugs-and-issues"></a>Fouten en problemen upstream

Gezien de snelheid van de ontwikkeling in het upstream-Kubernetes project, ontstaan er fouten invariably. Sommige van deze fouten kunnen niet worden gerepareerd of gewerkt in het AKS-systeem. In plaats daarvan vereisen fout oplossingen grotere patches voor upstream-projecten (zoals Kubernetes, node-of worker-besturings systemen en kernels). Voor onderdelen die micro soft eigenaar is (zoals de Azure-Cloud provider), zijn AKS en Azure-personeel in de buurt van de voor delen van het oplossen van problemen in de community.

Wanneer een probleem met technische ondersteuning wordt veroorzaakt door een of meer fouten in de upstream, AKS ondersteuning en technische teams:

* Identificeer en koppel de upstream-fouten met alle ondersteunings Details om u te helpen uitleggen waarom dit probleem van invloed is op uw cluster of werk belasting. Klanten ontvangen koppelingen naar de vereiste opslag plaatsen, zodat ze de problemen kunnen bekijken en zien wanneer een nieuwe release oplossingen biedt.
* Geef mogelijke tijdelijke oplossingen of beperkingen op. Als het probleem kan worden verholpen, wordt een [bekend probleem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) in de AKS-opslag plaats opgeslagen. Het archief met bekende problemen legt het volgende uit:
  * Het probleem, inclusief koppelingen naar Upstream-fouten.
  * De tijdelijke oplossing en informatie over een upgrade of een andere persistentie van de oplossing.
  * Ruwe tijd lijnen voor de opname van het probleem, op basis van de uitgebracht van de upstream-release.
