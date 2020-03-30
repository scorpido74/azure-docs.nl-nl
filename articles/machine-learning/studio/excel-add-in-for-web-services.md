---
title: Excel-invoegtoepassing voor webservices
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Web Services rechtstreeks in Excel gebruiken zonder code te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204372"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-invoegtoepassing voor Azure Machine Learning Studio (klassieke) webservices

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Excel maakt het eenvoudig om webservices rechtstreeks te bellen zonder dat u code hoeft te schrijven.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Stappen om een bestaande webservice in de werkmap te gebruiken

1. Open het [voorbeeld excelbestand](https://aka.ms/amlexcel-sample-2), dat de Excel-invoegtoepassing en gegevens over passagiers op de Titanic bevat. 
 
    > [!NOTE]
    > U ziet de lijst met webservices met betrekking tot het bestand en onderaan een selectievakje voor 'Automatisch voorspellen'. Als u automatisch voorspellen van de voorspellingen van **al** uw diensten zal worden bijgewerkt elke keer dat er een wijziging op de ingangen. Als u niet bent aangevinkt, moet u op 'Alles voorspellen' klikken om te vernieuwen. Ga naar stap 6 voor het inschakelen van automatisch voorspellen op serviceniveau.

2. Kies de webservice door erop te klikken - "Titanic Survivor Predictor (Excel Add-in Sample) [Score]" in dit voorbeeld.
   
    ![Webservice selecteren](./media/excel-add-in-for-web-services/image1.png)
3. Dit brengt u naar de sectie **Voorspellen.**  Deze werkmap bevat al voorbeeldgegevens, maar voor een lege werkmap u een cel in Excel selecteren en op **Voorbeeldgegevens gebruiken**.
4. Selecteer de gegevens met kopteksten en klik op het pictogram invoergegevensbereik.  Controleer of het vak "Mijn gegevens heeft kopteksten" is ingeschakeld.
5. Voer **onder Uitvoer**het celnummer in waar u de uitvoer wilt hebben, bijvoorbeeld 'H1'.
6. Klik **op Voorspellen**. Als u het selectievakje 'automatisch voorspellen' inschakelt, wordt een wijziging op de geselecteerde gebieden (de als invoer opgegeven) een verzoek en een update van de uitvoercellen geactiveerd zonder dat u op de voorspelbaarsknop hoeft te drukken.
   
    ![Sectie Voorspellen](./media/excel-add-in-for-web-services/image1.png)

Een webservice implementeren of een bestaande webservice gebruiken. Zie [Zelfstudie 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md)voor meer informatie over het implementeren van een webservice.

Download de API-sleutel voor uw webservice. Waar u deze actie uitvoert, hangt af van de vraag of u een Classic Machine Learning-webservice van een nieuwe machine learning-webservice hebt gepubliceerd.

**Een klassieke webservice gebruiken** 

1. Klik in Machine Learning Studio (klassiek) op de sectie **WEB SERVICES** in het linkerdeelvenster en selecteer de webservice.
   
    ![Studio selecteert een webservice](./media/excel-add-in-for-web-services/image4.png)
2. Kopieer de API-sleutel voor de webservice.
   
    ![Studio API-sleutel](./media/excel-add-in-for-web-services/image5.png)
3. Klik op het tabblad **DASHBOARD** voor de webservice op de koppeling **VERZOEK/ANTWOORD.**
4. Kijk voor de **sectie Aanvraag URI.**  Kopieer en sla de URL op.

> [!NOTE]
> Het is nu mogelijk om u aan te melden bij de [Azure Machine Learning Web Services-portal](https://services.azureml.net) om de API-sleutel voor een Classic Machine Learning-webservice te verkrijgen.
> 
> 

**Een nieuwe webservice gebruiken**

1. Klik in de [Azure Machine Learning Web Services-portal](https://services.azureml.net) op **Webservices**en selecteer vervolgens uw webservice. 
2. Klik **op Consumeren**.
3. Zoek naar de sectie **Basisverbruiksinformatie.** Kopieer en sla de **primaire sleutel** en de URL voor het antwoord op **verzoek op.**

## <a name="steps-to-add-a-new-web-service"></a>Stappen om een nieuwe webservice toe te voegen

1. Een webservice implementeren of een bestaande webservice gebruiken. Zie [Zelfstudie 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md)voor meer informatie over het implementeren van een webservice.
2. Klik **op Consumeren**.
3. Zoek naar de sectie **Basisverbruiksinformatie.** Kopieer en sla de **primaire sleutel** en de URL voor het antwoord op **verzoek op.**
4. Ga in Excel naar de sectie **Webservices** (als u zich in de sectie **Voorspellen** bevindt, klikt u op de pijl achter om naar de lijst met webservices te gaan).
   
    ![Ga naar webserviceselectie](./media/excel-add-in-for-web-services/image3.png)
5. Klik **op Webservice toevoegen**.
6. Plak de URL in het excel-invoegvak met het **label URL**.
7. Plak de API/Primary-sleutel in het tekstvak met het **label API-sleutel**.
8. Klik op**toevoegen**.
   
    ![URL- en API-sleutel voor een klassieke webservice.](./media/excel-add-in-for-web-services/image6.png)
9. Als u de webservice wilt gebruiken, volgt u de voorgaande aanwijzingen:'Stappen om een bestaande webservice te gebruiken'.

## <a name="sharing-your-workbook"></a>Uw werkmap delen
Als u uw werkmap opslaat, wordt ook de API/Primaire sleutel voor de webservices die u hebt toegevoegd opgeslagen. Dat betekent dat u de werkmap alleen moet delen met personen die u vertrouwt.

Stel vragen in de volgende reactie sectie of op ons [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
