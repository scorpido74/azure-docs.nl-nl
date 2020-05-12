---
title: Een project voor gegevens label maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en uitvoeren van labels projecten voor het labelen van gegevens voor machine learning.  De hulpprogram ma's bevatten bijzonderings labels of mensen in het lus labelen om te helpen bij de taak.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/09/2020
ms.openlocfilehash: 6a2dd84ec091a2e862dd788a740585827b5cbde1
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007538"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Een gegevens label project maken en labels exporteren 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Het labelen van Voluminous-gegevens in machine learning projecten is vaak een hoofd. Projecten met een computer vision-onderdeel, zoals afbeeldings classificatie of object detectie, hebben doorgaans labels nodig voor duizenden afbeeldingen.
 
[Azure machine learning](https://ml.azure.com/) biedt u een centrale locatie voor het maken, beheren en bewaken van label projecten (open bare preview). Gebruik deze functie om gegevens, labels en team leden te coördineren om op efficiënte wijze label taken te beheren. Machine Learning ondersteunt de classificatie van afbeeldingen, ofwel meerdere labels, meerdere klassen en object-id's met afhankelijke vakken.

Azure Machine Learning volgt de voortgang en onderhoudt de wachtrij met onvolledige label taken.

U kunt het project starten en stoppen en de voortgang van de labeling controleren. U kunt gelabelde gegevens in de COCO-indeling exporteren of als een Azure Machine Learning-gegevensset.

> [!Important]
> Er worden op dit moment alleen afbeeldings classificatie en object identificatie labeling voor projecten ondersteund. Daarnaast moeten de gegevens kopieën beschikbaar zijn in een Azure Blob-gegevens opslag. (Als u geen bestaande gegevens opslag hebt, kunt u afbeeldingen uploaden tijdens het maken van het project.)

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een project maken
> * De gegevens en structuur van het project opgeven
> * Het project uitvoeren en bewaken
> * De labels exporteren


## <a name="prerequisites"></a>Vereisten


* De gegevens die u wilt labelen, hetzij in lokale bestanden of in Azure Blob-opslag.
* De set labels die u wilt Toep assen.
* De instructies voor het labelen.
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werk ruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Een label project maken

Labels van projecten worden beheerd vanuit Azure Machine Learning. U gebruikt de pagina **Label projecten** om uw projecten te beheren.

Als uw gegevens al in Azure Blob-opslag aanwezig zijn, moet u deze beschikbaar maken als een gegevens opslag voordat u het label project maakt. Voor een voor beeld van het gebruik van een gegevens opslag raadpleegt u [zelf studie: uw eerste afbeelding classificatie label project maken](tutorial-labeling.md).

Selecteer **project toevoegen**om een project te maken. Geef het project een passende naam en selecteer het **taak type labelen**.

![Wizard voor het maken van labels](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)


* Kies **afbeeldings classificatie meerdere klassen** voor projecten wanneer u slechts *één klasse* van een reeks klassen wilt Toep assen op een installatie kopie.
* Kies **afbeeldings classificatie meerdere labels** voor projecten wanneer u *een of meer* labels wilt Toep assen van een set klassen naar een afbeelding. Zo kan een foto van een hond worden gelabeld met zowel *honden* als *Daytime*.
* Kies **object identificatie (begrenzingsvak)** voor projecten wanneer u een klasse en een selectie kader wilt toewijzen aan elk object binnen een afbeelding.

Selecteer **volgende** als u klaar bent om door te gaan.

## <a name="specify-the-data-to-label"></a>Geef de gegevens op die u wilt labelen

Als u al een gegevensset hebt gemaakt die uw gegevens bevat, selecteert u deze in de vervolg keuzelijst **een bestaande gegevensset selecteren** . Of selecteer **een gegevensset maken** voor het gebruik van een bestaande Azure-gegevens opslag of voor het uploaden van lokale bestanden.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Een gegevensset maken op basis van een Azure-gegevens opslag

In veel gevallen is het prima om alleen lokale bestanden te uploaden. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) biedt echter een snellere en robuuste manier om grote hoeveel heden gegevens over te dragen. U wordt aangeraden Storage Explorer als de standaard manier om bestanden te verplaatsen.

Een gegevensset maken op basis van gegevens die u al hebt opgeslagen in Azure Blob-opslag:

