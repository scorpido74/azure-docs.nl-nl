---
title: Overzicht van klassieke waarschuwingen in Azure Monitor
description: Klassieke waarschuwingen worden afgeschaft. Met waarschuwingen u Azure-bronstatistieken, -gebeurtenissen of -logboeken controleren en worden gewaarschuwd wanneer aan een door u opgegeven voorwaarde is voldaan.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: e9c269db870f582c176783a4654b5de251e24412
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114491"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Wat zijn klassieke waarschuwingen in Microsoft Azure?

> [!NOTE]
> In dit artikel wordt beschreven hoe u oudere klassieke metrische waarschuwingen maakt. Azure Monitor ondersteunt nu [nieuwere near-real-time metrische waarschuwingen en een nieuwe waarschuwingservaring.](../../azure-monitor/platform/alerts-overview.md) Klassieke waarschuwingen worden [buiten gebruik gesteld,](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)maar nog steeds in beperkte mate gebruikt voor resources die de nieuwe waarschuwingen nog niet ondersteunen. 
>

Met waarschuwingen u voorwaarden configureren boven gegevens en een melding krijgen wanneer de voorwaarden overeenkomen met de meest recente bewakingsgegevens.

## <a name="old-and-new-alerting-capabilities"></a>Oude en nieuwe waarschuwingsmogelijkheden

In het verleden beschikten Azure Monitor, Application Insights, Log Analytics en Service Status over afzonderlijke waarschuwingsmogelijkheden. Overwerk, Azure verbeterd en gecombineerd zowel de gebruikersinterface en verschillende methoden van waarschuwingen. De consolidatie is nog in behandeling.

U klassieke waarschuwingen alleen bekijken in het gebruikersscherm voor klassieke waarschuwingen in de Azure Portal. U krijgt dit scherm via de knop **Klassieke waarschuwingen weergeven** op het waarschuwingsscherm. 

 ![Waarschuwingsopties in Azure-portal](media/alerts-classic.overview/monitor-alert-screen2.png)

De nieuwe waarschuwingen gebruikerservaring heeft de volgende voordelen ten opzichte van de klassieke waarschuwingen ervaring:
- **Beter meldingssysteem** - Alle nieuwere waarschuwingen maken gebruik van actiegroepen, die groepen meldingen en acties worden genoemd die in meerdere waarschuwingen kunnen worden hergebruikt. Klassieke metrische waarschuwingen en oudere Log Analytics-waarschuwingen maken geen gebruik van actiegroepen.
- **Een uniforme ontwerpervaring** : alle waarschuwingen maken voor statistieken, logboeken en activiteitenlogboeken in Azure Monitor, Log Analytics en Application Insights is op één plek.
- **Ontslagen Log Analytics-waarschuwingen weergeven in Azure-portal** - U nu ook ontslagen Log Analytics-waarschuwingen in uw abonnement zien. Voorheen stonden deze in een apart portaal.
- **Scheiding van ontslagen waarschuwingen en waarschuwingsregels** - Waarschuwingsregels (de definitie van voorwaarde die een waarschuwing activeert) en Ontslagen waarschuwingen (een instantie van het afvuren van de waarschuwingsregel) worden gedifferentieerd, zodat de operationele en configuratieweergaven worden gescheiden.
- **Betere workflow** - De nieuwe gebruikershandleiding leidt de gebruiker bij het configureren van een waarschuwingsregel, waardoor het eenvoudiger wordt om de juiste dingen te ontdekken om gewaarschuwd te worden.
- **Smart Alerts consolidatie** en **waarschuwingsstatus instellen** - Nieuwere waarschuwingen omvatten automatische groeperingsfunctionaliteit met vergelijkbare waarschuwingen samen om overbelasting in de gebruikersinterface te verminderen. 

