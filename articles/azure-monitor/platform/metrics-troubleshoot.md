---
title: Problemen met Azure Monitor metrische grafieken oplossen
description: Problemen oplossen met metrische grafieken maken, aanpassen of interpreteren
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: b2f7e45c7f6092566314e6e4867bc0345683e4eb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318382"
---
# <a name="troubleshooting-metrics-charts"></a>Problemen met grafieken met metrische gegevens oplossen

Gebruik dit artikel als u problemen ondervindt met het maken, aanpassen of interpreteren van grafieken in azure Metrics Explorer. Als u niet bekend bent met metrische gegevens, kunt u leren hoe u aan de [slag gaat met metrische gegevens Verkenner](metrics-getting-started.md) en [geavanceerde functies van Metrics Explorer](metrics-charts.md). U kunt ook [voor beelden](metric-chart-samples.md) bekijken van de geconfigureerde metrische grafieken.

## <a name="cant-find-your-resource-to-select-it"></a>Kan de resource niet vinden om deze te selecteren

U hebt op de knop **Resource selecteren** geklikt, maar u ziet uw resource niet in het dialoogvenster Resourcekiezer.

**Oplossing:** Voor metrische gegevens Verkenner moeten abonnementen en resource groepen worden geselecteerd voordat de beschik bare resources worden vermeld. Ga als volgt te werk als u de resource niet ziet:

1. Controleer of u het juiste abonnement hebt geselecteerd in de vervolgkeuzelijst **Abonnement**. Als uw abonnement niet wordt weergegeven, klikt u op **Map- en abonnementsinstellingen ** en voegt u een abonnement met uw resource toe.

1. Controleer of u de juiste resourcegroep hebt geselecteerd.
    > [!WARNING]
    > Wanneer u Metrics Explorer de eerste keer opent, bevat de vervolgkeuzelijst **Resourcegroep** geen vooraf geselecteerde resourcegroepen. Dit is om de beste prestatie te kunnen bieden. U moet minstens één groep kiezen om resources te zien te krijgen.

## <a name="chart-shows-no-data"></a>Diagram toont geen gegevens

Soms kunnen de grafieken geen gegevens weer geven nadat u de juiste resources en meet waarden hebt geselecteerd. Dit gedrag kan een van de volgende oorzaken hebben:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>De resourceprovider Microsoft.Insights is niet geregistreerd voor uw abonnement

U kunt alleen metrische gegevens verkennen als de resourceprovider *Microsoft.Insights* is geregistreerd in uw abonnement. In veel gevallen gebeurt dit automatisch (dat wil zeggen, nadat u een waarschuwingsregel hebt geconfigureerd, diagnostische instellingen hebt aangepast voor een resource of een regel voor automatisch schalen hebt geconfigureerd). Als de resource provider van micro soft. Insights niet is geregistreerd, moet u deze hand matig registreren door de stappen te volgen die worden beschreven in [Azure resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md).

**Oplossing:** Open **abonnementen**, **resource providers** en controleer of *micro soft. Insights* is geregistreerd voor uw abonnement.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>U hebt niet voldoende toegangsrechten voor uw resource