1. Selecteer **een gegevensset** > maken**uit het gegevens archief**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies **bestand** als **type gegevensset**.  
1. Selecteer de gegevens opslag.
1. Als uw gegevens zich in een submap in uw Blob-opslag bevinden, kiest u **Bladeren** om het pad te selecteren.
    * Voeg "/* *" toe aan het pad om alle bestanden in de submappen van het geselecteerde pad op te kunnen bevatten.
    * Voeg "**/*. *" toe om alle gegevens in de huidige container en de submappen ervan op te halen.
1. Geef een beschrijving op voor de gegevensset.
1. Selecteer **Volgende**.
1. Bevestig de details. Selecteer **vorige** om de instellingen te wijzigen of **maken** om de gegevensset te maken.

> [!NOTE]
> De gegevens die u kiest, worden in uw project geladen.  Het toevoegen van meer gegevens aan het gegevens archief wordt niet weer gegeven in dit project wanneer het project is gemaakt.  

### <a name="create-a-dataset-from-uploaded-data"></a>Een gegevensset maken op basis van geüploade gegevens

Uw gegevens rechtstreeks uploaden:

1. Selecteer **een gegevensset** > maken**op basis van lokale bestanden**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies "bestand" als **type gegevensset**.
1. *Optioneel:* Selecteer **Geavanceerde instellingen** voor het aanpassen van de gegevens opslag, container en het pad naar uw data.
1. Selecteer **Bladeren** om de lokale bestanden te selecteren die u wilt uploaden.
1. Geef een beschrijving op voor de gegevensset.
1. Selecteer **Volgende**.
1. Bevestig de details. Selecteer **vorige** om de instellingen te wijzigen of **maken** om de gegevensset te maken.

De gegevens worden geüpload naar de standaard-BLOB Store ("workspaceblobstore") van uw Machine Learning-werk ruimte.

## <a name="specify-label-classes"></a>Label klassen opgeven

Geef op de pagina **Label klassen** de set klassen op om uw gegevens te categoriseren. Doe dit zorgvuldig omdat de nauw keurigheid en snelheid van uw labelers worden beïnvloed door de mogelijkheid om te kiezen uit de klassen. In plaats van de volledige geslacht en soort voor planten of dieren te spellen, moet u bijvoorbeeld een veld code gebruiken of het genus afkorten.

Voer één label per rij in. Gebruik de **+** knop om een nieuwe rij toe te voegen. Als u meer dan 3 of vier etiketten hebt, maar minder dan 10, wilt u mogelijk de namen met cijfers ("1:", "2:") als voor voegsel toevoegen, zodat de labelers de cijfer toetsen kunnen gebruiken om hun werk te versnellen.

## <a name="describe-the-labeling-task"></a>De taak labelen beschrijven

Het is belang rijk om de label taak duidelijk te verklaren. Op de pagina **Label instructies** kunt u een koppeling naar een externe site toevoegen voor het labelen van instructies of instructies geven in het invoervak op de pagina. Behoud de instructies op de taak gericht en geschikt voor de doel groep. Houd rekening met de volgende vragen:

* Wat zijn de labels die ze zien en hoe worden deze gekozen? Is er een referentie tekst waarnaar kan worden verwezen?
* Wat moet u doen als er geen label geschikt is?
* Wat moet u doen als er meerdere labels relevant zijn?
* Welke betrouwbaarheids drempel waarden moeten worden toegepast op een label? Wilt u de ' beste schatting ' als ze niet zeker zijn?
* Wat moet er gebeuren met gedeeltelijk occluded of overlappende objecten?
* Wat moet er gebeuren als een interessant object wordt geknipt door de rand van de afbeelding?
* Wat moet er gebeuren nadat ze een label hebben ingediend als ze denken dat ze een fout hebben gemaakt?

Voor selectie kaders zijn belang rijke vragen:

* Hoe wordt het begrenzingsvak voor deze taak gedefinieerd? Moet het zich helemaal in de binnenkant van het object bevallen, of moet het zich op de buiten kant bezien? Moet deze zo nauw keurig mogelijk worden bijgesneden of is een bepaalde vrije ruimte acceptabel?
* Welk niveau van zorg en consistentie verwacht u dat de labels worden toegepast bij het definiëren van selectie vakjes?
* Hoe labelt u het object dat gedeeltelijk wordt weer gegeven in de afbeelding? 
* Hoe kan ik het object labelen dat gedeeltelijk onder een ander object valt?

>[!NOTE]
> Houd er rekening mee dat de labelers de eerste 9 Labels kunnen selecteren met behulp van nummer toetsen 1-9.

