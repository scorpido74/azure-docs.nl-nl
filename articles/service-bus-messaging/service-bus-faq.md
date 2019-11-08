---
title: Veelgestelde vragen over Azure Service Bus | Microsoft Docs
description: Beantwoordt enkele veelgestelde vragen over Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8a2a704f39aa678be819a7297b30f8926e414e56
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748447"
---
# <a name="service-bus-faq"></a>Veelgestelde vragen over Service Bus

In dit artikel worden enkele veelgestelde vragen over Microsoft Azure Service Bus beschreven. U kunt ook de [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/) bezoeken voor algemene Azure-prijzen en-ondersteunings informatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Algemene vragen over Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?
[Azure service bus](service-bus-messaging-overview.md) is een asynchroon Cloud platform voor berichten waarmee u gegevens kunt verzenden tussen losgekoppelde systemen. Micro soft biedt deze functie als een service, wat betekent dat u uw eigen hardware niet hoeft te hosten om deze te gebruiken.

### <a name="what-is-a-service-bus-namespace"></a>Wat is een Service Bus naam ruimte?
Een [naam ruimte](service-bus-create-namespace-portal.md) biedt een bereik container voor het adresseren van service bus resources in uw toepassing. Het maken van een naam ruimte is nodig voor het gebruik van Service Bus en is een van de eerste stappen in aan de slag.

### <a name="what-is-an-azure-service-bus-queue"></a>Wat is een Azure Service Bus wachtrij?
Een [Service Bus wachtrij](service-bus-queues-topics-subscriptions.md) is een entiteit waarin berichten worden opgeslagen. Wacht rijen zijn handig wanneer u meerdere toepassingen hebt of meerdere delen van een gedistribueerde toepassing die met elkaar moeten communiceren. De wachtrij is vergelijkbaar met een distributie centrum waarin meerdere producten (berichten) worden ontvangen en vervolgens vanaf die locatie worden verzonden.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Wat zijn Azure Service Bus onderwerpen en abonnementen?
Een onderwerp kan worden gevisualiseerd als een wachtrij en wanneer meerdere abonnementen worden gebruikt, wordt het een rijkere berichten model. in wezen een een-op-veel-communicatie hulpprogramma. Dit model voor publiceren/abonneren (of *pub/sub*) maakt het mogelijk dat een toepassing die een bericht verzendt naar een onderwerp met meerdere abonnementen, het bericht ontvangt dat door meerdere toepassingen wordt ontvangen.

### <a name="what-is-a-partitioned-entity"></a>Wat is een gepartitioneerde entiteit?
Een conventionele wachtrij of onderwerp wordt verwerkt door één Message Broker en opgeslagen in één berichten archief. Een [gepartitioneerde wachtrij of onderwerp](service-bus-partitioning.md) wordt alleen ondersteund in de lagen basis en standaard berichten, maar wordt verwerkt door meerdere bericht brokers en opgeslagen in meerdere berichten archieven. Deze functie houdt in dat de algemene door Voer van een gepartitioneerde wachtrij of onderwerp niet langer wordt beperkt door de prestaties van één bericht Broker of berichten archief. Daarnaast wordt een tijdelijke onderbreking van een berichten archief niet weer gegeven in een gepartitioneerde wachtrij of onderwerp niet beschikbaar.

