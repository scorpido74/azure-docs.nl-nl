---
title: Actieregels voor Azure Monitor-waarschuwingen
description: Inzicht in welke actieregels in Azure Monitor zijn en hoe u deze configureren en beheren.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546489"
---
# <a name="action-rules-preview"></a>Actieregels (voorbeeld)

Met actieregels u acties definiëren of onderdrukken in een Azure Resource Manager-bereik (Azure-abonnement, resourcegroep of doelbron). Ze hebben verschillende filters waarmee u de specifieke subset van waarschuwingsinstanties beperken waarop u wilt reageren.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Waarom en wanneer moet u actieregels gebruiken?

### <a name="suppression-of-alerts"></a>Onderdrukking van waarschuwingen

Er zijn veel scenario's waarin het handig is om de meldingen die waarschuwingen genereren te onderdrukken. Deze scenario's variëren van onderdrukking tijdens een gepland onderhoudsvenster tot onderdrukking tijdens niet-kantooruren. Zo wil het team dat verantwoordelijk is voor **ContosoVM** waarschuwingsmeldingen voor het komende weekend onderdrukken, omdat **ContosoVM** gepland onderhoud ondergaat. 

Hoewel het team elke waarschuwingsregel die handmatig is geconfigureerd op **ContosoVM** kan uitschakelen (en deze na onderhoud opnieuw kan inschakelen), is het geen eenvoudig proces. Met actieregels u waarschuwingsonderdrukking op schaal definiëren met de mogelijkheid om de periode van onderdrukking flexibel te configureren. In het vorige voorbeeld kan het team één actieregel definiëren op **ContosoVM** die alle waarschuwingsmeldingen voor het weekend onderdrukt.


### <a name="actions-at-scale"></a>Acties op schaal

Hoewel waarschuwingsregels u helpen bij het definiëren van de actiegroep die wordt geactiveerd wanneer de waarschuwing wordt gegenereerd, hebben klanten vaak een gemeenschappelijke actiegroep binnen hun werkingssfeer. Een team dat verantwoordelijk is voor de resourcegroep **ContosoRG** zal bijvoorbeeld waarschijnlijk dezelfde actiegroep definiëren voor alle waarschuwingsregels die zijn gedefinieerd in **ContosoRG**. 

Actieregels helpen u dit proces te vereenvoudigen. Door acties op schaal te definiëren, kan een actiegroep worden geactiveerd voor elke waarschuwing die wordt gegenereerd op het geconfigureerde bereik. In het vorige voorbeeld kan het team één actieregel op **ContosoRG** definiëren die dezelfde actiegroep activeert voor alle waarschuwingen die erin worden gegenereerd.

> [!NOTE]
> Actieregels zijn momenteel niet van toepassing op Azure Service Health-waarschuwingen.

## <a name="configuring-an-action-rule"></a>Een actieregel configureren

U hebt toegang tot de functie door **acties beheren te** selecteren op de **bestemmingspagina Waarschuwingen** in Azure Monitor. Selecteer vervolgens **Actieregels (voorbeeld)**. U de regels openen door **Actieregels (voorbeeld)** te selecteren in het dashboard van de bestemmingspagina voor waarschuwingen.

