---
title: 'Snelstartgids: Azure Service Bus onderwerpen gebruiken met python'
description: 'Snelstartgids: informatie over het gebruik van Azure Service Bus-onderwerpen en-abonnementen van python.'
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 8f7d47879a025742dbca6a5cafa634899e60ee68
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719180"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Snelstartgids: Service Bus-onderwerpen en-abonnementen gebruiken met python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en -abonnementen gebruikt. De voor beelden zijn geschreven in Python en gebruiken het [Azure PYTHON SDK-pakket][Azure Python package]. De volgende scenario's zijn van toepassing:

- Onderwerpen en abonnementen maken 
- Abonnements filters maken 
- Berichten verzenden naar een onderwerp 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen van uw Visual Studio-of MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Snelstartgids: gebruik de Azure Portal om een service bus onderwerp en abonnementen aan het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een service bus **naam ruimte** te maken en de **Connection String**op te halen.

    > [!NOTE]
    > U maakt een **onderwerp** en een **abonnement** op het onderwerp met behulp van **python** in deze Quick Start. 
3. Installeer het [Azure python-pakket][Azure Python package]. Raadpleeg de [installatie handleiding voor python](/azure/python/python-sdk-azure-install).

## <a name="create-a-topic"></a>Een onderwerp maken

Met het **ServiceBusService** -object kunt u werken met onderwerpen. Voeg de volgende code toe aan de bovenkant van een python-bestand waarin u programmatisch toegang wilt krijgen Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Met de volgende code wordt een **ServiceBusService** -object gemaakt. Vervang `mynamespace`, `sharedaccesskeyname`en `sharedaccesskey` door uw werkelijke naam ruimte, Shared Access Signature (SAS)-sleutel en sleutel waarde.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

U kunt de waarden voor de SAS-sleutel naam en-waarde ophalen uit de [Azure Portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

De methode `create_topic` biedt ook ondersteuning voor extra opties, waarmee u standaard instellingen voor het onderwerp, zoals de bericht tijd, een live of maximale onderwerps grootte kunt overschrijven. In het volgende voor beeld wordt de maximum grootte van het onderwerp ingesteld op 5 GB en een TTL-waarde (time to Live) van één minuut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Abonnementen maken

Abonnementen op onderwerpen worden ook gemaakt met het **ServiceBusService** -object. Abonnementen krijgen een naam en kunnen een optioneel filter hebben waarmee de set berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt geleverd.

> [!NOTE]
> Standaard zijn abonnementen permanent en blijven deze bestaan totdat ze, of het onderwerp waarop ze zijn geabonneerd, worden verwijderd.
> 
> U kunt de abonnementen automatisch laten verwijderen door de [eigenschap auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python)in te stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)

Als er geen filter is opgegeven bij het maken van een nieuw abonnement, wordt het **MatchAll** -filter (standaard) gebruikt. Wanneer het filter **MatchAll** wordt gebruikt, worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voor beeld wordt een abonnement met de naam `AllMessages` gemaakt en wordt het standaard **MatchAll** -filter gebruikt.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken

U kunt ook filters definiëren om aan te geven welke berichten die naar een onderwerp worden verzonden, moeten worden weer gegeven in een specifiek onderwerp-abonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is een **SqlFilter**, waarmee een SUBSET van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie de [SqlFilter.SqlExpression][SqlFilter.SqlExpression]-syntaxis voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

U kunt filters toevoegen aan een abonnement met behulp van de methode **create\_Rule** van het object **ServiceBusService** . Met deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

> [!NOTE]
> Omdat het standaard filter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaard filter verwijderen of de **MatchAll** alle andere filters overschrijven die u kunt opgeven. U kunt de standaard regel verwijderen met de methode `delete_rule` van het object **ServiceBusService** .
> 
> 

In het volgende voor beeld wordt een abonnement met de naam `HighMessages` gemaakt met een **SqlFilter** die alleen berichten selecteert die een aangepaste `messagenumber` eigenschap hebben die groter is dan 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Op dezelfde manier maakt het volgende voor beeld een abonnement met de naam `LowMessages` met een **SqlFilter** die alleen berichten selecteert die een `messagenumber` eigenschap hebben die kleiner is dan of gelijk is aan 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Wanneer er nu een bericht wordt verzonden naar `mytopic` wordt het altijd bezorgd bij ontvangers die zich hebben geabonneerd op het **AllMessages** -onderwerp, en worden ze selectief geleverd aan ontvangers die zich hebben geabonneerd op het **HighMessages** -en **lowmessages gemaakt** -onderwerp abonnementen (afhankelijk van de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp

Uw toepassing moet de methode `send_topic_message` van het object **ServiceBusService** gebruiken om een bericht naar een service bus onderwerp te verzenden.

In het volgende voor beeld ziet u hoe u vijf test berichten naar `mytopic`verzendt. De waarde van de eigenschap `messagenumber` van elk bericht varieert afhankelijk van de herhaling van de lus (deze bepaalt welke abonnementen het heeft ontvangen):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB. Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement

Er worden berichten ontvangen van een abonnement met behulp van de methode `receive_subscription_message` op het **ServiceBusService** -object:

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Berichten worden uit het abonnement verwijderd wanneer ze worden gelezen wanneer de para meter `peek_lock` is ingesteld op **Onwaar**. U kunt het bericht lezen (bekijken) en dit vergren delen zonder het uit de wachtrij te verwijderen door de para meter `peek_lock` in te stellen op **True**.

Het gedrag van het lezen en verwijderen van het bericht als onderdeel van de receive-bewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht kan verdragen als er een fout optreedt. Om dit gedrag te begrijpen, moet u rekening houden met een scenario waarin de consumer de ontvangst aanvraag uitgeeft en vervolgens vastloopt voordat het proces wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als verbruikt, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

Als de para meter `peek_lock` is ingesteld op **True**, wordt de ontvangst een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door `delete` methode aan te roepen voor het **bericht** object. Met de methode `delete` wordt het bericht gemarkeerd als verbruikt en wordt het verwijderd uit het abonnement.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de `unlock`-methode aanroepen voor het **bericht** object. Deze methode zorgt ervoor Service Bus om het bericht in het abonnement te ontgrendelen en het beschikbaar te maken om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), Service Bus het bericht automatisch ontgrendelen en maakt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode `delete` wordt aangeroepen, wordt het bericht opnieuw aan de toepassing geleverd wanneer het opnieuw wordt gestart. Dit gedrag wordt vaak genoemd. Ten minste één keer worden verwerkt\*; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Hiervoor kunt u de eigenschap **MessageId** van het bericht gebruiken, dat constant blijft tijdens de bezorgings pogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen

Onderwerpen en abonnementen zijn permanent, tenzij de [eigenschap auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) is ingesteld. Ze kunnen worden verwijderd via de [Azure Portal][Azure portal] of via een programma. In het volgende voor beeld ziet u hoe u het onderwerp met de naam `mytopic`verwijdert:

```python
bus_service.delete_topic('mytopic')
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. De volgende code laat zien hoe u een abonnement met de naam `HighMessages` verwijdert uit het onderwerp `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Service Bus onderwerpen hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Zie [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions].
* Verwijzing voor [SqlFilter. SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