De volg orde wordt niet gegarandeerd wanneer gepartitioneerde entiteiten worden gebruikt. In het geval dat een partitie niet beschikbaar is, kunt u nog steeds berichten verzenden en ontvangen van de andere partities.

 Gepartitioneerde entiteiten worden niet meer ondersteund in de [Premium-SKU](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten moet ik op de firewall openen? 
U kunt de volgende protocollen gebruiken met Azure Service Bus voor het verzenden en ontvangen van berichten:

- Advanced Message Queueing Protocol (AMQP)
- Service Bus Messa ging Protocol (SBMP)
- HTTP

Zie de volgende tabel voor de uitgaande poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Event Hubs. 

| Protocol | Poorten | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 en 5672 | Zie [AMQP protocol Guide (Engelstalig](service-bus-amqp-protocol-guide.md) ) | 
| SBMP | 9350 tot 9354 | Zie [connectiviteits modus](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Welke IP-adressen moet ik white list?
Ga als volgt te werk om de juiste IP-adressen voor uw verbindingen te zoeken naar een witte lijst:

1. Voer de volgende opdracht uit vanaf een opdracht prompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer`. Dit IP-adres is statisch. Wanneer u de naam ruimte op een ander cluster herstelt, wordt het enige tijdstip gewijzigd dat het zou veranderen.

Als u de zone redundantie voor uw naam ruimte gebruikt, moet u een aantal extra stappen uitvoeren: 

1. Eerst voert u Nslookup uit op de naam ruimte.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de sectie **niet-bindende antwoord** , die een van de volgende indelingen heeft: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels S1, S2 en S3 om de IP-adressen te verkrijgen van alle drie de instanties die worden uitgevoerd in drie beschikbaarheids zones, 


## <a name="best-practices"></a>Aanbevolen procedures
### <a name="what-are-some-azure-service-bus-best-practices"></a>Wat zijn de aanbevolen procedures Azure Service Bus?
Bekijk [Aanbevolen procedures voor prestatie verbeteringen met behulp van service bus][Best practices for performance improvements using Service Bus] : in dit artikel wordt beschreven hoe u de prestaties optimaliseert bij het uitwisselen van berichten.

### <a name="what-should-i-know-before-creating-entities"></a>Wat moet ik weten voordat ik entiteiten Maak?
De volgende eigenschappen van een wachtrij en onderwerp zijn onveranderbaar. Houd rekening met deze beperking bij het inrichten van uw entiteiten, omdat deze eigenschappen niet kunnen worden gewijzigd zonder dat er een nieuwe vervangende entiteit wordt gemaakt.

* Partitionering
* Sessies
* Detectie van duplicaten
* Express-entiteit

## <a name="pricing"></a>Prijzen
In deze sectie vindt u antwoorden op enkele veelgestelde vragen over de Service Bus prijs structuur.

In het artikel [Service Bus prijzen en facturering](https://azure.microsoft.com/pricing/details/service-bus/) worden de facturerings meters in service bus uitgelegd. Zie [Service Bus prijs informatie](https://azure.microsoft.com/pricing/details/service-bus/)voor specifieke informatie over service bus prijs opties.

U kunt ook de [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/) bezoeken voor algemene informatie over Azure-prijzen. 

### <a name="how-do-you-charge-for-service-bus"></a>Hoe worden de kosten in rekening gebracht voor Service Bus?
Zie [Service Bus prijs informatie][Pricing overview]voor volledige informatie over service bus prijzen. Naast de genoteerde prijzen worden er kosten in rekening gebracht voor de bijbehorende gegevens overdrachten voor uitgaand verkeer buiten het Data Center waarin uw toepassing is ingericht.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Welk gebruik van Service Bus is onderhevig aan gegevens overdracht? Wat is dat niet?
Elke gegevens overdracht binnen een bepaalde Azure-regio wordt gratis geleverd, evenals een binnenkomende gegevens overdracht. Gegevens overdracht buiten een regio is onderhevig aan uitstaande kosten, die [hier](https://azure.microsoft.com/pricing/details/bandwidth/)kunnen worden gevonden.

### <a name="does-service-bus-charge-for-storage"></a>Worden er Service Bus kosten in rekening gebracht voor opslag?
Nee, er worden geen kosten in rekening gebracht voor opslag Service Bus. Er is echter een quotum dat de maximale hoeveelheid gegevens beperkt die kan worden bewaard per wachtrij/onderwerp. Zie de volgende veelgestelde vragen.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Ik heb een Service Bus standaard naam ruimte. Waarom worden kosten onder resource groep ' $system ' weer geven?
De facturerings onderdelen Azure Service Bus recent bijgewerkt. Als u een Service Bus standaard naam ruimte hebt, ziet u mogelijk regel items voor de resource '/Subscriptions/< azure_subscription_id >/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system ' onder resource groep $ systeem.

Deze kosten vertegenwoordigen de basis kosten per Azure-abonnement dat een Service Bus standaard naam ruimte heeft ingericht. 

Het is belang rijk te weten dat dit geen nieuwe kosten zijn, dat wil zeggen dat ze ook in het vorige facturerings model aanwezig waren. De enige wijziging is dat ze nu worden vermeld onder $system. Dit wordt gedaan door Contraints in het nieuwe facturerings systeem waarbij kosten op abonnements niveau worden gegroepeerd, niet zijn gebonden aan een specifieke resource, onder de resource-id $system.

## <a name="quotas"></a>Quota

Zie het [overzicht van service bus quota's][Quotas overview]voor een lijst met Service Bus limieten en quota's.

### <a name="does-service-bus-have-any-usage-quotas"></a>Zijn er gebruiks quota's Service Bus?
Standaard stelt micro soft voor elke Cloud service een cumulatief maandelijks gebruiks quotum in dat wordt berekend over alle abonnementen van een klant. Als u meer dan deze limieten nodig hebt, kunt u op elk gewenst moment contact opnemen met de klanten service om inzicht te krijgen in uw behoeften en deze limieten op de juiste manier aan te passen. Voor Service Bus is het statistische gebruiks quotum 5.000.000.000 berichten per maand.

Micro soft behoudt zich het recht voor het uitschakelen van een klant account dat de gebruiks quota's in een bepaalde maand heeft overschreden, maar e-mail meldingen worden verzonden en er worden meerdere pogingen gedaan om contact op te nemen met een klant voordat actie wordt ondernomen. Klanten die deze quota overschrijden, zijn nog steeds verantwoordelijk voor de kosten die de quota overschrijden.

Net als bij andere services in azure dwingt Service Bus een set specifieke quota's af om ervoor te zorgen dat resources redelijk worden gebruikt. In het [overzicht van service bus quota's][Quotas overview]vindt u meer informatie over deze quota's.

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hoe kan ik de grootte van een bericht afhandelen > 1 MB?
Service Bus Messa ging Services (wacht rijen en onderwerpen/abonnementen) toestaan dat toepassingen berichten met een grootte van Maxi maal 256 KB (Standard-laag) of 1 MB (Premium-laag) kunnen verzenden. Als u wilt omgaan met berichten van meer dan 1 MB, gebruikt u het claim controle patroon dat in [dit blog bericht](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)wordt beschreven.

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naam ruimte maken nadat ik deze heb verwijderd uit een ander abonnement? 
Wanneer u een naam ruimte uit een abonnement verwijdert, wacht u vier uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders wordt het volgende fout bericht weer gegeven: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Wat zijn de uitzonde ringen die worden gegenereerd door Azure Service Bus Api's en de voorgestelde acties?
Zie [overzicht van uitzonde ringen][Exceptions overview]voor een lijst met mogelijke service bus uitzonde ringen.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Wat is een Shared Access Signature en welke talen ondersteunen het genereren van een hand tekening?
Shared Access Signatures zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. Zie het artikel over [gedeelde hand tekeningen][Shared Access Signatures] voor meer informatie over het genereren van uw eigen hand tekeningen in node. C#js, PHP, Java, python en.

## <a name="subscription-and-namespace-management"></a>Abonnement-en naam ruimte beheer
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hoe kan ik een naam ruimte migreren naar een ander Azure-abonnement?

U kunt een naam ruimte van het ene Azure-abonnement naar het andere verplaatsen met behulp van de [Azure Portal](https://portal.azure.com) -of Power shell-opdrachten. De naam ruimte moet al actief zijn om de bewerking uit te voeren. De gebruiker die de opdrachten uitvoert, moet een beheerder zijn voor zowel de bron-als de doel abonnementen.

#### <a name="portal"></a>Portal

Volg de [onderstaande](../azure-resource-manager/resource-group-move-resources.md#use-the-portal)instructies als u de Azure Portal wilt gebruiken om service bus naam ruimten te migreren naar een ander abonnement. 

#### <a name="powershell"></a>PowerShell

Met de volgende reeks Power shell-opdrachten wordt een naam ruimte van het ene Azure-abonnement naar het andere verplaatst. Als u deze bewerking wilt uitvoeren, moet de naam ruimte al actief zijn. de gebruiker die de Power shell-opdrachten uitvoert, moet een beheerder zijn voor zowel de bron-als de doel abonnementen.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Bus:

* [Inleiding tot Azure Service Bus Premium (blog bericht)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introductie van Azure Service Bus Premium (Channel 9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Overzicht van Service Bus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