## <a name="use-ml-assisted-labeling"></a>Met ML gesteunde labels gebruiken

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Op de pagina met het **labelen** met de naam van ml kunt u automatische machine learning modellen activeren om de label taak te versnellen. Aan het begin van het label project worden de afbeeldingen in een wille keurige volg orde gerangschikt om de mogelijke afwijking te verminderen. Eventuele BIASS die aanwezig zijn in de gegevensset, worden echter wel weer gegeven in het getrainde model. Als bijvoorbeeld 80% van uw installatie kopieën van één klasse zijn, is ongeveer 80% van de gegevens die worden gebruikt om het model te trainen, van die klasse. Deze training omvat geen actief onderwijs.

Selecteer *ondersteuning voor het labelen van ml inschakelen* en geef een GPU op om ondersteuning van labeling in te scha kelen. deze bestaat uit twee fasen:
* Clustering
* Prelabelen

Het exacte aantal gelabelde installatie kopieën dat nodig is voor het starten van labeling is geen vast nummer.  Dit kan aanzienlijk verschillen van het ene label project naar het andere. Voor sommige projecten is het soms mogelijk om prelabel-of cluster taken weer te geven nadat 300-installatie kopieën hand matig zijn gelabeld. Bij het labelen van ML wordt gebruikgemaakt van een techniek voor het *leren van overdracht*, die een vooraf getraind model gebruikt om snel te beginnen met het trainings proces. Als de klassen van uw gegevensset vergelijkbaar zijn met die in het vooraf getrainde model, kunnen vooraf labels beschikbaar zijn na slechts een paar honderd hand matig gelabelde afbeeldingen. Als uw gegevensset aanzienlijk afwijkt van de gegevens die worden gebruikt om het model vooraf te trainen, kan het veel langer duren.

Omdat de uiteindelijke labels nog steeds afhankelijk zijn van de invoer van de Labeler, wordt deze technologie ook wel *mensen genoemd in het lus* label.

### <a name="clustering"></a>Clustering

Nadat een bepaald aantal labels is verzonden, begint het machine learning model voor de classificatie van installatie kopieën met gelijksoortige afbeeldingen te groeperen.  Deze vergelijk bare afbeeldingen worden weer gegeven aan de labels op hetzelfde scherm om hand matige labels te versnellen. Clustering is met name handig wanneer de Labeler een raster van 4, 6 of 9 afbeeldingen weergeeft. 

Zodra een machine learning model is getraind op uw hand matig gelabelde gegevens, wordt het model afgekapt tot de laatste volledig verbonden laag. Niet-gelabelde afbeeldingen worden vervolgens door gegeven via het afgekapte model in een proces dat ook wel ' insluiten ' of ' parametrisatie ' wordt genoemd. Hiermee wordt elke afbeelding Inge sloten in een zeer dimensionale ruimte die wordt gedefinieerd door deze laag. Installatie kopieën die zich op de dichtstbijzijnde neighbors in de ruimte bevinden, worden gebruikt voor het clusteren van taken. 

De clustering fase wordt niet weer gegeven voor object detectie modellen.

### <a name="prelabeling"></a>Prelabelen

Wanneer voldoende afbeeldings labels zijn verzonden, wordt een classificatie model gebruikt om afbeeldings Tags te voors pellen. Of een model voor object detectie wordt gebruikt om selectie vakjes te voors pellen. De Labeler ziet nu pagina's die voorspelde labels bevatten die al aanwezig zijn op elke afbeelding. Voor object detectie worden voorspelde vakken ook weer gegeven. De taak wordt vervolgens door deze voor spellingen gecontroleerd en eventuele onjuist gelabelde afbeeldingen te corrigeren voordat u de pagina verzendt.  

Zodra een machine learning model is getraind op uw hand matig gelabelde gegevens, wordt het model geëvalueerd op basis van een testset van hand matig gelabelde afbeeldingen om de nauw keurigheid van verschillende betrouwbaarheids drempels te bepalen. Dit evaluatie proces wordt gebruikt om een betrouwbaarheids drempelwaarde te bepalen waarboven het model nauw keurig genoeg is om vooraf labels weer te geven. Het model wordt vervolgens geëvalueerd op basis van niet-gelabelde gegevens. Afbeeldingen met voor spellingen die meer vertrouwen hebben dan deze drempel waarde, worden gebruikt voor het vooraf labelen.

> [!NOTE]
> De ondersteuning voor het labelen van ML is **alleen** beschikbaar in Enter prise Edition-werk ruimten.

## <a name="initialize-the-labeling-project"></a>Het label project initialiseren