In Azure wordt de toegang tot metrische gegevens geregeld via [op rollen gebaseerd toegangsbeheer (RBAC)](../../role-based-access-control/overview.md). U moet een [controlelezer](../../role-based-access-control/built-in-roles.md#monitoring-reader), [controlebijdrager](../../role-based-access-control/built-in-roles.md#monitoring-contributor) of [inzender](../../role-based-access-control/built-in-roles.md#contributor) zijn om metrische gegevens voor een resource te kunnen verkennen.

**Oplossing:** Zorg ervoor dat u voldoende machtigingen hebt voor de resource van waaruit u de metrische gegevens wilt verkennen.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Uw resource heeft geen metrische gegevens verzonden tijdens het geselecteerde tijdsbereik

Sommige resources verzenden niet voortdurend hun metrische gegevens. Azure verzamelt bijvoorbeeld geen metrische gegevens voor gestopte virtuele machines. Andere resources verzenden hun metrische gegevens alleen bij bepaalde omstandigheden. Een metrische waarde die de verwerkingstijd van een transactie aangeeft, vereist bijvoorbeeld ten minste één transactie. Als er in de geselecteerde periode geen transacties zijn voltooid, is de grafiek uiteraard leeg. Hoewel de meeste metrische gegevens in Azure elke minuut worden verzameld, zijn er ook die minder vaak worden verzameld. Raadpleeg de documentatie over metrische gegevens voor meer informatie over de metrische gegevens die u probeert te verkennen.

**Oplossing:** Wijzig de tijd van de grafiek in een breder bereik. U kunt beginnen met de laatste 30 dagen met een grotere tijd granulatie (of afhankelijk van de optie ' automatische tijd granulatie ').

### <a name="you-picked-a-time-range-greater-than-30-days"></a>U hebt een tijdsbereik geselecteerd dat groter is dan 30 dagen

[De meeste metrische gegevens in Azure worden gedurende 93 dagen opgeslagen](data-platform-metrics.md#retention-of-metrics). U kunt voor een grafiek echter niet meer dan 30 dagen aan gegevens opvragen. Deze beperking geldt niet voor [metrische gegevens op basis van een logboek](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Oplossing:** Als u een lege grafiek ziet of uw grafiek alleen een deel van metrische gegevens weergeeft, controleert u of het verschil tussen begin-en eind datums in de tijd kiezer niet groter is dan het interval van 30 dagen.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Alle metrische waarden bevinden zich buiten het bereik van de vergrendelde y-as

Door [de grenzen van de y-as van de grafiek te vergrendelen](metrics-charts.md#lock-boundaries-of-chart-y-axis), kunt u per ongeluk instellen dat de grafieklijn niet zichtbaar is in het grafiekgebied. Als de y-as bijvoorbeeld is vergrendeld op een bereik tussen 0% en 50% en de metrische gegevens een constante waarde van 100% hebben, wordt de lijn altijd buiten het zichtbare gebied weergegeven, waardoor het lijkt alsof de grafiek leeg is.

**Oplossing:** Controleer of de grenzen van de y-as van de grafiek niet zijn vergrendeld buiten het bereik van de meet waarden. Als de grenzen van de y-as zijn vergrendeld, kunt u deze tijdelijk opnieuw instellen om ervoor te zorgen dat de metrische waarden niet buiten het bereik van de grafiek vallen. Vergrendeling van het bereik van de y-as wordt niet aanbevolen met automatische granulariteit voor de grafieken met aggregatie via **som**, **min** en **max** omdat de waarden samen met de granulatie worden gewijzigd door het vergroten of verkleinen van het browservenster of het wijzigen van de schermresolutie. Wijziging van de granulariteit kan tot gevolg hebben dat het weergavegebied van uw grafiek leeg blijft.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>U bekijkt de metrische gegevens van het gast besturingssysteem, maar u hebt de diagnostische extensie van Azure niet ingeschakeld

Het verzamelen van metrische gegevens van een **gastbesturingssysteem** is alleen mogelijk als Azure Diagnostic Extension is geconfigureerd of als u de extensie hebt ingeschakeld met behulp van het venster **Diagnostische instellingen** voor uw resource.

**Oplossing:** Als Azure Diagnostics extensie is ingeschakeld, maar u nog steeds niet uw metrische gegevens kunt zien, volgt u de stappen die worden beschreven in de [hand leiding voor het oplossen van problemen met de Azure Diagnostics extensie](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Zie ook de stappen voor het oplossen van problemen met het [niet kiezen van naam ruimte en metrische gegevens voor het gast besturingssysteem](#cannot-pick-guest-os-namespace-and-metrics) .

## <a name="error-retrieving-data-message-on-dashboard"></a>Bericht ' fout bij het ophalen van gegevens ' op het dash board

Dit probleem kan optreden wanneer uw dashboard is gemaakt met een metriek die later is afgeschaft en verwijderd uit Azure. Als u wilt controleren of dit het geval is, opent u het tabblad **Metrische gegevens** van uw resource, en controleert u de beschikbare metrische gegevens in de metriekenkiezer. Als de metriek niet wordt weergegeven, is deze verwijderd uit Azure. Als een metriek is afgeschaft, is er meestal een betere nieuwe metriek die een vergelijkbaar perspectief op de resourcestatus biedt.

**Oplossing:** Werk de tegel fout bij door een alternatieve metriek voor uw grafiek op het dash board te kiezen. U kunt [een lijst met beschikbare metrische gegevens voor Azure-services bekijken](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Grafiek toont streepjes lijn

Voor grafieken van Azure-metrieken wordt gestreepte lijn stijl gebruikt om aan te geven dat er een ontbrekende waarde is (ook wel ' null-waarde ' genoemd) tussen twee bekende tijdgebonden gegevens punten. Als u bijvoorbeeld in de tijd kiezer hebt gekozen voor ' 1 minuut ' tijd granulatie, maar de metriek is gerapporteerd op 07:26, 07:27, 07:29 en 07:30 (Let op een minuut tussen de tweede en derde gegevens punten), dan wordt een stippel lijn verbonden met 07:27 en 07:29 en wordt er verbinding gemaakt met alle andere gegevens punten. De onderbroken lijn wordt op nul gezet wanneer de metriek **aantal** en **Sum** -aggregatie gebruikt. Voor de **gemiddeld**, **min** of **Max** aggregaties verbindt de streepjes lijn twee dichtstbijzijnde gegevens punten. Als er gegevens ontbreken aan de meest rechtse of linkse kant van de grafiek, wordt de stippellijn uitgebreid in de richting van het ontbrekende gegevenspunt.
  ![afbeelding van metrische gegevens](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Oplossing:** Dit gedrag is inherent aan het ontwerp. Dit is handig voor het identificeren van ontbrekende gegevenspunten. Het lijn diagram is een superieure keuze voor het visualiseren van trends van metrische gegevens met hoge dichtheid, maar is mogelijk moeilijk te interpreteren voor metrische gegevens met sparse-waarden, met name wanneer u met de tijd korrels een rol geeft. De stippellijn vergemakkelijkt het lezen van deze grafieken, maar als uw grafiek nog steeds niet duidelijk is, kunt u overwegen uw metrische gegevens weer te geven met een ander grafiektype. Bijvoorbeeld, een spreidings grafiek diagram voor dezelfde metrische informatie laat elke keer duidelijk zien door een punt te visualiseren als er een waarde is en het gegevens punt wordt overgeslagen wanneer de waarde ontbreekt: ![ metrische afbeelding](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Als u nog steeds liever een lijndiagram gebruikt voor uw metrische gegevens, kan het verplaatsen van de muis over de grafiek helpen om de tijdgranulatie weer te geven door het markeren van het gegevenspunt op de locatie van de muisaanwijzer.

## <a name="chart-shows-unexpected-drop-in-values"></a>Diagram toont onverwachte waarden voor neerzetten

In veel gevallen is de waargenomen daling van de metrische waarden een verkeerde interpretatie van de gegevens die in de grafiek worden weergegeven. U kunt worden misleid door een daling van optellingen of aantallen wanneer de grafiek de meest recente minuten toont omdat de laatste punten van metrische gegevens nog niet zijn ontvangen of verwerkt door Azure. Afhankelijk van de service kan de latentie van de verwerking van metrische gegevens enkele minuten bedragen. Voor grafieken die een recent tijd bereik met een granulatie van 1 of 5 minuten tonen, wordt de waarde in de laatste paar minuten meer duidelijker: ![ metrische afbeelding](./media/metrics-troubleshoot/drop-in-values.png)

**Oplossing:** Dit gedrag is inherent aan het ontwerp. Wij vinden dat het direct weergeven van ontvangen gegevens zinvol is, zelfs wanneer het *gedeeltelijke* of *onvolledige* gegevens betreft. U kunt zo namelijk sneller belangrijke conclusies trekken en meteen met uw onderzoek beginnen. In het geval van een metrische waarde die bijvoorbeeld het aantal storingen aangeeft, kunt u aan een gedeeltelijke waarde X al zien dat er ten minste X storingen zijn opgetreden in een bepaalde minuut. U kunt het probleem dan meteen gaan onderzoeken, in plaats van te wachten op het exacte aantal storingen dat zich heeft voorgedaan, wat misschien ook niet zo belangrijk is. De grafiek wordt bijgewerkt zodra we de volledige set gegevens ontvangen, maar op dat moment kunnen er ook weer nieuwe onvolledige gegevenspunten van meer recente minuten worden weergeven.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Kan geen naam ruimte en metrische gegevens voor het gast besturingssysteem kiezen

Virtuele machines en schaalsets voor virtuele machines kennen twee soorten metrische gegevens: metrische gegevens van de **host van de virtuele machine** die worden verzameld door de Azure-hostingomgeving en metrische gegevens van het **gastbesturingssysteem (klassiek)** die worden verzameld door de [bewakingsagent](agents-overview.md) die wordt uitgevoerd op uw virtuele machines. U installeert de bewakingsagent door de [Azure Diagnostics-extensie](diagnostics-extension-overview.md) in te schakelen.

Metrische gegevens van het gastbesturingssysteem worden standaard opgeslagen in een Azure Storage-account, die u kiest op het tabblad **Diagnostische instellingen** van uw resource. Als er geen metrische gegevens van het gastbesturingssysteem worden verzameld of als Metrics Explorer er geen toegang tot krijgt, ziet u alleen de naamruimte voor de metrische gegevens van **Host van virtuele machine**:

![afbeelding van metrische gegevens](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Oplossing:** Als de naam ruimte en metrische gegevens van het **gast besturingssysteem (klassiek)** niet worden weer gegeven in Metrics Explorer:

1. Controleer of de [Azure Diagnostics-extensie](diagnostics-extension-overview.md) is ingeschakeld en geconfigureerd voor het verzamelen van metrische gegevens.
    > [!WARNING]
    > U kunt geen gebruikmaken van de [Log Analytics-agent](agents-overview.md#log-analytics-agent) (ook wel de Microsoft Monitoring Agent of 'MMA' genoemd) voor het verzenden van **Gastbesturingssysteem** naar een opslagaccount.

1. Zorg ervoor dat de resource provider van **micro soft. Insights** is [geregistreerd voor uw abonnement](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Controleer of het opslagaccount niet is beveiligd door de firewall. De Azure-portal moet toegang hebben tot het opslagaccount om metrische gegevens op te halen en de grafieken te tekenen.

1. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om te controleren of er metrische gegevens worden overgebracht naar het opslagaccount. Als dat niet het geval is, raadpleegt u de [probleemoplossingsgids voor de Azure Diagnostics-extensie](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het aan de slag gaan met metrische Explorer](metrics-getting-started.md)
* [Meer informatie over geavanceerde functies van metrische Explorer](metrics-charts.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](metric-chart-samples.md)

