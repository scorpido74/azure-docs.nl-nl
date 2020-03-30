---
title: Herstel na noodgevallen - Azure Event Hubs| Microsoft Documenten
description: Geografische regio's gebruiken om te mislukken en noodherstel uit te voeren in Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281468"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - Herstel van georampen 

Wanneer hele Azure-regio's of datacenters (als er geen [beschikbaarheidszones](../availability-zones/az-overview.md) worden gebruikt) downtime ervaren, is het van cruciaal belang dat gegevensverwerking in een andere regio of datacenter blijft werken. Als zodanig zijn *Geo-disaster recovery* en *Geo-replicatie* belangrijke functies voor elke onderneming. Azure Event Hubs ondersteunt zowel geo-disaster recovery als geo-replicatie, op naamruimteniveau. 

> [!NOTE]
> De Geo-disaster recovery-functie is alleen beschikbaar voor de [standaard en speciale SKU's.](https://azure.microsoft.com/pricing/details/event-hubs/)  

## <a name="outages-and-disasters"></a>Storingen en rampen

Het is belangrijk om het onderscheid tussen "uitval" en "rampen" op te merken. Een *storing* is de tijdelijke onbeschikbaarheid van Azure Event Hubs en kan gevolgen hebben voor sommige onderdelen van de service, zoals een berichtenwinkel of zelfs het hele datacenter. Nadat het probleem is opgelost, is Event Hubs echter weer beschikbaar. Een storing leidt meestal niet tot het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke storing kan een stroomstoring in het datacenter zijn. Sommige storingen zijn slechts korte verbindingsverliezen als gevolg van tijdelijke of netwerkproblemen. 

Een *ramp* wordt gedefinieerd als het permanente of langere termijn verlies van een cluster van Gebeurtenishubs, Azure-regio of datacenter. De regio of het datacenter kan wel of niet weer beschikbaar komen, of kan worden uitgeschakeld voor uren of dagen. Voorbeelden van dergelijke rampen zijn brand, overstroming, of aardbeving. Een ramp die permanent wordt, kan het verlies van bepaalde berichten, gebeurtenissen of andere gegevens veroorzaken. In de meeste gevallen mag er echter geen gegevensverlies zijn en kunnen berichten worden hersteld zodra het datacenter een back-up maakt.

De geo-disaster recovery-functie van Azure Event Hubs is een oplossing voor noodherstel. De concepten en werkstroom die in dit artikel worden beschreven, zijn van toepassing op rampscenario's en niet op tijdelijke of tijdelijke onderbrekingen. Zie [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van noodherstel in Microsoft Azure.

## <a name="basic-concepts-and-terms"></a>Basisconcepten en termen

De functie voor noodherstel implementeert metagegevens disaster recovery en is gebaseerd op primaire en secundaire naamruimten voor noodherstel. 

De Geo-disaster recovery-functie is alleen beschikbaar voor de [standaard en speciale SKU's.](https://azure.microsoft.com/pricing/details/event-hubs/) U hoeft geen wijzigingen in de verbindingstekenreeks aan te brengen, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias:* de naam voor een noodherstelconfiguratie die u hebt ingesteld. De alias biedt één stabiele FQDN-verbindingstekenreeks (Fully Qualified Domain Name). Toepassingen gebruiken deze aliasverbindingstekenreeks om verbinding te maken met een naamruimte. 

-  *Primaire/secundaire naamruimte:* de naamruimten die overeenkomen met de alias. De primaire naamruimte is "actief" en ontvangt berichten (dit kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is "passief" en ontvangt geen berichten. De metagegevens tussen beide zijn gesynchroniseerd, zodat beide berichten naadloos kunnen accepteren zonder wijzigingen in toepassingscode of verbindingstekenreeks. Als u ervoor wilt zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias gebruiken. 

-  *Metagegevens*: entiteiten zoals gebeurtenishubs en consumentengroepen; en hun eigenschappen van de service die zijn gekoppeld aan de naamruimte. Houd er rekening mee dat alleen entiteiten en hun instellingen automatisch worden gerepliceerd. Berichten en gebeurtenissen worden niet gerepliceerd. 

-  *Failover:* Het proces van het activeren van de secundaire naamruimte.

## <a name="supported-namespace-pairs"></a>Ondersteunde naamruimteparen
De volgende combinaties van primaire en secundaire naamruimten worden ondersteund:  

| Primaire naamruimte | Secundaire naamruimte | Ondersteund | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Toegewezen | Ja | 
| Toegewezen | Toegewezen | Ja | 
| Toegewezen | Standard | Nee | 

> [!NOTE]
> U geen naamruimten koppelen die zich in hetzelfde specifieke cluster bevinden. U naamruimten koppelen die zich in afzonderlijke clusters bevinden. 

## <a name="setup-and-failover-flow"></a>Installatie- en failoverstroom

De volgende sectie is een overzicht van het failoverproces en legt uit hoe u de eerste failover instelt. 

![1][]

### <a name="setup"></a>Instellen

U maakt of gebruikt eerst een bestaande primaire naamruimte en een nieuwe secundaire naamruimte en koppelt de twee vervolgens. Deze koppeling geeft je een alias die je gebruiken om verbinding te maken. Omdat u een alias gebruikt, hoeft u de verbindingstekenreeksen niet te wijzigen. Alleen nieuwe naamruimten kunnen worden toegevoegd aan uw failoverkoppeling. Ten slotte moet u wat controle toevoegen om te detecteren of een failover noodzakelijk is. In de meeste gevallen is de service een onderdeel van een groot ecosysteem, waardoor automatische failovers zelden mogelijk zijn, omdat vaak failovers synchroon moeten worden uitgevoerd met het resterende subsysteem of de infrastructuur.

### <a name="example"></a>Voorbeeld

Overweeg in een voorbeeld van dit scenario een POS-oplossing (Point of Sale) die berichten of gebeurtenissen uitzendt. Gebeurtenishubs geven deze gebeurtenissen door aan een oplossing voor het toewijzen of opnieuw formatteren, waarna toegewezen gegevens naar een ander systeem worden doorgestuurd voor verdere verwerking. Op dat moment kunnen al deze systemen worden gehost in dezelfde Azure-regio. De beslissing over wanneer en welk onderdeel moet mislukken, is afhankelijk van de stroom van gegevens in uw infrastructuur. 

U failover automatiseren met bewakingssystemen of met op maat gemaakte bewakingsoplossingen. Echter, een dergelijke automatisering neemt extra planning en werk, die buiten het toepassingsgebied van dit artikel.

### <a name="failover-flow"></a>Failoverstroom

Als u de failover start, zijn twee stappen vereist:

1. Als er een andere storing optreedt, wilt u opnieuw kunnen mislukken. Stel daarom een andere passieve naamruimte in en werk de koppeling bij. 

2. Haal berichten uit de voormalige primaire naamruimte zodra deze weer beschikbaar is. Gebruik daarna die naamruimte voor regelmatige berichten buiten uw geoherstel-instelling of verwijder de oude primaire naamruimte.

> [!NOTE]
> Alleen fail forward semantiek worden ondersteund. In dit scenario mislukt u en koppelt u opnieuw met een nieuwe naamruimte. Falen terug wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

![2][]

## <a name="management"></a>Beheer

Als je een fout hebt gemaakt. U hebt bijvoorbeeld de verkeerde gebieden gekoppeld tijdens de eerste installatie, u de koppeling van de twee naamruimten op elk gewenst moment verbreken. Als u de gekoppelde naamruimten als gewone naamruimten wilt gebruiken, verwijdert u de alias.

## <a name="samples"></a>Voorbeelden

Het [voorbeeld op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) laat zien hoe u een failover instellen en starten. In dit voorbeeld worden de volgende begrippen gedemonstreerd:

- Instellingen die vereist zijn in Azure Active Directory om Azure Resource Manager te gebruiken met Gebeurtenishubs. 
- Stappen die nodig zijn om de voorbeeldcode uit te voeren. 
- Verzenden en ontvangen vanuit de huidige primaire naamruimte. 

## <a name="considerations"></a>Overwegingen

Let op de volgende overwegingen om in gedachten te houden met deze release:

1. Volgens het ontwerp repliceert het herstel van gebeurtenishubs geen gegevens en u de oude verschuivingswaarde van uw primaire gebeurtenishub niet opnieuw gebruiken op uw secundaire gebeurtenishub. We raden u aan uw evenementontvanger opnieuw te starten met een van de volgende opties:

- *EventPosition.FromStart()* - Lees desgewenst alle gegevens op uw secundaire gebeurtenishub.
- *EventPosition.FromEnd()* - Als u alle nieuwe gegevens wilt lezen vanaf het moment van verbinding met uw secundaire gebeurtenishub.
- *EventPosition.FromEnqueuedTime(dateTime)* - Als u alle gegevens wilt lezen die u vanaf een bepaalde datum en tijd in uw secundaire gebeurtenishub hebt ontvangen.

2. In uw failover planning, moet u ook rekening houden met de factor tijd. Als u bijvoorbeeld langer dan 15 tot 20 minuten de verbinding verliest, u besluiten de failover te starten. 
 
3. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies niet worden gerepliceerd. Bovendien werken dubbele detectie en geplande berichten mogelijk niet. Nieuwe sessies, geplande berichten en nieuwe duplicaten werken. 

4. Het niet overeenlaten van een complexe gedistribueerde infrastructuur moet ten minste één keer worden [gerepeteerd.](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) 

5. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut.

## <a name="availability-zones"></a>Beschikbaarheidszones 

De Standaard SKU van gebeurtenishubs ondersteunt [beschikbaarheidszones](../availability-zones/az-overview.md)en biedt op foutgeïsoleerde locaties binnen een Azure-gebied. 

> [!NOTE]
> De ondersteuning voor beschikbaarheidszones voor Azure Event Hubs Standard is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheidszones aanwezig zijn.

U beschikbaarheidszones alleen inschakelen op nieuwe naamruimten met behulp van de Azure-portal. Gebeurtenishubs ondersteunen geen migratie van bestaande naamruimten. U zoneredundantie niet uitschakelen nadat u deze hebt ingeschakeld op uw naamruimte.

![3][]

## <a name="next-steps"></a>Volgende stappen

* Het [voorbeeld op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) loopt door een eenvoudige workflow die een geokoppeling maakt en een failover initieert voor een scenario voor noodherstel.
* De [REST API-verwijzing](/rest/api/eventhub/disasterrecoveryconfigs) beschrijft API's voor het uitvoeren van de geo-disaster recovery-configuratie.

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
