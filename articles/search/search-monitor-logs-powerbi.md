---
title: Azure Cognitive Search Logs and Metrics visualiseren met Power BI
description: Azure Cognitive Search Logs and Metrics visualiseren met Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650138"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Azure Cognitive Search Logs and Metrics visualiseren met Power BI
[Met Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) u bedrijfslogboeken en servicestatistieken over uw zoekservice opslaan in een Azure Storage-account. Op deze pagina vindt u instructies voor hoe u die informatie visualiseren via een Power BI-sjabloon-app. De app biedt gedetailleerde inzichten over uw zoekservice, inclusief informatie over zoek-, indexerings-, operationele en servicestatistieken.

U vindt de Power BI Template App **Azure Cognitive Search: Analyseer logboeken en statistieken** in de Marktplaats van Power BI [Apps.](https://appsource.microsoft.com/marketplace/apps)

## <a name="how-to-get-started-with-the-app"></a>Aan de slag met de app
1. Diagnostische logboekregistratie inschakelen voor uw zoekservice:
    1. Een bestaand [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken of identificeren waar u de logboeken archiveren
    1. Navigeren naar uw Azure Cognitive Search-service in de Azure-portal
    1. Selecteer **diagnostische instellingen** onder de sectie Controle in de linkerkolom

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Selecteren **+ Diagnostische instelling toevoegen**
    1. Controleer **Archiveren in een opslagaccount,** geef uw opslagaccountgegevens op en controleer **OperationLogs** en **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Selecteer **Opslaan**

1. Nadat logboekregistratie is ingeschakeld, gebruikt u uw zoekservice om logboeken en statistieken te genereren. Het duurt maximaal een uur voordat de containers worden weergegeven in Blob-opslag met deze logboeken. U ziet een **insights-logs-operationlogs-container** voor logboeken voor zoekverkeer en een **insights-metrics-pt1m-container** voor statistieken.

1. Zoek de Azure Cognitive Search Power BI-app in de [Marketplace van Power BI Apps](https://appsource.microsoft.com/marketplace/apps) en installeer deze in een nieuwe werkruimte of een bestaande werkruimte. De app heet **Azure Cognitive Search: Analyseer logboeken en metrische gegevens**.

1. Nadat u de app hebt geïnstalleerd, selecteert u de app in uw lijst met apps in Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Selecteer **Verbinding maken** om uw gegevens met elkaar te verbinden

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Voer de naam in van het opslagaccount dat uw logboeken en statistieken bevat. Standaard kijkt de app naar de laatste 10 dagen aan gegevens, maar deze waarde kan worden gewijzigd met de parameter **Dagen.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Selecteer **Sleutel** als verificatiemethode en geef uw opslagaccountsleutel op. Selecteer **Privé** als privacyniveau. Klik op Aanmelden om te beginnen met het laadproces.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Wacht tot de gegevens worden vernieuwd. Dit kan enige tijd duren, afhankelijk van hoeveel gegevens je hebt. U zien of de gegevens nog worden vernieuwd op basis van de onderstaande indicator.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Zodra de vernieuwing van de gegevens is voltooid, selecteert u **Azure Cognitive Search Report** om het rapport weer te geven.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Zorg ervoor dat u de pagina vernieuwt nadat u het rapport hebt geopend, zodat deze wordt geopend met uw gegevens.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>De app-parameters wijzigen
Als u gegevens van een ander opslagaccount wilt visualiseren of het aantal dagen gegevens wilt wijzigen dat u wilt opvragen, volgt u de onderstaande stappen om de parameters **Dagen** en **StorageAccount** te wijzigen.

1. Navigeer naar uw Power BI-apps, zoek uw Azure Cognitive Search-app en selecteer de knop **App bewerken** om de werkruimte weer te geven.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Selecteer **Instellingen** in de opties voor gegevensset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Terwijl u op het tabblad Gegevenssets de parameterwaarden wijzigt en selecteert u **Toepassen**. Als er een probleem is met de verbinding, werkt u de gegevensbronreferenties op dezelfde pagina bij.

1. Navigeer terug naar de werkruimte en selecteer **Nu vernieuwen** in de opties voor gegevensset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Open het rapport om de bijgewerkte gegevens weer te geven. Mogelijk moet u het rapport ook vernieuwen om de meest recente gegevens weer te geven.

## <a name="troubleshooting"></a>Problemen oplossen
Als u merkt dat u niet zien dat uw gegevens de volgende stappen voor het oplossen van problemen volgen:

1. Open het rapport en vernieuw de pagina om er zeker van te zijn dat u de nieuwste gegevens bekijkt. Er is een optie in het rapport om de gegevens te vernieuwen. Selecteer dit om de nieuwste gegevens op te halen.

1. Zorg ervoor dat de naam en toegangssleutel van het opslagaccount die u hebt opgegeven, correct zijn. De naam van het opslagaccount moet overeenkomen met het account dat is geconfigureerd met uw logboeken van de zoekservice.

1. Controleer of uw opslagaccount de **containers insights-logs-operationlogs** en **insights-metrics-pt1m** bevat en dat elke container gegevens bevat. De logboeken en statistieken bevinden zich binnen een paar lagen mappen.

1. Controleer of de gegevensset nog steeds verfrissend is. De indicator voor de vernieuwingsstatus wordt weergegeven in stap 8 hierboven. Als deze nog steeds wordt vernieuwd, wacht u tot de vernieuwing is voltooid om het rapport te openen en te vernieuwen.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Wat is Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Basisconcepten voor ontwerpers in de Power BI-service](https://docs.microsoft.com/power-bi/service-basic-concepts)