De nieuwere metrische waarschuwingen hebben de volgende voordelen ten opzichte van de klassieke metrische waarschuwingen:
- **Verbeterde latentie**: Nieuwere metrische waarschuwingen kunnen net zo vaak worden uitgevoerd als elke minuut. Oudere metrische waarschuwingen worden altijd uitgevoerd op een frequentie van 5 minuten. Nieuwere waarschuwingen hebben een toenemende kleinere vertraging van het optreden van problemen tot melding of actie (3 tot 5 minuten). Oudere waarschuwingen zijn 5 tot 15 minuten, afhankelijk van het type.  Log waarschuwingen hebben meestal 10 tot 15 minuten vertraging als gevolg van de tijd die nodig is om de logs in te nemen, maar nieuwere verwerkingsmethoden zijn het verminderen van die tijd. 
- **Ondersteuning voor multidimensionale statistieken:** U waarschuwen voor dimensionale statistieken waarmee u een interessant segment van de statistiek controleren.
- **Meer controle over metrische voorwaarden**: U rijkere waarschuwingsregels definiëren. De nieuwere waarschuwingen ondersteunen het bewaken van de maximale, minimale, gemiddelde en totale waarden van statistieken.
- **Gecombineerde bewaking van meerdere statistieken:** U meerdere statistieken (momenteel maximaal twee statistieken) met één regel controleren. Er wordt een waarschuwing geactiveerd als beide statistieken hun respectieve drempelwaarden voor de opgegeven periode overschrijden.
- **Beter meldingssysteem**: Alle nieuwere waarschuwingen gebruiken [actiegroepen,](../../azure-monitor/platform/action-groups.md)die groepen meldingen en acties worden genoemd die in meerdere waarschuwingen kunnen worden hergebruikt.  Klassieke metrische waarschuwingen en oudere Log Analytics-waarschuwingen maken geen gebruik van actiegroepen. 
- **Statistieken uit logboeken** (openbare preview): Logboekgegevens die in Log Analytics worden opgenomen, kunnen nu worden geëxtraheerd en geconverteerd naar Azure Monitor-statistieken en vervolgens worden gewaarschuwd op net als andere statistieken. Zie [Waarschuwingen (klassiek)](alerts-classic.overview.md) voor de terminologie die specifiek is voor klassieke waarschuwingen. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klassieke waarschuwingen op Azure Monitor-gegevens
Er zijn twee soorten klassieke waarschuwingen beschikbaar: metrische waarschuwingen en waarschuwingen voor activiteitenlogboeken.

* **Klassieke metrische waarschuwingen** : deze waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven statistiek een drempelwaarde overschrijdt die u toewijst. De waarschuwing genereert een melding wanneer die drempel wordt overschreden en de waarschuwingsvoorwaarde is voldaan. Op dat moment wordt de waarschuwing beschouwd als "Geactiveerd". Het genereert een andere melding wanneer deze is opgelost - dat wil zeggen wanneer de drempelwaarde opnieuw wordt overschreden en de voorwaarde niet meer wordt voldaan.

* **Waarschuwingen voor klassieke activiteitenlogboeken** : een waarschuwing voor streaminglogboek dat wordt geactiveerd op een gebeurtenisitem activiteitslogboek dat overeenkomt met uw filtercriteria. Deze waarschuwingen hebben slechts één status, "Geactiveerd". De waarschuwingsengine past gewoon de filtercriteria toe op elke nieuwe gebeurtenis. Er wordt niet gezocht naar oudere vermeldingen. Deze waarschuwingen kunnen u op de hoogte stellen wanneer zich een nieuw servicestatusincident voordoet of wanneer een gebruiker of toepassing een bewerking in uw abonnement uitvoert, bijvoorbeeld 'Virtuele machine verwijderen'.

Voor gegevens van bronnenlogboeken die beschikbaar zijn via Azure Monitor, u de gegevens doorsturen naar Logboekanalyse en een waarschuwing voor logboekquery's gebruiken. Log Analytics maakt nu gebruik van de [nieuwe waarschuwingsmethode](../../azure-monitor/platform/alerts-overview.md) 

