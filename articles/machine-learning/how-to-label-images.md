---
title: Afbeeldingen taggen in een labelproject
title.suffix: Azure Machine Learning
description: Meer informatie over het gebruik van de hulpprogramma's voor het taggen van gegevens in een Azure Machine Learning-labelingproject.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 2e14b669aadeec4c6a7245be8940051ec604059b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79409555"
---
# <a name="tag-images-in-a-labeling-project"></a>Afbeeldingen taggen in een labelproject

Nadat uw projectbeheerder [een labelproject heeft gemaakt](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) in Azure Machine Learning, u de etiketteringstool gebruiken om snel gegevens voor te bereiden op een Machine Learning-project. In dit artikel wordt beschreven:

> [!div class="checklist"]
> * Toegang krijgen tot uw labelingprojecten
> * De etiketteringsgereedschappen
> * De hulpprogramma's gebruiken voor specifieke etiketteringstaken

## <a name="prerequisites"></a>Vereisten

* De URL van de labelportal voor een lopend project voor het labelen van gegevens
* Een [Microsoft-account](https://account.microsoft.com/account) of een Azure Active Directory-account voor de organisatie en het project

> [!NOTE]
> De projectbeheerder kan de URL van de labelportal vinden op het tabblad **Details** van de pagina **Projectdetails.**

## <a name="sign-in-to-the-projects-labeling-portal"></a>Aanmelden bij de etiketteringsportal van het project

Ga naar de URL van de labelportal die is verstrekt door de projectbeheerder. Meld u aan met het e-mailaccount dat de beheerder heeft gebruikt om u aan het team toe te voegen. Voor de meeste gebruikers is dit uw Microsoft-account. Als het labelproject Azure Active Directory gebruikt, meldt u zich zo aan.

## <a name="understand-the-labeling-task"></a>De etiketteringstaak begrijpen

Nadat u zich hebt aangemeld, ziet u de overzichtspagina van het project.

Ga naar **Gedetailleerde instructies weergeven**. Deze instructies zijn specifiek voor uw project. Ze leggen uit voor welke gegevens u te maken krijgt, hoe u uw beslissingen moet nemen en andere relevante informatie. Nadat u deze informatie hebt gelezen, gaat u terug naar de projectpagina en selecteert **u Labeling starten**.

## <a name="common-features-of-the-labeling-task"></a>Gemeenschappelijke kenmerken van de etiketteringstaak

In alle taken voor het labelen van afbeeldingen kiest u een geschikte tag of tags uit een set die is opgegeven door de projectbeheerder. U de eerste negen tags selecteren met behulp van de nummertoetsen op uw toetsenbord.  

Bij taken voor beeldclassificatie u ervoor kiezen om meerdere afbeeldingen tegelijk weer te geven. Gebruik de pictogrammen boven het afbeeldingsgebied om de indeling te selecteren. Als u alle weergegeven afbeeldingen tegelijkertijd wilt selecteren, gebruikt u **Alle selecteren**. Als u afzonderlijke afbeeldingen wilt selecteren, gebruikt u de knop Cirkelselectie in de rechterbovenhoek van de afbeelding. U moet ten minste één afbeelding selecteren om een tag toe te passen. Als u meerdere afbeeldingen selecteert, wordt elke tag die u selecteert toegepast op alle geselecteerde afbeeldingen.

Hier hebben we gekozen voor een twee-bij-twee lay-out en staan op het punt om de tag "Mammal" toe te passen op de beelden van de beer en orka. Het beeld van de haai was al gelabeld als "Kraakbeenvissen," en de leguaan is nog niet getagd.

![Meerdere afbeeldingsindelingen en selectie](./media/how-to-label-images/layouts.png)

> [!Important] 
> Wissel alleen van lay-out wanneer u een nieuwe pagina met niet-gelabelde gegevens hebt. Als u van indeling wisselt, wordt het werk voor het taggen van de pagina gewist.

Azure schakelt de knop **Verzenden** in wanneer u alle afbeeldingen op de pagina hebt getagd. Selecteer **Verzenden** om uw werk op te slaan.

Nadat u tags voor de gegevens bij de hand hebt ingediend, vernieuwt Azure de pagina met een nieuwe set afbeeldingen uit de werkwachtrij.

### <a name="assisted-machine-learning"></a>Ondersteunde machine learning 

Machine learning-algoritmen kunnen worden geactiveerd tijdens een classificatietaak met meerdere klassen of meerdere labelen. Als deze algoritmen zijn ingeschakeld in uw project, ziet u mogelijk het volgende:

* Nadat een bepaalde hoeveelheid afbeeldingen is gelabeld, ziet u mogelijk Taken die boven aan het scherm **zijn geclusterd** naast de projectnaam.  Dit betekent dat afbeeldingen worden gegroepeerd om vergelijkbare afbeeldingen op dezelfde pagina weer te geven.  Schakel dan over naar een van de meerdere afbeeldingsweergaven om te profiteren van de groepering.  

* Op een later moment ziet u **taken die vooraf zijn gelabeld** naast de projectnaam.  Afbeeldingen worden dan weergegeven met een voorgesteld label dat afkomstig is van een machine learning-classificatiemodel. Geen enkel machine learning-model heeft 100% nauwkeurigheid. Hoewel we alleen afbeeldingen gebruiken waarvan het model zeker is, kunnen deze afbeeldingen nog steeds onjuist vooraf zijn gelabeld.  Wanneer u deze labels ziet, corrigeert u eventuele verkeerde labels voordat u de pagina indient.  

Vooral in het begin van een labelingproject kan het machine learning-model alleen nauwkeurig genoeg zijn om een kleine subset van afbeeldingen vooraf te labelen. Zodra deze afbeeldingen zijn gelabeld, zal het labelingproject terugkeren naar handmatige etikettering om meer gegevens te verzamelen voor de volgende ronde modeltraining. Na verloop van tijd zal het model meer vertrouwen krijgen in een hoger percentage afbeeldingen, wat later in het project resulteert in meer prelabeltaken.

## <a name="tag-images-for-multi-class-classification"></a>Afbeeldingen taggen voor classificatie van meerdere klassen

Als uw project het type 'Afbeeldingsclassificatie multiklasse' is, wijst u één tag toe aan de hele afbeelding. Als u de routebeschrijving op elk gewenst moment wilt bekijken, gaat u naar de pagina **Instructies** en selecteert **u Gedetailleerde instructies weergeven**.

Als u zich realiseert dat u een fout hebt gemaakt nadat u een tag aan een afbeelding hebt toegewezen, u deze herstellen. Selecteer de "**X**" op het label dat onder de afbeelding wordt weergegeven om de tag te wissen. Of selecteer de afbeelding en kies een andere klasse. De nieuw geselecteerde waarde vervangt de eerder toegepaste tag.

## <a name="tag-images-for-multi-label-classification"></a>Afbeeldingen taggen voor classificatie met meerdere labelen

Als u werkt aan een project van het type 'Image Classification Multi-Label', past u een *of meer* tags toe op een afbeelding. Als u de projectspecifieke aanwijzingen wilt bekijken, selecteert u **Instructies** en gaat u naar **Gedetailleerde instructies weergeven**.

Selecteer de afbeelding die u wilt labelen en selecteer vervolgens de tag. De tag wordt toegepast op alle geselecteerde afbeeldingen en vervolgens worden de afbeeldingen uitgeschakeld. Als u meer tags wilt toepassen, moet u de afbeeldingen opnieuw selecteren. In de volgende animatie wordt een labellabel met meerdere labels weergegeven:

1. **Selecteer alles** wordt gebruikt om de tag "Ocean" toe te passen.
1. Eén afbeelding is geselecteerd en gelabeld met 'Close-up'.
1. Drie afbeeldingen zijn geselecteerd en gelabeld "Groothoek."

![Animatie toont de stroom van meerdere labelen](./media/how-to-label-images/multilabel.gif)

Als u een fout wilt corrigeren, klikt u op de "**X**" om een afzonderlijke tag te wissen of de afbeeldingen te selecteren en vervolgens de tag te selecteren, die de tag uit alle geselecteerde afbeeldingen wist. Dit scenario wordt hier getoond. Als u op "Land" klikt, wordt die tag gewist uit de twee geselecteerde afbeeldingen.

![Een schermafbeelding toont meerdere selecties](./media/how-to-label-images/multiple-deselection.png)

Azure schakelt de knop **Verzenden** alleen in nadat u ten minste één tag op elke afbeelding hebt toegepast. Selecteer **Verzenden** om uw werk op te slaan.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Afbeeldingen taggen en selectievakken opgeven voor objectdetectie

Als uw project het type 'Objectidentificatie (selectiekaders) is, geeft u een of meer selectiekaders in de afbeelding op en past u een tag toe op elk vak. Afbeeldingen kunnen meerdere selectievakken hebben, elk met één tag. Gebruik **Gedetailleerde instructies weergeven** om te bepalen of er meerdere selectiekaders in uw project worden gebruikt.

1. Selecteer een tag voor het selectiekader dat u wilt maken.
1. Selecteer het gereedschap ![](./media/how-to-label-images/rectangular-box-tool.png) **Rechthoekig vakje** Rechthoekig gereedschap of selecteer 'R'.
3. Klik en sleep diagonaal over uw doel om een ruw selectiekader te maken. Als u het selectiekader wilt aanpassen, sleept u de randen of hoeken.

![Een screenshot toont het maken van basisselectiekaders.](./media/how-to-label-images/bounding-box-sequence.png)

Als u een selectiekader wilt verwijderen, klikt u op het X-vormige doel dat na het maken naast het selectiekader wordt weergegeven.

U de tag van een bestaand selectiekader niet wijzigen. Als u een tagtoewijzingsfout maakt, moet u het selectiekader verwijderen en een nieuw kader maken met de juiste tag.

Standaard u bestaande selectievakken bewerken. Met het gereedschap ![](./media/how-to-label-images/lock-bounding-boxes-tool.png) **Vergrendel-/ontgrendelen-regio's** schakelt het gereedschap Gebieden vergrendelen/ontgrendelen of 'L' dat gedrag in. Als regio's zijn vergrendeld, u alleen de vorm of locatie van een nieuw selectiekader wijzigen.

Gebruik het ![](./media/how-to-label-images/regions-tool.png) **manipulatiegereedschap Regio's** Met het manipulatiegereedschap Regio's of 'M' om een bestaand selectiekader aan te passen. Sleep de randen of hoeken om de vorm aan te passen. Klik in het interieur om het hele selectiekader te kunnen slepen. Als u een regio niet bewerken, hebt u waarschijnlijk het gereedschap **Regio's vergrendelen/ontgrendelen** ingeschakeld.

Gebruik het gereedschap Sjabloon ![op basis](./media/how-to-label-images/template-box-tool.png) **van gereedschap** Sjabloon-vak of 'T' om meerdere selectiekaders van dezelfde grootte te maken. Als de afbeelding geen selectiekaders heeft en u op sjablonen gebaseerde vakken activeert, produceert het hulpprogramma dozen van 50 bij 50 pixels. Als u een selectiekader maakt en vervolgens op sjablonen gebaseerde vakken activeert, zijn alle nieuwe selectiekaders de grootte van het laatste vak dat u hebt gemaakt. Op sjablonen gebaseerde vakken kunnen na plaatsing worden aangepast. Het formaat van een op een sjabloon gebaseerd vak wijzigen, wijzigt alleen het formaat van dat specifieke vak.

Als u *alle* selectiekaders in de huidige afbeelding ![wilt](./media/how-to-label-images/delete-regions-tool.png)verwijderen, selecteert u het gereedschap Regio's verwijderen in **alle regio's** .

Nadat u de selectiekaders voor een afbeelding hebt gemaakt, selecteert u **Verzenden** om uw werk op te slaan of wordt uw werk in uitvoering niet opgeslagen.

## <a name="finish-up"></a>Voltooien

Wanneer u een pagina met gelabelde gegevens verzendt, wijst Azure u nieuwe niet-gelabelde gegevens toe vanuit een werkwachtrij. Als er geen niet-gelabelde gegevens meer beschikbaar zijn, krijgt u een bericht waarin dit wordt vermeld, samen met een koppeling naar de startpagina van de portal.

Wanneer u klaar bent met labelen, selecteert u uw naam in de rechterbovenhoek van de labelportal en selecteert u **afmelden**. Als u zich niet afmeldt, wordt u uiteindelijk "time-out" en wordt uw gegevens toegewezen aan een andere labeler.

## <a name="next-steps"></a>Volgende stappen

* Informatie over het trainen van [classificatiemodellen in Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

