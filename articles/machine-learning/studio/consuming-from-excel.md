---
title: Webservice gebruiken in Excel
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klassiek) maakt het eenvoudig om webservices rechtstreeks vanuit Excel te bellen zonder dat u code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218215"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Een Azure Machine Learning Studio (klassieke) webservice gebruiken vanuit Excel

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassiek) maakt het eenvoudig om webservices rechtstreeks vanuit Excel te bellen zonder dat u code hoeft te schrijven.

Als u Excel 2013 (of hoger) of Excel Online gebruikt, raden we u aan de [Excel-invoegtoepassing](excel-add-in-for-web-services.md)te gebruiken.



## <a name="steps"></a>Stappen
Een webservice publiceren. [Zelfstudie 3: Implementeer kredietrisico model](tutorial-part3-credit-risk-deploy.md) legt uit hoe dit te doen. Momenteel wordt de Functie Excel-werkmap alleen ondersteund voor aanvraag-/antwoordservices met één uitvoer (dat wil zeggen één scorelabel). 

Zodra u een webservice hebt, klikt u op de sectie **WEBSERVICES** aan de linkerkant van de studio en selecteert u de webservice die u vanuit Excel wilt gebruiken.

**Klassieke webservice**

1. Op het tabblad **DASHBOARD** voor de webservice is een rij voor de **REQUEST/RESPONSE-service.** Als deze service één uitvoer had, ziet u de koppeling **Excel-werkmap downloaden** in die rij.

    ![Excel-werkmap downloaden met de Studio-portal (klassieke) webservice](./media/consuming-from-excel/excellink.png)
2. Klik op **Excel-werkmap downloaden**.

**Nieuwe webservice**

1. Selecteer In de Azure Machine Learning Web Service-portal de optie **Consumeren**.
2. Klik op de pagina Consumeren in de sectie **Webserviceverbruiksopties** op het Excel-pictogram.

**De werkmap gebruiken**

1. Open de werkmap.
2. Er verschijnt een beveiligingswaarschuwing; klik op de knop **Bewerken inschakelen.**

    ![Bewerken inschakelen om de beveiligingswaarschuwing voor de beveiligde weergave te verwijderen](./media/consuming-from-excel/enableeditting.png)
3. Er verschijnt een beveiligingswaarschuwing. Klik op de knop **Inhoud inschakelen** om macro's op uw spreadsheet uit te voeren.

    ![Inhoud inschakelen om de beveiligingswaarschuwing uit te schakelen voor macro's](./media/consuming-from-excel/enablecontent.png)
4. Zodra macro's zijn ingeschakeld, wordt een tabel gegenereerd. Kolommen in het blauw zijn vereist als invoer in de RRS-webservice of **PARAMETERS**. Let op de uitvoer van de **RRS-service, VOORSPELDE WAARDEN** in het groen. Wanneer alle kolommen voor een bepaalde rij zijn gevuld, wordt in de werkmap automatisch de score-API aangebeld en worden de gescoorde resultaten weergegeven.

    ![Tabel voor parameteringangen en de resulterende voorspelde waarden](./media/consuming-from-excel/sampletable.png)
5. Als u meer dan één rij wilt scoren, vult u de tweede rij met gegevens en worden de voorspelde waarden geproduceerd. U zelfs meerdere rijen tegelijk plakken.

U een van de Excel-functies (grafieken, powermap, voorwaardelijke opmaak, enz.) gebruiken met de voorspelde waarden om de gegevens te visualiseren.

## <a name="sharing-your-workbook"></a>Uw werkmap delen
Als de macro's kunnen werken, moet uw API-sleutel deel uitmaken van de spreadsheet. Dat betekent dat u de werkmap alleen moet delen met entiteiten/personen die u vertrouwt.

## <a name="automatic-updates"></a>Automatische updates
In deze twee situaties wordt een RRS-oproep gedaan:

1. De eerste keer dat een rij inhoud heeft in al zijn **PARAMETERS**
2. Elke keer dat een van de **PARAMETERS** verandert in een rij die al de **PARAMETERS** had ingevoerd.