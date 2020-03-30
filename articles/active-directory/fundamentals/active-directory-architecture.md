---
title: Overzicht van architectuur - Azure Active Directory | Microsoft Documenten
description: Meer informatie over wat een Azure Active Directory-tenant is en hoe u Azure beheert met Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854fb4649f8c1113f20abe5807dd0ce473ba6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368062"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Wat is de Azure Active Directory-architectuur?

Met Azure AD (Azure Active Directory) kunt u veilig de toegang tot Azure-services en -resources beheren voor uw gebruikers. Azure AD omvat een volledige suite met mogelijkheden voor identiteitsbeheer. Zie [Wat is Azure Active Directory?](active-directory-whatis.md) voor meer informatie over de functies van Azure AD.

Met Azure AD kunt u gebruikers en groepen maken en beheren, en machtigingen inschakelen om toegang tot bedrijfsresources te verlenen of te weigeren. Zie [The fundamentals of Azure identity management](active-directory-whatis.md) (De grondbeginselen van Azure-identiteitsbeheer) voor meer informatie over identiteitsbeheer.

## <a name="azure-ad-architecture"></a>Azure AD-architectuur

De geografisch gedistribueerde architectuur van Azure AD combineert uitgebreide monitoring, geautomatiseerde omleidings-, failover- en herstelmogelijkheden, die bedrijfsbrede beschikbaarheid en prestaties leveren aan klanten.

In dit artikel worden de volgende elementen van de architectuur besproken:

*   Servicearchitectuurontwerp
*   Schaalbaarheid
*   Continue beschikbaarheid
*   Datacenters

### <a name="service-architecture-design"></a>Servicearchitectuurontwerp

De meest voorkomende manier om een toegankelijk en bruikbaar, datarijk systeem te bouwen is door middel van onafhankelijke bouwstenen of schaaleenheden. Voor de Azure AD-gegevenslaag worden schaaleenheden *partities*genoemd.

De gegevenslaag heeft meerdere front-end-services die mogelijkheden bieden voor lezen/schrijven. Het onderstaande diagram laat zien hoe de componenten van een partitie met één map worden geleverd in geografisch gedistribueerde datacenters.

  ![Partitiediagram met één map](./media/active-directory-architecture/active-directory-architecture.png)

De onderdelen van Azure AD-architectuur omvatten een primaire replica en secundaire replica's.

#### <a name="primary-replica"></a>Primaire replica

De *primaire replica* ontvangt alle *schrijfbewerkingen* voor de partitie waarbij deze hoort. Alle schrijfbewerkingen worden onmiddellijk gerepliceerd naar een secundaire replica in een ander datacenter, voordat de aanroeper een melding van slagen ontvangt. Op deze manier wordt de geografisch redundante duurzaamheid van schrijfbewerkingen verzekerd.

#### <a name="secondary-replicas"></a>Secundaire replica's

Alle directory *reads* worden onderhouden van *secundaire replica's,* die zich bevinden in datacenters die zich fysiek in verschillende regio's bevinden. Er zijn veel secundaire replica's, omdat gegevens asynchroon worden gerepliceerd. Directoryreads, zoals verificatieaanvragen, worden onderhouden vanuit datacenters die dicht bij klanten staan. De secundaire replica's zijn verantwoordelijk voor de schaalbaarheid van leesbewerkingen.

### <a name="scalability"></a>Schaalbaarheid

Schaalbaarheid is de mogelijkheid van een service om uit te breiden en zo te voldoen aan groeiende prestatievereisten. Schaalbaarheid van schrijfbewerkingen wordt bereikt door de gegevens te partitioneren. Schaalbaarheid van leesbewerkingen wordt bereikt door gegevens uit één partitie te repliceren naar meerdere secundaire replica's over de hele wereld.

Aanvragen van directorytoepassingen worden doorgestuurd naar het datacenter waar ze fysiek het dichtst bij zijn. Schrijfbewerkingen worden transparant omgeleid naar de primaire replica voor lees-/schrijfconsistentie. Secundaire replica's breiden de schaal van partities aanzienlijk uit, omdat in de mappen doorgaans leesbewerkingen worden afgehandeld.

