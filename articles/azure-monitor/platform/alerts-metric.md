---
title: Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor
description: Meer informatie over het gebruik van Azure Portal of CLI om metrische waarschuwings regels te maken, weer te geven en te beheren.
author: harelbr
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 00f5f37591ed2ed250cb756c686ea15136921512
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705527"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor

Metrische waarschuwingen in Azure Monitor bieden een manier om een melding te ontvangen wanneer metrische gegevens een drempelwaarde overschrijden. Metrische waarschuwingen werken voor verschillende metrische gegevens op platforms met meerdere dimensies, voor aangepaste metrische gegevens, en voor metrische Application Insights-gegevens (standaard en aangepast). In dit artikel wordt beschreven hoe u waarschuwingsregels voor metrische gegevens kunt maken, weergeven en beheren via de Azure-portal en Azure CLI. U kunt ook metrische waarschuwings regels maken met behulp van Azure Resource Manager sjablonen die in [een afzonderlijk artikel](alerts-metric-create-templates.md)worden beschreven.

Meer informatie over de werking van metrische waarschuwingen vindt u in [overzicht van metrische waarschuwingen](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Maken met Azure Portal

In de volgende procedure wordt beschreven hoe u een regel voor metrische waarschuwing maakt in Azure Portal:

1. Klik in [Azure Portal](https://portal.azure.com)op **monitor**. Op de Blade monitor worden al uw bewakings instellingen en-gegevens in één weer gave geconsolideerd.

2. Klik op **waarschuwingen** en vervolgens op **+ nieuwe waarschuwings regel**.

    > [!TIP]
    > De meeste resource-Blades hebben ook **waarschuwingen** in het resource menu onder **bewaking**, u kunt hier ook waarschuwingen van maken.

3. Klik op **doel selecteren**in het context deel venster dat wordt geladen, selecteer een doel resource waarop u een waarschuwing wilt ontvangen. Gebruik de vervolg keuzelijst **abonnement** en **resource type** om de resource te vinden die u wilt bewaken. U kunt ook de zoek balk gebruiken om uw bron te vinden.

4. Als de geselecteerde resource metrische gegevens bevat, kunt u waarschuwingen maken op **beschik bare signalen** onder aan de rechter kant, zoals metrische gegevens. U kunt de volledige lijst met resource typen die worden ondersteund voor metrische waarschuwingen in dit [artikel](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)weer geven.

5. Wanneer u een doel resource hebt geselecteerd, klikt u op **voor waarde toevoegen**.

6. U ziet een lijst met signalen die worden ondersteund voor de resource. Selecteer de metriek waarvoor u een waarschuwing wilt maken.

7. U kunt de metriek eventueel verfijnen door de **periode** en **aggregatie**aan te passen. Als de metriek dimensies heeft, ziet u de weer gegeven **dimensie** tabel. Selecteer een of meer waarden per dimensie. De metrische waarschuwing wordt uitgevoerd en evalueren de voor waarde voor alle combi Naties van geselecteerde waarden. Meer [informatie over hoe waarschuwingen voor multidimensionale metrische gegevens werken](alerts-metric-overview.md). U kunt ook **\*selecteren** voor een van de dimensies. **Selecteer \*** wordt de selectie dynamisch geschaald naar alle huidige en toekomstige waarden voor een dimensie.

8. Er wordt een grafiek weer gegeven met de metrische gegevens voor de afgelopen zes uur. Definieer de waarschuwings parameters. **Voorwaarde type**, **frequentie**, **operator** en **drempel waarde** of **gevoeligheid**, Hiermee wordt de logica bepaald die door de metrische waarschuwings regel wordt geëvalueerd. Meer [informatie over de drempel waarde voor het type en gevoeligheids opties voor dynamische waarden](alerts-dynamic-thresholds.md).

9. Als u een statische drempel waarde gebruikt, kan het meet diagram helpen bij het bepalen van de drempel waarde. Als u een dynamische drempel waarde gebruikt, worden de berekende drempel waarden in de grafiek weer gegeven op basis van recente gegevens.

10. Klik op **Gereed**.

11. Voeg eventueel nog andere criteria toe als u een complexe waarschuwings regel wilt bewaken. Momenteel kunnen gebruikers waarschuwings regels met dynamische drempel waarden criteria hebben als één criterium.

12. Details van de **waarschuwing** invullen, zoals de naam van de **waarschuwings regel**, **Beschrijving** en **Ernst**

13. Voeg een actie groep toe aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.

14. Klik op **gereed** om de waarschuwings regel voor metrische gegevens op te slaan.

> [!NOTE]
> Metrische waarschuwings regels die via de portal zijn gemaakt, worden gemaakt in dezelfde resource groep als de doel resource.

## <a name="view-and-manage-with-azure-portal"></a>Azure Portal weer geven en beheren

U kunt metrische waarschuwings regels weer geven en beheren met behulp van de Blade regels beheren onder waarschuwingen. De volgende procedure laat zien hoe u uw metrische waarschuwings regels kunt weer geven en een van deze kunt bewerken.

1. Ga in Azure Portal naar **monitor**

2. Klik op **waarschuwingen** en **beheer regels**

3. Op de Blade **regels beheren** kunt u al uw waarschuwings regels weer geven in abonnementen. U kunt de regels verder filteren met **resource groep**, **resource type** en **resource**. Als u alleen metrische waarschuwingen wilt weer geven, selecteert u **signaal type** als metrische gegevens.

    > [!TIP]
    > Op de Blade **regels beheren** kunt u meerdere waarschuwings regels selecteren en deze inschakelen/uitschakelen. Dit kan handig zijn wanneer bepaalde doel resources onder onderhoud moeten worden geplaatst.

4. Klik op de naam van de metrische waarschuwings regel die u wilt bewerken

5. Klik in de regel bewerken op de **waarschuwings criteria** die u wilt bewerken. U kunt de metrische gegevens, drempel waarde en andere velden als vereist wijzigen

    > [!NOTE]
    > U kunt de **doel resource** en de **naam van de waarschuwings regel** niet bewerken nadat de waarschuwing voor metrische gegevens is gemaakt.

6. Klik op **gereed** om uw wijzigingen op te slaan.

## <a name="with-azure-cli"></a>Met Azure CLI

In de vorige secties wordt beschreven hoe u metrische waarschuwings regels kunt maken, weer geven en beheren met behulp van Azure Portal. In deze sectie wordt beschreven hoe u hetzelfde kunt doen met meerdere platformen van [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). De snelste manier om te beginnen met het gebruik van Azure CLI is via [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Voor dit artikel wordt gebruikgemaakt van Cloud shell.

1. Ga naar Azure Portal en klik op **Cloud shell**.

2. Bij de prompt kunt u opdrachten gebruiken met ``--help`` optie om meer te weten te komen over de opdracht en hoe u deze gebruikt. Met de volgende opdracht wordt bijvoorbeeld een lijst weer gegeven met opdrachten die beschikbaar zijn voor het maken, weer geven en beheren van metrische waarschuwingen

    ```azurecli
    az monitor metrics alert --help
    ```

3. U kunt een eenvoudige waarschuwings regel voor metrische gegevens maken die controleert of het gemiddelde percentage CPU op een virtuele machine groter is dan 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. U kunt alle metrische waarschuwingen in een resource groep weer geven met behulp van de volgende opdracht

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. U kunt de details van een bepaalde metrische waarschuwings regel weer geven met behulp van de naam of de resource-ID van de regel.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. U kunt een waarschuwings regel voor metrische gegevens uitschakelen met behulp van de volgende opdracht.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Met de volgende opdracht kunt u een waarschuwings regel voor metrische gegevens verwijderen.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Maak metrische waarschuwingen met behulp van Azure Resource Manager sjablonen](../../azure-monitor/platform/alerts-enable-template.md).
- [Begrijpen hoe metrische waarschuwingen werken](alerts-metric-overview.md).
- [Begrijpen hoe metrische waarschuwingen met dynamische drempel waarden werken](alerts-dynamic-thresholds.md).
- [Informatie over het web Hook-schema voor metrische waarschuwingen](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

