---
title: Overzicht van azure servicebusbeperking | Microsoft Documenten
description: Overzicht van Service Bus throttling - Standaard- en Premium-lagen.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598286"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Beperkingsbewerkingen op Azure Service Bus

Cloud native oplossingen geven een notie van onbeperkte resources die kunnen schalen met uw workload. Hoewel dit begrip meer waar is in de cloud dan bij on-premises systemen, zijn er nog steeds beperkingen die in de cloud bestaan.

Deze beperkingen kunnen leiden tot beperking van aanvragen voor clienttoepassingen in zowel standaard- als Premium-lagen, zoals besproken in dit artikel. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Beperking in de standaardlaag Azure Service Bus

De Azure Service Bus Standard-laag werkt als een multi-tenant-instelling met een prijsmodel voor betalen per gebruik. Hier delen meerdere naamruimten in hetzelfde cluster de toegewezen resources. Standaardlaag is de aanbevolen keuze voor ontwikkelaars-, test- en QA-omgevingen, samen met productiesystemen met een lage doorvoer.

In het verleden had Azure Service Bus grove beperkingslimieten die strikt afhankelijk waren van het gebruik van resources. Er is echter een mogelijkheid om de beperkingslogica te verfijnen en voorspelbaar beperkend gedrag te bieden aan alle naamruimten die deze bronnen delen.

In een poging om een eerlijk gebruik en verdeling van resources over alle Azure Service Bus Standard-naamruimten die dezelfde resources gebruiken, te garanderen, is de beperkingslogica gewijzigd om op krediet te zijn gebaseerd.

> [!NOTE]
> Het is belangrijk op te merken dat beperking ***niet nieuw*** is voor Azure Service Bus of een cloud native service.
>
> Credit based throttling is gewoon het verfijnen van de manier waarop verschillende naamruimten resources delen in een multi-tenant standaardniveauomgeving en dus redelijk gebruik mogelijk maken door alle naamruimten die de resources delen.

### <a name="what-is-credit-based-throttling"></a>Wat is credit-based throttling?

Credit-based throttling beperkt het aantal bewerkingen dat in een bepaalde periode op een bepaalde naamruimte kan worden uitgevoerd. 

Hieronder vindt u de workflow voor op krediet gebaseerde beperking - 

  * Aan het begin van elke periode geven we een bepaald aantal credits aan elke naamruimte.
  * Alle bewerkingen die door de verzender of ontvangerclienttoepassingen worden uitgevoerd, worden meegeteld bij deze credits (en afgetrokken van de beschikbare credits).
  * Als de credits zijn uitgeput, zullen de volgende bewerkingen worden beperkt tot het begin van de volgende periode.
  * Credits worden aangevuld aan het begin van de volgende periode.

### <a name="what-are-the-credit-limits"></a>Wat zijn de kredietlimieten?

De kredietlimieten zijn momenteel ingesteld op '1000' credits per seconde (per naamruimte).

Niet alle bewerkingen zijn gelijk gemaakt. Hier zijn de kredietkosten van elk van de operaties - 

| Bewerking | Kredietkosten|
|-----------|-----------|
| Gegevensbewerkingen (verzenden, verzendenAsync, Ontvangen, OntvangenAsync, Peek) |1 tegoed per bericht |
| Beheerbewerkingen (maken, lezen, bijwerken, verwijderen op wachtrijen, onderwerpen, abonnementen, filters) | 10 credits |

> [!NOTE]
> Houd er rekening mee dat bij het verzenden naar een onderwerp elk bericht wordt geëvalueerd op basis van filter(s) voordat het beschikbaar wordt gesteld op het abonnement.
> Elke filterevaluatie telt ook mee voor de kredietlimiet (d.w.z. 1 krediet per filterevaluatie).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Hoe weet ik dat ik word gewurgd?

Wanneer de aanvragen voor clienttoepassingen worden beperkt, wordt het onderstaande serverantwoord ontvangen door uw toepassing en geregistreerd.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hoe kan ik voorkomen dat ik wordt gewurgd?

Met gedeelde resources is het belangrijk om een soort van redelijk gebruik af te dwingen in verschillende naamruimten van servicebus die deze bronnen delen. Beperking zorgt ervoor dat elke piek in één werkbelasting er niet toe leidt dat andere workloads op dezelfde resources worden beperkt.

Zoals later in het artikel vermeld, is er geen risico in wordt beperkt omdat de client SDKs (en andere Azure PaaS-aanbiedingen) hebben de standaard retry beleid ingebouwd in hen. Eventuele throttled verzoeken zullen opnieuw worden geprobeerd met exponentiële backoff en zal uiteindelijk gaan door wanneer de credits worden aangevuld.

Het is begrijpelijk dat sommige toepassingen gevoelig zijn voor het beperken. In dat geval is het raadzaam om [uw huidige servicebusstandaard-naamruimte te migreren naar Premium.](service-bus-migrate-standard-premium.md) 