Directory-toepassingen maken verbinding met de dichtstbijzijnde datacenters. Deze verbinding verbetert de prestaties en dus is uitschalen mogelijk. Omdat een mappartitie meerdere secundaire replica's kan hebben, kunnen deze secundaire replica's dichter bij Directory-clients worden geplaatst. Alleen interne Directory-serviceonderdelen die veel schrijfbewerkingen afhandelen, zijn rechtstreeks gericht op de actieve primaire replica.

### <a name="continuous-availability"></a>Continue beschikbaarheid

Beschikbaarheid (of bedrijfstijd) definieert de mogelijkheid van een systeem om ononderbroken actief te zijn. De sleutel tot de hoge beschikbaarheid van Azure AD is dat de services snel verkeer kunnen verplaatsen over meerdere geografisch gedistribueerde datacenters. Elk datacenter is onafhankelijk, waardoor gederecorreleerde foutmodi mogelijk zijn. Door dit ontwerp met hoge beschikbaarheid vereist Azure AD geen downtime voor onderhoudsactiviteiten.

Het partitieontwerp van Azure AD wordt vereenvoudigd in vergelijking met het bedrijfsAD-ontwerp, met behulp van een ontwerp met één master dat een zorgvuldig georkestreerd en deterministisch primaire replica-failoverproces bevat.

#### <a name="fault-tolerance"></a>Fouttolerantie

Een systeem is beschikbaarder als het tolerant is voor fouten in hardware, software en het netwerk. Voor elke mappartitie bestaat een maximaal beschikbare hoofdreplica: de primaire replica. Op deze replica worden alleen schrijfbewerkingen naar de partitie uitgevoerd. Deze replica wordt voortdurend en nauwlettend gecontroleerd. Indien er een fout wordt gedetecteerd, kunnen schrijfbewerkingen onmiddellijk worden verplaatst naar een andere replica (die dan de nieuwe primaire replica wordt). Tijdens de failover kan er een verlies van schrijfbeschikbaarheid optreden. Dit duurt meestal maar 1-2 minuten. De leesbeschikbaarheid wordt gedurende deze tijd niet beïnvloed.

Leesbewerkingen (die vele malen vaker voorkomen dan schrijfbewerkingen) worden alleen opgeslagen in secundaire replica's. Aangezien secundaire replica's idempotent zijn, kan het verlies van een van de replica's in een bepaalde partitie eenvoudig worden gecompenseerd door de leesbewerkingen naar een andere replica te leiden. Meestal is dit dan een replica in hetzelfde datacenter.

#### <a name="data-durability"></a>Duurzaamheid van gegevens

Een schrijven is blijvend toegewijd aan ten minste twee datacenters voordat het wordt erkend. Dit gebeurt door eerst het schrijven op de primaire, en vervolgens onmiddellijk repliceren van de schrijven naar ten minste een ander datacenter. Deze schrijfactie zorgt ervoor dat een mogelijk catastrofaal verlies van het datacenter dat de primaire host niet leidt tot gegevensverlies.

