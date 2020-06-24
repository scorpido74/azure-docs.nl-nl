---
title: Problemen met metrische waarschuwingen voor Azure oplossen
description: Veelvoorkomende problemen met Azure Monitor metrische waarschuwingen en mogelijke oplossingen.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 06/21/2020
ms.subservice: alerts
ms.openlocfilehash: 36ff80bc0858d6d08cc120d126628de02ba6e703
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130735"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Problemen in Azure Monitor metrische waarschuwingen oplossen 

In dit artikel worden veelvoorkomende problemen in Azure Monitor [metrische waarschuwingen](alerts-metric-overview.md) beschreven en wordt uitgelegd hoe u deze problemen kunt oplossen.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Zie [overzicht van waarschuwingen in Microsoft Azure](alerts-overview.md)voor meer informatie over waarschuwingen.

## <a name="metric-alert-should-have-fired-but-didnt"></a>De metrische waarschuwing moet worden geactiveerd, maar niet 

Als u denkt dat een metrische waarschuwing moet worden geactiveerd, maar niet is geactiveerd en niet wordt gevonden in de Azure Portal, probeert u de volgende stappen uit te voeren:

1. **Configuratie** : Controleer de configuratie van de metrische waarschuwings regel om er zeker van te zijn dat deze correct is geconfigureerd:
    - Controleer of het **samenvoegings type**, de **aggregatie granulatie (punt)** en de **drempel waarde** of **gevoeligheid** als verwacht zijn geconfigureerd
    - Voor een waarschuwings regel die gebruikmaakt van dynamische drempel waarden, controleert u of geavanceerde instellingen zijn geconfigureerd, omdat het **aantal schendingen** kan waarschuwingen filteren en **gegevens negeert voordat** de drempel waarden worden berekend

       > [!NOTE] 
       > Dynamische drempel waarden vereisen ten minste 3 dagen en 30 metrische voor beelden voordat ze actief zijn.

2. **Gestart, maar geen melding** : Controleer de [lijst met geactiveerde waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) om te zien of u de geactiveerde waarschuwing kunt vinden. Als u de waarschuwing in de lijst kunt zien, maar een probleem met een aantal acties of meldingen hebt, raadpleegt u [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)meer informatie.

3. **Al actief** : Controleer of er al een waarschuwing is geactiveerd voor de metrische tijds reeks waarnaar u verwacht een waarschuwing te ontvangen. Metrische waarschuwingen zijn stateful, wat betekent dat wanneer een waarschuwing wordt geactiveerd voor een specifieke metrische tijd reeks, extra waarschuwingen over die tijd reeksen niet worden geactiveerd totdat het probleem niet meer wordt waargenomen. Deze ontwerp keuze vermindert de ruis. De waarschuwing wordt automatisch opgelost wanneer aan de voor waarde van de waarschuwing niet wordt voldaan voor drie opeenvolgende evaluaties.

4. **Gebruikte dimensies** : als u bepaalde [dimensie waarden voor een metriek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)hebt geselecteerd, wordt met de waarschuwings regel elke afzonderlijke meet tijd reeks gecontroleerd (zoals gedefinieerd door de combi natie van dimensie waarden) voor een schending van drempel waarden. Als u ook de cumulatieve metrische tijd reeks (zonder geselecteerde dimensies) wilt bewaken, moet u een extra waarschuwings regel voor de metriek configureren zonder dat u dimensies hoeft te selecteren.

5. **Aggregatie en tijd granulatie** : als u de metrische gegevens wilt visualiseren met [metrische grafieken](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), zorgt u ervoor dat:
    * De geselecteerde **aggregatie** in de metrische grafiek is hetzelfde als het **aggregatie type** in uw waarschuwings regel
    * De geselecteerde **tijd granulariteit** is hetzelfde als de **granulariteit van de aggregatie (punt)** in uw waarschuwings regel (en niet ingesteld op ' automatisch ')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>De metrische waarschuwing wordt geactiveerd wanneer deze niet

