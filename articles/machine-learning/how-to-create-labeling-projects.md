---
title: Een project voor het labelen van gegevens maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en uitvoeren van labelprojecten om gegevens voor machine learning te taggen.  De tools zijn ml assisted labeling, of mens in de lus etikettering om te helpen met de taak.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: d39cf8745c6f53cb11bb12561fd452325fe52ac6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296945"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Een project voor het labelen van gegevens maken en labels exporteren 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Het labelen van omvangrijke gegevens in machine learning-projecten is vaak een hoofdpijn. Projecten met een computerzichtcomponent, zoals beeldclassificatie of objectdetectie, vereisen over het algemeen labels voor duizenden afbeeldingen.
 
[Azure Machine Learning](https://ml.azure.com/) biedt u een centrale plek om etiketteringsprojecten te maken, beheren en bewaken. Gebruik het om gegevens, labels en teamleden te coördineren om labeltaken efficiënt te beheren. Machine Learning ondersteunt beeldclassificatie, meerdere of meerdere klassen, en objectidentificatie met begrensde vakken.

Machine Learning houdt de voortgang bij en onderhoudt de wachtrij met onvolledige labeltaken. Labelers hebben geen Azure-account nodig om deel te nemen. Nadat ze zijn geverifieerd met uw Microsoft-account of [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), kunnen ze zoveel etikettering doen als hun tijd het toelaat.

U start en stopt het project, voegt labelers en teams toe en verwijdert deze en bewaakt de voortgang van de etikettering. U gelabelde gegevens exporteren in coco-indeling of als Azure Machine Learning-gegevensset.

> [!Important]
> Alleen projecten voor beeldclassificatie en objectidentificatielabeling worden momenteel ondersteund. Bovendien moeten de gegevensafbeeldingen beschikbaar zijn in een Azure blob-gegevensarchief. (Als u geen bestaande gegevenswinkel hebt, u afbeeldingen uploaden tijdens het maken van projecten.) 

In dit artikel leer je hoe je:

> [!div class="checklist"]
> * Een project maken
> * De gegevens en structuur van het project opgeven
> * De teams en mensen beheren die aan het project werken
> * Het project uitvoeren en bewaken
> * De labels exporteren


## <a name="prerequisites"></a>Vereisten

* De gegevens die u wilt labelen, in lokale bestanden of in Azure blob-opslag.
* De set labels die u wilt toepassen.
* De instructies voor etikettering.
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree) voordat u begint.
* Een Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Een labelproject maken

Labelingprojecten worden beheerd vanuit Azure Machine Learning. U gebruikt de pagina **Labelingprojecten** om uw projecten en mensen te beheren. Aan een project zijn een of meer teams toegewezen en een team heeft een of meer personen toegewezen.

Als uw gegevens zich al in Azure Blob-opslag bevinden, moet u deze beschikbaar stellen als gegevensarchief voordat u het labelproject maakt. Zie [Gegevenswinkels maken en registreren](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)voor meer informatie.

Als u een project wilt maken, selecteert u **Project toevoegen**. Geef het project een passende naam en selecteer **Labeltaaktype**.

