---
title: Geo-noodherstel - Azure Event Hubs | Microsoft Docs
description: Het gebruik van geografische regio's voor failover en herstel na noodgeval uitvoeren in Azure Event Hubs
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281468"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Eventhubs - Geo-noodherstel 

Als er voor de hele Azure-regio's of-Data Centers (als er geen [beschikbaarheids zones](../availability-zones/az-overview.md) worden gebruikt) downtime actief is, is het van essentieel belang dat de gegevens worden verwerkt in een andere regio of Data Center. Daarom zijn *geo-nood herstel* en *geo-replicatie* belang rijke functies voor elke onderneming. Azure Event Hubs biedt ondersteuning voor geo-noodherstel en geo-replicatie, op het niveau van de naamruimte. 

> [!NOTE]
> De functie voor geo-nood herstel is alleen beschikbaar voor de [Standard-en speciale sku's](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Uitval en noodgevallen afhandelen

Het is belangrijk te weten het onderscheid tussen "storingen" en "rampen." Een *storing* is de tijdelijke niet-beschik baarheid van Azure Event hubs en kan van invloed zijn op sommige onderdelen van de service, zoals een berichten archief of zelfs het hele Data Center. Echter, nadat het probleem is opgelost, Event Hubs weer beschikbaar is. Een storing wordt normaal gesproken niet het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke onderbreking mogelijk een stroomstoring in het datacenter. Sommige storingen worden alleen korte verbinding verliezen vanwege problemen met de tijdelijke of netwerk. 

Een *nood* geval wordt gedefinieerd als het permanente verlies of een langere periode van een event hubs cluster, Azure-regio of Data Center. De regio of het datacenter mogelijk niet weer beschikbaar, kan of mogelijk niet beschikbaar voor uren of dagen kwijt bent. Voorbeelden van dergelijke rampen zijn brand, dat wordt overspoeld of aardbeving. Een ramp die permanent wordt kan leiden tot het verlies van enkele berichten, evenementen of andere gegevens. Echter, in de meeste gevallen moet er zonder verlies van gegevens en berichten kunnen worden hersteld nadat het datacenter een back-up is.

De functie voor het herstel bij Geo-gerelateerde noodgevallen van Azure Event Hubs is een oplossing voor noodherstel. De concepten en werkstroom beschreven in dit artikel van toepassing op noodgevallen, en niet op tijdelijke of tijdelijke storingen. Raadpleeg [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van herstel na nood gevallen in Microsoft Azure.

## <a name="basic-concepts-and-terms"></a>Eenvoudige concepten en termen

De functie van het herstel na noodgevallen noodherstel metagegevens geïmplementeerd, en is afhankelijk van de primaire en secundaire disaster recovery-naamruimten. 

De functie voor het opnieuw gebruiken van geo-nood herstel is alleen beschikbaar voor de [Standard-en speciale sku's](https://azure.microsoft.com/pricing/details/event-hubs/) . U hoeft niet te connection string wijzigen, omdat de verbinding is gemaakt via een alias.

De volgende termen worden gebruikt in dit artikel:

-  *Alias*: de naam voor een nood herstel configuratie die u hebt ingesteld. De alias bevat een enkele stabiel volledig FULLY Qualified Domain Name ()-verbindingsreeks. Deze verbindingsreeks alias toepassingen gebruiken voor verbinding met een naamruimte. 

-  *Primaire/secundaire naam ruimte*: de naam ruimten die overeenkomen met de alias. De primaire naamruimte ' actief ' en ontvangt berichten (dit kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is 'passieve' en ontvangt geen berichten. De metagegevens tussen beide is synchroon, zodat beide berichten zonder toepassing code of connection string wijzigingen aan te kunnen naadloos worden geaccepteerd. Om ervoor te zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias. 

-  *Meta gegevens*: entiteiten zoals Event hubs en consumenten groepen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Houd er rekening mee dat alleen de entiteiten en de bijbehorende instellingen automatisch worden gerepliceerd. Berichten en gebeurtenissen worden niet gerepliceerd. 

-  *Failover*: het proces van het activeren van de secundaire naam ruimte.

## <a name="supported-namespace-pairs"></a>Ondersteunde naam ruimte paren
De volgende combi Naties van primaire en secundaire naam ruimten worden ondersteund:  

| Primaire naam ruimte | Secundaire naam ruimte | Ondersteund | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Toegewezen | Ja | 
| Toegewezen | Toegewezen | Ja | 
| Toegewezen | Standard | Nee | 

> [!NOTE]
> U kunt geen naam ruimten koppelen die zich in hetzelfde toegewezen cluster bevinden. U kunt naam ruimten in afzonderlijke clusters koppelen. 

## <a name="setup-and-failover-flow"></a>Installatie en failover-stroom

De volgende sectie wordt een overzicht van de failoverproces en wordt uitgelegd hoe u voor het instellen van de eerste failover. 

![1][]

### <a name="setup"></a>Instellen

U maakt eerst of gebruik een bestaande primaire naamruimte en een nieuwe secundaire naamruimte, en de twee worden gekoppeld. Deze koppeling geeft u een alias die u gebruiken kunt om verbinding te maken. Omdat u een alias gebruiken, hoeft u niet te wijzigen van tekenreeksen voor databaseverbindingen. Alleen nieuwe naamruimten kunnen worden toegevoegd aan uw failover-koppelen. Tot slot moet u toevoegen enige controle om te detecteren of een failover nodig is. In de meeste gevallen maakt deel uit van een groot ecosysteem van de service, dus automatische failovers worden zelden mogelijk, zoals heel vaak failovers gesynchroniseerd met de resterende subsysteem of infrastructuur moet worden uitgevoerd.

### <a name="example"></a>Voorbeeld

In een voorbeeld van dit scenario kunt u een punt van verkoop (POS)-oplossing die berichten of gebeurtenissen verzendt. Eventhubs geeft de gebeurtenissen die aan bepaalde koppelen of opnieuw formatteren-oplossing, die vervolgens verzendt toegewezen gegevens naar een ander systeem voor verdere verwerking. Op dat moment mogelijk al deze systemen worden gehost in dezelfde Azure-regio. De beslissing over wanneer en welk onderdeel Failover wilt uitvoeren, is afhankelijk van de stroom van gegevens in uw infrastructuur. 

U kunt failover met bewaking van systemen of met op maat gemaakte bewakingsoplossingen automatiseren. Deze automatisering wordt echter extra planning en werk, die buiten het bereik van dit artikel.

### <a name="failover-flow"></a>Failover-stroom

Als u de failover start, zijn twee stappen vereist:

1. Als er een andere storing optreedt, die u wilt opnieuw kan worden. Daarom een andere passieve naamruimte instellen en bijwerken van de koppeling. 

2. Berichten van de vorige primaire naamruimte op te halen wanneer deze weer beschikbaar is. Hierna die naamruimte gebruiken voor het regelmatig berichten buiten de geo-herstel-configuratie of verwijder de oude primaire naamruimte.

> [!NOTE]
> Alleen fouten doorsturen semantiek worden ondersteund. In dit scenario, failover en vervolgens opnieuw koppelen aan een nieuwe naamruimte. Failback wordt niet ondersteund. bijvoorbeeld, in een SQL-cluster. 

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt; gemaakt bijvoorbeeld, u de verkeerde regio's tijdens de eerste installatie gekoppeld, u kunt de koppeling van de twee naamruimten op elk gewenst moment verbreken. Als u gebruiken van de gekoppelde naamruimten als normale naamruimten wilt, verwijdert u de alias.

## <a name="samples"></a>Voorbeelden

Het [voor beeld op github](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) laat zien hoe u een failover instelt en initieert. In dit voorbeeld ziet u de volgende concepten:

- Instellingen voor het gebruik van Azure Resource Manager met Event Hubs in Azure Active Directory vereist. 
- Stappen die nodig zijn voor het uitvoeren van de voorbeeldcode. 
- Verzenden en ontvangen van de huidige primaire naamruimte. 

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten moet rekening houden met deze release:

1. Event Hubs met geo-nood herstel worden geen gegevens gerepliceerd, waardoor u de oude offset waarde van uw primaire Event Hub niet opnieuw kunt gebruiken op uw secundaire Event Hub. U wordt aangeraden om de ontvanger van uw gebeurtenis opnieuw te starten met een van de volgende:

- *EventPosition. FromStart ()* : als u alle gegevens op de secundaire Event hub wilt lezen.
- *EventPosition. FromEnd ()* : als u alle nieuwe gegevens wilt lezen vanaf het moment van verbinding met uw secundaire Event hub.
- *EventPosition. FromEnqueuedTime (datetime)* : als u alle ontvangen gegevens in uw secundaire Event hub wilt lezen vanaf een bepaalde datum en tijd.

2. In de planning van failover moet u ook rekening houden met de tijd van meerdere factoren. Als u langer dan 15-20 minuten-connectiviteit verliest, wilt u mogelijk de failover te initiëren. 
 
3. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies worden niet gerepliceerd. Detectie van duplicaten en geplande berichten mag bovendien niet werken. Nieuwe sessies, geplande berichten en nieuwe duplicaten werkt. 

4. Failover van een complexe gedistribueerde infra structuur moet ten minste één keer worden [gereageerd](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) . 

5. Synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut.

## <a name="availability-zones"></a>Beschikbaarheidszones 

De Event Hubs standaard-SKU biedt ondersteuning voor [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor fout geïsoleerde locaties binnen een Azure-regio worden geboden. 

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Event Hubs Standard is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones inschakelen op nieuwe naamruimten, met behulp van de Azure portal. Eventhubs biedt geen ondersteuning voor migratie van bestaande naamruimten. U kunt zoneredundantie niet uitschakelen nadat deze is ingeschakeld op uw naamruimte.

![3][]

## <a name="next-steps"></a>Volgende stappen

* Het [voor beeld op github gaat over](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) een eenvoudige werk stroom die een geo-koppeling maakt en initieert een failover voor een nood herstel scenario.
* In de [rest API-verwijzing](/rest/api/eventhub/disasterrecoveryconfigs) worden api's beschreven voor het uitvoeren van de configuratie voor geo-nood herstel.

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
