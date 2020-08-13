---
title: 'Quickstart: Azure Service Bus-onderwerpen en -abonnementen met Python gebruiken'
description: In dit artikel ziet u hoe u een Azure Service Bus-onderwerp en -abonnement kunt maken, en hoe u berichten naar een onderwerp verzendt en berichten van een abonnement ontvangt.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064380"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Quickstart: Service Bus-onderwerpen en -abonnementen met Python gebruiken

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Python met Azure Service Bus-onderwerpen en -abonnementen gebruikt. De voorbeelden gebruiken het [Azure Python SDK][Azure Python package]-pakket voor het volgende: 

- Onderwerpen en abonnementen op onderwerpen maken
- Abonnementsfilters en -regels maken
- Berichten verzenden naar onderwerpen 
- Berichten ontvangen van abonnementen
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Een Service Bus-naamruimte, gemaakt door de stappen uit te voeren in [Quickstart: De Azure-portal gebruiken om een Service Bus-onderwerp en -abonnementen te maken](service-bus-quickstart-topics-subscriptions-portal.md). Kopieer de naamruimte, de naam van de gedeelde toegangssleutel, en de primaire sleutelwaarde op het scherm **Beleid voor gedeelde toegang** voor later gebruik in deze quickstart. 
- Python 3.4x of hoger, met het [Azure Python SDK][Azure Python package]-pakket geïnstalleerd. Raadpleeg de [Python-installatiehandleiding](/azure/developer/python/azure-sdk-install) voor meer informatie.

## <a name="create-a-servicebusservice-object"></a>Een ServiceBusService-object maken

