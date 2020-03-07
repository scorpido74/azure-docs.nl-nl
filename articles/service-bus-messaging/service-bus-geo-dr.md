---
title: Azure Service Bus geo-nood herstel | Microsoft Docs
description: Over het gebruik van geografische regio's om een failover uit te voeren en herstel na nood geval in Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355824"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-nood herstel

Als er voor de hele Azure-regio's of-Data Centers (als er geen [beschikbaarheids zones](../availability-zones/az-overview.md) worden gebruikt) downtime actief is, is het van essentieel belang dat de gegevens worden verwerkt in een andere regio of Data Center. Daarom is het niet zo dat *geo-nood herstel* een belang rijk onderdeel is van elke onderneming. Azure Service Bus ondersteunt geo-nood herstel op het niveau van de naam ruimte.

De functie voor het opnieuw gebruiken van geo-nood herstel is wereld wijd beschikbaar voor de Service Bus Premium-SKU. 

>[!NOTE]
> Met geo-nood herstel wordt momenteel alleen gegarandeerd dat de meta gegevens (wacht rijen, onderwerpen, abonnementen, filters) worden gekopieerd van de primaire naam ruimte naar een secundaire naam ruimte wanneer deze zijn gekoppeld.

## <a name="outages-and-disasters"></a>Uitval en noodgevallen afhandelen

Het is belangrijk te weten het onderscheid tussen "storingen" en "rampen." 

Een *storing* is de tijdelijke niet-beschik baarheid van Azure service bus en kan van invloed zijn op sommige onderdelen van de service, zoals een berichten archief of zelfs het hele Data Center. Nadat het probleem is opgelost, wordt Service Bus echter weer beschikbaar. Een storing wordt normaal gesproken niet het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke onderbreking mogelijk een stroomstoring in het datacenter. Sommige storingen worden alleen korte verbinding verliezen vanwege problemen met de tijdelijke of netwerk. 

Een *nood* geval wordt gedefinieerd als het permanente verlies of een langere periode van een service bus cluster, Azure-regio of Data Center. De regio of het datacenter mogelijk niet weer beschikbaar, kan of mogelijk niet beschikbaar voor uren of dagen kwijt bent. Voorbeelden van dergelijke rampen zijn brand, dat wordt overspoeld of aardbeving. Een ramp die permanent wordt kan leiden tot het verlies van enkele berichten, evenementen of andere gegevens. Echter, in de meeste gevallen moet er zonder verlies van gegevens en berichten kunnen worden hersteld nadat het datacenter een back-up is.

