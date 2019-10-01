---
title: Actie regels voor Azure Monitor waarschuwingen
description: Meer informatie over actie regels in Azure Monitor zijn en hoe u deze kunt configureren en beheren.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 34997c130094b7e8b209b3ad3030038670d0a254
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702980"
---
# <a name="action-rules-preview"></a>Actie regels (preview-versie)

Met actie regels kunt u acties definiëren of onderdrukken voor elk Azure Resource Manager bereik (Azure-abonnement, resource groep of doel resource). Ze hebben verschillende filters waarmee u de specifieke subset van waarschuwings instanties kunt beperken waarop u wilt handelen.

## <a name="why-and-when-should-you-use-action-rules"></a>Waarom en wanneer moet u actie regels gebruiken?

### <a name="suppression-of-alerts"></a>Waarschuwingen onderdrukken

Er zijn veel scenario's waarin het nuttig is om de meldingen te onderdrukken die worden gegenereerd door waarschuwingen. Deze scenario's lopen uiteen van onderdrukking tijdens het geplande onderhouds venster tot onderdrukking tijdens niet-kantoor uren. Het team dat verantwoordelijk is voor **ContosoVM** wil bijvoorbeeld waarschuwings meldingen onderdrukken voor het komende weekend, omdat **ContosoVM** het gepland onderhoud ondergaat. 

Hoewel het team elke waarschuwings regel die is geconfigureerd voor **ContosoVM** hand matig kan uitschakelen (en deze opnieuw inschakelt na onderhoud), is het geen eenvoudig proces. Met actie regels kunt u waarschuwings onderdrukking op schaal definiëren, zodat de periode van onderdrukking flexibel kan worden geconfigureerd. In het vorige voor beeld kan het team één actie regel definiëren voor **ContosoVM** die alle waarschuwings meldingen voor het weekend onderdrukt.


### <a name="actions-at-scale"></a>Acties op schaal

Hoewel waarschuwings regels u helpen om de actie groep te definiëren die wordt geactiveerd wanneer de waarschuwing wordt gegenereerd, hebben klanten vaak een gemeen schappelijke actie groep binnen hun bereik van bewerkingen. Een team dat verantwoordelijk is voor de **ContosoRG** van de resource groep, definieert bijvoorbeeld waarschijnlijk dezelfde actie groep voor alle waarschuwings regels die zijn gedefinieerd in **ContosoRG**. 

Met actie regels kunt u dit proces vereenvoudigen. Door acties op schaal te definiëren, kan een actie groep worden geactiveerd voor elke waarschuwing die wordt gegenereerd voor het geconfigureerde bereik. In het vorige voor beeld kan het team één actie regel definiëren voor **ContosoRG** die dezelfde actie groep activeren voor alle waarschuwingen die erin worden gegenereerd.

> [!NOTE]
> Actie regels zijn momenteel niet van toepassing op Azure Service Health-waarschuwingen.

## <a name="configuring-an-action-rule"></a>Een actie regel configureren

U kunt de functie openen door **acties beheren** te selecteren op de pagina **waarschuwings signalen** in azure monitor. Selecteer vervolgens **actie regels (preview)** . U kunt de regels openen door **actie regels (preview)** te selecteren in het dash board van de landings pagina voor waarschuwingen.

![Actie regels van de Azure Monitor landings pagina](media/alerts-action-rules/action-rules-landing-page.png)

Selecteer **+ nieuwe actie regel**. 

![Nieuwe actie regel toevoegen](media/alerts-action-rules/action-rules-new-rule.png)

U kunt ook een actie regel maken tijdens het configureren van een waarschuwings regel.

![Nieuwe actie regel toevoegen](media/alerts-action-rules/action-rules-alert-rule.png)

U ziet nu de pagina stroom voor het maken van actie regels. Configureer de volgende elementen: 