![Wizard Projectcreatie labelen](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Kies **Afbeeldingsclassificatie Multiklasse** voor projecten wanneer u slechts *één klasse* wilt toepassen uit een reeks klassen op een afbeelding.
* Kies **Multilabel Afbeeldingsclassificatie** voor projecten wanneer u *een of meer* labels uit een reeks klassen op een afbeelding wilt toepassen. Bijvoorbeeld, een foto van een hond kan worden gelabeld met zowel *hond* en *overdag*.
* Kies **Objectidentificatie (selectiekader)** voor projecten wanneer u een klasse en een selectiekader wilt toewijzen aan elk object in een afbeelding.

Selecteer **Volgende** wanneer u klaar bent om door te gaan.

## <a name="specify-the-data-to-label"></a>De gegevens opgeven die moeten worden gelabeld

Als u al een gegevensset hebt gemaakt die uw gegevens bevat, selecteert u deze in de vervolgkeuzelijst **Een bestaande gegevensset selecteren.** Of selecteer **Een gegevensset maken** om een bestaande Azure-gegevensarchief te gebruiken of lokale bestanden te uploaden.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Een gegevensset maken vanuit een Azure-gegevensarchief

In veel gevallen is het prima om gewoon lokale bestanden te uploaden. [Maar Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) biedt een snellere en robuustere manier om een grote hoeveelheid gegevens over te dragen. We raden Storage Explorer aan als de standaardmanier om bestanden te verplaatsen.

Ga als eerste op zoek naar een gegevensset op basis van gegevens die u al hebt opgeslagen in Azure Blob-opslag:

1. Selecteer **Een gegevensset maken** > **uit gegevensarchief**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies **Bestand** als **gegevenssettype**.  
1. Selecteer het gegevensarchief.
1. Als uw gegevens zich in een submap in uw blobopslag bevinden, kiest **u Bladeren** om het pad te selecteren.
    * Voeg '/**' toe aan het pad om alle bestanden in submappen van het geselecteerde pad op te nemen.
    * Voeg "**/*.*" toe om alle gegevens in de huidige container en de submappen op te nemen.
1. Geef een beschrijving voor uw gegevensset.
1. Selecteer **Volgende**.
1. Bevestig de details. Selecteer **Terug** om de instellingen te wijzigen of **Maak om** de gegevensset te maken.

> [!NOTE]
> De gegevens die u kiest, worden in uw project geladen.  Als u meer gegevens toevoegt aan het gegevensarchief, wordt deze niet meer weergegeven in dit project zodra het project is gemaakt.  

### <a name="create-a-dataset-from-uploaded-data"></a>Een gegevensset maken op basis van geüploade gegevens

Ga als eerste over het uploaden van uw gegevens:

1. Selecteer **Een gegevensset** > maken**van lokale bestanden**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies 'Bestand' als het **type Gegevensset**.
1. *Optioneel:* Selecteer **Geavanceerde instellingen** om het gegevensarchief, de container en het pad aan te passen aan uw gegevens.
1. Selecteer **Bladeren** om de lokale bestanden te selecteren die u wilt uploaden.
1. Geef een beschrijving van uw gegevensset.
1. Selecteer **Volgende**.
1. Bevestig de details. Selecteer **Terug** om de instellingen te wijzigen of **Maak om** de gegevensset te maken.

De gegevens worden geüpload naar de standaardblobstore ('workspaceblobstore') van uw Machine Learning-werkruimte.

## <a name="specify-label-classes"></a>Labelklassen opgeven

Geef op de pagina **Klassen label** de set klassen op om uw gegevens te categoriseren. Doe dit zorgvuldig, omdat de nauwkeurigheid en snelheid van uw labelers zullen worden beïnvloed door hun vermogen om te kiezen tussen de klassen. Gebruik bijvoorbeeld in plaats van het volledige geslacht en de soorten voor planten of dieren te spellen, een veldcode te gebruiken of het geslacht af te korten.

Voer één label per rij in. Gebruik **+** de knop om een nieuwe rij toe te voegen. Als u meer dan 3 of 4 labels hebt, maar minder dan 10, u de namen vooraf maken met nummers (1:, 2: ) zodat de labelers de nummertoetsen kunnen gebruiken om hun werk te versnellen.

## <a name="describe-the-labeling-task"></a>De etiketteringstaak beschrijven

Het is belangrijk om de etiketteringstaak duidelijk uit te leggen. Op de pagina **Instructies voor labeling** u een koppeling naar een externe site toevoegen voor etiketteringsinstructies of instructies geven in het vak bewerken op de pagina. Houd de instructies taakgericht en geschikt voor het publiek. Overweeg deze vragen:

* Wat zijn de labels die ze zullen zien, en hoe zullen ze kiezen tussen hen? Is er een referentietekst om naar te verwijzen?
* Wat moeten ze doen als geen enkel etiket geschikt lijkt?
* Wat moeten ze doen als meerdere labels geschikt lijken?
* Welke betrouwbaarheidsdrempel moeten ze op een label toepassen? Wil je hun "beste gok" als ze niet zeker zijn?
* Wat moeten ze doen met gedeeltelijk afgesloten of overlappende objecten van belang?
* Wat moeten ze doen als een voorwerp van belang wordt geknipt door de rand van de afbeelding?
* Wat moeten ze doen nadat ze een label hebben ingediend als ze denken dat ze een fout hebben gemaakt?

Voor selectievakken zijn belangrijke vragen:

* Hoe wordt het selectiekader voor deze taak gedefinieerd? Moet het volledig op het interieur van het object, of moet het aan de buitenkant? Moet het zo dicht mogelijk worden bijgesneden, of is enige klaring aanvaardbaar?
* Welk niveau van zorg en consistentie verwacht u van de labelers toe te passen bij het definiëren van grensdozen?
* Hoe label je het object dat gedeeltelijk in de afbeelding wordt weergegeven? 
* Hoe label je het object dat gedeeltelijk bedekt is met een ander object?

>[!NOTE]
> Zorg ervoor dat u er rekening mee dat de labelers in staat zal zijn om de eerste 9 labels te selecteren met behulp van nummertoetsen 1-9.

## <a name="use-ml-assisted-labeling"></a>Gebruik ML assisted labeling

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Met de pagina **MET ML-gelabelde** etikettering u automatische machine learning-modellen activeren om de etiketteringstaak te versnellen. Aan het begin van uw labeling project, worden de beelden geschud in een willekeurige volgorde om potentiële bias te verminderen. Eventuele vooroordelen die aanwezig zijn in de gegevensset worden echter weerspiegeld in het getrainde model. Als bijvoorbeeld 80% van uw afbeeldingen één klasse heeft, is ongeveer 80% van de gegevens die worden gebruikt om het model te trainen van die klasse. Deze training omvat geen actief leren.

Deze functie is beschikbaar voor taken voor beeldclassificatie (multi-class of multi-label).  

Selecteer *ML-gelabelde gelabelde ML inschakelen* en geef een GPU op om ondersteunde etikettering mogelijk te maken, die uit twee fasen bestaat:
* Clustering
* Prelabeling

Het exacte aantal gelabelde afbeeldingen dat nodig is om te beginnen met het labelen is geen vast getal.  Dit kan aanzienlijk verschillen van het ene labelingproject tot het andere. Voor sommige projecten is het soms mogelijk om prelabel- of clustertaken te zien nadat 300 afbeeldingen handmatig zijn gelabeld. ML Assisted Labeling maakt gebruik van een techniek genaamd *Transfer Learning*, die een vooraf opgeleid model gebruikt om het trainingsproces een vliegende start te geven. Als de klassen van uw gegevensset vergelijkbaar zijn met die in het vooraf getrainde model, zijn prelabels mogelijk beschikbaar na slechts een paar honderd handmatig gelabelde afbeeldingen. Als uw gegevensset aanzienlijk verschilt van de gegevens die worden gebruikt om het model vooraf te trainen, kan het veel langer duren.

Aangezien de uiteindelijke labels nog steeds afhankelijk zijn van input van de labeler, wordt deze technologie ook wel *menselijk in de lus* etikettering genoemd.

### <a name="clustering"></a>Clustering

Nadat een bepaald aantal labels is ingediend, begint het machine learning-model vergelijkbare afbeeldingen samen te groeperen.  Deze soortgelijke afbeeldingen worden gepresenteerd aan de labelers op hetzelfde scherm om handmatige tagging te versnellen. Clustering is vooral handig wanneer de labeler een raster van 4, 6 of 9 afbeeldingen bekijkt. 

Zodra een machine learning-model is getraind op uw handmatig gelabelde gegevens, wordt het model afgekapt tot de laatste volledig verbonden laag. Niet-gelabelde afbeeldingen worden vervolgens door het afgekapte model geleid in een proces dat algemeen bekend staat als 'inbedden' of 'featurisatie'. Hiermee wordt elke afbeelding ingesloten in een hoogdimensionale ruimte die wordt gedefinieerd door deze modellaag. Afbeeldingen die het dichtst bij de buren in de ruimte staan, worden gebruikt voor clustertaken. 

### <a name="prelabeling"></a>Prelabeling

Nadat meer afbeeldingslabels zijn ingediend, wordt een classificatiemodel gebruikt om afbeeldingstags te voorspellen.  De labeler ziet nu pagina's die voorspelde labels bevatten die al op elke afbeelding aanwezig zijn.  De taak is vervolgens om deze labels te controleren en eventuele verkeerd gelabelde afbeeldingen te corrigeren voordat u de pagina indient.  

Zodra een machine learning-model is getraind op uw handmatig gelabelde gegevens, wordt het model geëvalueerd op een testset met handmatig gelabelde afbeeldingen om de nauwkeurigheid ervan te bepalen op verschillende betrouwbaarheidsdrempels. Dit evaluatieproces wordt gebruikt om een betrouwbaarheidsdrempel te bepalen waarboven het model nauwkeurig genoeg is om prelabels weer te geven. Het model wordt vervolgens geëvalueerd aan de hand van niet-gelabelde gegevens. Afbeeldingen met meer vertrouwen dan deze drempelwaarde worden gebruikt voor pre-labeling.

> [!NOTE]
> Ml assisted labeling is **alleen** beschikbaar in Werkruimten van enterprise-edities.

## <a name="initialize-the-labeling-project"></a>Initialiseren van het labelingproject

Nadat het etiketteringsproject is geïnitialiseerd, zijn sommige aspecten van het project onveranderlijk. U het taaktype of de gegevensset niet wijzigen. U *kunt* labels en de URL voor de taakbeschrijving wijzigen. Controleer zorgvuldig de instellingen voordat u het project maakt. Nadat u het project hebt ingediend, wordt u teruggestuurd naar de startpagina **van Data Labeling,** waarin het project wordt weergegeven als **Initialiseren.** Deze pagina wordt niet automatisch vernieuwd. Dus, na een pauze, handmatig vernieuwen van de pagina om de status van het project te zien als **Gemaakt**.

## <a name="manage-teams-and-people"></a>Teams en mensen beheren

Standaard krijgt elk labelproject dat u maakt een nieuw team met u als lid. Maar teams kunnen ook worden gedeeld tussen projecten. En projecten kunnen meer dan één team hebben. Als u een team wilt maken, selecteert u **Team toevoegen** op de pagina **Teams.**

Je beheert mensen op de pagina **Personen.** Personen toevoegen en verwijderen via het e-mailadres. Elke labeler moet zich verifiëren via uw Microsoft-account of Azure Active Directory als u deze gebruikt.  

Nadat u een persoon hebt toegevoegd, u die persoon toewijzen aan een of meer teams: Ga naar de pagina **Teams,** selecteer het team en selecteer vervolgens **Personen toewijzen** of **Personen verwijderen.**

Als u een e-mail naar het team wilt verzenden, selecteert u het team dat de pagina **Teamgegevens wilt** bekijken. Selecteer op deze pagina **E-mailteam** om een e-mailconcept te openen met de adressen van iedereen in het team.

## <a name="run-and-monitor-the-project"></a>Het project uitvoeren en bewaken

Nadat u het project hebt geïnitialiseerde, wordt Azure gestart met het uitvoeren van het project. Selecteer het project op de hoofdpagina **voor gegevensetikettering** om naar **Projectdetails**te gaan. Op het tabblad **Dashboard** ziet u de voortgang van de labeltaak.

Op het tabblad **Gegevens** u uw gegevensset bekijken en gelabelde gegevens bekijken. Als u onjuist gelabelde gegevens ziet, selecteert u deze en kiest **u Weigeren**, waarbij de labels worden verwijderd en de gegevens weer in de wachtrij zonder label worden geplaatst.

Gebruik het tabblad **Team** om teams aan het project toe te wijzen of niet los te maken.

Als u het project wilt onderbreken of opnieuw wilt starten, selecteert u de knop**Start** **onderbreken.**/ U alleen gegevens labelen wanneer het project wordt uitgevoerd.

U gegevens rechtstreeks op de pagina **Projectdetails** labelen door **Labelgegevens te**selecteren.

## <a name="add-new-label-class-to-a-project"></a>Nieuwe labelklasse toevoegen aan een project

Tijdens het etiketteringsproces u merken dat extra labels nodig zijn om uw afbeeldingen te classificeren.  U bijvoorbeeld een label 'Onbekend' of 'Ander' toevoegen om verwarrende afbeeldingen aan te geven.

Gebruik deze stappen om een of meer labels aan een project toe te voegen:

1. Selecteer het project op de hoofdpagina **van datalabeling.**
1. Selecteer boven aan de pagina **Onderbreken** om labelers te stoppen bij hun activiteit.
1. Selecteer het tabblad **Details**.
1. Selecteer **Labelklassen**in de lijst aan de linkerkant .
1. Selecteer boven aan de lijst **+ Labels** ![toevoegen Een label toevoegen](media/how-to-create-labeling-projects/add-label.png)
1. Voeg in het formulier uw nieuwe label toe en kies hoe u verder gaat.  Aangezien u de beschikbare labels voor een afbeelding hebt gewijzigd, kiest u hoe u de reeds gelabelde gegevens behandelt:
    * Begin opnieuw en verwijder alle bestaande labels.  Kies deze optie als u vanaf het begin wilt beginnen met labelen met de nieuwe volledige set labels. 
    * Opnieuw beginnen, alle bestaande labels behouden.  Kies deze optie om alle gegevens als niet-gelabeld te markeren, maar bewaar de bestaande labels als standaardtag voor afbeeldingen die eerder zijn gelabeld.
    * Doorgaan, alle bestaande labels bijhouden. Kies deze optie om alle gegevens die al zijn gelabeld te houden zoals het is, en begin met het nieuwe label voor gegevens die nog niet zijn gelabeld.
1. Wijzig de instructiespagina indien nodig voor het nieuwe label(en).
1. Zodra u alle nieuwe labels hebt toegevoegd, selecteert u boven aan de pagina **Start** om het project opnieuw te starten.  

## <a name="export-the-labels"></a>De labels exporteren

U de labelgegevens voor Machine Learning-experimenten op elk gewenst moment exporteren. Afbeeldingslabels kunnen worden geëxporteerd in [COCO-indeling](http://cocodataset.org/#format-data) of als azure machine learning-gegevensset. Gebruik de knop **Exporteren** op de pagina **Projectdetails** van uw labelproject.

Het COCO-bestand wordt gemaakt in de standaardblobstore van de Azure Machine Learning-werkruimte in een map binnen *exporteren/coco*. U hebt toegang tot de geëxporteerde Azure Machine Learning-gegevensset in de sectie **Gegevenssets** van Machine Learning. De pagina met gegevensgegevens bevat ook voorbeeldcode om toegang te krijgen tot uw labels van Python.

![Geëxporteerde gegevensset](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Volgende stappen

* Afbeeldingen labelen voor [beeldclassificatie of objectdetectie](how-to-label-images.md)
* Meer informatie over [Azure Machine Learning en Machine Learning Studio (klassiek)](compare-azure-ml-to-studio-classic.md)
