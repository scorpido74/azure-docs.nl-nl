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
ms.openlocfilehash: 49748006baf779e6aea4322068ca3bd07a03a0a3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209397"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-nood herstel

Als er voor de hele Azure-regio's of-Data Centers (als er geen [beschikbaarheids zones](../availability-zones/az-overview.md) worden gebruikt) downtime actief is, is het van essentieel belang dat de gegevens worden verwerkt in een andere regio of Data Center. Daarom is het niet zo dat *geo-nood herstel* een belang rijk onderdeel is van elke onderneming. Azure Service Bus ondersteunt geo-nood herstel op het niveau van de naam ruimte.

De functie voor het opnieuw gebruiken van geo-nood herstel is wereld wijd beschikbaar voor de Service Bus Premium-SKU. 

>[!NOTE]
> Met geo-nood herstel wordt momenteel alleen gegarandeerd dat de meta gegevens (wacht rijen, onderwerpen, abonnementen, filters) worden gekopieerd van de primaire naam ruimte naar een secundaire naam ruimte wanneer deze zijn gekoppeld.

## <a name="outages-and-disasters"></a>Storingen en rampen

Het is belang rijk om het onderscheid tussen ' storingen ' en ' rampen ' te noteren. 

Een *storing* is de tijdelijke niet-beschik baarheid van Azure service bus en kan van invloed zijn op sommige onderdelen van de service, zoals een berichten archief of zelfs het hele Data Center. Nadat het probleem is opgelost, wordt Service Bus echter weer beschikbaar. Normaal gesp roken veroorzaakt een storing geen verlies van berichten of andere gegevens. Een voor beeld van een dergelijke storing kan een stroom storing in het Data Center zijn. Sommige storingen zijn alleen korte verbindings verliezen als gevolg van tijdelijke of netwerk problemen. 

Een *nood* geval wordt gedefinieerd als het permanente verlies of een langere periode van een service bus cluster, Azure-regio of Data Center. De regio of het Data Center kan al dan niet meer beschikbaar zijn, of is mogelijk niet actief voor uren of dagen. Voor beelden van dergelijke nood gevallen zijn brand, overstroming of aard beving. Een nood herstel bewerking die permanent wordt, kan leiden tot verlies van sommige berichten, gebeurtenissen of andere gegevens. In de meeste gevallen moeten er echter geen gegevens verloren gaan en kunnen berichten worden hersteld wanneer er een back-up van het Data Center wordt gemaakt.

De functie voor het opnieuw gebruiken van geo-nood herstel van Azure Service Bus is een oplossing voor nood herstel. De concepten en werk stroom die in dit artikel worden beschreven, zijn van toepassing op scenario's voor nood gevallen en niet op tijdelijke storingen. Raadpleeg [dit artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor een gedetailleerde bespreking van herstel na nood gevallen in Microsoft Azure.   

## <a name="basic-concepts-and-terms"></a>Basis concepten en termen

De functie nood herstel implementeert herstel na nood gevallen van meta gegevens en is afhankelijk van de primaire en secundaire naam ruimten voor nood herstel. De geo-nood herstel functie is alleen beschikbaar voor de [Premium-SKU](service-bus-premium-messaging.md) . U hoeft geen connection string wijzigingen aan te brengen, omdat de verbinding wordt gemaakt via een alias.

In dit artikel worden de volgende termen gebruikt:

-  *Alias*: de naam voor een nood herstel configuratie die u hebt ingesteld. De alias biedt een enkele stabiele FQDN-naam (Fully Qualified Domain Name) connection string. Toepassingen gebruiken deze alias connection string om verbinding te maken met een naam ruimte. Als u een alias gebruikt, zorgt u ervoor dat de connection string niet wordt gewijzigd wanneer de failover wordt geactiveerd.

-  *Primaire/secundaire naam ruimte*: de naam ruimten die overeenkomen met de alias. De primaire naam ruimte is actief en ontvangt berichten (dit kan een bestaande of nieuwe naam ruimte zijn). De secundaire naam ruimte is ' passief ' en ontvangt geen berichten. De meta gegevens tussen beide zijn synchroon, zodat beide berichten zonder toepassings code of connection string wijzigingen naadloos kunnen accepteren. Om ervoor te zorgen dat alleen de actieve naam ruimte berichten ontvangt, moet u de alias gebruiken. 

