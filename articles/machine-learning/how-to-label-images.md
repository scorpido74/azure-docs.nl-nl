---
title: Afbeeldingen labelen in een label project
title.suffix: Azure Machine Learning
description: Meer informatie over het gebruik van de hulpprogram ma's voor gegevens labelen in een Azure Machine Learning label project.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/09/2020
ms.openlocfilehash: 1a928726665a743cb874a2f8a51ee63fcf64d9ad
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007527"
---
# <a name="tag-images-in-a-labeling-project"></a>Afbeeldingen labelen in een label project

Nadat de Project beheerder [een label project heeft gemaakt](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) in azure machine learning, kunt u het hulp programma labelen (open bare preview) gebruiken om snel gegevens voor een machine learning project voor te bereiden. In dit artikel wordt beschreven:

> [!div class="checklist"]
> * Toegang krijgen tot uw etiket projecten
> * De hulp middelen voor labels
> * De hulpprogram ma's voor specifieke label taken gebruiken

## <a name="prerequisites"></a>Vereisten

* Een [Microsoft-account](https://account.microsoft.com/account) -of Azure Active Directory-account voor de organisatie en het project
* Inzender niveau toegang tot de werk ruimte die het label project bevat.

## <a name="sign-in-to-the-workspace"></a>Aanmelden bij de werk ruimte

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Selecteer het abonnement en de werk ruimte die het label project bevat.  Vraag deze informatie aan bij de Project beheerder.

1. Selecteer **gegevens labelen** aan de linkerkant om het project te zoeken.  

1. Selecteer de project naam in de lijst.

## <a name="understand-the-labeling-task"></a>De taak labelen begrijpen

Wanneer u het project hebt geselecteerd, selecteert u boven aan de pagina **Label gegevens**.

U ziet de instructies die specifiek zijn voor uw project. Hierin wordt uitgelegd welk type gegevens u gebruikt, hoe u uw beslissingen moet nemen en andere relevante informatie. Nadat u deze informatie hebt gelezen, klikt u boven aan de pagina **taken**.  Of Klik onder aan de pagina op **Start labelen**.

## <a name="common-features-of-the-labeling-task"></a>Algemene functies van de label taak

In alle taken voor het labelen van afbeeldingen kiest u een geschikte tag of labels uit een set die is opgegeven door de Project beheerder. U kunt de eerste negen tags selecteren met behulp van de cijfer toetsen op het toetsen bord.  

In installatie kopie classificatie taken kunt u ervoor kiezen om meerdere installatie kopieën tegelijk weer te geven. Gebruik de pictogrammen boven het gebied afbeelding om de indeling te selecteren. 

Als u alle weer gegeven afbeeldingen tegelijk wilt selecteren, gebruikt u **alle selecteren**. Als u afzonderlijke installatie kopieën wilt selecteren, gebruikt u de ronde selectie knop in de rechter bovenhoek van de afbeelding. U moet ten minste één afbeelding selecteren om een tag toe te passen. Als u meerdere installatie kopieën selecteert, worden alle tags die u selecteert, toegepast op alle geselecteerde installatie kopieën.

Hier hebben we een twee-op-twee-indeling gekozen en het label "zoog" toegepast op de installatie kopieën van de beer en Orca. De afbeelding van de haai is al gelabeld als "Cartilaginous vissen" en het Iguana is nog niet gelabeld.

![Indelingen en selecties van meerdere afbeeldingen](./media/how-to-label-images/layouts.png)

> [!Important] 
> Schakel alleen indelingen in als u een nieuwe pagina hebt met niet-gelabelde gegevens. Als u inschakelt, wordt de code ring van de pagina in uitvoering gewist.

In azure wordt de **Verzend** knop ingeschakeld wanneer u alle afbeeldingen op de pagina hebt gelabeld. Selecteer **verzenden** om uw werk op te slaan.

Nadat u labels voor de gegevens hebt verzonden, vernieuwt Azure de pagina met een nieuwe set installatie kopieën uit de werk wachtrij.

### <a name="assisted-machine-learning"></a>Ondersteunde machine learning 

Machine learning-algoritmen kunnen worden geactiveerd. Als deze algoritmen in uw project zijn ingeschakeld, ziet u mogelijk het volgende:

* Nadat een aantal installatie kopieën zijn gelabeld, ziet u mogelijk taken die boven aan het scherm worden **geclusterd** naast de project naam.  Dit betekent dat installatie kopieën samen worden gegroepeerd om Vergelijk bare afbeeldingen op dezelfde pagina te presen teren.  Als dit het geval is, gaat u naar een van de weer gaven met meerdere afbeeldingen om de groepering te benutten.  

* Op een later tijdstip ziet u mogelijk taken met een **Label** naast de project naam.  Afbeeldingen worden weer gegeven met een aanbevolen label dat afkomstig is van een machine learning classificatie model. Geen van de machine learning model heeft een nauw keurigheid van 100%. We gebruiken alleen installatie kopieën waarvoor het model betrouwbaar is, maar deze installatie kopieën zijn mogelijk nog niet goed gelabeld.  Wanneer u deze labels ziet, corrigeert u de verkeerde labels voordat u de pagina verzendt.  

* Voor object detectie modellen ziet u mogelijk selectie vakjes en labels die al aanwezig zijn.  Corrigeer deze fouten voordat u de pagina verzendt.

Met name in een label project is het machine learning model mogelijk alleen nauw keurig genoeg om een kleine subset van afbeeldingen te labelen. Zodra deze afbeeldingen zijn gelabeld, keert het labelen project terug naar hand matige labeling om meer gegevens te verzamelen voor de volgende ronde van model training. In de loop van de tijd zal het model meer vertrouwen hebben over een hoger gedeelte van de installatie kopieën, wat resulteert in meer prelabel-taken verderop in het project.

## <a name="tag-images-for-multi-class-classification"></a>Tag-installatie kopieën voor classificatie met meerdere klassen

Als uw project van het type ' afbeeldings classificatie met meerdere klassen ' is, wijst u één tag toe aan de volledige afbeelding. Als u de instructies op elk gewenst moment wilt bekijken, gaat u naar de pagina **instructies** en selecteert u **gedetailleerde instructies weer geven**.

Als u zich realiseert dat u een fout hebt gemaakt nadat u een label aan een afbeelding hebt toegewezen, kunt u dit probleem oplossen. Selecteer de '**X**' op het label dat wordt weer gegeven onder de afbeelding om het label te wissen. Of selecteer de installatie kopie en kies een andere klasse. Met de zojuist geselecteerde waarde wordt het eerder toegepaste label vervangen.

## <a name="tag-images-for-multi-label-classification"></a>Tag-installatie kopieën voor classificatie met meerdere labels

Als u werkt met een project van het type ' afbeeldings classificatie met meerdere labels ', past u een *of meer* tags toe op een afbeelding. Als u de project-specifieke instructies wilt bekijken, selecteert u **instructies** en gaat u naar **gedetailleerde instructies weer geven**.

Selecteer de afbeelding die u wilt labelen en selecteer vervolgens de tag. Het label wordt toegepast op alle geselecteerde installatie kopieën, waarna de selectie van de installatie kopieën ongedaan wordt gemaakt. Als u meer tags wilt Toep assen, moet u de installatie kopieën opnieuw selecteren. De volgende animatie bevat labels met meerdere labels:

1. **Alles selecteren** wordt gebruikt om de tag "Oceaan" toe te passen.
1. Er is één afbeelding geselecteerd en gelabeld ' Close '.
1. Er zijn drie afbeeldingen geselecteerd en voorzien van het label breed.

![Animatie toont de multilabel stroom](./media/how-to-label-images/multilabel.gif)

Als u een fout wilt corrigeren, klikt u op de "**X**" om een afzonderlijke tag te wissen of selecteert u de afbeeldingen en selecteert u vervolgens het label, waardoor de tag uit de geselecteerde installatie kopieën wordt gewist. Dit scenario wordt hier weer gegeven. Als u op ' land ' klikt, wordt dat label uit de twee geselecteerde installatie kopieën gewist.

![Een scherm opname waarin meerdere selectie vakjes worden weer gegeven](./media/how-to-label-images/multiple-deselection.png)

De **Verzend** knop wordt alleen door Azure ingeschakeld nadat u ten minste één tag hebt toegepast op elke afbeelding. Selecteer **verzenden** om uw werk op te slaan.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Afbeeldingen labelen en selectie kaders voor object detectie opgeven

Als uw project van het type ' object Identification (selectie vakjes) ' is, geeft u een of meer selectie vakjes in de afbeelding op en past u een label toe aan elk vak. Afbeeldingen kunnen meerdere selectie kaders hebben, elk met één tag. Gebruik **gedetailleerde instructies weer geven** om te bepalen of er in uw project meerdere selectie kaders worden gebruikt.

1. Selecteer een label voor het selectie kader dat u wilt maken.
1. Selecteer ![het **gereedschap rechthoekend vak rechthoekig** vak](./media/how-to-label-images/rectangular-box-tool.png) of selecteer R.
3. Klik en sleep diagonaal in het doel om een ruw begrenzingsvak te maken. Als u het selectie kader wilt aanpassen, sleept u de randen of hoeken.

![Een scherm afbeelding toont het maken van eenvoudige selectie vakjes.](./media/how-to-label-images/bounding-box-sequence.png)

Als u een selectie kader wilt verwijderen, klikt u op het X-vormig doel dat naast het selectie kader wordt weer gegeven na het maken.

U kunt de tag van een bestaand selectie kader niet wijzigen. Als u een fout code toewijzing maakt, moet u het selectie kader verwijderen en een nieuwe maken met de juiste tag.

Standaard kunt u bestaande selectie kaders bewerken. Met het hulp programma voor het vergren delen **/ontgrendelen** van de regio's vergren ![delen/ontgrendelen](./media/how-to-label-images/lock-bounding-boxes-tool.png) of "L" wordt dat gedrag gewisseld. Als er regio's zijn vergrendeld, kunt u alleen de vorm of locatie van een nieuw selectie kader wijzigen.

Gebruik het hulp programma](./media/how-to-label-images/regions-tool.png) voor ![het bewerken van regio's **in regio's of** ' M ' om een bestaand begrenzingsvak aan te passen. Sleep de randen of hoeken om de vorm aan te passen. Klik in de binnenkant om het hele begrenzingsvak te kunnen slepen. Als u een regio niet kunt bewerken, hebt u waarschijnlijk het hulp programma voor **vergren delen/ontgrendelen in-** of uitgeschakeld.

Gebruik het hulp programma](./media/how-to-label-images/template-box-tool.png) voor het maken ![van een **sjabloon op basis** van een selectie vakje of T om meerdere selectie kaders met dezelfde grootte. Als de afbeelding geen selectie kaders bevat en u op sjablonen gebaseerde vakken activeert, produceert het hulp programma 50-bij-50-pixels vakken. Als u een selectie kader maakt en vervolgens op sjablonen gebaseerde vakken activeert, worden nieuwe selectie vakjes de grootte van het laatste vak dat u hebt gemaakt. Het formaat van op een sjabloon gebaseerde vakken kunnen worden gewijzigd na plaatsing. Het wijzigen van het formaat van een op een sjabloon gebaseerd vak wijzigt alleen het formaat van het desbetreffende vak.

Als u *alle* selectie vakjes in de huidige afbeelding wilt verwijderen, selecteert u het hulp ![programma](./media/how-to-label-images/delete-regions-tool.png) **alle regio's verwijderen** regio's verwijderen.

Nadat u de selectie kaders voor een afbeelding hebt gemaakt, selecteert u **verzenden** om uw werk op te slaan of wordt de voortgang van uw werk niet opgeslagen.

## <a name="finish-up"></a>Voltooien

Wanneer u een pagina met gelabelde gegevens verzendt, wijst Azure nieuwe niet-gelabelde gegevens toe vanuit een werk wachtrij. Als er geen niet meer gelabelde gegevens beschikbaar zijn, wordt er een bericht weer gegeven met een koppeling naar de start pagina van de portal.

Wanneer u klaar bent met labelen, selecteert u uw naam in de rechter bovenhoek van de portal voor labels en selecteert u vervolgens **Afmelden**. Als u zich niet afmeldt, wordt er uiteindelijk door Azure gevraagd en worden uw gegevens toegewezen aan een andere Labeler.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [trainen van afbeeldings classificatie modellen in azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

