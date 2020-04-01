---
title: 'Snelstart: Azure Service Bus-onderwerpen en -abonnementen gebruiken met Python'
description: In dit artikel ziet u hoe u een Azure Service Bus-onderwerp, abonnement, berichten naar een onderwerp verzendt en berichten van een abonnement ontvangt.
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
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478066"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Snelstart: ServiceBus-onderwerpen en abonnementen gebruiken met Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Python gebruiken met azure servicebus-onderwerpen en -abonnementen. De voorbeelden gebruiken het [Azure Python SDK-pakket][Azure Python package] om: 

- Onderwerpen en abonnementen op onderwerpen maken
- Abonnementsfilters en -regels maken
- Berichten verzenden naar onderwerpen 
- Berichten ontvangen van abonnementen
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Een naamruimte voor servicebus die is gemaakt door de stappen te volgen bij [Quickstart: gebruik de Azure-portal om een servicebusonderwerp en -abonnementen te maken.](service-bus-quickstart-topics-subscriptions-portal.md) Kopieer de naamnaam naamruimte, de naam van gedeelde toegangssleutel en de primaire sleutelwaarde vanuit het scherm **Beleid voor gedeelde toegang** om later in deze quickstart te gebruiken. 
- Python 3,4x of hoger, met het [Azure Python SDK-pakket][Azure Python package] geïnstalleerd. Zie de [Python-installatiehandleiding](/azure/developer/python/azure-sdk-install)voor meer informatie.

## <a name="create-a-servicebusservice-object"></a>Een ServiceBusService-object maken

Met een **ServiceBusService-object** u werken met onderwerpen en abonnementen op onderwerpen. Als u programmatisch toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe boven aan uw Python-bestand:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Voeg de volgende code toe om een **ServiceBusService-object** te maken. Vervang `<namespace>` `<sharedaccesskeyname>`, `<sharedaccesskeyvalue>` en met de naam van de servicebusnaam, de sleutelnaam van De Gedeelde Toegangshandtekening (SAS) en de primaire sleutelwaarde. U deze waarden vinden onder **Beleid voor gedeelde toegang** in de naamruimte van uw servicebus in de [Azure-portal.][Azure portal]

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Een onderwerp maken

De volgende code `create_topic` gebruikt de methode om `mytopic`een servicebusonderwerp te maken met de standaardinstellingen:

```python
bus_service.create_topic('mytopic')
```

U onderwerpopties gebruiken om standaardonderwerpinstellingen te overschrijven, zoals berichttijd om te leven (TTL) of maximale onderwerpgrootte. In het volgende voorbeeld `mytopic` wordt een onderwerp gemaakt met de naam van een maximaal onderwerp van 5 GB en standaardbericht TTL van één minuut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Abonnementen maken

U gebruikt ook het object **ServiceBusService** om abonnementen op onderwerpen te maken. Een abonnement kan een filter hebben om de berichtset te beperken die wordt geleverd in de virtuele wachtrij. Als u geen filter opgeeft, gebruiken nieuwe abonnementen het standaard **Filter MatchAll,** waarmee alle berichten die zijn gepubliceerd in het onderwerp in de virtuele wachtrij van het abonnement worden geplaatst. In het volgende voorbeeld `mytopic` `AllMessages` wordt een abonnement op de naam gemaakt dat het filter **MatchAll** gebruikt:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Filters gebruiken met abonnementen

Gebruik `create_rule` de methode van het object **ServiceBusService** om de berichten te filteren die in een abonnement worden weergegeven. U regels opgeven wanneer u het abonnement maakt of regels toevoegen aan bestaande abonnementen.

Het meest flexibele type filter is een **SqlFilter**, dat een subset van SQL-92 gebruikt. SQL-filters werken op basis van de eigenschappen van berichten die zijn gepubliceerd in het onderwerp. Zie de syntaxis [sqlfilter SqlFilter.SqlExpression][SqlFilter.SqlExpression] voor meer informatie over de expressies die u met een SQL-filter gebruiken.

Omdat het standaardfilter **MatchAll** automatisch van toepassing is op alle nieuwe abonnementen, moet u het verwijderen uit abonnementen die u wilt filteren of overschrijft **MatchAll** alle andere filters die u opgeeft. U de standaardregel `delete_rule` verwijderen met de methode van het **object ServiceBusService.**

In het volgende voorbeeld `mytopic` `HighMessages`wordt een abonnement op `HighMessageFilter`de naam gemaakt met een **SqlFilter-regel** met de naam . De `HighMessageFilter` regel selecteert alleen berichten `messageposition` met een aangepaste eigenschap groter dan 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

In het volgende voorbeeld `mytopic` `LowMessages`wordt een abonnement op `LowMessageFilter`de naam gemaakt met een **SqlFilter-regel** met de naam . De `LowMessageFilter` regel selecteert alleen berichten `messageposition` met een eigenschap die kleiner is dan of gelijk is aan 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Met `AllMessages` `HighMessages`, `LowMessages` , en alles in `mytopic` feite, berichten verzonden `AllMessages` naar worden altijd geleverd aan ontvangers van het abonnement. Berichten worden ook selectief geleverd `HighMessages` `LowMessages` aan de of abonnement, `messageposition` afhankelijk van de waarde van het bericht eigenschap. 

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp

