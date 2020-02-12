---
title: Webservice in Excel gebruiken
titleSuffix: ML Studio (classic) - Azure
description: Met Azure Machine Learning Studio (klassiek) kunt u eenvoudig webservices rechtstreeks vanuit Excel aanroepen zonder dat u code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150099"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Een Azure Machine Learning Studio-webservice (klassiek) gebruiken vanuit Excel

 Met Azure Machine Learning Studio (klassiek) kunt u eenvoudig webservices rechtstreeks vanuit Excel aanroepen zonder dat u code hoeft te schrijven.

Als u Excel 2013 (of hoger) of Excel online gebruikt, wordt u aangeraden de Excel [Excel-invoeg toepassing](excel-add-in-for-web-services.md)te gebruiken.



## <a name="steps"></a>Stappen
Een webservice publiceren. [Zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md) voor het maken van een credit risico geeft uitleg hoe dit moet doen. De Excel-werkmap-functie wordt momenteel alleen ondersteund voor aanvraag/antwoord-services die een enkele uitvoer (dat wil zeggen, een enkel scoring label) hebben. 

Zodra u een webservice hebt, klikt u op de **sectie webservices** aan de linkerkant van Studio en selecteert u vervolgens de webservice die vanuit Excel moet worden gebruikt.

**Klassieke webservice**

1. Op het tabblad **dash board** voor de webservice is een rij voor de **aanvraag/antwoord** service. Als deze service één uitvoer had, ziet u de koppeling **Excel-werkmap downloaden** in die rij.

    ![Excel-werkmap downloaden met behulp van de Web Service-Portal van Studio (klassiek)](./media/consuming-from-excel/excellink.png)
2. Klik op **Excel-werkmap downloaden**.

**Nieuwe webservice**

1. Selecteer in de portal Azure Machine Learning-webservice de optie **verbruik**.
2. Klik op de pagina verbruik in de sectie opties voor het gebruik van **webservices** op het pictogram Excel.

**De werkmap gebruiken**

1. Open de werkmap.
2. Er wordt een beveiligings waarschuwing weer gegeven. Klik op de knop **bewerken inschakelen** .

    ![Bewerken inschakelen om de beveiligde weer gave beveiligings waarschuwing te verwijderen](./media/consuming-from-excel/enableeditting.png)
3. Er wordt een beveiligingswaarschuwing weergegeven. Klik op de knop **inhoud inschakelen** om macro's uit te voeren in uw werk blad.

    ![Inhoud inschakelen voor het uitschakelen van de beveiligings waarschuwing macro's verwijderen](./media/consuming-from-excel/enablecontent.png)
4. Zodra de macro's zijn ingeschakeld, wordt een tabel wordt gegenereerd. Kolommen in blauw zijn vereist als invoer in de bron records-webservice, of **para meters**. Let op de uitvoer van de RR'S-service, **VOORspelde waarden** groen. Wanneer alle kolommen voor een bepaalde rij zijn ingevuld, de werkmap wordt automatisch de scoring-API-aanroepen en de beoordeelde resultaten worden weergegeven.

    ![Tabel voor parameter invoer en de resulterende voorspelde waarden](./media/consuming-from-excel/sampletable.png)
5. Voor het scoren van meer dan één rij opvulling van de tweede rij met gegevens en de voorspelde waarden worden geproduceerd. U kunt zelfs meerdere rijen in één keer plakken.

U kunt een van de Excel-functies (grafieken, power map, voorwaardelijke opmaak, enzovoort) met de voorspelde waarden gebruiken om u te helpen bij het visualiseren van de gegevens.

## <a name="sharing-your-workbook"></a>Delen van uw werkmap
Voor de macro's om te werken, moet uw API-sleutel deel uitmaken van het werkblad. Dat betekent dat u de werkmap moet delen alleen met entiteiten/personen die u vertrouwt.

## <a name="automatic-updates"></a>Automatische updates
Een RRS-aanroep wordt uitgevoerd in deze twee gevallen:

1. De eerste keer dat een rij inhoud bevat in alle **para meters**
2. Telkens wanneer een van de **para meters** verandert in een rij waarvoor alle **para meters** zijn opgegeven.