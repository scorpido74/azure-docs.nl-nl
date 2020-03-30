---
title: Een Azure Storage-account controleren in de Azure-portal | Microsoft Documenten
description: Meer informatie over het bewaken van een opslagaccount in Azure met behulp van de Azure-portal.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268286"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Een Storage-account bewaken in de Azure-portal

[Azure Storage Analytics](storage-analytics.md) biedt statistieken voor alle opslagservices en logboeken voor blobs, wachtrijen en tabellen. U de [Azure-portal](https://portal.azure.com) gebruiken om te configureren welke statistieken en logboeken voor uw account worden geregistreerd en grafieken configureren die visuele weergaven van uw metrische gegevens bieden. 

We raden u aan [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (voorbeeld) te controleren. Het is een functie van Azure Monitor die uitgebreide bewaking van uw Azure Storage-accounts biedt door een uniforme weergave te bieden van de prestaties, capaciteit en beschikbaarheid van uw Azure Storage Services. Het vereist niet dat u iets in- of configureert en u deze statistieken onmiddellijk bekijken vanuit de vooraf gedefinieerde interactieve grafieken en andere visualisaties die zijn opgenomen.

> [!NOTE]
> Er zijn kosten verbonden aan het onderzoeken van bewakingsgegevens in de Azure-portal. Zie [Storage Analytics](storage-analytics.md)voor meer informatie.
>
> Azure Files ondersteunt momenteel Storage Analytics-statistieken, maar ondersteunt nog geen logboekregistratie.
>
> Premium performance block blob-opslagaccounts bieden geen ondersteuning voor Storage Analytic-statistieken, maar ondersteunen wel logboekregistratie. U logboekregistratie programmatisch inschakelen via de REST API of de clientbibliotheek. Als u statistieken met blob-opslagaccounts met premium prestaties wilt weergeven, u [azure-opslagstatistieken gebruiken in Azure Monitor](storage-metrics-in-azure-monitor.md).
>
> Zie [Microsoft Azure Storage controleren, diagnosticeren en oplossen](storage-monitoring-diagnosing-troubleshooting.md)van Problemen met Azure Storage voor een uitgebreide handleiding voor het gebruik van Storage Analytics en andere hulpprogramma's voor het identificeren, diagnosticeren en oplossen van azure storage.
>

## <a name="configure-monitoring-for-a-storage-account"></a>Controle configureren voor een opslagaccount

1. Selecteer in de [Azure-portal](https://portal.azure.com) **Opslagaccounts**en vervolgens de naam van het opslagaccount om het accountdashboard te openen.
1. Selecteer **Diagnostische gegevens** in de sectie **CONTROLE** van het menublad.

    ![Monitoringopties](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecteer het **type** metrische gegevens voor elke **service** die u wilt controleren en het **bewaarbeleid** voor de gegevens. U de bewaking ook uitschakelen door **Status** in te stellen op **Uit.**

    ![Monitoringopties](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Als u het beleid voor het bewaren van gegevens wilt instellen, verplaatst u de schuifregelaar **Retentie (dagen)** of voert u het aantal dagen gegevens in dat moet worden bewaard, van 1 naar 365. De standaardinstelling voor nieuwe opslagaccounts is zeven dagen. Als u geen bewaarbeleid wilt instellen, voert u nul in. Als er geen bewaarbeleid is, is het aan u om de bewakingsgegevens te verwijderen.

   > [!WARNING]
   > Er worden kosten in rekening gebracht wanneer u metrische gegevens handmatig verwijdert. Verouderde analysegegevens (gegevens die ouder zijn dan uw bewaarbeleid) worden zonder kosten door het systeem verwijderd. We raden u aan een bewaarbeleid in te stellen op basis van hoe lang u opslaganalysegegevens voor uw account wilt bewaren. Zie [Facturering op opslagstatistieken](storage-analytics-metrics.md#billing-on-storage-metrics) voor meer informatie.
   >

1. Wanneer u klaar bent met de bewakingsconfiguratie, selecteert u **Opslaan**.

Een standaardset met statistieken wordt weergegeven in grafieken op het opslagaccountblad, evenals de afzonderlijke serviceblades (blob, wachtrij, tabel en bestand). Zodra u metrische gegevens voor een service hebt ingeschakeld, kan het tot een uur duren voordat gegevens in de grafieken worden weergegeven. U **Bewerken** in een metrische grafiek selecteren om te configureren welke statistieken in de grafiek worden weergegeven.

U het verzamelen en registreren van statistieken uitschakelen door **Status** in te stellen in **Uit**.

> [!NOTE]
> Azure Storage gebruikt [tabelopslag](storage-introduction.md#table-storage) om de statistieken voor uw opslagaccount op te slaan en slaat de statistieken op in tabellen in uw account. Zie voor meer informatie [Hoe statistieken worden opgeslagen](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Statistiekengrafieken aanpassen

Gebruik de volgende procedure om te kiezen welke opslagstatistieken u wilt weergeven in een grafiek met statistieken.

1. Begin met het weergeven van een opslagmetrische grafiek in de Azure-portal. U grafieken vinden op het **opslagaccountblad** en in het blade **metrics** voor een afzonderlijke service (blob, wachtrij, tabel, bestand).

   In dit voorbeeld wordt de volgende grafiek gebruikt die wordt weergegeven op het blad van het **opslagaccount:**

   ![Grafiekselectie in Azure-portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klik ergens in de grafiek om de grafiek te bewerken.

1. Selecteer vervolgens het **tijdsbereik** van de statistieken die u in de grafiek wilt weergeven en de **service** (blob, wachtrij, tabel, bestand) waarvan u de statistieken wilt weergeven. Hier worden de statistieken van de afgelopen week geselecteerd om weer te geven voor de blobservice:

   ![Tijdbereik en serviceselectie in het blade van het grafiekblad bewerken](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecteer de afzonderlijke **statistieken die** u in de grafiek wilt weergeven en klik op **OK**.

   ![Individuele metrische selectie in Grafiekblad bewerken](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Uw grafiekinstellingen hebben geen invloed op het verzamelen, samenvoegen of opslaan van bewakingsgegevens in het opslagaccount.

### <a name="metrics-availability-in-charts"></a>Beschikbaarheid van statistieken in grafieken

De lijst met beschikbare statistieken wordt gewijzigd op basis van de service die u hebt gekozen in de vervolgkeuzelijst en het eenheidstype van de grafiek die u bewerkt. U bijvoorbeeld alleen percentagestatistieken zoals *PercentNetworkError* en *PercentThrottlingError* selecteren als u een grafiek bewerkt waarin eenheden in percentage worden weergegeven:

![Foutpercentagegrafiek aanvragen in de Azure-portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Statistieken oplossing

De statistieken die u in **Diagnostics** hebt geselecteerd, bepalen de oplossing van de statistieken die beschikbaar zijn voor uw account:

* **Geaggregeerde** monitoring biedt statistieken zoals ingress/uitgang, beschikbaarheid, latentie en succespercentages. Deze statistieken worden samengevoegd vanuit de blob-, tabel-, bestands- en wachtrijservices.
* **Per API** biedt een fijnere oplossing, met statistieken die beschikbaar zijn voor afzonderlijke opslagbewerkingen, naast de aggregaten op serviceniveau.

## <a name="configure-metrics-alerts"></a>Metrische waarschuwingen configureren

U waarschuwingen maken om u te waarschuwen wanneer er drempelwaarden zijn bereikt voor statistieken van opslagbronnen.

1. Als u het **blad Waarschuwingsregels wilt openen,** schuift u omlaag naar de sectie **CONTROLE** van het **menublad** en selecteert u **Waarschuwingen (klassiek).**
2. Selecteer **Metrische waarschuwing toevoegen (klassiek)** om het blad Van een **waarschuwingsregel toevoegen** te openen
3. Voer een **naam** en **beschrijving in** voor uw nieuwe waarschuwingsregel.
4. Selecteer de **statistiek** waarvoor u een waarschuwing, een **waarschuwingsvoorwaarde**en een **drempelwaarde**wilt toevoegen . Het type drempeleenheid verandert afhankelijk van de statistiek die u hebt gekozen. 'Tellen' is bijvoorbeeld het eenheidstype voor *ContainerCount,* terwijl de eenheid voor de statistiek *PercentNetworkError* een percentage is.
5. Selecteer de **periode**. Statistieken die de drempelwaarde binnen de periode bereiken of overschrijden, activeren een waarschuwing.
6. (Optioneel) **E-mail-** en **webhook-meldingen** configureren. Zie [Een webhook configureren op een Azure-metrische waarschuwing](../../azure-monitor/platform/alerts-webhooks.md)voor meer informatie over webhooks. Als u geen e-mail- of webhookmeldingen configureert, worden waarschuwingen alleen weergegeven in de Azure-portal.

![Blade 'Een waarschuwingsregel' toevoegen in de Azure-portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metrische grafieken toevoegen aan het portaldashboard

U Azure Storage-statistiekendiagrammen voor al uw opslagaccounts toevoegen aan uw portaldashboard.

1. Selecteer klik op **Dashboard bewerken** terwijl u uw dashboard weergeeft in de [Azure-portal.](https://portal.azure.com)
1. Selecteer tegels zoeken **op** > **type**in de **tegelgalerie**.
1. Selecteer **Opslagaccounts van** > **type**.
1. Selecteer **in Resources**het opslagaccount waarvan u de statistieken wilt toevoegen aan het dashboard.
1. Selecteer **rubriekencontrole** > **Monitoring**.
1. Sleep de grafiektegel naar uw dashboard voor de statistiek die u wilt weergeven. Herhaal dit voor alle statistieken die u op het dashboard wilt weergeven. In de volgende afbeelding wordt het diagram 'Blobs - Totaal aanvragen' gemarkeerd als voorbeeld, maar alle grafieken zijn beschikbaar voor plaatsing op uw dashboard.

   ![Tegelgalerie in Azure-portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecteer **Gereed aanpassen** boven aan het dashboard wanneer u klaar bent met het toevoegen van grafieken.

Zodra u grafieken aan uw dashboard hebt toegevoegd, u ze verder aanpassen zoals beschreven in grafieken met statistieken aanpassen.

## <a name="configure-logging"></a>Logboekregistratie configureren

U Azure Storage instrueren om diagnostische logboeken op te slaan voor lees-, schrijf- en verwijderaanvragen voor de blob-, tabel- en wachtrijservices. Het beleid voor gegevensbewaring dat u instelt, is ook van toepassing op deze logboeken.

> [!NOTE]
> Azure Files ondersteunt momenteel Storage Analytics-statistieken, maar ondersteunt nog geen logboekregistratie.
>

1. Selecteer in de [Azure-portal](https://portal.azure.com) **Opslagaccounts**en vervolgens de naam van het opslagaccount om het opslagaccountblad te openen.
1. Selecteer **Diagnostische instellingen (klassiek)** in het gedeelte **Controle (klassiek)** van het menublad.

    ![Menu-item voor diagnostische gegevens onder CONTROLE in de Azure-portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Zorg ervoor dat **status** is ingesteld **op Aan**en selecteer de **services** waarvoor u logboekregistratie wilt inschakelen.

    ![Logboekregistratie configureren in de Azure-portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klik op **Opslaan**.

De diagnostische logboeken worden opgeslagen in een blobcontainer met de naam *$logs* in uw opslagaccount. U de logboekgegevens bekijken met behulp van een opslagverkenner zoals de [Microsoft Storage Explorer](https://storageexplorer.com)of programmatisch met behulp van de opslagclientbibliotheek of PowerShell.

Zie [Logboekregistratie opslaganalyses](storage-analytics-logging.md)voor informatie over toegang tot de $logs container.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [statistieken, logboekregistratie en facturering](storage-analytics.md) voor Storage Analytics.
