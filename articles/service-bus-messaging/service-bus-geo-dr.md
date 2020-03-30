---
title: Herstel van Azure Service Bus Geo-ramp | Microsoft Documenten
description: Geografische regio's gebruiken om te mislukken en noodherstel uit te voeren in Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259576"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-disaster recovery

Wanneer hele Azure-regio's of datacenters (als er geen [beschikbaarheidszones](../availability-zones/az-overview.md) worden gebruikt) downtime ervaren, is het van cruciaal belang dat gegevensverwerking in een andere regio of datacenter blijft werken. Als zodanig is *Geo-disaster recovery* een belangrijke functie voor elke onderneming. Azure Service Bus ondersteunt geo-disaster recovery op naamruimteniveau.

De functie Geo-disaster recovery is wereldwijd beschikbaar voor de Service Bus Premium SKU. 

>[!NOTE]
> Geo-Disaster recovery zorgt er momenteel alleen voor dat de metagegevens (wachtrijen, onderwerpen, abonnementen, filters) worden gekopieerd van de primaire naamruimte naar de secundaire naamruimte wanneer deze worden gekoppeld.

## <a name="outages-and-disasters"></a>Storingen en rampen

Het is belangrijk om het onderscheid tussen "uitval" en "rampen" op te merken. 

Een *storing* is de tijdelijke onbeschikbaarheid van Azure Service Bus en kan gevolgen hebben voor sommige onderdelen van de service, zoals een berichtenwinkel of zelfs het hele datacenter. Echter, nadat het probleem is opgelost, service bus wordt weer beschikbaar. Een storing leidt meestal niet tot het verlies van berichten of andere gegevens. Een voorbeeld van een dergelijke storing kan een stroomstoring in het datacenter zijn. Sommige storingen zijn slechts korte verbindingsverliezen als gevolg van tijdelijke of netwerkproblemen. 

Een *ramp* wordt gedefinieerd als het permanente of langere termijn verlies van een Service Bus-cluster, Azure-regio of datacenter. De regio of het datacenter kan wel of niet weer beschikbaar komen, of kan worden uitgeschakeld voor uren of dagen. Voorbeelden van dergelijke rampen zijn brand, overstroming, of aardbeving. Een ramp die permanent wordt, kan het verlies van bepaalde berichten, gebeurtenissen of andere gegevens veroorzaken. In de meeste gevallen mag er echter geen gegevensverlies zijn en kunnen berichten worden hersteld zodra het datacenter een back-up maakt.

De geo-disaster recovery-functie van Azure Service Bus is een oplossing voor noodherstel. De concepten en werkstroom die in dit artikel worden beschreven, zijn van toepassing op rampscenario's en niet op tijdelijke of tijdelijke onderbrekingen. Zie [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van noodherstel in Microsoft Azure.   

## <a name="basic-concepts-and-terms"></a>Basisconcepten en termen

De functie voor noodherstel implementeert metagegevens disaster recovery en is gebaseerd op primaire en secundaire naamruimten voor noodherstel. Houd er rekening mee dat de functie voor herstel van georampen alleen beschikbaar is voor de [Premium SKU.](service-bus-premium-messaging.md) U hoeft geen wijzigingen in de verbindingstekenreeks aan te brengen, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias:* de naam voor een noodherstelconfiguratie die u hebt ingesteld. De alias biedt één stabiele FQDN-verbindingstekenreeks (Fully Qualified Domain Name). Toepassingen gebruiken deze aliasverbindingstekenreeks om verbinding te maken met een naamruimte. Het gebruik van een alias zorgt ervoor dat de verbindingstekenreeks ongewijzigd blijft wanneer de failover wordt geactiveerd.

-  *Primaire/secundaire naamruimte:* de naamruimten die overeenkomen met de alias. De primaire naamruimte is "actief" en ontvangt berichten (dit kan een bestaande of nieuwe naamruimte zijn). De secundaire naamruimte is "passief" en ontvangt geen berichten. De metagegevens tussen beide zijn gesynchroniseerd, zodat beide berichten naadloos kunnen accepteren zonder wijzigingen in toepassingscode of verbindingstekenreeks. Als u ervoor wilt zorgen dat alleen de actieve naamruimte berichten ontvangt, moet u de alias gebruiken. 

-  *Metagegevens:* entiteiten zoals wachtrijen, onderwerpen en abonnementen; en hun eigenschappen van de service die zijn gekoppeld aan de naamruimte. Houd er rekening mee dat alleen entiteiten en hun instellingen automatisch worden gerepliceerd. Berichten worden niet gerepliceerd.

-  *Failover:* Het proces van het activeren van de secundaire naamruimte.

## <a name="setup"></a>Instellen

De volgende sectie is een overzicht voor het instellen van koppeling tussen de naamruimten.

![1][]

Het installatieproces is als volgt -

1. Inrichten van een Premium Naamruimte ***voor primaire*** servicebus.

2. Een ***secundaire*** servicebuspremiumnaamruimte inrichten in een andere regio *dan waar de primaire naamruimte is ingericht.* Dit zal helpen om foutisolatie in verschillende datacenterregio's mogelijk te maken.

