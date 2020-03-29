---
title: Gegevens analyseren in Azure Data Lake Storage Gen1 met Power BI | Microsoft Documenten
description: Power BI gebruiken om gegevens te analyseren die zijn opgeslagen in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65603205"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Gegevens analyseren in Azure Data Lake Storage Gen1 met Power BI
In dit artikel leert u hoe u Power BI Desktop gebruiken om gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1 te analyseren en te visualiseren.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](data-lake-store-get-started-portal.md) In dit artikel wordt ervan uitgegaan dat u al een Data Lake Storage Gen1-account hebt gemaakt, **myadlsg1**genaamd, en er een voorbeeldgegevensbestand **(Drivers.txt)** naar hebt geüpload. Dit voorbeeldbestand is beschikbaar om te downloaden van [Azure Data Lake Git Repository.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)
* **Power BI-bureaublad**. U dit downloaden van [Microsoft Download Center.](https://www.microsoft.com/en-us/download/details.aspx?id=45331) 

## <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop
1. Start Power BI Desktop op uw computer.
2. Klik **op** het lint Start op **Gegevens ophalen**en klik vervolgens op Meer. Klik in het dialoogvenster **Gegevens opvragen** op **Azure,** klik op **Azure Data Lake Store**en klik vervolgens op **Verbinden**.
   
    ![Verbinding maken met Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Verbinding maken met Data Lake Storage Gen1")
3. Als u een dialoogvenster ziet over de connector die zich in een ontwikkelingsfase bevindt, kiest u ervoor om door te gaan.
4. Geef in het dialoogvenster **Azure Data Lake Store** de URL op naar uw Data Lake Storage Gen1-account en klik op **OK.**
   
    ![URL voor Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL voor Data Lake Storage Gen1")
5. Klik in het volgende dialoogvenster op **Aanmelden** om u aan te melden bij het Data Lake Storage Gen1-account. U wordt doorgestuurd naar de aanmeldingspagina van uw organisatie. Volg de aanwijzingen om u aan te melden bij het account.
   
    ![Meld u aan bij Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Meld u aan bij Data Lake Storage Gen1")
6. Nadat u zich hebt aangemeld, klikt u op **Verbinden**.
   
    ![Verbinding maken met Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Verbinding maken met Data Lake Storage Gen1")
7. In het volgende dialoogvenster wordt het bestand weergegeven dat u hebt geüpload naar uw Data Lake Storage Gen1-account. Controleer de gegevens en klik op **Laden**.
   
    ![Gegevens laden van Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Gegevens laden van Data Lake Storage Gen1")
8. Nadat de gegevens zijn geladen in Power BI, ziet u de volgende velden op het tabblad **Velden.**
   
    ![Geïmporteerde velden](./media/data-lake-store-power-bi/imported-fields.png "Geïmporteerde velden")
   
    Om de gegevens te visualiseren en te analyseren, geven we echter de voorkeur aan de gegevens die beschikbaar zijn volgens de volgende velden
   
    ![Gewenste velden](./media/data-lake-store-power-bi/desired-fields.png "Gewenste velden")
   
    In de volgende stappen werken we de query bij om de geïmporteerde gegevens in de gewenste indeling om te zetten.
9. Klik op het lint **Start** op **Query's bewerken**.
   
    ![Query's bewerken](./media/data-lake-store-power-bi/edit-queries.png "Query's bewerken")
10. Klik in de queryeditor onder de kolom **Inhoud** op **Binair**.
    
    ![Query's bewerken](./media/data-lake-store-power-bi/convert-query1.png "Query's bewerken")
11. U ziet een bestandspictogram dat het **bestand Drivers.txt** vertegenwoordigt dat u hebt geüpload. Klik met de rechtermuisknop op het bestand en klik op **CSV**.    
    
    ![Query's bewerken](./media/data-lake-store-power-bi/convert-query2.png "Query's bewerken")
12. U ziet een uitvoer zoals hieronder weergegeven. Uw gegevens zijn nu beschikbaar in een indeling die u gebruiken om visualisaties te maken.
    
    ![Query's bewerken](./media/data-lake-store-power-bi/convert-query3.png "Query's bewerken")
13. Klik op het lint **Start** op **Sluiten en Toepassen**en klik vervolgens op Sluiten en **toepassen**.
    
    ![Query's bewerken](./media/data-lake-store-power-bi/load-edited-query.png "Query's bewerken")
14. Zodra de query is bijgewerkt, worden op het tabblad **Velden** de nieuwe velden weergegeven die beschikbaar zijn voor visualisatie.
    
    ![Bijgewerkte velden](./media/data-lake-store-power-bi/updated-query-fields.png "Bijgewerkte velden")
15. Laten we een cirkeldiagram maken om de chauffeurs in elke stad voor een bepaald land/regio te vertegenwoordigen. Maak hiervoor de volgende selecties.
    
    1. Klik op het tabblad Visualisaties op het symbool voor een cirkeldiagram.
       
        ![Cirkeldiagram maken](./media/data-lake-store-power-bi/create-pie-chart.png "Cirkeldiagram maken")
    2. De kolommen die we gaan gebruiken zijn **Kolom 4** (naam van de stad) en **Kolom 7** (naam van het land/regio). Sleep deze kolommen van het tabblad **Velden** naar het tabblad **Visualisaties** zoals hieronder weergegeven.
       
        ![Visualisaties maken](./media/data-lake-store-power-bi/create-visualizations.png "Visualisaties maken")
    3. De cirkel grafiek moet nu lijken op de hieronder weergegeven.
       
        ![Cirkeldiagram](./media/data-lake-store-power-bi/pie-chart.png "Visualisaties maken")
16. Door een specifiek land/regio te selecteren op basis van de filters op paginaniveau, u nu het aantal stuurprogramma's in elke stad van het geselecteerde land/regio zien. Selecteer bijvoorbeeld onder het tabblad **Visualisaties** onder **filters op paginaniveau**de optie **Brazilië**.
    
    ![Een land selecteren](./media/data-lake-store-power-bi/select-country.png "Een land/regio selecteren")
17. Het cirkeldiagram wordt automatisch bijgewerkt om de stuurprogramma's in de steden van Brazilië weer te geven.
    
    ![Chauffeurs in een land](./media/data-lake-store-power-bi/driver-per-country.png "Chauffeurs per land/regio")
18. Klik **in** het menu Bestand op **Opslaan** om de visualisatie op te slaan als een Power BI-bureaubladbestand.

## <a name="publish-report-to-power-bi-service"></a>Rapport publiceren naar Power BI-service
Zodra u de visualisaties in Power BI-bureaublad hebt gemaakt, u deze delen met anderen door deze te publiceren naar de Power BI-service. Zie [Publiceren vanaf Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)voor instructies over hoe u dat doen.

## <a name="see-also"></a>Zie ook
* [Gegevens analyseren in Data Lake Storage Gen1 met Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

