---
title: Excel-invoeg toepassing voor webservices
titleSuffix: ML Studio (classic) - Azure
description: Het gebruik van Azure Machine Learning-webservices rechtstreeks in Excel zonder een code te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: f7b5ca7112a6fb79586dc66b385e8015fe10e0b0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153448"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-invoeg toepassing voor Azure Machine Learning Studio (klassieke) webservices
Excel maakt het eenvoudig om aan te roepen webservices rechtstreeks zonder code te schrijven.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Stappen voor het gebruik van een bestaande webservice in de werkmap

1. Open het [Excel-voorbeeld bestand](https://aka.ms/amlexcel-sample-2), dat de Excel-invoeg toepassing bevat en gegevens over reizigers op het Titanic. 
 
    > [!NOTE]
    > U ziet de lijst met de webservices die betrekking hebben op het bestand en onder een selectievakje voor "automatisch"voorspellen. Als u automatische voor speling inschakelt, worden de voor spellingen van **alle** Services telkens bijgewerkt wanneer er een wijziging wordt doorgevoerd in de invoer. Als dit selectievakje is uitgeschakeld wordt u voor vernieuwing te klikken op 'Alle voorspellen' hebben. Voor het inschakelen van automatische-voorspellen op een service level gaat u naar stap 6.

2. De webservice kiezen door erop te klikken-' Titanic nagelaten voorspelde (Excel-invoegtoepassing voorbeeld) [Score] ' in dit voorbeeld.
   
    ![-Webservice selecteren](./media/excel-add-in-for-web-services/image1.png)
3. Hiermee gaat u naar de sectie voor **speld** .  Deze werkmap bevat al voorbeeld gegevens, maar voor een lege werkmap kunt u een cel in Excel selecteren en op **voorbeeld gegevens gebruiken**klikken.
4. Selecteer de gegevens met kopteksten en klikt u op het pictogram voor het bereik van invoergegevens.  Zorg ervoor dat het selectievakje 'Mijn gegevens bevatten kopteksten' is ingeschakeld.
5. Onder **uitvoer**voert u het nummer in van de cel waar de uitvoer moet worden opgegeven, bijvoorbeeld "H1".
6. Klik op voor **spel**. Als u het selectievakje "auto-predict" activeert geen wijzigingen in de geselecteerde gebieden (de items die zijn opgegeven als invoer) een aanvraag en een update van de cellen uitvoer zonder de noodzaak voor u om de predict-knop te drukken.
   
    ![Sectie voorspellen](./media/excel-add-in-for-web-services/image1.png)

Een webservice implementeren of gebruik een bestaande webservice. Zie voor meer informatie over het implementeren van een webservice [zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md)voor het implementeren van een credit risico.

Haal de API-sleutel voor uw webservice. Wanneer u deze actie is afhankelijk van of u een klassieke Machine Learning-webservice van een nieuwe Machine Learning-webservice hebt gepubliceerd.

**Een klassieke webservice gebruiken** 

1. Klik in Machine Learning Studio (klassiek) op de sectie **WEBservices** in het linkerdeel venster en selecteer vervolgens de webservice.
   
    ![Studio selecteert een webservice](./media/excel-add-in-for-web-services/image4.png)
2. Kopieer de API-sleutel voor de webservice.
   
    ![Studio API-sleutel](./media/excel-add-in-for-web-services/image5.png)
3. Klik op het tabblad **dash board** voor de webservice op de koppeling **aanvraag/antwoord** .
4. Zoek naar de sectie **URI van aanvraag** .  Kopieer en bewaar de URL.

> [!NOTE]
> Het is nu mogelijk om u aan te melden bij de [Azure machine learning Web Services](https://services.azureml.net) -Portal om de API-sleutel voor een klassieke machine learning-webservice te verkrijgen.
> 
> 

**Een nieuwe webservice gebruiken**

1. Klik in het [Azure machine learning Web Services](https://services.azureml.net) -Portal op **webservices**en selecteer vervolgens uw webservice. 
2. Klik op **verbruik**.
3. Zoek naar de sectie **basis informatie over verbruik** . Kopieer de **primaire sleutel** en sla de **aanvraag-antwoord-** URL op.

## <a name="steps-to-add-a-new-web-service"></a>Stappen voor het toevoegen van een nieuwe webservice

1. Een webservice implementeren of gebruik een bestaande webservice. Zie voor meer informatie over het implementeren van een webservice [zelf studie 3: het model](tutorial-part3-credit-risk-deploy.md)voor het implementeren van een credit risico.
2. Klik op **verbruik**.
3. Zoek naar de sectie **basis informatie over verbruik** . Kopieer de **primaire sleutel** en sla de **aanvraag-antwoord-** URL op.
4. Ga in Excel naar de sectie **webservices** (als u in de sectie voor **spelt** klikt u op de pijl terug om naar de lijst met webservices te gaan).
   
    ![Ga naar de Web service selecteren](./media/excel-add-in-for-web-services/image3.png)
5. Klik op **Add Web Service**.
6. Plak de URL in het tekstvak Excel-invoeg toepassing met de naam **URL**.
7. Plak de API/Primary-sleutel in de label- **API-sleutel**van het tekstvak.
8. Klik op **Toevoegen**.
   
    ![URL en API-sleutel voor een klassieke webservice.](./media/excel-add-in-for-web-services/image6.png)
9. Voor het gebruik van de webservice, volgt u de voorgaande instructies, "Stappen voor het gebruik van een bestaande web Service."

## <a name="sharing-your-workbook"></a>Delen van uw werkmap
Als u uw werkmap opslaat, wordt ook de API/primaire sleutel voor de web-services die u hebt toegevoegd opgeslagen. Dit betekent dat u moet de werkmap alleen delen met personen die u vertrouwt.

Stel alle vragen in de volgende sectie met opmerkingen of op ons [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
