---
title: 'Snelstartgids: Azure Service Bus-onderwerpen en-abonnementen gebruiken met python'
description: In dit artikel leest u hoe u een Azure Service Bus onderwerp, een abonnement, berichten verzendt naar een onderwerp en berichten van het abonnement ontvangt.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 03e22c4c179850e5140015c0abc2d89f16d4b624
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774547"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Snelstartgids: Service Bus-onderwerpen en-abonnementen gebruiken met python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u python gebruikt met Azure Service Bus-onderwerpen en-abonnementen. De voor beelden gebruiken het [Azure PYTHON SDK][Azure Python package] -pakket voor het volgende: 

- Onderwerpen en abonnementen maken voor onderwerpen
- Abonnements filters en-regels maken
- Berichten verzenden naar onderwerpen 
- Berichten ontvangen van abonnementen
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt de [voor delen van uw Visual Studio-of MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Een Service Bus naam ruimte, gemaakt door de stappen te volgen op [Quick Start: gebruik de Azure Portal om een service bus onderwerp en abonnementen te maken](service-bus-quickstart-topics-subscriptions-portal.md). Kopieer de naam van de naam ruimte, de naam van de gedeelde toegangs sleutel en de waarde van de primaire sleutel van het scherm **gedeelde toegangs beleid** , zodat u dit later in deze Snelstartgids kunt gebruiken. 
- Python 3.4 x of hoger, met het [Azure PYTHON SDK][Azure Python package] -pakket geïnstalleerd. Raadpleeg de [installatie handleiding voor python](/azure/python/python-sdk-azure-install)voor meer informatie.

## <a name="create-a-servicebusservice-object"></a>Een ServiceBusService-object maken

Met een **ServiceBusService** -object kunt u werken met onderwerpen en abonnementen op onderwerpen. Als u toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe aan de bovenkant van uw python-bestand:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Voeg de volgende code toe om een **ServiceBusService** -object te maken. Vervang `<namespace>`, `<sharedaccesskeyname>`en `<sharedaccesskeyvalue>` door de naam van uw Service Bus naam ruimte, Shared Access Signature (SAS)-sleutel naam en waarde voor de primaire sleutel. U kunt deze waarden vinden onder **beleid voor gedeelde toegang** in uw service bus naam ruimte in de [Azure Portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Een onderwerp maken

De volgende code gebruikt de `create_topic`-methode om een Service Bus onderwerp te maken met de naam `mytopic`, met de standaard instellingen:

```python
bus_service.create_topic('mytopic')
```

U kunt de opties voor het onderwerp gebruiken om standaard instellingen voor het onderwerp te overschrijven, zoals de TTL (time to Live) van het bericht of de maximum grootte van het onderwerp. In het volgende voor beeld wordt een onderwerp gemaakt met de naam `mytopic` met een maximale grootte van 5 GB en een standaard bericht-TTL van één minuut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Abonnementen maken

U kunt ook het object **ServiceBusService** gebruiken om abonnementen op onderwerpen te maken. Een abonnement kan een filter hebben om de ontvangen berichten te beperken tot de virtuele wachtrij. Als u geen filter opgeeft, gebruiken nieuwe abonnementen het standaard **MatchAll** -filter, waarmee alle berichten die in het onderwerp worden gepubliceerd, worden geplaatst in de virtuele wachtrij van het abonnement. In het volgende voor beeld wordt een abonnement gemaakt op `mytopic` met de naam `AllMessages` die gebruikmaakt van het **MatchAll** -filter:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Filters gebruiken met abonnementen

Gebruik de `create_rule` methode van het object **ServiceBusService** om de berichten te filteren die worden weer gegeven in een abonnement. U kunt regels opgeven wanneer u het abonnement maakt, of regels toevoegen aan bestaande abonnementen.

Het meest flexibele type filter is een **SqlFilter**die gebruikmaakt van een SUBSET van SQL-92. SQL-filters worden toegepast op basis van de eigenschappen van berichten die zijn gepubliceerd in het onderwerp. Zie de syntaxis van [SqlFilter. SqlExpression][SqlFilter.SqlExpression] voor meer informatie over de expressies die u kunt gebruiken met een SQL-filter.

Omdat het standaard filter van **MatchAll** automatisch wordt toegepast op alle nieuwe abonnementen, moet u het verwijderen van de abonnementen die u wilt filteren, of worden door **MatchAll** alle andere filters overschreven die u opgeeft. U kunt de standaard regel verwijderen met de methode `delete_rule` van het object **ServiceBusService** .

In het volgende voor beeld wordt een abonnement gemaakt op `mytopic` met de naam `HighMessages`, met een **SqlFilter** -regel met de naam `HighMessageFilter`. Met de `HighMessageFilter` regel worden alleen berichten geselecteerd met een aangepaste `messageposition`-eigenschap die groter is dan 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

In het volgende voor beeld wordt een abonnement gemaakt op `mytopic` met de naam `LowMessages`, met een **SqlFilter** -regel met de naam `LowMessageFilter`. Met de `LowMessageFilter` regel worden alleen berichten geselecteerd met een `messageposition`-eigenschap die kleiner is dan of gelijk is aan 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Met `AllMessages`, `HighMessages`en `LowMessages` alle van kracht, worden berichten die worden verzonden naar `mytopic` altijd bezorgd bij ontvangers van het `AllMessages`-abonnement. Berichten worden ook selectief bezorgd bij het `HighMessages`-of `LowMessages`-abonnement, afhankelijk van de waarde van de `messageposition` eigenschap van het bericht. 

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp

Toepassingen gebruiken de `send_topic_message` methode van het object **ServiceBusService** om berichten naar een service bus onderwerp te verzenden.

In het volgende voor beeld worden vijf test berichten naar het onderwerp `mytopic` verzonden. De waarde van de aangepaste `messageposition` eigenschap is afhankelijk van de herhaling van de lus en bepaalt welke abonnementen de berichten ontvangen. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor de bericht grootte

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten dat een onderwerp kan bevatten, maar er is een kapje op de totale grootte van de berichten die het onderwerp bevat. U kunt de grootte van het onderwerp opgeven tijdens de aanmaak tijd, met een bovengrens van 5 GB. 

Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement

Toepassingen gebruiken de `receive_subscription_message` methode voor het object **ServiceBusService** om berichten van een abonnement te ontvangen. In het volgende voor beeld worden berichten van het `LowMessages`-abonnement ontvangen en worden deze verwijderd wanneer ze worden gelezen:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

De optionele `peek_lock` para meter van `receive_subscription_message` bepaalt of Service Bus berichten uit het abonnement verwijdert wanneer ze worden gelezen. De standaard modus voor het ontvangen van berichten is *PeekLock*, of `peek_lock` ingesteld op **True**, waarmee berichten worden gelezen en vergrendeld zonder dat ze worden verwijderd uit het abonnement. Elk bericht moet vervolgens expliciet worden voltooid om het te verwijderen uit het abonnement.

Als u berichten van het abonnement wilt verwijderen wanneer ze worden gelezen, kunt u de para meter `peek_lock` instellen op **False**, zoals in het vorige voor beeld. Berichten verwijderen als onderdeel van de receive-bewerking is het eenvoudigste model en werkt prima als de toepassing ontbrekende berichten kan verdragen als er een fout optreedt. Om dit gedrag te begrijpen, moet u een scenario overwegen waarin de toepassing een receive-aanvraag uitgeeft en vervolgens vastloopt voordat het proces wordt verwerkt. Als het bericht is verwijderd op het moment dat de toepassing opnieuw wordt gestart en opnieuw bezig is met het uitvoeren van berichten, heeft het bericht gemist dat het is ontvangen voordat het vastloopt.

Als uw toepassing gemiste berichten niet kan verdragen, wordt de ontvangst een bewerking met twee fasen. PeekLock vindt het volgende bericht dat moet worden gebruikt, vergrendelt het om te voor komen dat andere gebruikers het ontvangen en retourneert het naar de toepassing. Nadat het bericht is verwerkt of opgeslagen, voltooit de toepassing de tweede fase van het ontvangst proces door de `complete`-methode aan te roepen voor het **bericht** object.  Met de methode `complete` wordt het bericht gemarkeerd als verbruikt en wordt het verwijderd uit het abonnement.

In het volgende voor beeld ziet u een kort vergrendelings scenario:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Vastlopen van toepassingen en onleesbare berichten verwerken

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger een bericht om de een of andere reden niet kan verwerken, kan de toepassing de `unlock`-methode aanroepen voor het **bericht** object. Service Bus ontgrendelt het bericht in het abonnement en maakt het beschikbaar om opnieuw te worden ontvangen, ofwel door dezelfde of een andere verbruikte toepassing.

Er is ook een time-out voor berichten die in het abonnement zijn vergrendeld. Als een toepassing een bericht niet kan verwerken voordat de time-out voor de vergren deling is verlopen, bijvoorbeeld als de toepassing vastloopt, Service Bus het bericht automatisch ontgrendelen en wordt het beschikbaar om opnieuw te worden ontvangen.

Als een toepassing vastloopt na het verwerken van een bericht, maar voordat de `complete` methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing geleverd wanneer het opnieuw wordt gestart. Dit gedrag wordt vaak een eenmalige *verwerking*genoemd. Elk bericht wordt ten minste één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw scenario dubbele verwerking niet kan verdragen, kunt u de eigenschap **MessageId** van het bericht gebruiken, dat constant blijft tijdens bezorgings pogingen, om dubbele bericht bezorging af te handelen. 

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen

Als u onderwerpen en abonnementen wilt verwijderen, gebruikt u de [Azure Portal][Azure portal] of de methode `delete_topic`. Met de volgende code wordt het onderwerp met de naam `mytopic`verwijderd:

```python
bus_service.delete_topic('mytopic')
```

Als u een onderwerp verwijdert, worden alle abonnementen op het onderwerp verwijderd. U kunt ook afzonderlijke abonnementen verwijderen. Met de volgende code wordt het abonnement met de naam `HighMessages` verwijderd uit het onderwerp `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Onderwerpen en abonnementen zijn standaard permanent en bestaan tot u deze verwijdert. Als u automatisch abonnementen wilt verwijderen nadat een bepaalde tijds periode is verstreken, kunt u de para meter [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) instellen voor het abonnement. 

> [!TIP]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunt u verbinding maken met een Service Bus naam ruimte en eenvoudig bericht entiteiten beheren. Het hulp programma biedt geavanceerde functies, zoals de functionaliteit voor importeren/exporteren, en de mogelijkheid om onderwerpen, wacht rijen, abonnementen, relay-Services, Notification hubs en Event hubs te testen. 

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Service Bus onderwerpen hebt geleerd, volgt u deze koppelingen voor meer informatie:

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* Naslag informatie voor [SqlFilter. SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
