---
title: Azure Cognitive Search-logboeken en-metrische gegevens visualiseren met Power BI
description: Azure Cognitive Search-logboeken en-metrische gegevens visualiseren met Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650138"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Azure Cognitive Search-logboeken en-metrische gegevens visualiseren met Power BI
Met [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) kunt u bewerkings logboeken en metrische service gegevens over uw zoek service opslaan in een Azure Storage-account. Op deze pagina vindt u instructies voor het visualiseren van deze informatie met behulp van een app Power BI-sjabloon. De app biedt gedetailleerde inzichten over uw zoek service, inclusief informatie over zoeken, indexeren, bewerkingen en metrische service gegevens.

U vindt de Power BI sjabloon app **Azure Cognitive Search: Analyseer logboeken en metrische gegevens** in de [Power bi apps Marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Aan de slag met de app
1. Diagnostische logboek registratie inschakelen voor uw zoek service:
    1. Maak of Identificeer een bestaand [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) waar u de logboeken kunt archiveren
    1. Ga naar de Azure Cognitive Search-service in de Azure Portal
    1. Selecteer in de sectie bewaking in de linkerkolom **Diagnostische instellingen**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Selecteer **+ Diagnostische instelling toevoegen**
    1. Controleer het **Archief op een opslag account**, geef de gegevens van uw opslag account op en controleer **OperationLogs** en **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Selecteer **Opslaan**

1. Nadat logboek registratie is ingeschakeld, gebruikt u uw zoek service om logboeken en metrische gegevens te genereren. Het duurt Maxi maal een uur voordat de containers in Blob Storage met deze logboeken worden weer gegeven. U ziet de container **Insights-logs-operationlogs** voor het zoeken naar verkeers logboeken en een **pt1m** -container voor metrische gegevens.

1. Zoek de Azure Cognitive Search Power BI-app in de [Power bi apps Marketplace](https://appsource.microsoft.com/marketplace/apps) en installeer deze in een nieuwe werk ruimte of een bestaande werk ruimte. De app heet **Azure Cognitive Search: Logboeken en metrische gegevens analyseren**.

1. Nadat u de app hebt geïnstalleerd, selecteert u de app in de lijst met apps in Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Selecteer **verbinden** om uw gegevens te verbinden

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Voer de naam in van het opslag account dat uw logboeken en metrische gegevens bevat. De app bekijkt standaard de laatste 10 dagen gegevens, maar deze waarde kan worden gewijzigd met de para meter **dagen** .

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Selecteer **sleutel** als verificatie methode en geef de sleutel van uw opslag account op. Selecteer **privé** als privacyniveau. Klik op aanmelden om te beginnen met het laad proces.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Wacht tot de gegevens zijn vernieuwd. Dit kan enige tijd duren, afhankelijk van de hoeveelheid gegevens die u hebt. U kunt zien of de gegevens nog steeds worden vernieuwd op basis van de onderstaande indicator.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Wanneer het vernieuwen van gegevens is voltooid, selecteert u **Azure Cognitive Search rapport** om het rapport weer te geven.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Zorg ervoor dat u de pagina vernieuwt nadat u het rapport hebt geopend, zodat het wordt geopend met uw gegevens.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>De app-para meters wijzigen
Als u gegevens uit een ander opslag account wilt visualiseren of het aantal dagen aan gegevens wilt wijzigen, volgt u de onderstaande stappen om de **dagen** en **Storage account** para meters te wijzigen.

1. Ga naar uw Power BI-apps, Zoek uw Azure Cognitive Search-app en selecteer de knop **app bewerken** om de werk ruimte weer te geven.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Selecteer **instellingen** in de opties voor de gegevensset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Wijzig op het tabblad gegevens sets de parameter waarden en selecteer **Toep assen**. Als er een probleem is met de verbinding, werkt u de referenties voor de gegevens bron op dezelfde pagina bij.

1. Ga terug naar de werk ruimte en selecteer **Nu vernieuwen** in de opties voor de gegevensset.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Open het rapport om de bijgewerkte gegevens weer te geven. Mogelijk moet u het rapport ook vernieuwen om de meest recente gegevens weer te geven.

## <a name="troubleshooting"></a>Problemen oplossen
Als u vindt dat uw gegevens niet kunnen worden weer gegeven, volgt u deze stappen voor probleem oplossing:

1. Open het rapport en vernieuw de pagina om er zeker van te zijn dat u de meest recente gegevens bekijkt. Er is een optie in het rapport om de gegevens te vernieuwen. Selecteer deze optie om de meest recente gegevens op te halen.

1. Controleer of de naam van het opslag account en de toegangs sleutel die u hebt ingevoerd juist zijn. De naam van het opslag account moet overeenkomen met het account dat is geconfigureerd met de logboeken van de zoek service.

1. Controleer of uw opslag account de containers **Insights-logboeken-operationlogs** en **inzichten-Metrics-pt1m** bevat en elke container gegevens bevat. De logboeken en metrische gegevens bevallen binnen een paar mappen-lagen.

1. Controleer of de gegevensset nog steeds wordt vernieuwd. De status indicator voor vernieuwen wordt weer gegeven in stap 8 hierboven. Als de service nog steeds wordt vernieuwd, wacht u totdat het vernieuwen is voltooid om het rapport te openen en te vernieuwen.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Wat is Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Basis concepten voor ontwerpers in de Power BI-service](https://docs.microsoft.com/power-bi/service-basic-concepts)