Toepassingen gebruiken `send_topic_message` de methode van het **object ServiceBusService** om berichten naar een servicebusonderwerp te verzenden.

In het volgende voorbeeld worden `mytopic` vijf testberichten naar het onderwerp verzonden. De `messageposition` aangepaste eigenschapswaarde is afhankelijk van de iteratie van de lus en bepaalt welke abonnementen de berichten ontvangen. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor berichtgrootte

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet aan het aantal berichten dat een onderwerp kan bevatten, maar er is een limiet op de totale grootte van de berichten die het onderwerp bevat. U de onderwerpgrootte definiëren bij het maken van de tijd, met een bovengrens van 5 GB. 

Zie [ServiceBus-quota voor][Service Bus quotas]meer informatie over quota.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement

Toepassingen gebruiken `receive_subscription_message` de methode op het object **ServiceBusService** om berichten van een abonnement te ontvangen. In het volgende voorbeeld `LowMessages` worden berichten van het abonnement ontvangen en deze verwijderd terwijl ze worden gelezen:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

De `peek_lock` optionele `receive_subscription_message` parameter bepaalt of Service Bus berichten uit het abonnement verwijdert terwijl ze worden gelezen. De standaardmodus voor het ontvangen `peek_lock` van berichten is *PeekLock*of ingesteld op **True,** dat berichten leest (gluurt) en vergrendelt zonder ze uit het abonnement te verwijderen. Elk bericht moet vervolgens expliciet worden ingevuld om het uit het abonnement te verwijderen.

Als u berichten uit het abonnement wilt verwijderen terwijl `peek_lock` ze worden gelezen, u de parameter instellen op **False,** zoals in het voorgaande voorbeeld. Het verwijderen van berichten als onderdeel van de ontvangstbewerking is het eenvoudigste model en werkt prima als de toepassing ontbrekende berichten kan tolereren als er een storing optreedt. Als u dit gedrag wilt begrijpen, houdt u rekening met een scenario waarin de toepassing een ontvangstverzoek uitgeeft en vervolgens vastloopt voordat deze wordt verwerkt. Als het bericht is verwijderd nadat het is ontvangen, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, heeft het het bericht gemist dat het vóór de crash heeft ontvangen.

Als uw toepassing gemiste berichten niet kan tolereren, wordt het ontvangen een bewerking in twee fasen. PeekLock vindt het volgende bericht te worden verbruikt, vergrendelt het om te voorkomen dat andere consumenten het ontvangen en stuurt het terug naar de toepassing. Nadat de toepassing het bericht hebt verwerkt of opgeslagen, voltooit `complete` de toepassing de tweede fase van het ontvangstproces door de methode op het **object Bericht** aan te roepen.  De `complete` methode markeert het bericht als wordt verbruikt en verwijdert het uit het abonnement.

In het volgende voorbeeld wordt een scenario voor het vergrendelen van een kijkje getoond:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Toepassingscrashes en onleesbare berichten verwerken

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing om de een of andere reden `unlock` een bericht niet kan verwerken, kan deze de methode op het object **Message** aanroepen. Service Bus ontgrendelt het bericht binnen het abonnement en maakt het beschikbaar om opnieuw te worden ontvangen, hetzij door dezelfde of een andere verbruikende toepassing.

Er is ook een time-out voor berichten die zijn vergrendeld binnen het abonnement. Als een toepassing een bericht niet verwerkt voordat de vergrendelingstime-out verloopt, bijvoorbeeld als de toepassing vastloopt, ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te worden ontvangen.

Als een toepassing vastloopt na het `complete` verwerken van een bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw naar de toepassing verzonden wanneer het opnieuw wordt opgestart. Dit gedrag wordt vaak *At-least-once Processing*genoemd. Elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw scenario dubbele verwerking niet kan tolereren, u de eigenschap **MessageId** van het bericht, dat constant blijft bij leveringspogingen, gebruiken om dubbele berichtbezorging af te handelen. 

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen

Als u onderwerpen en abonnementen wilt `delete_topic` verwijderen, gebruikt u de [Azure-portal][Azure portal] of de methode. Met de volgende code `mytopic`wordt het onderwerp met de naam verwijderd:

```python
bus_service.delete_topic('mytopic')
```

Als u een onderwerp verwijdert, worden alle abonnementen op het onderwerp verwijderd. U abonnementen ook onafhankelijk van elkaar verwijderen. Met de volgende code `HighMessages` wordt `mytopic` het abonnement verwijderd dat uit het onderwerp wordt genoemd:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Standaard zijn onderwerpen en abonnementen blijvend en bestaan ze totdat u ze verwijdert. Als u abonnementen automatisch wilt verwijderen nadat een bepaalde periode is verstreken, u de [parameter auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) op het abonnement instellen. 

> [!TIP]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met Service Bus Explorer u verbinding maken met een naamruimte van een ServiceBus en eenvoudig berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/exportfunctionaliteit en de mogelijkheid om onderwerpen, wachtrijen, abonnementen, relayservices, meldingshubs en gebeurtenishubs te testen. 

## <a name="next-steps"></a>Volgende stappen

Nu je de basisprincipes van Service Bus-onderwerpen hebt geleerd, volg je deze links voor meer informatie:

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression-verwijzing][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
