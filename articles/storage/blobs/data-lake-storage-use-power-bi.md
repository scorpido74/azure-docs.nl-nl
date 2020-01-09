---
title: Gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Power BI | Microsoft Docs
description: Power BI gebruiken voor het analyseren van gegevens die zijn opgeslagen in Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: 156999c6b6d8451516ac1c0f095e1a864420d0b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354819"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Power BI

In dit artikel leert u hoe u Power BI Desktop kunt gebruiken voor het analyseren en visualiseren van gegevens die zijn opgeslagen in een opslag account met een hiërarchische naam ruimte (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> In dit artikel wordt ervan uitgegaan dat u een account hebt gemaakt met de naam `myadlsg2`.
> * U hebt een van de volgende rollen verleend voor het opslag account: **BLOB data Reader**, **BLOB data contributor**of **BLOB data eigenaar**.
> * Een voor beeld van een gegevens bestand met de naam `Drivers.txt` zich in uw opslag account bevindt.
> U kunt dit voor beeld downloaden van [Azure data Lake Git-opslag plaats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)en dat bestand vervolgens uploaden naar uw opslag account.
> * **Power bi Desktop**. U kunt dit downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop

1. Start Power BI Desktop op de computer.
2. Klik op het tabblad **Start** van het lint op **gegevens ophalen**en klik vervolgens op **meer**.
3. Klik in het dialoog venster **gegevens ophalen** op **Azure**, klik op **Azure data Lake Store Gen2 (bèta)** en klik vervolgens op **verbinding maken**.

    ![Pagina gegevens ophalen](media/data-lake-storage-use-power-bi/get-data-page.png)

4. In het dialoog venster **Azure data Lake Storage Gen2** kunt u de URL naar uw Azure data Lake Storage Gen2 account, bestands systeem of submap, met de indeling van het container eindpunt opgeven. Url's voor Data Lake Storage Gen2 hebben het volgende patroon `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` en klik vervolgens op **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. Klik in het volgende dialoog venster op **Aanmelden** om u aan te melden bij uw opslag account. U wordt omgeleid naar de aanmeldings pagina van uw organisatie. Volg de aanwijzingen om u aan te melden bij het account.

    ![Aanmeldingspagina](media/data-lake-storage-use-power-bi/sign-in.png)

6. Nadat u zich hebt aangemeld, klikt u op **verbinden**.

    ![Pagina aangemeld](media/data-lake-storage-use-power-bi/signed-in.png)

7. In het volgende dialoog venster worden alle bestanden weer gegeven onder de URL die u in stap 4 hierboven hebt gegeven, inclusief het bestand dat u hebt geüpload naar uw opslag account. Controleer de informatie en klik vervolgens op **laden**.

    ![Bestandssystemen](media/data-lake-storage-use-power-bi/file-systems.png)

8. Nadat de gegevens zijn geladen in Power BI, ziet u de volgende velden op het tabblad **velden** .

    ![Tabblad velden](media/data-lake-storage-use-power-bi/fields.png)

    Als u de gegevens wilt visualiseren en analyseren, hebben we de voor keur dat de gegevens beschikbaar zijn volgens de volgende velden.

    ![Velden](media/data-lake-storage-use-power-bi/preferred-fields.png)

    In de volgende stappen wordt de query bijgewerkt voor het converteren van de geïmporteerde gegevens in de gewenste indeling.

9. Klik op het tabblad **Start** op het lint op **query's bewerken**.

    ![Query's](media/data-lake-storage-use-power-bi/queries.png)

10. Klik in de **query-editor**, onder de kolom **inhoud** , op **binair**. Het bestand wordt automatisch gedetecteerd als CSV en u ziet een uitvoer zoals hieronder wordt weer gegeven. Uw gegevens zijn nu beschikbaar in een indeling die u kunt gebruiken om visualisaties te maken.

    ![Uitvoer](media/data-lake-storage-use-power-bi/binary.png)

11. Klik op het tabblad **Start** op het lint op **sluiten** en **Toep assen**en klik vervolgens op **sluiten** en **Toep assen**.

    ![Sluiten en toepassen](media/data-lake-storage-use-power-bi/close-apply.png)

12. Zodra de query is bijgewerkt, worden de nieuwe velden die beschikbaar zijn voor visualisatie weer gegeven op het tabblad **velden** .

    ![Nieuwe velden](media/data-lake-storage-use-power-bi/new-fields.png)

13. Laat ons een cirkel diagram maken om de Stuur Programma's in elke plaats voor een bepaald land weer te geven. U doet dit door de volgende selecties te maken.

    Klik op het tabblad **Visualisaties** op het symbool voor een cirkel diagram.

    ![Visualisaties](media/data-lake-storage-use-power-bi/visualizations.png)

    De kolommen die we gaan gebruiken, zijn kolom 4 (naam van de plaats) en kolom 7 (naam van het land). Sleep deze kolommen van het tabblad **velden** naar het tabblad **Visualisaties** , zoals hieronder wordt weer gegeven.

    ![Velden slepen](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Het cirkel diagram moet er nu uitzien zoals hieronder wordt weer gegeven.

    ![Cirkeldiagram](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Als u een specifiek land selecteert in de filters op pagina niveau, kunt u nu het aantal Stuur Programma's in elke plaats van het geselecteerde land bekijken. Selecteer bijvoorbeeld onder het tabblad **Visualisaties** de optie **Brazilië**onder **filters op pagina niveau**.

    ![Pagina filters](media/data-lake-storage-use-power-bi/page-filters.png)

15. Het cirkel diagram wordt automatisch bijgewerkt om de Stuur Programma's in de steden van Brazilië weer te geven.

    ![Brazilië](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. Klik in het menu **bestand** op **Opslaan** om de visualisatie op te slaan als een Power bi desktop bestand.

## <a name="publish-report-to-power-bi-service"></a>Rapport publiceren naar Power BI-service

Nadat u de visualisaties in Power BI Desktop hebt gemaakt, kunt u deze delen met anderen door deze te publiceren naar de Power BI-service. Zie [publiceren vanuit Power bi Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)voor instructies over hoe u dit doet.
