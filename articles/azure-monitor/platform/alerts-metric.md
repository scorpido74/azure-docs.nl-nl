---
title: Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor
description: Meer informatie over het gebruik van Azure-portal of CLI om metrische waarschuwingsregels te maken, weer te geven en te beheren.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369383"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Metrische waarschuwing maken, bekijken en beheren met Azure Monitor

Metrische waarschuwingen in Azure Monitor bieden een manier om een melding te krijgen wanneer een van uw statistieken een drempel overschrijdt. Metrische waarschuwingen werken voor verschillende metrische gegevens op platforms met meerdere dimensies, voor aangepaste metrische gegevens, en voor metrische Application Insights-gegevens (standaard en aangepast). In dit artikel beschrijven we hoe u metrische waarschuwingsregels maken, weergeven en beheren via Azure portal en Azure CLI. U ook metrische waarschuwingsregels maken met Azure Resource Manager-sjablonen, die worden beschreven in [een apart artikel.](alerts-metric-create-templates.md)

U meer informatie krijgen over hoe metrische waarschuwingen werken vanuit het overzicht van [metrische waarschuwingen.](alerts-metric-overview.md)

## <a name="create-with-azure-portal"></a>Maken met Azure Portal

In de volgende procedure wordt beschreven hoe u een metrische waarschuwingsregel maakt in azure-portal:

1. Klik in [Azure-portal](https://portal.azure.com)op **Monitor**. Het monitorblad consolideert al uw bewakingsinstellingen en gegevens in één weergave.

2. Klik **op Waarschuwingen** en klik **op + Nieuwe waarschuwingsregel**.

    > [!TIP]
    > De meeste resource blades hebben ook **Waarschuwingen** in hun resource menu onder **Monitoring**, je zou kunnen maken waarschuwingen vanaf daar ook.

3. Klik op **Doel selecteren**, selecteer in het contextvenster dat wordt geladen een doelbron waarop u wilt waarschuwen. Gebruik vervolgkeuze-ups **van abonnement** **en resourcetype** om de resource te vinden die u wilt controleren. U ook de zoekbalk gebruiken om uw bron te vinden.

4. Als de geselecteerde resource statistieken bevat waarop u waarschuwingen maken, bevatten **beschikbare signalen** rechtsonder statistieken. U de volledige lijst met resourcetypen weergeven die worden ondersteund voor metrische waarschuwingen in dit [artikel.](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)

5. Nadat u een doelbron hebt geselecteerd, klikt u op **Voorwaarde toevoegen**.

6. U ziet een lijst met signalen die worden ondersteund voor de resource, selecteer de statistiek waarop u een waarschuwing wilt maken.

7. U ziet een grafiek voor de statistiek van de afgelopen zes uur. Gebruik de vervolgkeuzelijst **Grafiekperiode** om te selecteren om een langere geschiedenis voor de statistiek weer te geven.

8. Als de statistiek afmetingen heeft, wordt een tabel met afmetingen weergegeven. Selecteer een of meer waarden per dimensie.
    - De weergegeven dimensiewaarden zijn gebaseerd op metrische gegevens van de afgelopen drie dagen.
    - Als de dimensiewaarde die u zoekt niet wordt weergegeven, klikt u op +om een aangepaste waarde toe te voegen.
    - U ook **selecteren \* ** voor een van de afmetingen. **Select \* ** schaalt de selectie dynamisch op naar alle huidige en toekomstige waarden voor een dimensie.

    De metrische waarschuwingsregel evalueert de voorwaarde voor alle geselecteerde combinaties van waarden. [Meer informatie over hoe waarschuwingen voor multidimensionale statistieken werken.](alerts-metric-overview.md)

9. Selecteer het **type drempelwaarde,** **operator**en **aggregatie**. Dit bepaalt de logica die de metrische waarschuwingsregel zal evalueren.
    - Als u een **statische** drempelwaarde gebruikt, blijft u een **drempelwaarde**definiëren . De metrische grafiek kan helpen bepalen wat een redelijke drempelwaarde kan zijn.
    - Als u een **dynamische** drempelwaarde gebruikt, blijft u de **gevoeligheid Drempel**waarden definiëren . In de metrische grafiek worden de berekende drempelwaarden weergegeven op basis van recente gegevens. [Meer informatie over het voorwaardetype dynamische drempels en gevoeligheidsopties](alerts-dynamic-thresholds.md).

10. Verfijn eventueel de voorwaarde door **aggregatiegranulariteit** en **evaluatiefrequentie**aan te passen . 

11. Klik op **Gereed**.

12. Voeg destijds een andere criteria toe als u een complexe waarschuwingsregel wilt controleren. Momenteel kunnen gebruikers waarschuwingsregels hebben met dynamische drempelwaardencriteria als één criterium.

13. Vul **waarschuwingsgegevens** in, zoals **naam van de waarschuwingsregel**, **Beschrijving**en **Ernst**.

14. Voeg een actiegroep toe aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.

15. Klik **op Gereed** om de metrische waarschuwingsregel op te slaan.

> [!NOTE]
> Metrische waarschuwingsregels die via portal zijn gemaakt, worden gemaakt in dezelfde resourcegroep als de doelbron.

## <a name="view-and-manage-with-azure-portal"></a>Azure-portal weergeven en beheren

U metrische waarschuwingsregels weergeven en beheren met het blad Regels beheren onder Waarschuwingen. In de onderstaande procedure ziet u hoe u uw metrische waarschuwingsregels weergeven en een van deze regels bewerken.

1. Navigeer in Azure-portal naar **Monitor**

2. Klik op **Regels voor waarschuwingen** **en beheren**

3. In het blad **Regels beheren** u al uw waarschuwingsregels voor abonnementen weergeven. U de regels verder filteren met **behulp van resourcegroep,** **resourcetype**en **Resource**. Als u alleen metrische waarschuwingen wilt zien, selecteert u **Signaaltype** als metrische gegevens.

    > [!TIP]
    > In het blad **Regels beheren** u meerdere waarschuwingsregels selecteren en deze in-/uitschakelen. Dit kan handig zijn wanneer bepaalde doelresources onder onderhoud moeten worden

4. Klik op de naam van de metrische waarschuwingsregel die u wilt bewerken

5. Klik in de regel Bewerken op de **waarschuwingscriteria** die u wilt bewerken. U de metrische, drempelvoorwaarde en andere velden naar behoefte wijzigen

    > [!NOTE]
    > U de **doelbron** en **de naam van de waarschuwingsregel** niet bewerken nadat de metrische waarschuwing is gemaakt.

6. Klik **op Gereed** om uw bewerkingen op te slaan.

## <a name="with-azure-cli"></a>Met Azure CLI

In de vorige secties beschreven hoe u metrische waarschuwingsregels maken, weergeven en beheren met Azure-portal. In deze sectie wordt beschreven hoe u hetzelfde doen met behulp van azure [CLI met](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)cross-platform. De snelste manier om Azure CLI te gebruiken is via [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) Voor dit artikel gebruiken we Cloud Shell.

1. Ga naar Azure portal, klik op **Cloud Shell**.

2. Bij de prompt u ``--help`` opdrachten met optie gebruiken om meer te weten te komen over de opdracht en hoe u deze gebruiken. In de volgende opdracht ziet u bijvoorbeeld de lijst met opdrachten die beschikbaar zijn voor het maken, weergeven en beheren van metrische waarschuwingen

    ```azurecli
    az monitor metrics alert --help
    ```

3. U een eenvoudige metrische waarschuwingsregel maken die controleert of het gemiddelde cpu-percentage op een vm groter is dan 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. U alle metrische waarschuwingen in een resourcegroep weergeven met de volgende opdracht

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. U de details van een bepaalde metrische waarschuwingsregel zien met behulp van de naam of de resource-id van de regel.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. U een metrische waarschuwingsregel uitschakelen met de volgende opdracht.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. U een metrische waarschuwingsregel verwijderen met de volgende opdracht.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Maak metrische waarschuwingen met Azure Resource Manager-sjablonen](../../azure-monitor/platform/alerts-metric-create-templates.md).
- [Begrijpen hoe metrische waarschuwingen werken](alerts-metric-overview.md).
- [Begrijpen hoe metrische waarschuwingen met de voorwaarde Dynamische drempels werken](alerts-dynamic-thresholds.md).
- [Het webhaakschema voor metrische waarschuwingen begrijpen](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