De functie voor het opnieuw gebruiken van geo-nood herstel van Azure Service Bus is een oplossing voor nood herstel. De concepten en werkstroom beschreven in dit artikel van toepassing op noodgevallen, en niet op tijdelijke of tijdelijke storingen. Raadpleeg [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van herstel na nood gevallen in Microsoft Azure.   

## <a name="basic-concepts-and-terms"></a>Eenvoudige concepten en termen

De functie van het herstel na noodgevallen noodherstel metagegevens geïmplementeerd, en is afhankelijk van de primaire en secundaire disaster recovery-naamruimten. De geo-nood herstel functie is alleen beschikbaar voor de [Premium-SKU](service-bus-premium-messaging.md) . U hoeft niet te connection string wijzigen, omdat de verbinding is gemaakt via een alias.

De volgende termen worden gebruikt in dit artikel:

-  *Alias*: de naam voor een nood herstel configuratie die u hebt ingesteld. De alias bevat een enkele stabiel volledig FULLY Qualified Domain Name ()-verbindingsreeks. Deze verbindingsreeks alias toepassingen gebruiken voor verbinding met een naamruimte. Als u een alias gebruikt, zorgt u ervoor dat de connection string niet wordt gewijzigd wanneer de failover wordt geactiveerd.

-  *Primaire/secundaire naam ruimte*: de naam ruimten die overeenkomen met de alias. De primaire naamruimte ' actief ' en ontvangt berichten (dit kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is 'passieve' en ontvangt geen berichten. De metagegevens tussen beide is synchroon, zodat beide berichten zonder toepassing code of connection string wijzigingen aan te kunnen naadloos worden geaccepteerd. Om ervoor te zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias. 

-  *Meta gegevens*: entiteiten zoals wacht rijen, onderwerpen en abonnementen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Houd er rekening mee dat alleen de entiteiten en de bijbehorende instellingen automatisch worden gerepliceerd. Berichten worden niet gerepliceerd.

-  *Failover*: het proces van het activeren van de secundaire naam ruimte.

## <a name="setup"></a>Instellen

In de volgende sectie vindt u een overzicht van het instellen van koppeling tussen de naam ruimten.

![1][]

Het installatie proces is als volgt:

1. Richt een ***primaire*** service bus Premium-naam ruimte in.

2. Richt een ***secundaire*** service bus Premium-naam ruimte in in een *andere regio dan waar de primaire naam ruimte is ingericht*. Dit helpt de fout isolatie tussen verschillende datacenter regio's mogelijk te maken.

3. Maak een koppeling tussen de primaire naam ruimte en de secundaire naam ruimte om de ***alias***te verkrijgen.

    >[!NOTE] 
    > Als u [uw Azure service bus Standard-naam ruimte hebt gemigreerd naar Azure service bus Premium](service-bus-migrate-standard-premium.md), moet u de bestaande alias (de service bus Standard-naam ruimte Connection String) gebruiken om de configuratie voor herstel na nood gevallen te maken via de **PS/CLI** of de **rest API**.
    >
    >
    > Dit komt doordat uw Azure Service Bus Standard-naam ruimte connection string/DNS-naam zelf een alias vormt voor uw Azure Service Bus Premium-naam ruimte.
    >
    > Uw client toepassingen moeten gebruikmaken van deze alias (dat wil zeggen de Azure Service Bus standaard naam ruimte connection string) om verbinding te maken met de Premium-naam ruimte waarin de nood herstel koppeling is ingesteld.
    >
    > Als u de portal gebruikt voor het instellen van de configuratie voor herstel na nood gevallen, zal de portal dit voor behoud van u van u samen stellen.


4. Gebruik de ***alias*** die is verkregen in stap 3 om uw client toepassingen te verbinden met de primaire naam ruimte die geschikt is voor het gebruik van geo-Dr. In eerste instantie wijst de alias naar de primaire naam ruimte.

5. Beschrijving Voeg bewaking toe om te detecteren of een failover nood zakelijk is.

## <a name="failover-flow"></a>Failover-stroom

Een failover wordt hand matig geactiveerd door de klant (expliciet via een opdracht of door de bedrijfs logica van de client waarmee de opdracht wordt geactiveerd) en nooit door Azure. Dit geeft het volledige eigendom van de klant en de zicht baarheid van de uitval oplossing op de backbone van Azure.

![4][]

Nadat de failover is geactiveerd-

1. De ***alias*** Connection String wordt bijgewerkt zodat deze verwijst naar de secundaire Premium-naam ruimte.

2. Clients (afzenders en ontvangers) maken automatisch verbinding met de secundaire naam ruimte.

3. De bestaande koppeling tussen de primaire en secundaire Premium-naam ruimte is verbroken.

Zodra de failover is gestart,

1. Als er zich een andere storing voordoet, wilt u opnieuw een failover kunnen uitvoeren. Daarom een andere passieve naamruimte instellen en bijwerken van de koppeling. 

2. Berichten van de vorige primaire naamruimte op te halen wanneer deze weer beschikbaar is. Hierna die naamruimte gebruiken voor het regelmatig berichten buiten de geo-herstel-configuratie of verwijder de oude primaire naamruimte.

> [!NOTE]
> Alleen fouten doorsturen semantiek worden ondersteund. In dit scenario, failover en vervolgens opnieuw koppelen aan een nieuwe naamruimte. Failback wordt niet ondersteund. bijvoorbeeld, in een SQL-cluster. 

U kunt failover met bewaking van systemen of met op maat gemaakte bewakingsoplossingen automatiseren. Deze automatisering wordt echter extra planning en werk, die buiten het bereik van dit artikel.

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt; gemaakt bijvoorbeeld, u de verkeerde regio's tijdens de eerste installatie gekoppeld, u kunt de koppeling van de twee naamruimten op elk gewenst moment verbreken. Als u gebruiken van de gekoppelde naamruimten als normale naamruimten wilt, verwijdert u de alias.

## <a name="use-existing-namespace-as-alias"></a>Bestaande naam ruimte als alias gebruiken

Als u een scenario hebt waarin u de verbindingen van producenten en consumenten niet kunt wijzigen, kunt u de naam van de naam ruimte opnieuw gebruiken als alias naam. Bekijk [hier de voorbeeld code op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Voorbeelden

De [voor beelden op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) laten zien hoe u een failover instelt en initieert. In deze voor beelden worden de volgende concepten gedemonstreerd:

- Een .NET-voor beeld en-instellingen die vereist zijn in Azure Active Directory om Azure Resource Manager te gebruiken met Service Bus, om geo-nood herstel in te stellen en in te scha kelen.
- Stappen die nodig zijn voor het uitvoeren van de voorbeeldcode.
- Een bestaande naam ruimte gebruiken als alias.
- Stappen voor het inschakelen van een geo-nood herstel via Power shell of CLI.
- [Verzend en ontvang berichten](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) van de huidige primaire of secundaire naam ruimte met behulp van de alias.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten moet rekening houden met deze release:

1. In de planning van failover moet u ook rekening houden met de tijd van meerdere factoren. Als u langer dan 15-20 minuten-connectiviteit verliest, wilt u mogelijk de failover te initiëren.

2. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies worden niet gerepliceerd. Detectie van duplicaten en geplande berichten mag bovendien niet werken. Nieuwe sessies, nieuwe geplande berichten en nieuwe duplicaten worden gebruikt. 

3. Failover van een complexe gedistribueerde infra structuur moet ten minste één keer worden [gereageerd](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut. Abonnementen en regels tellen ook als entiteiten.

## <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU biedt ook ondersteuning voor [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor fout geïsoleerde locaties binnen een Azure-regio worden geboden.

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones inschakelen op nieuwe naamruimten, met behulp van de Azure portal. Service Bus biedt geen ondersteuning voor de migratie van bestaande naam ruimten. U kunt zoneredundantie niet uitschakelen nadat deze is ingeschakeld op uw naamruimte.

![3][]

## <a name="next-steps"></a>Volgende stappen

- Zie de [rest API referentie](/rest/api/servicebus/disasterrecoveryconfigs)voor geo-nood herstel hier.
- Voer het [voor beeld van](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)een geo-nood herstel uit op github.
- Zie het voor beeld van geo-nood herstel [dat berichten naar een alias verzendt](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Raadpleeg de volgende artikelen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
