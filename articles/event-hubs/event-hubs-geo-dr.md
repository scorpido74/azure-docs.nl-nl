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
ms.openlocfilehash: 22cf2be8eaed47a9440c6798acfb4383bd84c916
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611717"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Eventhubs - Geo-noodherstel 

Wanneer volledige Azure-regio's of datacenters (als er geen [beschikbaarheidszones](../availability-zones/az-overview.md) worden gebruikt) treedt downtime, is het essentieel is voor de verwerking van gegevens om te kunnen blijven werken in een andere regio of het datacenter. Als zodanig *Geo-noodherstel* en *Geo-replicatie* zijn belangrijke functies voor dat elke onderneming. Azure Event Hubs biedt ondersteuning voor geo-noodherstel en geo-replicatie, op het niveau van de naamruimte. 

> [!NOTE]
> De functie voor geo-nood herstel is alleen beschikbaar voor de [Standard-en speciale sku's](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Uitval en noodgevallen afhandelen

Het is belangrijk te weten het onderscheid tussen "storingen" en "rampen." Een *onderbreking* is tijdelijk ontbreken van Azure Event Hubs en kunnen invloed hebben op sommige onderdelen van de service, zoals een berichten-store, of zelfs het gehele datacenter. Echter, nadat het probleem is opgelost, Event Hubs weer beschikbaar is. Een storing wordt normaal gesproken niet het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke onderbreking mogelijk een stroomstoring in het datacenter. Sommige storingen worden alleen korte verbinding verliezen vanwege problemen met de tijdelijke of netwerk. 

Een *na noodgevallen* wordt gedefinieerd als de permanente of langere verlies van een cluster Event Hubs, Azure-regio of datacenter. De regio of het datacenter mogelijk niet weer beschikbaar, kan of mogelijk niet beschikbaar voor uren of dagen kwijt bent. Voorbeelden van dergelijke rampen zijn brand, dat wordt overspoeld of aardbeving. Een ramp die permanent wordt kan leiden tot het verlies van enkele berichten, evenementen of andere gegevens. Echter, in de meeste gevallen moet er zonder verlies van gegevens en berichten kunnen worden hersteld nadat het datacenter een back-up is.

De functie voor het herstel bij Geo-gerelateerde noodgevallen van Azure Event Hubs is een oplossing voor noodherstel. De concepten en werkstroom beschreven in dit artikel van toepassing op noodgevallen, en niet op tijdelijke of tijdelijke storingen. Zie voor een gedetailleerde bespreking van herstel na noodgevallen in Microsoft Azure, [in dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Eenvoudige concepten en termen

De functie van het herstel na noodgevallen noodherstel metagegevens geïmplementeerd, en is afhankelijk van de primaire en secundaire disaster recovery-naamruimten. 

De functie voor het opnieuw gebruiken van geo-nood herstel is alleen beschikbaar voor de [Standard-en speciale sku's](https://azure.microsoft.com/pricing/details/event-hubs/) . U hoeft niet te connection string wijzigen, omdat de verbinding is gemaakt via een alias.

De volgende termen worden gebruikt in dit artikel:

-  *Alias*: De naam voor een nood herstel configuratie die u hebt ingesteld. De alias bevat een enkele stabiel volledig FULLY Qualified Domain Name ()-verbindingsreeks. Deze verbindingsreeks alias toepassingen gebruiken voor verbinding met een naamruimte. 

-  *Primaire/secundaire naam ruimte*: De naam ruimten die overeenkomen met de alias. De primaire naamruimte ' actief ' en ontvangt berichten (dit kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is 'passieve' en ontvangt geen berichten. De metagegevens tussen beide is synchroon, zodat beide berichten zonder toepassing code of connection string wijzigingen aan te kunnen naadloos worden geaccepteerd. Om ervoor te zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias. 

-  *Meta gegevens*: Entiteiten zoals Event hubs en consumenten groepen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Houd er rekening mee dat alleen de entiteiten en de bijbehorende instellingen automatisch worden gerepliceerd. Berichten en gebeurtenissen worden niet gerepliceerd. 

-  *Failover*: Het proces voor het activeren van de secundaire naam ruimte.

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

De [op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) laat zien hoe u instellen en een failover worden gestart. In dit voorbeeld ziet u de volgende concepten:

- Instellingen voor het gebruik van Azure Resource Manager met Event Hubs in Azure Active Directory vereist. 
- Stappen die nodig zijn voor het uitvoeren van de voorbeeldcode. 
- Verzenden en ontvangen van de huidige primaire naamruimte. 

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten moet rekening houden met deze release:

1. In de planning van failover moet u ook rekening houden met de tijd van meerdere factoren. Als u langer dan 15-20 minuten-connectiviteit verliest, wilt u mogelijk de failover te initiëren. 
 
2. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies worden niet gerepliceerd. Detectie van duplicaten en geplande berichten mag bovendien niet werken. Nieuwe sessies, geplande berichten en nieuwe duplicaten werkt. 

3. Failover wordt uitgevoerd een complexe gedistribueerde infrastructuur moet [uitgetest](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) ten minste één keer. 

4. Synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut.

## <a name="availability-zones"></a>Beschikbaarheidszones 

De Event Hubs standaard-SKU biedt ondersteuning voor [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor fout geïsoleerde locaties binnen een Azure-regio worden geboden. 

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Event Hubs Standard is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones inschakelen op nieuwe naamruimten, met behulp van de Azure portal. Eventhubs biedt geen ondersteuning voor migratie van bestaande naamruimten. U kunt zoneredundantie niet uitschakelen nadat deze is ingeschakeld op uw naamruimte.

![3][]

## <a name="next-steps"></a>Volgende stappen

* De [op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) doorloopt een eenvoudige werkstroom die u maakt een geo-koppeling en start een failover voor een noodherstelscenario.
* De [REST API-verwijzing](/rest/api/eventhub/disasterrecoveryconfigs) API's voor het uitvoeren van de Geo-noodherstelconfiguratie beschrijft.

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

* Aan de slag met een [Event Hubs-zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