-  *Meta gegevens*: entiteiten zoals wacht rijen, onderwerpen en abonnementen; en hun eigenschappen van de service die aan de naam ruimte zijn gekoppeld. Houd er rekening mee dat alleen entiteiten en hun instellingen automatisch worden gerepliceerd. Berichten worden niet gerepliceerd.

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

1. Als er zich een andere storing voordoet, wilt u opnieuw een failover kunnen uitvoeren. Stel daarom een andere passieve naam ruimte in en werk de koppeling bij. 

2. Berichten ophalen uit de voormalige primaire naam ruimte zodra deze weer beschikbaar is. Daarna gebruikt u die naam ruimte voor normale berichten buiten uw Geo-herstel configuratie of verwijdert u de oude primaire naam ruimte.

> [!NOTE]
> Alleen mislukte doorstuur semantiek worden ondersteund. In dit scenario kunt u een failover uitvoeren en vervolgens opnieuw koppelen met een nieuwe naam ruimte. Failback wordt niet ondersteund; bijvoorbeeld in een SQL-cluster. 

U kunt de failover automatiseren met bewakings systemen of met aangepaste bewakings oplossingen. Een dergelijke automatisering vergt echter wel extra planning en werk, wat buiten het bereik van dit artikel valt.

![2][]

## <a name="management"></a>Beheer

Als u een fout hebt gemaakt, u koppelt bijvoorbeeld de verkeerde regio's tijdens de eerste installatie. u kunt het koppelen van de twee naam ruimten op elk gewenst moment verstoren. Als u de gekoppelde naam ruimten als gewone naam ruimten wilt gebruiken, verwijdert u de alias.

## <a name="use-existing-namespace-as-alias"></a>Bestaande naam ruimte als alias gebruiken

Als u een scenario hebt waarin u de verbindingen van producenten en consumenten niet kunt wijzigen, kunt u de naam van de naam ruimte opnieuw gebruiken als alias naam. Bekijk [hier de voorbeeld code op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Voorbeelden

De [voor beelden op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) laten zien hoe u een failover instelt en initieert. In deze voor beelden worden de volgende concepten gedemonstreerd:

- Een .NET-voor beeld en-instellingen die vereist zijn in Azure Active Directory om Azure Resource Manager te gebruiken met Service Bus, om geo-nood herstel in te stellen en in te scha kelen.
- Stappen die nodig zijn om de voorbeeld code uit te voeren.
- Een bestaande naam ruimte gebruiken als alias.
- Stappen voor het inschakelen van een geo-nood herstel via Power shell of CLI.
- [Verzend en ontvang berichten](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) van de huidige primaire of secundaire naam ruimte met behulp van de alias.

## <a name="considerations"></a>Overwegingen

Let op de volgende punten als u rekening moet houden met deze release:

1. Bij het plannen van de failover moet u ook rekening houden met de tijds factor. Als u bijvoorbeeld langer dan 15 tot 20 minuten geen verbinding meer hebt, kunt u ervoor kiezen om de failover te initiëren.

2. Het feit dat er geen gegevens worden gerepliceerd, betekent dat momenteel actieve sessies niet worden gerepliceerd. Bovendien werken duplicaten detectie en geplande berichten mogelijk niet. Nieuwe sessies, nieuwe geplande berichten en nieuwe duplicaten worden gebruikt. 

3. Failover van een complexe gedistribueerde infra structuur moet ten minste één keer worden [gereageerd](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Het synchroniseren van entiteiten kan enige tijd duren, ongeveer 50-100 entiteiten per minuut. Abonnementen en regels tellen ook als entiteiten.

## <a name="availability-zones"></a>Beschikbaarheidszones

De Service Bus Premium SKU biedt ook ondersteuning voor [Beschikbaarheidszones](../availability-zones/az-overview.md), waardoor fout geïsoleerde locaties binnen een Azure-regio worden geboden.

> [!NOTE]
> De Beschikbaarheidszones ondersteuning voor Azure Service Bus Premium is alleen beschikbaar in [Azure-regio's](../availability-zones/az-region.md) waar beschikbaarheids zones aanwezig zijn.

U kunt Beschikbaarheidszones alleen inschakelen voor nieuwe naam ruimten, met behulp van de Azure Portal. Service Bus biedt geen ondersteuning voor de migratie van bestaande naam ruimten. U kunt zone redundantie niet uitschakelen nadat u deze in uw naam ruimte hebt ingeschakeld.

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
