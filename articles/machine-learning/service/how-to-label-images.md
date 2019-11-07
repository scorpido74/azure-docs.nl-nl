---
title: Het hulp programma voor gegevens labeling van Azure Machine Learning gebruiken
title.suffix: Azure Machine Learning
description: In dit artikel leert u hoe u de hulpprogram ma's voor gegevens labelen gebruikt in een Azure Machine Learning labelen project.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: af12361ed11d0a16e5a5d0cfe5989bb3918ce154
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586395"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Afbeeldingen in een label project labelen

Zodra de Project beheerder een label project heeft gemaakt in Azure Machine Learning Studio, kunt u het hulp programma labelen gebruiken om snel gegevens voor een machine learning project voor te bereiden. 

> [!div class="checklist"]
> * Toegang krijgen tot uw etiket projecten
> * Hulp middelen voor labels
> * De hulpprogram ma's voor specifieke label taken gebruiken

## <a name="prerequisites"></a>Vereisten

* De Portal-URL voor het labelen van een actief gegevens label project
* Een [micro soft-account](https://account.microsoft.com/account) of
* Een Azure Active Directory-account voor de organisatie en het project

> [!Note]
> De Project beheerder kan de URL van de labeling Portal vinden op het tabblad **Details** van de pagina **Project Details** . 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Aanmelden bij de portal voor het labelen van het project

Ga naar de URL voor het labelen van de portal die u door de Project beheerder hebt gekregen. 

Meld u aan met het e-mail account dat de beheerder heeft gebruikt om u toe te voegen aan het team. Voor de meeste gebruikers wordt het aanmelden met uw Microsoft-account uitgevoerd. Als het label project Azure Active Directory gebruikt, is dat de manier waarop u zich aanmeldt. 

## <a name="understanding-the-labeling-task"></a>Uitleg over de taak labelen

Zodra u bent aangemeld, wordt u naar de overzichts pagina van het project geleid. 

Het eerste wat u moet doen, is **gedetailleerde instructies weer geven**. Deze instructies zijn specifiek voor uw project en geven een uitleg over het type gegevens dat u gebruikt, hoe u uw beslissingen moet nemen en andere relevante informatie. Wanneer u klaar bent, gaat u terug naar de pagina project en kiest u **labelen starten**.

## <a name="common-features-of-the-labeling-task"></a>Algemene functies van de label taak

Alle taken voor het labelen van afbeeldingen omvatten het kiezen van een geschikte tag of labels uit een set die is opgegeven door de Project beheerder. U kunt kiezen uit de eerste negen Tags door de cijfer toetsen op het toetsen bord te gebruiken.  

Met de classificatie taken voor installatie kopieën kunt u ervoor kiezen om meerdere installatie kopieën tegelijk te presen teren. De verschillende indelingen kunnen worden gekozen met behulp van de pictogrammen boven het gebied afbeelding. U kunt alle weer gegeven installatie kopieën tegelijk selecteren door op de knop **Alles selecteren** te drukken. Selecteer afzonderlijke Foto's met behulp van de ronde selectie knop in de rechter bovenhoek van het gebied van de afbeelding. U moet ten minste één afbeelding selecteren om een tag toe te passen. Als u meerdere installatie kopieën hebt geselecteerd en u een tag selecteert, wordt deze tag toegepast op elk van de geselecteerde foto's.

Hier hebben we een indeling van 2x2 gekozen en gaan we het label ' zoog ' toep assen op de installatie kopieën van de beer en Orca. De afbeelding met de haai is al gelabeld als "Cartilaginous vissen" en de Iguana is nog niet gelabeld.

![Indelingen en selecties van meerdere afbeeldingen](media/how-to-label-images/layouts.png)

> [!Important] 
> Schakel alleen indelingen in als u een nieuwe pagina hebt met niet-gelabelde gegevens. Als u inschakelt, wordt de code ring van de pagina in uitvoering gewist. 

In azure wordt de **Verzend** knop ingeschakeld wanneer u alle afbeeldingen op de pagina hebt gelabeld. Druk op **verzenden** om uw werk op te slaan. 

Zodra u labels voor de gegevens hebt verzonden, wordt de pagina door Azure vernieuwd met een nieuwe set installatie kopieën uit de werk wachtrij.  

## <a name="tagging-images-for-multi-class-classification"></a>Afbeeldingen labelen voor classificatie met meerdere klassen

Als uw project van het type ' afbeeldings classificatie met meerdere klassen ' is, wijst u één tag toe aan de volledige afbeelding. Kies op elk gewenst moment de **instructies** pagina en navigeer naar **gedetailleerde instructies voor het weer geven** van projectgerelateerde richt lijnen. 

Zoals eerder is beschreven, kunt u kiezen uit verschillende indelingen voor het presen teren van afbeeldingen. Als, na het selecteren van een afbeelding en het toewijzen van een tag, u zich realiseert dat u een fout hebt gemaakt, kunt u het probleem oplossen. Als u in het label dat onder de afbeelding wordt weer gegeven, op het `X` doel klikt, wordt het label gewist. Als u de afbeelding selecteert en een andere klasse kiest, wordt het label overgeschakeld naar de zojuist geselecteerde waarde.

## <a name="tagging-images-for-multi-label-classification"></a>Afbeeldingen labelen voor classificatie met meerdere labels

Als u werkt met een project van het type ' afbeeldings classificatie met meerdere labels ', past u een _of meer_ tags toe op een afbeelding. Kies op elk gewenst moment de **instructies** pagina en navigeer naar **gedetailleerde instructies voor het weer geven** van projectgerelateerde richt lijnen. 

Selecteer de afbeelding die u wilt labelen en klik vervolgens op de tag. Door de tag te kiezen, past u deze toe op alle geselecteerde installatie kopieën en wordt de selectie opheffen. Als u meer tags wilt Toep assen, moet u de installatie kopieën opnieuw selecteren. Deze animatie toont een pagina voor labelen met meerdere labels:

* **Alles selecteren** wordt gebruikt om de tag "Oceaan" toe te passen
* Er is één afbeelding geselecteerd en gelabeld ' Close '
* Er zijn drie afbeeldingen geselecteerd en gelabeld ' breed '

![Animatie met een multilabel stroom](media/how-to-label-images/multilabel.gif)

Als u een fout wilt corrigeren, klikt u op de `X` om afzonderlijke Tags te wissen of de afbeeldingen te selecteren en kiest u de tag, waardoor de tag wordt gewist van alle geselecteerde installatie kopieën. Dit scenario wordt hier weer gegeven. Als u op ' land ' klikt, wordt dat label uit de twee geselecteerde installatie kopieën gewist.

![Scherm afbeelding met meerdere selectie](media/how-to-label-images/multiple-deselection.png)

De **Verzend** knop wordt alleen door Azure ingeschakeld nadat u ten minste één tag hebt toegepast op elke afbeelding. Druk op **verzenden** om uw werk op te slaan.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Afbeeldingen en selectie kaders labelen voor object detectie

Als uw project van het type ' object Identification (selectie vakjes) ' is, geeft u een of meer selectie vakjes in de afbeelding op en past u een label toe aan het vak. Elke afbeelding kan meerdere selectie kaders bevatten, elk met één tag. Gebruik **gedetailleerde instructies weer geven** om te bepalen of het toevoegen van meerdere selectie kaders geschikt is voor uw project.

1. Selecteer een label voor het selectie kader dat u wilt maken
1. Selecteer het hulp programma voor vier Kante **vakken** ![rechthoekig vak](media/how-to-label-images/rectangular-box-tool.png) of druk op R 
1. Klik en sleep diagonaal in het doel om een ruw begrenzingsvak te maken
    * Het selectie kader aanpassen door te klikken en de randen of hoeken van het vak te slepen

![Scherm opname van eenvoudige selectie kaders maken](media/how-to-label-images/bounding-box-sequence.png)

Als u het selectie kader wilt verwijderen, klikt u op het X-vormig doel dat naast het selectie kader wordt weer gegeven na het maken.

U kunt de tag van een bestaand selectie kader niet opnieuw toewijzen. Als u een fout maakt met het toewijzen van labels, moet u het selectie kader verwijderen en een nieuw item met de juiste tag maken.

Bestaande selectie kaders kunnen standaard worden bewerkt. Met het hulp programma voor **vergren delen/ontgrendelen** ![u het hulp programma voor vergren delen/ontgrendelen](media/how-to-label-images/lock-bounding-boxes-tool.png) of L, wordt dat gedrag gewisseld. Als er regio's zijn vergrendeld, kunt u alleen de vorm of locatie van een nieuw selectie kader wijzigen.

Gebruik het hulp programma voor het **bewerken van regio's** ![regio's](media/how-to-label-images/regions-tool.png) of ' M ' om een bestaand begrenzingsvak aan te passen. U kunt op de randen of de hoeken klikken en slepen om de vorm aan te passen. Als u in de binnenkant klikt, kunt u het hele begrenzingsvak slepen. Als u een regio niet kunt bewerken, hebt u waarschijnlijk het hulp programma voor **vergren delen/ontgrendelen in-** of uitgeschakeld. 