Als u denkt dat uw metrische waarschuwing niet hoeft te worden geactiveerd, maar dit wel is gelukt, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Raadpleeg de [lijst met geactiveerde waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) om de geactiveerde waarschuwing te vinden en klik om de details ervan weer te geven. Lees de informatie in **Waarom is deze waarschuwing geactiveerd?** om de metrische grafiek, **metrische waarde**en **drempel waarde** weer te geven op het moment dat de waarschuwing werd geactiveerd.

    > [!NOTE] 
    > Als u het voorwaardetype Dynamische drempelwaarden gebruikt en denkt dat de gebruikte drempelwaarden niet kloppen, geeft u feedback via het pictogram Frons. Deze feedback is van invloed op het machine learning algoritme onderzoek en helpt toekomstige detecties te verbeteren.

2. Als u meerdere dimensie waarden voor een metriek hebt geselecteerd, wordt de waarschuwing geactiveerd wanneer **een** van de metrische time series (zoals gedefinieerd door de combi natie van dimensie waarden) de drempel overschrijdt. Kijk [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions) voor meer informatie over het gebruik van dimensies in metrische waarschuwingen.

3. Controleer de configuratie van de waarschuwings regel om er zeker van te zijn dat deze correct is geconfigureerd:
    - Controleer of het **samenvoegings type**, de **aggregatie granulatie (punt)** en de **drempel waarde** of **gevoeligheid** als verwacht zijn geconfigureerd
    - Voor een waarschuwings regel die gebruikmaakt van dynamische drempel waarden, controleert u of geavanceerde instellingen zijn geconfigureerd, omdat het **aantal schendingen** kan waarschuwingen filteren en **gegevens negeert voordat** de drempel waarden worden berekend

   > [!NOTE]
   > Dynamische drempel waarden vereisen ten minste 3 dagen en 30 metrische voor beelden voordat ze actief zijn.

4. Als u de metriek wilt visualiseren met [metrische gegevens](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), moet u ervoor zorgen dat:
    - De geselecteerde **aggregatie** in de metrische grafiek is hetzelfde als het **aggregatie type** in uw waarschuwings regel
    - De geselecteerde **tijd granulariteit** is hetzelfde als de **granulariteit van de aggregatie (punt)** in uw waarschuwings regel (en niet ingesteld op ' automatisch ')

5. Als de waarschuwing wordt geactiveerd terwijl er al waarschuwingen zijn die dezelfde criteria bewaken (die niet zijn opgelost), controleert u of de waarschuwings regel is geconfigureerd met de eigenschap *Autoreduce* ingesteld op **False** (deze eigenschap kan alleen worden geconfigureerd via rest/Power shell/CLI, dus controleer het script dat wordt gebruikt om de waarschuwings regel te implementeren). In dat geval worden geactiveerde waarschuwingen niet automatisch opgelost met de waarschuwings regel en is er geen geactiveerde waarschuwing voor het oplossen van problemen vereist.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Kan geen metrische gegevens vinden voor waarschuwingen op virtuele machines 

