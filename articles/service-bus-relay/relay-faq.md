---
title: Veelgestelde vragen over Azure Relay | Microsoft Documenten
description: In dit artikel vindt u antwoorden op enkele van de veelgestelde vragen over de Azure Relay-service.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513997"
---
# <a name="azure-relay-faqs"></a>Veelgestelde vragen over Azure Relay

In dit artikel worden enkele veelgestelde vragen (veelgestelde vragen) over [Azure Relay beantwoord.](https://azure.microsoft.com/services/service-bus/) Zie de [veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq/)voor algemene Azure-prijs- en ondersteuningsinformatie.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Algemene vragen
### <a name="what-is-azure-relay"></a>Wat is Azure Relay?
De [Azure Relay-service](relay-what-is-it.md) faciliteert uw hybride toepassingen door u te helpen services die zich binnen een bedrijfsnetwerk bevinden, veiliger bloot te stellen aan de openbare cloud. U de services blootstellen zonder een firewallverbinding te openen en zonder ingrijpende wijzigingen in een bedrijfsnetwerkinfrastructuur.

### <a name="what-is-a-relay-namespace"></a>Wat is een relay-naamruimte?
Een [naamruimte](relay-create-namespace-portal.md) is een scopingcontainer die u gebruiken om Relay-bronnen in uw toepassing aan te pakken. U moet een naamruimte maken om Relay te gebruiken. Dit is een van de eerste stappen om aan de slag te gaan.

### <a name="what-happened-to-service-bus-relay-service"></a>Wat is er gebeurd met de Service Bus Relay service?
De eerder genoemde Service Bus Relay-service heet nu [WCF Relay.](service-bus-relay-tutorial.md) U deze service gewoon blijven gebruiken. De functie Hybride verbindingen is een bijgewerkte versie van een service die is getransplanteerd vanuit Azure BizTalk Services. WCF Relay en Hybrid Connections worden beide nog steeds ondersteund.

## <a name="pricing"></a>Prijzen
In deze sectie worden enkele veelgestelde vragen beantwoord over de prijsstructuur van Relay. U ook de [veelgestelde vragen](https://azure.microsoft.com/support/faq/) van Azure Support voor algemene Azure-prijsinformatie bekijken. Zie [Service Bus-prijsgegevens][Pricing overview]voor volledige informatie over de prijzen van relay.

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hoe rekent u voor hybride verbindingen en WCF Relay?
Zie de tabel [Hybride verbindingen en WCF-relais][Pricing overview] op de pagina ServiceBus-prijzen voor volledige informatie over de prijzen voor relais. Naast de prijzen die op die pagina worden vermeld, worden er kosten in rekening gebracht voor de bijbehorende gegevensoverdracht en het overschrijden van het datacenter waarin uw toepassing is ingericht.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hoe worden er kosten in rekening gebracht voor hybride verbindingen?
Hier volgen drie voorbeeldfactureringsscenario's voor hybride verbindingen:

*   Scenario 1:
    *   U hebt één listener, zoals een instantie van de Hybrid Connections Manager die de hele maand is geïnstalleerd en continu wordt uitgevoerd.
    *   U verzendt gedurende de maand 3 GB aan gegevens over de verbinding. 
    *   Uw totale kosten bedragen $5.
*   Scenario 2:
    *   U hebt één listener, zoals een instantie van de Hybrid Connections Manager die de hele maand is geïnstalleerd en continu wordt uitgevoerd.
    *   U verzendt gedurende de maand 10 GB aan gegevens over de verbinding.
    *   Uw totale kosten bedragen $7.50. Dat is $ 5 voor de verbinding en de eerste 5 GB + $ 2,50 voor de extra 5 GB aan gegevens.
*   Scenario 3:
    *   U hebt twee exemplaren, A en B, van de Hybrid Connections Manager geïnstalleerd en continu uitgevoerd voor de hele maand.
    *   U verzendt gedurende de maand 3 GB aan gegevens via verbinding A.
    *   U verzendt gedurende de maand 6 GB aan gegevens via verbinding B.
    *   Uw totale kosten bedragen $10.50. Dat is $ 5 voor verbinding A + $ 5 voor verbinding B + $ 0,50 (voor de zesde gigabyte op verbinding B).

Houd er rekening mee dat de prijzen die in de voorbeelden worden gebruikt, alleen van toepassing zijn tijdens de previewperiode Hybride verbindingen. Prijzen kunnen worden gewijzigd bij de algemene beschikbaarheid van hybride verbindingen.

### <a name="how-are-hours-calculated-for-relay"></a>Hoe worden uren berekend voor Relay?

WCF Relay is alleen beschikbaar in standaardnaamruimten. Prijzen en [verbindingsquota](../service-bus-messaging/service-bus-quotas.md) voor relais zijn anders niet gewijzigd. Dit betekent dat relays nog steeds in rekening worden gebracht op basis van het aantal berichten (niet bewerkingen) en relaisuren. Zie voor meer informatie de tabel ['Hybride verbindingen en WCF-relais'](https://azure.microsoft.com/pricing/details/service-bus/) op de pagina met prijsdetails.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Wat gebeurt er als ik meer dan één luisteraar heb aangesloten op een specifiek relais?
In sommige gevallen kan een enkel relais meerdere verbonden listeners hebben. Een relais wordt als geopend beschouwd wanneer ten minste één relaislistener ermee verbonden is. Het toevoegen van listeners aan een open relais resulteert in extra relaisuren. Het aantal relayafzenders (clients die berichten aanroepen of verzenden naar relais) die zijn verbonden met een relais, heeft geen invloed op de berekening van relaisuren.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hoe wordt de berichtenmeter berekend voor WCF Relays?
(**Dit geldt alleen voor WCF relais. Berichten zijn geen kosten voor hybride verbindingen.**)

Over het algemeen worden factureerbare berichten voor relays berekend met dezelfde methode die wordt gebruikt voor tussengesloten entiteiten (wachtrijen, onderwerpen en abonnementen), die eerder zijn beschreven. Er zijn echter enkele opmerkelijke verschillen.

Het verzenden van een bericht naar een Service Bus relay wordt behandeld als een "full through" verzenden naar de relaisluisteraar die het bericht ontvangt. Het wordt niet behandeld als een verzendbewerking naar het servicebusrelais, gevolgd door een levering aan de relaisluisteraar. Een request-reply stijl service aanroep (van maximaal 64 KB) tegen een relais luisteraar resulteert in twee factureerbare berichten: een factureerbaar bericht voor de aanvraag en een factureerbaar bericht voor het antwoord (ervan uitgaande dat het antwoord is ook 64 KB of kleiner). Dit is anders dan het gebruik van een wachtrij om te bemiddelen tussen een client en een service. Als u een wachtrij gebruikt om te bemiddelen tussen een client en een service, vereist hetzelfde aanvraag-antwoordpatroon een verzoek om naar de wachtrij te worden verzonden, gevolgd door een wachtrij/levering van de wachtrij naar de service. Dit wordt gevolgd door een antwoord dat naar een andere wachtrij wordt verzonden en een wachtrij/levering van die wachtrij naar de client. Met behulp van dezelfde grootte veronderstellingen in de hele (tot 64 KB), de bemiddelde wachtrij patroon resulteert in 4 factureerbare berichten. Er wordt twee keer zoveel in rekening gebracht als het aantal berichten dat u uitvoert met behulp van relay. Natuurlijk zijn er voordelen aan het gebruik van wachtrijen om dit patroon te bereiken, zoals duurzaamheid en belastingnivellering. Deze voordelen kunnen de extra kosten rechtvaardigen.

Relais die worden geopend met behulp van de **netTCPRelay** WCF-binding behandelen berichten niet als afzonderlijke berichten, maar als een stroom van gegevens die door het systeem stromen. Wanneer u deze binding gebruikt, hebben alleen de afzender en de luisteraar inzicht in de inlijsten van de afzonderlijke verzonden en ontvangen berichten. Voor relays die de **netTCPRelay-binding** gebruiken, worden alle gegevens behandeld als een stroom voor het berekenen van factureerbare berichten. In dit geval berekent Service Bus de totale hoeveelheid gegevens die via elk afzonderlijk relais worden verzonden of ontvangen op een basis van 5 minuten. Vervolgens wordt die totale hoeveelheid gegevens verdeeld door 64 KB om het aantal factureerbare berichten voor dat relais in die periode te bepalen.

## <a name="quotas"></a>Quota
| Naam quotum | Bereik |  Opmerkingen | Waarde |
| --- | --- | --- | --- |
| Gelijktijdige luisteraars op een relais |Entiteit |Latere aanvragen voor aanvullende verbindingen worden afgewezen en een uitzondering wordt ontvangen door de belcode. |25 |
| Gelijktijdige relayverbindingen per alle relayeindpunten in een servicenaamruimte |Naamruimte |- |5.000 |
| Eindpunten relay en per servicenaamruimte |Naamruimte |- |10.000 |
| Berichtgrootte voor [NetOnewayRelayBinding en](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) [NetEventRelayBinding relays](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Naamruimte |Binnenkomende berichten die deze quota overschrijden, worden afgewezen en er wordt een uitzondering ontvangen door de aanroepcode. |64 kB |
| Berichtgrootte voor [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) en [NetTcpRelayBinding relays](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Naamruimte |Geen limiet op de grootte van het bericht. |Onbeperkt |

### <a name="does-relay-have-any-usage-quotas"></a>Heeft Relay gebruiksquota?
Standaard stelt Microsoft voor elke cloudservice een geaggregeerd maandelijks gebruiksquotum in dat wordt berekend voor alle abonnementen van een klant. We begrijpen dat uw behoeften soms deze limieten kunnen overschrijden. U op elk gewenst moment contact opnemen met de klantenservice, zodat we uw behoeften kunnen begrijpen en deze limieten op de juiste manier kunnen aanpassen. Voor ServiceBus zijn de geaggregeerde gebruiksquota als volgt:

* 5 miljard berichten
* 2 miljoen estafette-uren

Hoewel we ons het recht voorbehouden om een account uit te schakelen dat de maandelijkse gebruiksquota overschrijdt, bieden we een e-mailmelding en doen we meerdere pogingen om contact op te nemen met de klant voordat we actie ondernemen. Klanten die deze quota overschrijden, zijn nog steeds verantwoordelijk voor extra kosten.

### <a name="naming-restrictions"></a>Naamgevingsbeperkingen
Een naamruimte van een relais moet tussen de 6 en 50 tekens lang zijn.

## <a name="subscription-and-namespace-management"></a>Abonnements- en naamruimtebeheer
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hoe migreer ik een naamruimte naar een ander Azure-abonnement?

Als u een naamruimte wilt verplaatsen van het ene Azure-abonnement naar een ander abonnement, u de [Azure-portal](https://portal.azure.com) gebruiken of PowerShell-opdrachten gebruiken. Als u een naamruimte naar een ander abonnement wilt verplaatsen, moet de naamruimte al actief zijn. De gebruiker die de opdrachten uitvoert, moet een administratorgebruiker zijn op zowel de bron- als doelabonnementen.

#### <a name="azure-portal"></a>Azure Portal

Zie Resources verplaatsen [naar een nieuwe brongroep of -abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)als u de Azure-portal wilt gebruiken om naamruimten voor Azure Relay van het ene abonnement naar een ander abonnement te migreren. 

#### <a name="powershell"></a>PowerShell

Als u PowerShell wilt gebruiken om een naamruimte van het ene Azure-abonnement naar een ander abonnement te verplaatsen, gebruikt u de volgende reeks opdrachten. Als u deze bewerking wilt uitvoeren, moet de naamruimte al actief zijn en moet de gebruiker die de PowerShell-opdrachten uitvoert, een beheerdergebruiker zijn op zowel de bron- als doelabonnementen.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Wat zijn enkele uitzonderingen die worden gegenereerd door Azure Relay API's en voorgestelde acties die u uitvoeren?
Zie [Uitzonderingen op relais][Relay exceptions]voor een beschrijving van veelvoorkomende uitzonderingen en voorgestelde acties.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Wat is een handtekening voor gedeelde toegang en welke talen kan ik gebruiken om een handtekening te genereren?
Shared Access Signatures (SAS) zijn een verificatiemechanisme gebaseerd op SHA-256 beveiligde hashes of URI's. Zie [ServiceBus-verificatie met gedeelde toegangshandtekeningen][Shared Access Signatures]voor informatie over het genereren van uw eigen handtekeningen in Node.js, PHP, Python, Java, C en C#.

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Is het mogelijk om relay endpoints op de witte lijst te zetten?
Ja. De relayclient maakt verbindingen met de Azure Relay-service met volledig gekwalificeerde domeinnamen. Klanten kunnen een `*.servicebus.windows.net` vermelding toevoegen voor op firewalls die DNS-whitelisting ondersteunen.

## <a name="next-steps"></a>Volgende stappen
* [Een naamruimte maken](relay-create-namespace-portal.md)
* [Aan de slag met .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met knooppunten](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