In het volgende diagram worden gegevensbronnen in Azure Monitor samengevat en, conceptueel, hoe u van die gegevens waarschuwen.

![Waarschuwingen uitgelegd](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie van waarschuwingen (klassiek)
Azure gebruikt de volgende termen om klassieke waarschuwingen en hun functies te beschrijven:
* **Waarschuwing** - een definitie van criteria (een of meer regels of voorwaarden) die wordt geactiveerd wanneer wordt voldaan.
* **Actief** - de status wanneer aan de criteria van een klassieke waarschuwing is voldaan.
* **Opgelost** - de status wanneer niet meer wordt voldaan aan de criteria die door een klassieke waarschuwing zijn gedefinieerd, nadat eerder is voldaan.
* **Melding** - de actie die is ondernomen op basis van een klassieke waarschuwing die actief wordt.
* **Actie** - een specifiek gesprek dat naar een ontvanger van een melding wordt verzonden (bijvoorbeeld een e-mail naar een adres of een bericht naar een webhook-URL). Meldingen kunnen meestal meerdere acties activeren.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hoe ontvang ik een melding van een klassieke Azure Monitor-waarschuwing?
In het verleden gebruikten Azure-waarschuwingen van verschillende services hun eigen ingebouwde meldingsmethoden. 

Azure Monitor heeft een herbruikbare meldingsgroepering gemaakt, *actiegroepen*genaamd . Actiegroepen geven een set ontvangers op voor een melding. Elke keer dat een waarschuwing wordt geactiveerd die verwijst naar de actiegroep, ontvangen alle ontvangers die melding. Met actiegroepen u een groepering van ontvangers (bijvoorbeeld uw lijst met oproepprogramma's) opnieuw gebruiken voor veel waarschuwingsobjecten. Actiegroepen ondersteunen meldingen door naast e-mailadressen, sms-nummers en een aantal andere acties ook een webhook-URL te plaatsen.  Zie [actiegroepen voor](../../azure-monitor/platform/action-groups.md)meer informatie. 

Oudere klassieke activiteitslogboekwaarschuwingen gebruiken actiegroepen.

De oudere metrische waarschuwingen maken echter geen gebruik van actiegroepen. In plaats daarvan u de volgende acties configureren: 
- Stuur e-mailmeldingen naar de servicebeheerder, naar medebeheerders of naar extra e-mailadressen die u opgeeft.
- Bel een webhook, waarmee u extra automatiseringsacties starten.

Webhooks maakt automatisering en herstel mogelijk, bijvoorbeeld met behulp van:
- Azure Automation Runbook
- Azure Functions
- Azure Logic-app
- een service van derden

## <a name="next-steps"></a>Volgende stappen
Informatie over waarschuwingsregels en deze configureren met behulp van:

* Meer informatie over [statistieken](data-platform.md)
* [Klassieke metrische waarschuwingen configureren via Azure-portal](alerts-classic-portal.md)
* [Klassieke metrische waarschuwingen PowerShell configureren](alerts-classic-portal.md)
* Cli [(Classic Metric Alerts Command Line Interface)](alerts-classic-portal.md) configureren
* Klassieke [metrische waarschuwingen Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) configureren
* Meer informatie over [activiteitenlogboek](platform-logs-overview.md)
* [Waarschuwingen voor activiteitenlogboeken configureren via Azure-portal](activity-log-alerts.md)
* [Waarschuwingen voor activiteitenlogboeken](alerts-activity-log.md) configureren via Resourcebeheer
* Het [webhook-schema voor de waarschuwing voor activiteitenlogboeken controleren](activity-log-alerts-webhook.md)
* Meer informatie over [actiegroepen](action-groups.md)
* Nieuwere [waarschuwingen](alerts-metric.md) configureren