Als u een waarschuwing wilt ontvangen over metrische gegevens van de gast op virtuele machines (voor geheugen, schijf ruimte), moet u ervoor zorgen dat u Diagnostische instellingen hebt ingesteld voor het naar een Azure Monitor-Sink verzenden van een filter:
    * [Voor Windows-VM's](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Voor Linux-VM's](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Als u de metrische gegevens van de gast hebt geconfigureerd om te worden verzonden naar een Log Analytics-werk ruimte, worden de metrische gegevens weer gegeven onder de Log Analytics werkruimte resource en worden **alleen** data weer gegeven nadat u een waarschuwings regel hebt gemaakt waarmee deze worden gecontroleerd. Volg hiervoor de stappen voor het [configureren van een metrische waarschuwing voor logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Kan de metrische gegevens niet vinden voor waarschuwing

Als u op zoek bent naar een specifieke metriek, maar er geen metrische gegevens voor de resource worden weer gegeven, [controleert u of het resource type wordt ondersteund voor metrische waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time).
Als er bepaalde metrische gegevens voor de resource worden weer gegeven, maar u geen specifieke metriek kunt vinden, [controleert u of de metrische gegevens beschikbaar](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)zijn en, als dit het geval is, raadpleegt u de beschrijving van de metrische gegevens om te zien of deze alleen beschikbaar is in specifieke versies of edities van de resource.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Kan de metrische dimensie niet vinden om te waarschuwen

Als u op zoek bent naar [specifieke dimensie waarden van een metriek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), maar deze waarden niet kunt vinden, moet u rekening houden met het volgende:

1. Het kan enkele minuten duren voordat de dimensiewaarden worden weergegeven onder de lijst **Dimensiewaarden**.
1. De weergegeven dimensiewaarden zijn gebaseerd op de metrische gegevens die in de afgelopen drie dagen zijn verzameld.
1. Als de dimensie waarde nog niet is verzonden, klikt u op het plus teken (+) om een aangepaste waarde toe te voegen
1. Als u wilt waarschuwen voor alle mogelijke waarden van een dimensie (inclusief toekomstige waarden), schakelt u het selectie vakje selecteren in

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>De regels voor metrische waarschuwingen zijn nog steeds gedefinieerd voor een verwijderde resource 

Wanneer u een Azure-resource verwijdert, worden de gekoppelde waarschuwingsregels voor metrische gegevens niet automatisch verwijderd. Waarschuwings regels verwijderen die zijn gekoppeld aan een resource die is verwijderd:

1. Open de resourcegroep waarin de verwijderde resource was gedefinieerd
1. Schakel in de lijst met resources het selectievakje **Verborgen typen weergeven** in
1. Filter de lijst op Type == **microsoft.insights/metricalerts**
1. Selecteer de relevante waarschuwings regels en selecteer **verwijderen** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Er worden metrische waarschuwingen gegeven telkens wanneer aan mijn voor waarde wordt voldaan

Metrische waarschuwingen zijn standaard stateful en daarom worden er geen extra waarschuwingen geactiveerd als er al een waarschuwing voor een bepaalde tijd reeks wordt geactiveerd. Als u een specifieke metrische waarschuwings regel wilt maken en u waarschuwingen wilt ontvangen voor elke evaluatie waarbij aan de waarschuwings voorwaarde wordt voldaan, maakt u de waarschuwings regel programmatisch (bijvoorbeeld via [Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), [Power shell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), [rest](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), [cli](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) en stelt u de eigenschap *autoreduce* in op ' false '.

> [!NOTE] 
> Als u een metrische waarschuwings regel stateless maakt, voor komt u dat er geactiveerde waarschuwingen worden opgelost, dus zelfs nadat de voor waarde niet meer is gehaald, blijven de geactiveerde waarschuwingen in de status geactiveerd totdat de Bewaar periode van 30 dagen is verstreken.


## <a name="metric-alert-rules-quota-too-small"></a>Het quotum voor de waarschuwings regels voor metrische gegevens is te klein

Voor het toegestane aantal metrische waarschuwings regels per abonnement gelden [quotum limieten](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Als u de quotumlimiet hebt bereikt, kunt u de volgende stappen uitvoeren om het probleem op te lossen:
1. Probeer regels voor metrische waarschuwingen te verwijderen of uit te scha kelen die niet meer worden gebruikt.

2. Overschakelen naar het gebruik van regels voor metrische waarschuwingen voor het bewaken van meerdere resources. Met deze mogelijkheid kan één waarschuwings regel meerdere resources bewaken met slechts één waarschuwings regel die bij het quotum wordt geteld. Zie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)voor meer informatie over deze functie en de ondersteunde resourcetypen.

3. Als de quotum limiet moet worden verhoogd, opent u een ondersteunings aanvraag en geeft u de volgende informatie op:

    - Abonnements-id ('s) waarvoor de quotum limiet moet worden verhoogd
    - Resource type voor de quota verhoging: **metrische waarschuwingen** of **metrische waarschuwingen (klassiek)**
    - Aangevraagde quotumlimiet

## <a name="check-total-number-of-metric-alert-rules"></a>Het totale aantal metrische waarschuwings regels controleren

Volg de onderstaande stappen om het huidige gebruik van metrische waarschuwings regels te controleren.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Open het scherm **Waarschuwingen** en klik op **Waarschuwingsregels beheren**
2. Filteren op het relevante abonnement met behulp van de vervolg keuzelijst voor het **abonnement**
3. Zorg ervoor dat u niet filtert op een specifieke resource groep, resource type of resource
4. Selecteer **metrische gegevens** in de vervolg keuzelijst **type signaal**
5. Controleer of het besturings element voor de vervolg keuzelijst **status** is ingesteld op **ingeschakeld**
6. Het totale aantal metrische waarschuwings regels wordt weer gegeven boven de lijst met regels

### <a name="from-api"></a>Van API

- GPowerShell - [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [Lijst per abonnement](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [lijst met metrische waarschuwingen van Az.Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Waarschuwings regels beheren met Resource Manager-sjablonen, REST API, Power shell of Azure CLI

Als u problemen ondervindt bij het maken, bijwerken, ophalen of verwijderen van metrische waarschuwingen met behulp van Resource Manager-sjablonen, REST API, Power shell of de Azure-opdracht regel interface (CLI), kunt u de volgende stappen uitvoeren om het probleem op te lossen.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

- Bekijk de lijst met [veelvoorkomende implementatiefouten in Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) en los problemen in overeenstemming met de lijst op
- Raadpleeg de [metrische waarschuwingen Azure Resource Manager sjabloon voor beelden](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) om te controleren of alle para meters correct worden door gegeven

### <a name="rest-api"></a>REST-API

Raadpleeg de [REST API-handleiding](https://docs.microsoft.com/rest/api/monitor/metricalerts/) om te controleren of u alle parameters correct invoert

### <a name="powershell"></a>PowerShell

Zorg ervoor dat u de juiste Power shell-cmdlets gebruikt voor metrische waarschuwingen:

- PowerShell-cmdlets voor metrische waarschuwingen zijn beschikbaar in de [Az.Monitor-module](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1).
- Zorg ervoor dat u de cmdlets die eindigen op v2 gebruikt voor nieuwe (niet-klassieke) metrische waarschuwingen (bijvoorbeeld [add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1))

### <a name="azure-cli"></a>Azure CLI

Zorg ervoor dat u de juiste CLI-opdrachten gebruikt voor metrische waarschuwingen:

- CLI-opdrachten voor metrische waarschuwingen beginnen met `az monitor metrics alert`. Lees het [referentiemateriaal over Azure CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) voor informatie over de syntaxis.
- U kunt een [voorbeeld bekijken over het gebruik van de CLI voor metrische waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli).
- Als u een waarschuwing wilt ontvangen voor aangepaste metrische gegevens, moet u de naam van de metrische gegevens vooraf laten gaan door de desbetreffende metrische naamruimte: NAAMRUIMTE.METRISCHE GEGEVENS

### <a name="general"></a>Algemeen

- Als u een `Metric not found`-fout krijgt:

   - Voor een platform metriek: Zorg ervoor dat u de naam van de **metrische gegevens** gebruikt op [de pagina Azure monitor ondersteunde metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)en niet de **weergave naam van de metrische gegevens**

   - Voor een aangepaste metriek: Zorg ervoor dat de metriek al wordt verzonden (u kunt geen waarschuwings regel maken op een aangepaste metriek die nog niet bestaat) en dat u de naam ruimte van de aangepaste metriek opgeeft (Zie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)een arm-sjabloon)

- Als u [metrische waarschuwingen voor logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs) maakt, zorg er dan voor dat de juiste afhankelijkheden zijn opgenomen. Zie de [voorbeeldsjabloon](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Als u een waarschuwings regel maakt die meerdere criteria bevat, moet u rekening houden met de volgende beperkingen:

   - U kunt slechts één waarde per dimensie in elk criterium selecteren
   - U kunt '\*' niet als dimensiewaarde gebruiken
   - Wanneer de metrische gegevens die in verschillende criteria zijn geconfigureerd, dezelfde dimensie ondersteunen, moet een geconfigureerde dimensie waarde expliciet op dezelfde manier worden ingesteld voor al deze metrische gegevens (Zie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)een resource manager-sjabloon voor beeld)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Er zijn geen machtigingen om regels voor metrische waarschuwingen te maken

Als u een waarschuwings regel voor metrische gegevens wilt maken, moet u over de volgende machtigingen beschikken:

- Lees machtiging voor de doel resource van de waarschuwings regel
- Schrijf machtiging voor de resource groep waarin de waarschuwings regel is gemaakt (als u de waarschuwings regel maakt op basis van de Azure Portal, wordt de waarschuwings regel gemaakt in dezelfde resource groep waarin de doel resource zich bevindt)
- Lees machtiging voor elke actie groep die is gekoppeld aan de waarschuwings regel (indien van toepassing)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Naamgevings beperkingen voor metrische waarschuwings regels

Houd rekening met de volgende beperkingen voor de naam van de regel voor metrische waarschuwingen:

- De namen van de waarschuwings regels voor metrische gegevens kunnen niet worden gewijzigd (hernoemd) nadat deze is gemaakt
- De naam van de metrische waarschuwings regel moet uniek zijn binnen een resource groep
- De naam van de regel voor metrische waarschuwingen mag niet de volgende tekens bevatten: * # & +:  < > ? @ % { } \ / 
- De namen van regels voor metrische waarschuwingen kunnen niet eindigen op het volgende teken:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Beperkingen bij het gebruik van dimensies in een metrische waarschuwings regel met meerdere voor waarden

Metrische waarschuwingen ondersteunen waarschuwingen over multidimensionale metrische gegevens en bieden ondersteuning voor het definiëren van meerdere voor waarden (Maxi maal 5 voor waarden per waarschuwings regel).

Houd rekening met de volgende beperkingen bij het gebruik van dimensies in een waarschuwings regel die meerdere voor waarden bevat:
1. U kunt binnen elke voor waarde slechts één waarde per dimensie selecteren.
2. U kunt de optie ' alle huidige en toekomstige waarden selecteren ' (selecteren) niet gebruiken \* .
3. Wanneer de metrische gegevens die in verschillende voor waarden zijn geconfigureerd, dezelfde dimensie ondersteunen, moet een geconfigureerde dimensie waarde expliciet op dezelfde manier worden ingesteld voor al deze metrische gegevens (in de relevante omstandigheden).
Bijvoorbeeld:
    - Houd rekening met een metrische waarschuwings regel die is gedefinieerd in een opslag account en controleert twee voor waarden:
        * Totaal aantal **trans acties** > 5
        * Gemiddeld **SuccessE2ELatency** > 250 MS
    - Ik wil de eerste voor waarde bijwerken en alleen trans acties bewaken waarbij de dimensie **ApiName** gelijk is aan *' GetBlob '*
    - Omdat zowel de metrische gegevens van de **trans acties** als de **SuccessE2ELatency** een **ApiName** -dimensie ondersteunen, moet ik beide voor waarden bijwerken en beide de dimensie **ApiName** opgeven met de waarde *' GetBlob '* .


## <a name="next-steps"></a>Volgende stappen

- Zie [problemen oplossen in azure monitor-waarschuwingen](alerts-troubleshoot.md)voor algemene informatie over het oplossen van problemen met waarschuwingen en meldingen.