Azure AD behoudt een Zero [Recovery Time Objective (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) om geen gegevens te verliezen over failovers. Dit omvat:

* Tokenuitgifte en directoryleest
* Toestaan van slechts ongeveer 5 minuten RTO voor directory schrijft

### <a name="datacenters"></a>Datacenters

Azure AD-replica's worden opgeslagen in datacenters over de hele wereld. Zie [Azure Global Infrastructure voor](https://azure.microsoft.com/global-infrastructure/)meer informatie.

Azure AD werkt in verschillende datacenters met de volgende kenmerken:

* Verificatie, Graph en andere AD-services bevinden zich achter de Gateway-service. De taakverdeling van deze services wordt via de gateway beheerd. Het zal automatisch mislukken als er ongezonde servers worden gedetecteerd met behulp van transactionele statussondes. Op basis van deze statussondes leidt de Gateway het verkeer dynamisch naar gezonde datacenters.
* Voor *reads*heeft de map secundaire replica's en bijbehorende front-endservices in een actief-actieve configuratie die in meerdere datacenters werkt. In het geval van een storing van een heel datacenter, wordt het verkeer automatisch doorgestuurd naar een ander datacenter.
 *Voor *schrijft,* zal de map mislukken over primaire (master) replica in datacenters via gepland (nieuwe primaire wordt gesynchroniseerd met oude primaire) of noodfailover procedures. De duurzaamheid van gegevens wordt bereikt door een commit te repliceren naar ten minste twee datacenters.

#### <a name="data-consistency"></a>Gegevensconsistentie

Het directorymodel is een van de uiteindelijke consistenties. Een typisch probleem met gedistribueerde asynchrone replicerende systemen is dat de gegevens die zijn geretourneerd van een "bepaalde" replica mogelijk niet up-to-date zijn. 

Met behulp van een secundaire replica biedt Azure AD lees-/schrijfconsistentie voor toepassingen door schrijfbewerkingen naar de primaire replica te leiden en ze tegelijkertijd terug te halen naar de secundaire replica.

Toepassingsschrijft met de Microsoft Graph API van Azure AD wordt geabstraheerd van het onderhouden van affiniteit tot een directoryreplica voor consistentie in lezen schrijven. De Api-service van Microsoft Graph onderhoudt een logische sessie, die affiniteit heeft met een secundaire replica die wordt gebruikt voor het lezen. affiniteit wordt vastgelegd in een 'replicatoken' dat de service caches met behulp van een gedistribueerde cache in de secundaire replica datacenter. Dit token wordt vervolgens gebruikt voor verdere bewerkingen in dezelfde logische sessie. Als u dezelfde logische sessie wilt blijven gebruiken, moeten volgende aanvragen worden doorgestuurd naar hetzelfde Azure AD-datacenter. Het is niet mogelijk om een logische sessie voort te zetten als de directoryclientaanvragen worden doorgestuurd naar meerdere Azure AD-datacenters; als dit gebeurt dan heeft de client meerdere logische sessies die onafhankelijke lees-schrijven consistenties hebben.

 >[!NOTE]
 >Schrijfbewerkingen worden onmiddellijk gerepliceerd naar de secundaire replica waarop de leesbewerkingen van de logische sessie zijn weggeschreven.

#### <a name="backup-protection"></a>Back-upbeveiliging

De map implementeert voorlopig verwijderen, in plaats van definitief, voor gebruikers en tenants, wat eenvoudig herstel mogelijk maakt wanneer items per ongeluk worden verwijderd door een klant. Als uw tenantbeheerder per ongeluk gebruikers verwijdert, kunnen ze de verwijderde gebruikers eenvoudig ongedaan maken en herstellen.

Met Azure AD worden dagelijkse back-ups van alle gegevens geïmplementeerd. Daarom kunnen gegevens bindend worden teruggezet in het geval van logische verwijderingen of beschadigingen. De gegevenslaag maakt gebruik van foutcorrigerende codes, zodat deze kan controleren op fouten en automatisch bepaalde typen schijffouten kan corrigeren.

#### <a name="metrics-and-monitors"></a>Metrische gegevens en controles

Voor het uitvoeren van een service met een hoge beschikbaarheid zijn uitstekende mogelijkheden voor metrische gegevens en controle vereist. Met Azure AD worden belangrijke metrische gegevens met betrekking tot de status van de service voortdurend geanalyseerd voor elk van de services. Er is ook continue ontwikkeling en afstemming van statistieken en monitoring en waarschuwingen voor elk scenario, binnen elke Azure AD-service en voor alle services.

Als een Azure AD-service niet werkt zoals verwacht, wordt onmiddellijk actie ondernomen om de functionaliteit zo snel mogelijk te herstellen. De belangrijkste metrische Azure AD-tracks is hoe snel live siteproblemen kunnen worden gedetecteerd en beperkt voor klanten. We investeren veel in controle en waarschuwingen om de detectietijd (TTD-doel: < 5 minuten) te minimaliseren en in operationele paraatheid om de hersteltijd (TTM-doel: < 30 minuten) zo kort mogelijk te houden.

#### <a name="secure-operations"></a>Veilige bewerkingen

Het gebruik van operationele besturingselementen zoals multi-factor authenticatie (MFA) voor elke bewerking, evenals het controleren van alle bewerkingen. Bovendien, met behulp van een just-in-time hoogtesysteem om de nodige tijdelijke toegang te verlenen voor elke operationele taak-on-demand op een permanente basis. Zie [De vertrouwde cloud](https://azure.microsoft.com/support/trust-center) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Ontwikkelaarshandleiding voor Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)