Met een **ServiceBusService**-object kunt u werken met onderwerpen en abonnementen op onderwerpen. Als u via programmacode toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe bovenaan het Python-bestand:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Voeg de volgende code toe om een **ServiceBusClient**-object te maken. Vervang `<namespace>`, `<sharedaccesskeyname>` en `<sharedaccesskeyvalue>` door de naam van uw Service Bus-naamruimte, de naam van uw SAS-sleutel (Shared Access Signature) en de waarde voor de primaire sleutel. U vindt deze waarden onder **Beleid voor gedeelde toegang** in uw Service Bus-naamruimte in de [Azure-portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Een onderwerp maken

De volgende code maakt gebruik van de methode `create_topic` om een Service Bus-onderwerp te maken met de naam `mytopic`, met de standaardinstellingen:

```python
bus_service.create_topic('mytopic')
```

U kunt onderwerpopties gebruiken om de standaardinstellingen voor onderwerpen te overschrijven, zoals Time to Live van bericht (TTL) of de maximumgrootte van het onderwerp. In het volgende voorbeeld wordt een onderwerp gemaakt met de naam `mytopic`, met een maximale grootte van 5 GB en een standaardbericht-TTL van één minuut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Abonnementen maken

U kunt ook het **ServiceBusService**-object gebruiken om abonnementen op onderwerpen te maken. Een abonnement kan een filter hebben om te beperken hoeveel berichten in de bijbehorende virtuele wachtrij worden geplaatst. Als u geen filter opgeeft, maken nieuwe abonnementen gebruik van het standaardfilter **MatchAll**, waarmee alle berichten die in het onderwerp zijn gepubliceerd, in de virtuele wachtrij van het abonnement worden geplaatst. In het volgende voorbeeld maakt u een abonnement in `mytopic` met de naam `AllMessages`, dat gebruikmaakt van het filter **MatchAll**:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Filters gebruiken met abonnementen

Gebruik de methode `create_rule` van het **ServiceBusService**-object om de berichten te filteren die worden weergegeven in een abonnement. U kunt regels opgeven wanneer u het abonnement maakt, of regels toevoegen aan bestaande abonnementen.

Het meest flexibele type filter is een **SqlFilter**, dat gebruikmaakt van een subset van SQL-92. SQL-filters worden uitgevoerd op basis van de eigenschappen van berichten die zijn gepubliceerd in het onderwerp. Zie de [SqlFilter.SqlExpression][SqlFilter.SqlExpression]-syntaxis voor meer informatie over de expressies die u kunt gebruiken met een SQL-filter.

Omdat het standaardfilter **MatchAll** automatisch wordt toegepast op alle nieuwe abonnementen, moet u dit filter verwijderen uit abonnementen die u wilt filteren. Als u dit niet doet, overschrijft **MatchAll** alle andere filters die u eventueel opgeeft. U kunt de standaardregel verwijderen met de methode `delete_rule` van het **ServiceBusService**-object.

In het volgende voorbeeld maakt u een abonnement in `mytopic` met de naam `HighMessages`, met een regel **SqlFilter** met de naam `HighMessageFilter`. Met de regel `HighMessageFilter` worden alleen berichten geselecteerd met een aangepaste eigenschap `messageposition` die groter is dan 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

In het volgende voorbeeld maakt u een abonnement in `mytopic` met de naam `LowMessages`, met een regel **SqlFilter** met de naam `LowMessageFilter`. Met de regel `LowMessageFilter` worden alleen berichten geselecteerd met een eigenschap `messageposition` die kleiner is dan of gelijk is aan 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Wanneer `AllMessages`, `HighMessages` en `LowMessages` alle van kracht zijn, worden berichten die worden verzonden naar `mytopic`, altijd bezorgd bij ontvangers van het `AllMessages`-abonnement. Berichten worden ook selectief bezorgd bij het `HighMessages`- of `LowMessages`-abonnement, afhankelijk van de eigenschapswaarde `messageposition` van het bericht. 

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp

Toepassingen gebruiken de methode `send_topic_message` van het **ServiceBusService**-object om berichten naar een Service Bus-onderwerp te verzenden.

In het volgende voorbeeld worden vijf testberichten naar het onderwerp `mytopic` verzonden. De eigenschapswaarde van de aangepaste `messageposition` is afhankelijk van de herhaling van de lus en bepaalt welke abonnementen de berichten ontvangen. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor berichtgrootten

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten dat een onderwerp kan bevatten, maar er is een limiet voor de totale grootte van de berichten die het onderwerp kan bevatten. U kunt tijdens het maken van het onderwerp de grootte ervan definiëren, met een bovengrens van 5 GB. 

Zie [Service Bus-quota][Service Bus quotas] voor meer informatie over quota.

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen

Toepassingen gebruiken de methode `receive_subscription_message` van het **ServiceBusService**-object om berichten van een abonnement te ontvangen. In het volgende voorbeeld worden berichten van het `LowMessages`-abonnement ontvangen en verwijderd wanneer ze zijn gelezen:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Met de optionele parameter `peek_lock` van `receive_subscription_message` wordt bepaald of Service Bus berichten verwijdert uit het abonnement wanneer ze zijn gelezen. De standaardmodus voor het ontvangen van berichten is *PeekLock*, of `peek_lock` ingesteld op **Waar**. Hierbij worden berichten gelezen (peek) en vergrendeld zonder dat ze uit het abonnement worden verwijderd. Elk bericht moet vervolgens expliciet worden voltooid om het uit het abonnement te verwijderen.

Als u berichten uit het abonnement wilt verwijderen wanneer ze zijn gelezen, kunt u de parameter `peek_lock` instellen op **False**, zoals in het voorgaande voorbeeld. Berichten verwijderen als onderdeel van de ontvangstbewerking is het eenvoudigste model, en werkt prima als de app toelaat dat berichten ontbreken als er een fout optreedt. Neem bijvoorbeeld een scenario waarin de toepassing een ontvangstaanvraag indient en vervolgens vastloopt voordat het bericht wordt verwerkt. Als het bericht bij ontvangst is verwijderd en de app opnieuw wordt gestart en opnieuw berichten gaat verwerken, heeft deze het bericht gemist dat is ontvangen vóór het vastlopen.

Als de toepassing niet toelaat dat berichten worden gemist, bestaat het ontvangen uit twee fasen. PeekLock zoekt naar het volgende bericht dat moet worden verwerkt, vergrendelt dit om te voorkomen dat andere verwerkende apps het ontvangen, en retourneert het bericht vervolgens naar de app. Nadat het bericht is verwerkt of opgeslagen, voltooit de app de tweede fase van het ontvangstproces door de methode `complete` in het **Message**-object aan te roepen.  Met de methode `complete` wordt het bericht gemarkeerd als verwerkt en wordt het uit het abonnement verwijderd.

In het volgende voorbeeld ziet u een PeekLock-scenario:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de app en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende app een bericht om de een of andere reden niet kan verwerken, kan deze de methode `unlock` in het **Message**-object aanroepen. Service Bus ontgrendelt het bericht in het abonnement en maakt het beschikbaar, zodat het opnieuw kan worden ontvangen, ofwel door dezelfde of door een andere verwerkende app.

Er is ook een time-out voor berichten die in het abonnement zijn vergrendeld. Als een app een bericht niet kan verwerken voordat de time-out voor vergrendeling verloopt, bijvoorbeeld als de app vastloopt, dan ontgrendelt Service Bus automatisch het bericht en maakt het dit beschikbaar, zodat het weer kan worden ontvangen.

Als een app vastloopt na het verwerken van een bericht, maar voordat deze de methode `complete` aanroept, wordt het bericht opnieuw bij de app bezorgd wanneer deze opnieuw wordt gestart. Deze werkwijze wordt ook wel *Ten minste één keer verwerken* genoemd. Elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw situatie geen dubbele verwerking toelaat, kunt u de eigenschap **MessageId** van het bericht gebruiken, die constant blijft tijdens de bezorgingspogingen, om dubbele berichtbezorging te verwerken. 

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen

Als u onderwerpen en abonnementen wilt verwijderen, gebruikt u de [Azure-portal][Azure portal] of de methode `delete_topic`. Met de volgende code verwijdert u het onderwerp met de naam `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Als u een onderwerp verwijdert, worden alle abonnementen op het onderwerp verwijderd. U kunt abonnementen ook afzonderlijk verwijderen. Met de volgende code wordt het abonnement met de naam `HighMessages` verwijderd uit het onderwerp `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Onderwerpen en abonnementen zijn standaard permanent en bestaan tot u ze verwijdert. Als u abonnementen automatisch wilt verwijderen nadat een bepaalde tijdsperiode is verstreken, kunt u de parameter [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) instellen voor het abonnement. 

> [!TIP]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunt u verbinding maken met een Service Bus-naamruimte en eenvoudig berichtenentiteiten beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren en de mogelijkheid om onderwerpen, wachtrijen, abonnementen, relayservices, notification hubs en event hubs te testen. 

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes van Service Bus-onderwerpen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie:

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression]-referentie

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md