Gebruik het hulp programma voor het maken van een **sjabloon** ![vak voor de sjabloon](media/how-to-label-images/template-box-tool.png) of t om meerdere selectie kaders met dezelfde grootte te creëren. Als de afbeelding geen selectie kaders bevat en u op sjablonen gebaseerde vakken activeert, produceert het hulp programma 50x50 pixels vakjes. Als u een selectie kader hebt gemaakt en vervolgens op een sjabloon gebaseerde vakjes inschakelt, worden nieuwe selectie kaders de grootte van de laatste die u hebt gemaakt. Het formaat van op een sjabloon gebaseerde vakken kunnen worden gewijzigd na plaatsing. Als u het formaat van een op een sjabloon gebaseerd vak wijzigt, wordt alleen het formaat van het desbetreffende vak gewijzigd. 

Als u _alle_ selectie kaders in de huidige afbeelding wilt verwijderen, kunt u het hulp programma **alle regio's verwijderen** ![](media/how-to-label-images/delete-regions-tool.png)regio's verwijderen kiezen. 

Wanneer u de selectie kaders voor de afbeelding hebt gemaakt, drukt u op **verzenden** om uw werk op te slaan. Uw onderhanden werk wordt niet opgeslagen, tenzij u op de knop **verzenden** drukt. 

## <a name="finishing-up"></a>Volt ooien 

Wanneer u een pagina met gelabelde gegevens verzendt, wijst Azure nieuwe niet-gelabelde gegevens uit een werk wachtrij toe. Als er geen niet-gelabelde gegevens meer zijn, wordt er een bericht weer gegeven met dit effect, met een koppeling naar de start pagina van de portal. 

Als u weet dat u nog geen label meer wilt maken, kiest u uw naam in de rechter bovenhoek van de portal voor labels en kiest u **Afmelden**. Als u zich niet afmeldt, wordt er uiteindelijk door Azure gevraagd en worden uw gegevens toegewezen aan een andere Labeler. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [trainen van afbeeldings classificatie modellen in azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Meer informatie over [object detectie met Azure en de snellere R-CNN-techniek](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)