![Actieregels vanaf de bestemmingspagina azure monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecteer **+ Nieuwe actieregel**. 

![Nieuwe actieregel toevoegen](media/alerts-action-rules/action-rules-new-rule.png)

U ook een actieregel maken terwijl u een waarschuwingsregel configureert.

![Nieuwe actieregel toevoegen](media/alerts-action-rules/action-rules-alert-rule.png)

U ziet nu de stroompagina voor het maken van actieregels. Configureer de volgende elementen: 

![Nieuwe actieregelcreatiestroom](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Bereik

Kies eerst het bereik (Azure-abonnement, resourcegroep of doelbron). U ook een combinatie van scopes binnen één abonnement selecteren.

![Werkingssfeer van de actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtercriteria

U bovendien filters definiëren om ze te beperken tot een specifieke subset van de waarschuwingen. 

De beschikbare filters zijn: 

* **Ernst:** de optie om een of meer waarschuwingsernsten te selecteren. **Ernst = Sev1** betekent dat de actieregel van toepassing is op alle waarschuwingen die zijn ingesteld op Sev1.
* **Monitorservice:** een filter op basis van de oorspronkelijke bewakingsservice. Dit filter is ook multiple-select. **Monitorservice = 'Toepassingsinzichten'** betekent bijvoorbeeld dat de actieregel van toepassing is op alle waarschuwingen op basis van Application Insights.
* **Resourcetype:** een filter op basis van een specifiek resourcetype. Dit filter is ook multiple-select. **Resourcetype = 'Virtuele machines'** betekent bijvoorbeeld dat de actieregel van toepassing is op alle virtuele machines.
* **Waarschuwingsregel-ID:** een optie om te filteren op specifieke waarschuwingsregels met behulp van de Resource Manager-id van de waarschuwingsregel.
* **Monitorvoorwaarde:** een filter voor waarschuwingsinstanties met **vuur** of **opgelost** als status van de monitor.
* **Beschrijving**: Een regex -overeenkomst (reguliere expressie) die een tekenreeks definieert die overeenkomt met de beschrijving, gedefinieerd als onderdeel van de waarschuwingsregel. Beschrijving bevat bijvoorbeeld **'prod'** komt overeen met alle waarschuwingen die de tekenreeks 'prik' in hun beschrijvingen bevatten.
* **Alertcontext (payload):** een regex-overeenkomst die een tekenreeksmatch definieert met de waarschuwingscontextvelden van de payload van een waarschuwing. **Waarschuwingscontext (payload) bevat bijvoorbeeld 'Computer-01'** komt overeen met alle waarschuwingen waarvan de payloads de tekenreeks 'Computer-01' bevatten.

Deze filters worden samen met elkaar toegepast. Als u bijvoorbeeld **Resourcetype ' = Virtuele machines** en ernst ' = **Sev0**instelt, hebt u gefilterd op alle **Sev0-waarschuwingen** op alleen uw VM's. 

![Actieregelfilters](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Onderdrukking of configuratie van actiegroepen

Configureer vervolgens de actieregel voor waarschuwingonderdrukking of ondersteuning van de actiegroep. Je niet beide kiezen. De configuratie werkt op alle waarschuwingsinstanties die overeenkomen met het eerder gedefinieerde bereik en de filters.

#### <a name="suppression"></a>Onderdrukking

Als u **onderdrukking**selecteert, configureert u de duur voor het onderdrukken van acties en meldingen. Kies een van de volgende opties:
* **Vanaf nu (Altijd)**: Onderdrukt alle meldingen voor onbepaalde tijd.
* **Op een gepland tijdstip**: Onderdrukt meldingen binnen een begrensde duur.
* **Met een herhaling**: Onderdrukt meldingen op een terugkerend dagelijks, wekelijks of maandelijks schema.

![Onderdrukking van de actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Actiegroep

Als u **Actiegroep** selecteert in de schakelknop, voegt u een bestaande actiegroep toe of maakt u een nieuwe groep. 

> [!NOTE]
> U slechts één actiegroep koppelen aan een actieregel.

![Nieuwe actieregel toevoegen of maken door actiegroep te selecteren](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Details van de actieregel

Configureer ten slotte de volgende details voor de actieregel:
* Name
* Resourcegroep waarin deze is opgeslagen
* Beschrijving 

## <a name="example-scenarios"></a>Voorbeeldscenario 's

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Onderdrukking van waarschuwingen op basis van ernst

Contoso wil elk weekend meldingen voor alle Sev4-waarschuwingen op alle VM's binnen het abonnement **ContosoSub** onderdrukken.

**Oplossing:** Maak een actieregel met:
* Bereik = **ContosoSub**
* Filters
    * Ernst = **Sev4**
    * Resourcetype = **Virtuele machines**
* Onderdrukking met herhaling ingesteld op wekelijkse, en **zaterdag** en **zondag** gecontroleerd

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Onderdrukking van waarschuwingen op basis van waarschuwingscontext (payload)

Contoso wil meldingen voor alle logboekwaarschuwingen die voor **Computer-01** in **ContosoSub** worden gegenereerd voor onbepaalde tijd onderdrukken omdat het onderhoud doormaakt.

**Oplossing:** Maak een actieregel met:
* Bereik = **ContosoSub**
* Filters
    * MonitorService = **Logboekanalyse**
    * Waarschuwingscontext (payload) bevat **Computer-01**
* Onderdrukking ingesteld op **Vanaf nu (Altijd)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Actiegroep gedefinieerd in een resourcegroep

Contoso heeft [een metrische waarschuwing gedefinieerd op abonnementsniveau](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Maar het wil de acties die specifiek trigger voor waarschuwingen gegenereerd uit de resource groep **ContosoRG**definiëren .

**Oplossing:** Maak een actieregel met:
* Toepassingsgebied = **ContosoRG**
* Geen filters
* Actiegroep ingesteld op **ContosoActionGroup**

> [!NOTE]
> *Actiegroepen die zijn gedefinieerd in actieregels en waarschuwingsregels werken onafhankelijk, zonder deduplicatie.* In het eerder beschreven scenario wordt, als een actiegroep is gedefinieerd voor de waarschuwingsregel, geactiveerd in combinatie met de actiegroep die is gedefinieerd in de actieregel. 

## <a name="managing-your-action-rules"></a>Uw actieregels beheren

U uw actieregels bekijken en beheren vanuit de lijstweergave:

![Lijstweergave actieregels](media/alerts-action-rules/action-rules-list-view.png)

Vanaf hier u actieregels op schaal in- en uitschakelen of verwijderen door het selectievakje ernaast in te schakelen. Wanneer u een actieregel selecteert, wordt de configuratiepagina geopend. Met de pagina u de definitie van de actieregel bijwerken en deze in- of uitschakelen.

## <a name="best-practices"></a>Aanbevolen procedures

Log meldingen die u maakt met het [aantal resultaten,](alerts-unified-log.md) genereren één waarschuwingsinstantie met behulp van het hele zoekresultaat (dat zich over meerdere computers kan uitstrekken). Als in dit scenario een actieregel het filter **Alert Context (payload)** gebruikt, wordt deze ingeschakeld op de waarschuwingsinstantie zolang er een overeenkomst is. In scenario 2, eerder beschreven, als de zoekresultaten voor de gegenereerde logboekwaarschuwing zowel **Computer-01** als **Computer-02**bevatten, wordt de volledige melding onderdrukt. Er is helemaal geen melding gegenereerd voor **Computer-02.**

![Actieregels en logboekwaarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Als u logboekwaarschuwingen met actieregels optimaal wilt gebruiken, maakt u logboekwaarschuwingen met de [metrische metingsoptie.](alerts-unified-log.md) Afzonderlijke waarschuwingsinstanties worden gegenereerd door deze optie, op basis van het gedefinieerde groepsveld. Vervolgens worden in scenario 2 afzonderlijke waarschuwingsinstanties gegenereerd voor **Computer-01** en **Computer-02**. Vanwege de actieregel die in het scenario wordt beschreven, wordt alleen de melding voor **Computer-01** onderdrukt. De melding voor **Computer-02** blijft normaal branden.

![Actieregels en logboekwaarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Terwijl ik een actieregel configureer, wil ik alle mogelijke overlappende actieregels zien, zodat ik dubbele meldingen vermijd. Is het mogelijk om dat te doen?

Nadat u een bereik hebt gedefinieerd terwijl u een actieregel configureert, ziet u een lijst met actieregels die elkaar overlappen op hetzelfde bereik (indien van toepassing). Deze overlapping kan een van de volgende opties zijn:

* Een exacte overeenkomst: de actieregel die u definieert en de overlappende actieregel zijn bijvoorbeeld op hetzelfde abonnement.
* Een subset: de actieregel die u definieert, is bijvoorbeeld een abonnement en de overlappende actieregel bevindt zich op een resourcegroep binnen het abonnement.
* Een superset: de actieregel die u definieert, bevindt zich bijvoorbeeld in een resourcegroep en de overlappende actieregel bevindt zich op het abonnement dat de resourcegroep bevat.
* Een snijpunt: de actieregel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**en de overlappende actieregel bevindt zich op **VM2** en **VM3**.

![Overlappende actieregels](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Terwijl ik een waarschuwingsregel configureer, is het mogelijk om te weten of er al actieregels zijn gedefinieerd die kunnen werken op basis van de waarschuwingsregel die ik definieer?

Nadat u de doelbron voor uw waarschuwingsregel hebt gedefinieerd, u de lijst met actieregels zien die op hetzelfde (eventuele) bereik werken door **geconfigureerde acties weergeven** te selecteren onder de sectie **Acties.** Deze lijst wordt ingevuld op basis van de volgende scenario's voor het bereik:

* Een exacte overeenkomst: bijvoorbeeld de waarschuwingsregel die u definieert en de actieregel zijn op hetzelfde abonnement.
* Een subset: de waarschuwingsregel die u definieert, is bijvoorbeeld een abonnement en de actieregel bevindt zich op een resourcegroep binnen het abonnement.
* Een superset: de waarschuwingsregel die u definieert, bevindt zich bijvoorbeeld in een resourcegroep en de actieregel bevindt zich op het abonnement dat de brongroep bevat.
* Een snijpunt: de waarschuwingsregel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**en de actieregel bevindt zich op **VM2** en **VM3**.
    
![Overlappende actieregels](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan ik de waarschuwingen zien die zijn onderdrukt door een actieregel?

Op de [pagina met de lijst met waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)u een extra kolom kiezen met de naam **Onderdrukkingsstatus**. Als de melding voor een waarschuwingsinstantie is onderdrukt, wordt die status in de lijst weergegeven.

![Onderdrukte waarschuwingsinstanties](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Als er een actieregel is met een actiegroep en een andere met onderdrukking die op hetzelfde bereik actief is, wat gebeurt er dan?

Onderdrukking heeft altijd voorrang op hetzelfde bereik.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als ik een resource heb die wordt gecontroleerd in twee afzonderlijke actieregels? Krijg ik een of twee meldingen? **VM2** bijvoorbeeld in het volgende scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Voor elke waarschuwing op VM1 en VM3 wordt actiegroep AG1 één keer geactiveerd. Voor elke waarschuwing op **VM2**wordt actiegroep AG1 twee keer geactiveerd, omdat actieregels acties niet dedupliceren. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als ik een resource heb die wordt gecontroleerd in twee afzonderlijke actieregels en de ene oproept tot actie terwijl een andere voor onderdrukking? **VM2** bijvoorbeeld in het volgende scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Voor elke waarschuwing op VM1 wordt actiegroep AG1 één keer geactiveerd. Acties en meldingen voor elke waarschuwing op VM2 en VM3 worden onderdrukt. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Wat gebeurt er als ik een waarschuwingsregel en een actieregel heb gedefinieerd voor dezelfde resource die verschillende actiegroepen aanroept? **VM1** bijvoorbeeld in het volgende scenario:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Voor elke waarschuwing op VM1 wordt actiegroep AG1 één keer geactiveerd. Wanneer waarschuwingsregel "regel1" wordt geactiveerd, wordt ag2 ook geactiveerd. Actiegroepen die zijn gedefinieerd in actieregels en waarschuwingsregels werken onafhankelijk, zonder deduplicatie. 

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over waarschuwingen in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