![Stroom voor maken van nieuwe actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Kies eerst het bereik (Azure-abonnement, resource groep of doel resource). U kunt ook meerdere combi Naties van bereiken binnen één abonnement selecteren.

![Bereik van actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filter criteria

U kunt ook filters definiëren om ze te beperken tot een specifieke subset van de waarschuwingen. 

De beschik bare filters zijn: 

* **Ernst**: De optie om een of meer waarschuwings ernst te selecteren. **Ernst = Sev1** betekent dat de actie regel van toepassing is op alle waarschuwingen die zijn ingesteld op Sev1.
* **Bewakings service**: Een filter op basis van de oorspronkelijke bewakings service. Dit filter is ook meervoudige selectie. Bijvoorbeeld: de **controle service = "Application Insights"** betekent dat de actie regel van toepassing is voor alle waarschuwingen op basis van Application Insights.
* **Resource type**:  Een filter dat is gebaseerd op een specifiek resource type. Dit filter is ook meervoudige selectie. Bijvoorbeeld, **resource type = "virtual machines"** betekent dat de actie regel van toepassing is voor alle virtuele machines.
* **Id van waarschuwings regel**: Een optie om te filteren op specifieke waarschuwings regels met behulp van de Resource Manager-ID van de waarschuwings regel.
* **Bewakings voorwaarde**:  Een filter voor waarschuwings exemplaren die worden **geactiveerd** of **omgezet** als de monitor voorwaarde.
* **Beschrijving**: Een regex (reguliere expressie) die overeenkomt met een teken reeks die overeenkomt met de beschrijving, gedefinieerd als onderdeel van de waarschuwings regel. De **Beschrijving bevat bijvoorbeeld ' Prod '** die overeenkomt met alle waarschuwingen die de teken reeks ' Prod ' in hun beschrijvingen bevatten.
* **Waarschuwings context (Payload)** : Een regex match die een teken reeks overeenkomst definieert op basis van de waarschuwings context velden van de nettolading van een waarschuwing. Bijvoorbeeld: **waarschuwings context (Payload) bevat ' computer-01 '** komt overeen met alle waarschuwingen waarvan de nettolading de teken reeks ' computer-01 ' bevatten.

Deze filters worden in combi natie met elkaar toegepast. Als u bijvoorbeeld **resource type = virtual machines** en **Ernst = Sev0**hebt ingesteld, hebt u gefilterd op alle **Sev0** -waarschuwingen op uw virtuele machines. 

![Actie regel filters](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuratie van onderdrukking of actie groep

Vervolgens configureert u de actie regel voor de ondersteuning van waarschuwingen of actie groepen. U kunt niet beide kiezen. De configuratie reageert op alle waarschuwings instanties die overeenkomen met het eerder gedefinieerde bereik en filters.

#### <a name="suppression"></a>Onderdrukking

Als u **onderdrukking**selecteert, configureert u de duur voor het onderdrukken van acties en meldingen. Kies een van de volgende opties:
* **Vanaf nu (altijd)** : Onderdrukt alle meldingen voor onbepaalde tijd.
* **Op een gepland tijdstip**: Onderdrukt meldingen binnen een gebonden duur.
* **Met een terugkeer patroon**: Onderdrukt meldingen op een terugkerende dag, wekelijks of maandelijks schema.

![Onderdrukking van actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Actiegroep

Als u in de wissel knop **actie groep** selecteert, moet u een bestaande actie groep toevoegen of een nieuwe maken. 

> [!NOTE]
> U kunt slechts één actie groep koppelen aan een actie regel.

![Nieuwe actie regel toevoegen of maken door actie groep te selecteren](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Details van de actie regel

Configureer als laatste de volgende Details voor de actie regel:
* Name
* De resource groep waarin deze is opgeslagen
* Description 

## <a name="example-scenarios"></a>Voorbeeldscenario's

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Waarschuwingen op basis van Ernst onderdrukken

Contoso wil meldingen onderdrukken voor alle Sev4-waarschuwingen op alle virtuele machines binnen het abonnement **ContosoSub** elk weekend.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoSub**
* Filters
    * Ernst = **Sev4**
    * Resource type = **virtual machines**
* Onderdrukking met terugkeer patroon ingesteld op wekelijks, **zaterdag** en **zondag** gecontroleerd

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Waarschuwingen op basis van een waarschuwings context (Payload) onderdrukken

Contoso wil meldingen onderdrukken voor alle logboek waarschuwingen die zijn gegenereerd voor **computer-01** in **ContosoSub** voor onbepaalde tijd tijdens het onderhoud.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoSub**
* Filters
    * Service bewaken = **log Analytics**
    * Waarschuwings context (Payload) bevat **computer-01**
* De onderdrukking is ingesteld op **vanaf nu (altijd)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Een actie groep die is gedefinieerd voor een resource groep

Contoso heeft [een metrische waarschuwing gedefinieerd op abonnements niveau](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Maar het wil ook bepalen van de acties die specifiek worden geactiveerd voor waarschuwingen die zijn gegenereerd op basis van de resource groep **ContosoRG**.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoRG**
* Er zijn geen filters gevonden
* De actie groep is ingesteld op **ContosoActionGroup**

> [!NOTE]
> *Actie groepen die zijn gedefinieerd in actie regels en waarschuwings regels, worden onafhankelijk van elkaar uitgevoerd, zonder ontdubbeling.* Als er in het eerder beschreven scenario een actie groep is gedefinieerd voor de waarschuwings regel, wordt deze geactiveerd in combi natie met de actie groep die is gedefinieerd in de actie regel. 

## <a name="managing-your-action-rules"></a>Uw actie regels beheren

U kunt uw actie regels weer geven en beheren in de lijst weergave:

![Lijst weergave actie regels](media/alerts-action-rules/action-rules-list-view.png)

Hier kunt u actie regels op schaal inschakelen, uitschakelen of verwijderen door het selectie vakje ernaast in te scha kelen. Wanneer u een actie regel selecteert, wordt de bijbehorende configuratie pagina geopend. De pagina helpt u de definitie van de actie regel bij te werken en in of uit te scha kelen.

## <a name="best-practices"></a>Aanbevolen procedures

Logboek waarschuwingen die u met de optie [aantal resultaten](alerts-unified-log.md) maakt, genereren één waarschuwings exemplaar met behulp van het hele Zoek resultaat (dat mogelijk meerdere computers kan omvatten). Als in dit scenario een actie regel gebruikmaakt van het filter van de **waarschuwings context (Payload)** , wordt het op de waarschuwings instantie gereageerd zolang er een overeenkomst is. In scenario 2, dat eerder beschreven, wordt de volledige melding onderdrukt als de zoek resultaten voor de gegenereerde logboek waarschuwing zowel **computer-01** als **computer-02**bevatten. Er is helemaal geen melding voor **computer-02** gegenereerd.

![Actie regels en logboek waarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Als u het beste logboek waarschuwingen met actie regels wilt gebruiken, maakt u logboek waarschuwingen met de optie [metrische meet](alerts-unified-log.md) waarde. Afzonderlijke waarschuwings instanties worden gegenereerd door deze optie op basis van het gedefinieerde groeps veld. In scenario 2 worden afzonderlijke waarschuwings instanties gegenereerd voor **computer-01** en **computer-02**. Als gevolg van de actie regel die in het scenario wordt beschreven, wordt alleen de melding voor **computer-01** onderdrukt. De melding voor **computer-02** blijft gewoon actief.

![Actie regels en logboek waarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Ik wil een actie regel configureren, maar ik wil alle mogelijke overlappende actie regels zien, zodat er geen dubbele meldingen worden weer geven. Is het mogelijk dat u dit kunt doen?

Nadat u een bereik hebt gedefinieerd tijdens het configureren van een actie regel, ziet u een lijst met actie regels die overlappen op hetzelfde bereik (indien van toepassing). Deze overlap ping kan een van de volgende opties zijn:

* Een exacte overeenkomst: De actie regel die u definieert en de regel voor overlappende acties bevinden zich bijvoorbeeld op hetzelfde abonnement.
* Een subset: De actie regel die u definieert, bevindt zich bijvoorbeeld op een abonnement en de regel voor overlappende acties bevindt zich in een resource groep binnen het abonnement.
* Een hoofdverzameling: De actie regel die u definieert, bevindt zich bijvoorbeeld in een resource groep en de overlappende actie regel staat op het abonnement dat de resource groep bevat.
* Een kruispunt: De actie regel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**, en de overlappende actie regel bevindt zich op **VM2** en **VM3**.

![Overlappende actie regels](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Terwijl ik een waarschuwings regel Configureer, is het mogelijk te weten of er al actie regels zijn gedefinieerd die kunnen worden toegepast op de waarschuwings regel die ik Definieer?

Nadat u de doel resource voor uw waarschuwings regel hebt gedefinieerd, kunt u de lijst met actie regels die op hetzelfde bereik handelen (indien van toepassing) bekijken door **geconfigureerde acties weer geven** te selecteren in de sectie **acties** . Deze lijst wordt ingevuld op basis van de volgende scenario's voor de Scope:

* Een exacte overeenkomst: Bijvoorbeeld, de waarschuwings regel die u definieert en de actie regel bevinden zich op hetzelfde abonnement.
* Een subset: De waarschuwings regel die u definieert, bevindt zich bijvoorbeeld op een abonnement en de actie regel bevindt zich in een resource groep binnen het abonnement.
* Een hoofdverzameling: De waarschuwings regel die u definieert, bevindt zich bijvoorbeeld in een resource groep en de actie regel bevindt zich op het abonnement dat de resource groep bevat.
* Een kruispunt: De waarschuwings regel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**, en de actie regel bevindt zich op **VM2** en **VM3**.
    
![Overlappende actie regels](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan ik de waarschuwingen zien die zijn onderdrukt door een actie regel?

Op de [pagina waarschuwingen lijst](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)kunt u een aanvullende kolom met de naam **status onderdrukken**kiezen. Als de melding voor een waarschuwings exemplaar is onderdrukt, wordt deze status in de lijst weer gegeven.

![Onderdrukte waarschuwings instanties](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Wat gebeurt er als er een actie regel met een actie groep en een andere met onderdrukking actief is op hetzelfde bereik?

Onderdrukking heeft altijd voor rang op hetzelfde bereik.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als er een resource is die in twee afzonderlijke actie regels wordt bewaakt? Ontvang ik een of twee meldingen? Bijvoorbeeld, **VM2** in het volgende scenario:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Voor elke waarschuwing voor VM1 en VM3 wordt de actie groeps AG1 één keer geactiveerd. Voor elke waarschuwing op **VM2**wordt de AG1 van de actie groep twee keer geactiveerd, omdat actie regels geen acties ontdubbelen. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als er een resource wordt gevolgd in twee afzonderlijke actie regels en één aanroepen voor actie terwijl een andere wordt onderdrukt? Bijvoorbeeld, **VM2** in het volgende scenario:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Voor elke waarschuwing op VM1 wordt de AG1 van de actie groep eenmaal geactiveerd. Acties en meldingen voor elke waarschuwing in VM2 en VM3 worden onderdrukt. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Wat gebeurt er als er een waarschuwings regel en een actie regel zijn gedefinieerd voor dezelfde resource die verschillende actie groepen aanroept? Bijvoorbeeld, **VM1** in het volgende scenario:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Voor elke waarschuwing op VM1 wordt de AG1 van de actie groep eenmaal geactiveerd. Wanneer waarschuwings regel "firewallregel1" wordt geactiveerd, wordt ook AG2 geactiveerd. Actie groepen die zijn gedefinieerd in actie regels en waarschuwings regels, worden onafhankelijk van elkaar uitgevoerd, zonder ontdubbeling. 

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over waarschuwingen in azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