3. Maak koppeling tussen de primaire naamruimte en secundaire naamruimte om de ***alias***te verkrijgen.

    >[!NOTE] 
    > Als u [uw Azure Service Bus Standard-naamruimte hebt gemigreerd naar Azure Service Bus Premium,](service-bus-migrate-standard-premium.md)moet u de reeds bestaande alias (d.w.z. uw servicebusstandaardverbindingstekenreeks) gebruiken om de disaster recovery-configuratie te maken via de **PS/CLI-** of **REST API.**
    >
    >
    > Dit komt omdat uw Azure Service Bus Standard namespace-verbindingstekenreeks/DNS-naam zelf een alias wordt voor de naamruimte van Uw Azure Service Bus Premium.
    >
    > Uw clienttoepassingen moeten deze alias (d.w.z. de Azure Service Bus Standard-naamruimteverbindingstekenreeks) gebruiken om verbinding te maken met de Premium-naamruimte waar de koppeling voor noodherstel is ingesteld.
    >
    > Als u de Portal gebruikt om de configuratie van disaster recovery in te stellen, zal de portal dit voorbehoud van u abstraheren.


4. Gebruik de ***alias*** die in stap 3 is verkregen om uw clienttoepassingen te verbinden met de primaire naamruimte met Geo-DR. In eerste instantie wijst de alias naar de primaire naamruimte.

5. [Optioneel] Voeg wat controle toe om te detecteren of een failover nodig is.

## <a name="failover-flow"></a>Failoverstroom

Een failover wordt handmatig geactiveerd door de klant (expliciet via een opdracht of via bedrijfslogica die eigendom is van de klant en nooit door Azure. Dit geeft de klant volledige eigendom en zichtbaarheid voor het oplossen van storingen op azure's backbone.

![4][]

Nadat de failover is geactiveerd -

1. De ***verbindingstekenreeks alias*** wordt bijgewerkt om naar de naamruimte Secundaire Premie te wijzen.

2. Clients(afzenders en ontvangers) maken automatisch verbinding met de secundaire naamruimte.

3. De bestaande koppeling tussen primaire en secundaire premiumnaamruimte wordt verbroken.

Zodra de failover is gestart -

1. Als er opnieuw een storing optreedt, wilt u opnieuw kunnen mislukken. Stel daarom een andere passieve naamruimte in en werk de koppeling bij. 

2. Haal berichten uit de voormalige primaire naamruimte zodra deze weer beschikbaar is. Gebruik daarna die naamruimte voor regelmatige berichten buiten uw geoherstel-instelling of verwijder de oude primaire naamruimte.

> [!NOTE]
> Alleen fail forward semantiek worden ondersteund. In dit scenario mislukt u en koppelt u opnieuw met een nieuwe naamruimte. Falen terug wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

U failover automatiseren met bewakingssystemen of met op maat gemaakte bewakingsoplossingen. Echter, een dergelijke automatisering neemt extra planning en werk, die buiten het toepassingsgebied van dit artikel.

![2][]

## <a name="management"></a>Beheer

Als je een fout hebt gemaakt. U hebt bijvoorbeeld de verkeerde gebieden gekoppeld tijdens de eerste installatie, u de koppeling van de twee naamruimten op elk gewenst moment verbreken. Als u de gekoppelde naamruimten als gewone naamruimten wilt gebruiken, verwijdert u de alias.

## <a name="use-existing-namespace-as-alias"></a>Bestaande naamruimte gebruiken als alias

Als u een scenario hebt waarin u de verbindingen van producenten en consumenten niet wijzigen, u uw naamruimtenaam als aliasnaam opnieuw gebruiken. Zie de [voorbeeldcode op GitHub hier.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)

## <a name="samples"></a>Voorbeelden

De [voorbeelden op GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) laten zien hoe u een failover instellen en starten. Deze voorbeelden tonen de volgende begrippen aan:

- Een .NET-voorbeeld en instellingen die in Azure Active Directory nodig zijn om Azure Resource Manager met Service Bus te gebruiken, om geo-disaster recovery in te stellen en in te schakelen.
- Stappen die nodig zijn om de voorbeeldcode uit te voeren.
- Een bestaande naamruimte gebruiken als alias.
- Stappen om geo-disaster recovery mogelijk te maken via PowerShell of CLI.
- [Verzenden en ontvangen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) van de huidige primaire of secundaire naamruimte met behulp van de alias.

## <a name="considerations"></a>Overwegingen

Let op de volgende overwegingen om in gedachten te houden met deze release:

1. In uw failover planning, moet u ook rekening houden met de factor tijd. Als u bijvoorbeeld langer dan 15 tot 20 minuten de verbinding verliest, u besluiten de failover te starten.

2. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies niet worden gerepliceerd. Bovendien werken dubbele detectie en geplande berichten mogelijk niet. Nieuwe sessies, nieuwe geplande berichten en nieuwe duplicaten werken. 

3. Het niet overeenlaten van een complexe gedistribueerde infrastructuur moet ten minste één keer worden [gerepeteerd.](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)

4. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut. Abonnementen en regels tellen ook als entiteiten.

## <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU ondersteunt ook [beschikbaarheidszones](../availability-zones/az-overview.md)en biedt op foutgeïsoleerde locaties binnen een Azure-regio.

> [!NOTE]
> De ondersteuning voor beschikbaarheidszones voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-overview.md#services-support-by-region) waar beschikbaarheidszones aanwezig zijn.

U beschikbaarheidszones alleen inschakelen op nieuwe naamruimten met behulp van de Azure-portal. Service Bus ondersteunt geen migratie van bestaande naamruimten. U zoneredundantie niet uitschakelen nadat u deze hebt ingeschakeld op uw naamruimte.

![3][]

## <a name="next-steps"></a>Volgende stappen

- Zie [de API-referentie](/rest/api/servicebus/disasterrecoveryconfigs)voor geo-disaster recovery REST hier .
- Voer het voorbeeld van geo-disaster recovery [uit op GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zie het voorbeeld voor herstel bij herstel bij georampen [dat berichten naar een alias verzendt.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

Zie de volgende artikelen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