Bij migratie u speciale resources toewijzen aan de naamruimte van uw servicebus en de resources op de juiste manier opschalen als er een piek in uw werkbelasting is en de kans op een beperking verminderen. Bovendien u, wanneer uw werklast wordt verminderd tot normale niveaus, de resources die aan uw naamruimte zijn toegewezen, verkleinen.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Beperking in Azure Service Bus Premium-laag

De [Azure Service Bus Premium-laag](service-bus-premium-messaging.md) kent specifieke resources, in termen van berichteneenheden, toe aan elke naamruimtedie door de klant is ingesteld. Deze speciale resources bieden voorspelbare doorvoer en latentie en worden aanbevolen voor systemen met een hoge doorvoer of gevoelige productiekwaliteit.

Bovendien stelt de Premium-laag klanten ook in staat om hun doorvoercapaciteit op te schalen wanneer ze pieken in de werkbelasting ervaren.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hoe werkt beperking in Service Bus Premium?

Met exclusieve toewijzing van resources voor Service Bus Premium wordt beperking puur gedreven door de beperkingen van de resources die aan de naamruimte zijn toegewezen.

Als het aantal aanvragen groter is dan de huidige resources kunnen service, dan zullen de aanvragen worden beperkt.

### <a name="how-will-i-know-that-im-being-throttled"></a>Hoe weet ik dat ik word gewurgd?

Er zijn verschillende manieren om beperking te identificeren in Azure Service Bus Premium - 
  * **Throttled Requests** worden weergegeven in de [Azure Monitor Request-statistieken](service-bus-metrics-azure-monitor.md#request-metrics) om te bepalen hoeveel aanvragen zijn beperkt.
  * Hoog **CPU-gebruik** geeft aan dat de huidige toewijzing van resources hoog is en dat aanvragen mogelijk worden beperkt als de huidige werkbelasting niet vermindert.
  * Hoog **geheugengebruik** geeft aan dat de huidige toewijzing van resources hoog is en dat aanvragen mogelijk worden beperkt als de huidige werkbelasting niet vermindert.

### <a name="how-can-i-avoid-being-throttled"></a>Hoe kan ik voorkomen dat ik wordt gewurgd?

Aangezien de naamruimte van Service Bus Premium al speciale resources heeft, u de mogelijkheid verkleinen om te worden beperkt door het aantal Berichteneenheden dat aan uw naamruimte is toegewezen in de gebeurtenis (of anticipatie) van een piek in de werkbelasting op te schalen.

Het opschalen/omlaag kan worden bereikt door [runbooks te](../automation/automation-create-alert-triggered-runbook.md) maken die kunnen worden geactiveerd door wijzigingen in de bovenstaande statistieken.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="how-does-throttling-affect-my-application"></a>Welke invloed heeft beperking op mijn toepassing?

Wanneer een aanvraag wordt beperkt, houdt dit in dat de service bezet is omdat deze meer aanvragen onder vindt dan de resources toestaan. Als dezelfde bewerking na enkele ogenblikken opnieuw wordt geprobeerd, zodra de service de huidige werkbelasting heeft doorlopen, kan de aanvraag worden gehonoreerd.

Aangezien beperking het verwachte gedrag is van elke cloud native service, hebben we de logica voor het opnieuw proberen ingebouwd in de Azure Service Bus SDK zelf. De standaardinstelling is ingesteld op automatisch opnieuw proberen met een exponentiële back-off om ervoor te zorgen dat we niet hetzelfde verzoek hebben dat elke keer wordt beperkt.

De standaardlogica voor opnieuw proberen is van toepassing op elke bewerking.

### <a name="does-throttling-result-in-data-loss"></a>Leidt beperking tot gegevensverlies?

Azure Service Bus is geoptimaliseerd voor persistentie, we zorgen ervoor dat alle gegevens die naar Service Bus worden verzonden, zijn vastgelegd in opslag voordat de service het succes van de aanvraag erkent.

Zodra de aanvraag met succes 'ACK' (erkend) door Service Bus is, impliceert dit dat Service Bus de aanvraag met succes heeft verwerkt. Als Service Bus een 'NACK' (fout) retourneert, betekent dit dat Service Bus de aanvraag niet heeft kunnen verwerken en dat de clienttoepassing de aanvraag opnieuw moet proberen.

Wanneer een aanvraag echter wordt beperkt, impliceert de service dat deze de aanvraag op dit moment niet kan accepteren en verwerken vanwege resourcebeperkingen. Dit **impliceert geen** enkele vorm van gegevensverlies omdat Service Bus gewoon niet naar het verzoek heeft gekeken. In dit geval zorgt een beroep op het standaardbeleid voor opnieuw proberen van de Service Bus SDK ervoor dat de aanvraag uiteindelijk wordt verwerkt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voorbeelden van het gebruik van servicebusberichten:

* [Overzicht van servicebusberichten](service-bus-messaging-overview.md)
* [Snelstart: Berichten verzenden en ontvangen met behulp van Azure Portal en .NET](service-bus-quickstart-portal.md)
* [Zelfstudie: voorraad bijwerken met Azure Portal en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)

