---
title: Veelgestelde vragen van Azure Service Bus (FAQ) | Microsoft Documenten
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen (FAQ) over Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760246"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - Veelgestelde vragen (FAQ)

In dit artikel worden enkele veelgestelde vragen over Microsoft Azure Service Bus besproken. U ook de [veelgestelde vragen](https://azure.microsoft.com/support/faq/) van Azure Support voor algemene Azure-prijs- en ondersteuningsinformatie bezoeken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Algemene vragen over Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) is een asynchrone messaging cloud platform waarmee u gegevens verzenden tussen ontkoppelde systemen. Microsoft biedt deze functie als een service, wat betekent dat u uw eigen hardware niet hoeft te hosten om deze te gebruiken.

### <a name="what-is-a-service-bus-namespace"></a>Wat is een servicebusnameruimte?
Een [naamruimte](service-bus-create-namespace-portal.md) biedt een scopingcontainer voor het aanpakken van Service Bus-bronnen in uw toepassing. Het maken van een naamruimte is noodzakelijk om Service Bus te gebruiken en is een van de eerste stappen om aan de slag te gaan.

### <a name="what-is-an-azure-service-bus-queue"></a>Wat is een Azure Service Bus-wachtrij?
Een [wachtrij met servicebus](service-bus-queues-topics-subscriptions.md) is een entiteit waarin berichten worden opgeslagen. Wachtrijen zijn handig wanneer u meerdere toepassingen hebt of meerdere delen van een gedistribueerde toepassing die met elkaar moeten communiceren. De wachtrij is vergelijkbaar met een distributiecentrum in dat meerdere producten (berichten) worden ontvangen en vervolgens vanaf die locatie worden verzonden.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Wat zijn Azure Service Bus-onderwerpen en -abonnementen?
Een onderwerp kan worden gevisualiseerd als een wachtrij en bij het gebruik van meerdere abonnementen wordt het een rijker berichtenmodel; in wezen een een-op-veel communicatie-instrument. Met dit publicatie-/abonneermodel (of *pub/sub)* kan een toepassing worden verzonden die een bericht verzendt naar een onderwerp met meerdere abonnementen om dat bericht door meerdere toepassingen te laten ontvangen.

### <a name="what-is-a-partitioned-entity"></a>Wat is een verdeelde entiteit?
Een conventionele wachtrij of onderwerp wordt behandeld door een enkele bericht makelaar en opgeslagen in een messaging store. Een [partitiewachtrij of onderwerp](service-bus-partitioning.md) wordt alleen ondersteund in de standaardberichtenlagen voor basis- en standaardberichten en wordt behandeld door meerdere berichtenmakelaars en opgeslagen in meerdere berichtenwinkels. Deze functie betekent dat de algehele doorvoer van een partitiewachtrij of onderwerp niet langer wordt beperkt door de prestaties van een enkele berichtenmakelaar of berichtenwinkel. Bovendien is een tijdelijke uitval van een berichtenwinkel niet beschikbaar voor een partitiewachtrij of onderwerp.

Bestellen is niet gegarandeerd bij het gebruik van partitieentiteiten. In het geval dat een partitie niet beschikbaar is, u nog steeds berichten verzenden en ontvangen van de andere partities.

 Gepartitioneerde entiteiten worden niet langer ondersteund in de [Premium SKU](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten heb ik nodig om te openen op de firewall? 
U de volgende protocollen met Azure Service Bus gebruiken om berichten te verzenden en te ontvangen:

- Advanced Message Queuing Protocol (AMQP)
- Service Bus Messaging Protocol (SBMP)
- HTTP

Zie de volgende tabel voor de uitgaande poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Event Hubs. 

| Protocol | Poorten | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 en 5672 | Zie [AMQP-protocolgids](service-bus-amqp-protocol-guide.md) | 
| SBMP (SBMP) | 9350 tot 9354 | Zie [Connectiviteitsmodus](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Welke IP-adressen moet ik op de witte lijst krijgen?
Voer de volgende stappen uit om de juiste IP-adressen voor uw verbindingen te vinden voor de juiste IP-adressen in de witte lijst voor uw verbindingen:

1. Voer de volgende opdracht uit vanuit een opdrachtprompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer`. Dit IP-adres is statisch. Het enige punt in de tijd zou veranderen is als u de naamruimte te herstellen op een ander cluster.

Als u de zoneredundantie voor uw naamruimte gebruikt, moet u een paar extra stappen uitvoeren: 

1. Eerst laat je nslookup op de naamruimte lopen.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de **niet-gezaghebbende antwoordsectie,** die zich in een van de volgende indelingen bevindt: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels s1, s2 en s3 om de IP-adressen van alle drie de instanties in drie beschikbaarheidszones te laten draaien, 


## <a name="best-practices"></a>Aanbevolen procedures
### <a name="what-are-some-azure-service-bus-best-practices"></a>Wat zijn enkele aanbevolen procedures voor Azure Service Bus?
Zie [Aanbevolen procedures voor prestatieverbeteringen met Service Bus][Best practices for performance improvements using Service Bus] : in dit artikel wordt beschreven hoe u de prestaties optimaliseren bij het uitwisselen van berichten.

### <a name="what-should-i-know-before-creating-entities"></a>Wat moet ik weten voordat ik entiteiten maak?
De volgende eigenschappen van een wachtrij en onderwerp zijn onveranderlijk. Houd rekening met deze beperking wanneer u uw entiteiten indient, omdat deze eigenschappen niet kunnen worden gewijzigd zonder een nieuwe vervangende entiteit te maken.

* Partitionering
* Sessies
* Detectie van duplicaten
* Expresentiteit

## <a name="pricing"></a>Prijzen
In dit gedeelte worden enkele veelgestelde vragen beantwoord over de prijsstructuur van Service Bus.

In [het servicebusprijs- en factureringsartikel](https://azure.microsoft.com/pricing/details/service-bus/) worden de factureringsmeters in servicebus uitgelegd. Zie [Service Bus-prijsgegevens](https://azure.microsoft.com/pricing/details/service-bus/)voor specifieke informatie over prijsopties voor servicebus.

U ook de [veelgestelde vragen](https://azure.microsoft.com/support/faq/) van Azure Support voor algemene Azure-prijsinformatie bezoeken. 

### <a name="how-do-you-charge-for-service-bus"></a>Hoe brengt u kosten in rekening voor servicebus?
Zie [Service Bus-prijsgegevens][Pricing overview]voor volledige informatie over de prijzen van Service Bus. Naast de aangegeven prijzen worden er kosten in rekening gebracht voor de bijbehorende gegevensoverdracht en het verlaten van het datacenter waarin uw toepassing is ingericht.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Welk gebruik van Service Bus is onderworpen aan gegevensoverdracht? Wat niet?
Elke gegevensoverdracht binnen een bepaalde Azure-regio wordt kosteloos verstrekt, evenals elke inkomende gegevensoverdracht. Gegevensoverdracht buiten een regio is onderworpen aan uitgaande kosten, die [hier](https://azure.microsoft.com/pricing/details/bandwidth/)kunnen worden gevonden.

### <a name="does-service-bus-charge-for-storage"></a>Brengt Service Bus kosten in rekening voor opslag?
Nee, Service Bus brengt geen kosten in rekening voor opslag. Er is echter een quotum dat de maximale hoeveelheid gegevens beperkt die per wachtrij/onderwerp kan worden gehandhaafd. Zie de volgende FAQ.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Ik heb een Service Bus Standard naamruimte. Waarom zie ik kosten onder resourcegroep '$system'?
Azure Service Bus heeft onlangs de factureringsonderdelen geüpgraded. Als u een servicebusstandaard-naamruimte hebt, ziet u daarom regelitems voor de bron '/abonnementen/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system' onder resourcegroep '$system'.

Deze kosten vertegenwoordigen de basiskosten per Azure-abonnement dat een servicebusstandaard-naamruimte heeft ingericht. 

Het is belangrijk op te merken dat dit geen nieuwe kosten zijn, d.w.z. ze bestonden ook in het vorige factureringsmodel. De enige wijziging is dat ze nu worden vermeld onder '$system'. Dit wordt gedaan vanwege overtredingen in het nieuwe factureringssysteem dat abonnementskosten groepeert, niet gekoppeld aan een specifieke resource, onder de resource-id '$system'.

## <a name="quotas"></a>Quota

Zie het [overzicht servicebusquota][Quotas overview]voor een lijst met servicebuslimieten en -quota.

### <a name="does-service-bus-have-any-usage-quotas"></a>Heeft Service Bus gebruiksquota?
Standaard stelt Microsoft voor elke cloudservice een geaggregeerd maandelijks gebruiksquotum in dat wordt berekend voor alle abonnementen van een klant. Als u meer dan deze limieten nodig hebt, u op elk gewenst moment contact opnemen met de klantenservice om uw behoeften te begrijpen en deze limieten op de juiste manier aan te passen. Voor Service Bus is het totale gebruiksquotum 5 miljard berichten per maand.

Hoewel Microsoft het recht behoudt om een klantaccount uit te schakelen dat de gebruiksquota in een bepaalde maand heeft overschreden, worden e-mailmeldingen verzonden en worden meerdere pogingen gedaan om contact op te nemen met een klant voordat actie wordt ondernomen. Klanten die deze quota overschrijden, zijn nog steeds verantwoordelijk voor kosten die de quota overschrijden.

Net als bij andere services op Azure, voert Service Bus een reeks specifieke quota af om ervoor te zorgen dat er een eerlijk gebruik van resources is. Meer informatie over deze quota vindt u in het [overzicht servicebusquota.][Quotas overview]

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hoe om te gaan met berichten van formaat > 1 MB?
Met servicebusberichten (wachtrijen en onderwerpen/abonnementen) kunnen toepassingen berichten verzenden van grootte tot 256 KB (standaardlaag) of 1 MB (premiumlaag). Als u te maken hebt met berichten van groter dan 1 MB, gebruikt u het claimcontrolepatroon dat in [deze blogpost wordt](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)beschreven.

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naamruimte maken nadat ik deze uit een ander abonnement heb verwijderd? 
Wanneer u een naamruimte uit een abonnement verwijdert, wacht u 4 uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders ontvangt u mogelijk het `Namespace already exists`volgende foutbericht: . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Wat zijn enkele uitzonderingen die worden gegenereerd door Azure Service Bus API's en hun voorgestelde acties?
Zie [Uitzonderingenoverzicht][Exceptions overview]voor een lijst met mogelijke uitzonderingen voor servicebussen.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Wat is een handtekening voor gedeelde toegang en welke talen ondersteunen het genereren van een handtekening?
Gedeelde toegangshandtekeningen zijn een verificatiemechanisme op basis van SHA-256 beveiligde hashes of URI's. Zie het artikel [Gedeelde toegangshandtekeningen][Shared Access Signatures] voor informatie over het genereren van uw eigen handtekeningen in Node.js, PHP, Java, Python en C#.

## <a name="subscription-and-namespace-management"></a>Abonnements- en naamruimtebeheer
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hoe migreer ik een naamruimte naar een ander Azure-abonnement?

U een naamruimte verplaatsen van het ene Azure-abonnement naar het andere, met behulp van de [Azure-portal](https://portal.azure.com) of PowerShell-opdrachten. Om de bewerking uit te voeren, moet de naamruimte al actief zijn. De gebruiker die de opdrachten uitvoert, moet een beheerder zijn op zowel de bron- als doelabonnementen.

#### <a name="portal"></a>Portal

Als u de Azure-portal wilt gebruiken om naamruimten van servicebus te migreren naar een ander abonnement, volgt u [hier](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)de aanwijzingen . 

#### <a name="powershell"></a>PowerShell

In de volgende reeks PowerShell-opdrachten wordt een naamruimte verplaatst van het ene Azure-abonnement naar het andere. Als u deze bewerking wilt uitvoeren, moet de naamruimte al actief zijn en moet de gebruiker die de PowerShell-opdrachten uitvoert, een beheerder zijn op zowel de bron- als doelabonnementen.

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
Zie de volgende artikelen voor meer informatie over Service Bus:

* [Introductie van Azure Service Bus Premium (blogpost)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introductie van Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Overzicht servicebus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