Nadat het label project is geïnitialiseerd, zijn sommige aspecten van het project onveranderbaar. U kunt het taak type of de gegevensset niet wijzigen. U *kunt* labels en de URL voor de taak beschrijving wijzigen. Controleer de instellingen zorgvuldig voordat u het project maakt. Nadat u het project hebt verzonden, keert u terug naar de start pagina van de **gegevens label** , waarmee het project wordt weer gegeven als **bezig met initialiseren**. Deze pagina wordt niet automatisch vernieuwd. Daarom moet u na een onderbreking de pagina hand matig vernieuwen om de status van het project weer te **geven.**

## <a name="run-and-monitor-the-project"></a>Het project uitvoeren en bewaken

Nadat u het project hebt geïnitialiseerd, wordt het met Azure gestart. Selecteer het project op de pagina hoofd **gegevens labelen** om naar **Project Details**te gaan. Op het tabblad **dash board** ziet u de voortgang van de taak labelen.

Op het tabblad **gegevens** kunt u uw gegevensset bekijken en gelabelde gegevens bekijken. Als u onjuist gelabelde gegevens ziet, selecteert u deze en kiest u **weigeren**, waardoor de labels worden verwijderd en de gegevens weer in de niet-gelabelde wachtrij worden geplaatst.

Selecteer de knop**Start** **stoppen**/om het project te onderbreken of opnieuw te starten. U kunt alleen gegevens labelen wanneer het project wordt uitgevoerd.

U kunt gegevens rechtstreeks labelen op de pagina **Project Details** door **Label gegevens**te selecteren.

## <a name="add-new-label-class-to-a-project"></a>Nieuwe klasse Label toevoegen aan een project

Tijdens het labelen is het mogelijk dat er extra labels nodig zijn om uw afbeeldingen te classificeren.  U kunt bijvoorbeeld een label "onbekend" of "Overig" toevoegen om ongemerkte afbeeldingen aan te duiden.

Gebruik deze stappen om een of meer labels aan een project toe te voegen:

1. Selecteer het project op de pagina belangrijkste **gegevens labelen** .
1. Selecteer aan de bovenkant van de pagina **onderbreken** om de labels te stoppen vanuit hun activiteit.
1. Selecteer het tabblad **Details**.
1. Selecteer in de lijst aan de linkerkant **Label klassen**.
1. Selecteer boven aan de lijst **+ labels** ![toevoegen een label toevoegen](media/how-to-create-labeling-projects/add-label.png)
1. Voeg in het formulier het nieuwe label toe en kies hoe u wilt door gaan.  Omdat u de beschik bare labels voor een afbeelding hebt gewijzigd, kiest u hoe u de al gelabelde gegevens wilt behandelen:
    * Opnieuw beginnen, waarbij alle bestaande labels worden verwijderd.  Kies deze optie als u vanaf het begin met de nieuwe volledige set etiketten wilt beginnen met labelen. 
    * Opnieuw beginnen, waarbij alle bestaande labels behouden blijven.  Kies deze optie om alle gegevens als niet-gelabeld te markeren, maar behoud de bestaande labels als een standaard label voor installatie kopieën die eerder zijn gelabeld.
    * Ga door en behoud alle bestaande labels. Kies deze optie om ervoor te zorgen dat alle gegevens al worden gelabeld als is en begin het nieuwe label te gebruiken voor gegevens die nog niet zijn gelabeld.
1. Wijzig uw instructies pagina indien nodig voor de nieuwe label (s).
1. Zodra u alle nieuwe labels hebt toegevoegd, klikt u boven aan de pagina op **Start** om het project opnieuw te starten.  

## <a name="export-the-labels"></a>De labels exporteren

U kunt de label gegevens voor Machine Learning experimenten op elk gewenst moment exporteren. Labels van afbeeldingen kunnen worden geëxporteerd in [Coco-indeling](http://cocodataset.org/#format-data) of als een Azure machine learning-gegevensset. Gebruik de knop **exporteren** op de pagina **Project Details** van het label project.

Het COCO-bestand wordt gemaakt in de standaard-Blob-opslag van de Azure Machine Learning werk ruimte in een map in *export-Coco*. U kunt toegang krijgen tot de geëxporteerde Azure Machine Learning-gegevensset in het gedeelte **gegevens sets** van machine learning. De pagina Details gegevensset biedt ook voorbeeld code voor toegang tot uw labels vanuit Python.

![Geëxporteerde gegevensset](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: uw eerste afbeeldings classificatie label project maken](tutorial-labeling.md).
* Label afbeeldingen voor [afbeeldings classificatie of object detectie](how-to-label-images.md)
* Meer informatie over [Azure machine learning en machine learning Studio (klassiek)](compare-azure-ml-to-studio-classic.md)
