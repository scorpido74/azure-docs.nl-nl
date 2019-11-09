---
title: Labels voor gegevens ophalen
titleSuffix: Azure Machine Learning
description: In dit artikel wordt beschreven hoe u labels projecten maakt en uitvoert om gegevens te markeren voor machine learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837489"
---
# <a name="get-labels-for-data"></a>Labels voor gegevens ophalen

Het labelen van grote hoeveel heden gegevens is vaak een hoofd in machine learning projecten. ML projecten met een computer vision-onderdeel, zoals afbeeldings classificatie of object detectie, vereisen doorgaans duizenden afbeeldingen en bijbehorende labels. 
 
Azure Machine Learning biedt u een centrale locatie voor het maken, beheren en bewaken van label projecten. Het labelen van projecten helpt bij het coördineren van de gegevens, labels en team leden, zodat u de taken voor het label efficiënter kunt beheren. Momenteel ondersteunde taken zijn afbeeldings classificatie, ofwel meerdere labels, meerdere klassen en object-id's die gebruikmaken van begrensde vakken.

Azure traceert de voortgang en onderhoudt de wachtrij met onvolledige label taken. Labels hebben geen Azure-account nodig om deel te nemen. Als ze zijn geverifieerd met hun micro soft-account (MSA) of [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), kunnen ze net zo veel of zo weinig labels krijgen als in hun tijd. Ze kunnen labels toewijzen en wijzigen met behulp van sneltoetsen. 

U kunt het project starten en stoppen, mensen en teams toevoegen en verwijderen, en de voortgang controleren. U kunt gelabelde gegevens exporteren in de COCO-indeling of als een Azure ML-gegevensset. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een project maken
> * De gegevens en structuur van het project opgeven
> * De teams en personen beheren die aan het project werken
> * Het project uitvoeren en bewaken
> * De labels exporteren 

## <a name="prerequisites"></a>Vereisten

* De gegevens die u wilt labelen, hetzij in lokale bestanden of al in azure Storage
* De set labels die u wilt Toep assen
* Instructies voor het labelen
* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)
* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Een label project maken

Labels van projecten worden beheerd vanuit [Azure machine learning](https://ml.azure.com/). Op de pagina **labelen projecten** kunt u uw projecten, teams en personen beheren. Aan een project zijn een of meer teams toegewezen en aan een team zijn een of meer personen toegewezen. 

Als uw gegevens al zijn opgeslagen in Azure Blob-opslag, moet u deze beschikbaar maken als een gegevens opslag voordat u het label project maakt. Zie gegevens [opslag maken en registreren](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores)voor meer informatie. 

Kies **project toevoegen**om een project te maken. Geef het een passende naam en selecteer het **taak type labelen**. 

![Wizard voor het maken van labels](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Kies **afbeeldings classificatie meerdere labels** voor projecten waarin **een _of meer_**  labels van een set klassen kunnen worden toegepast op een afbeelding. Zo kan een foto van een hond worden gelabeld met zowel *hond* als *Daytime*
* Kies **afbeeldings classificatie meerdere klassen** voor projecten waarin slechts **één klasse** van een set klassen kan worden toegepast op een installatie kopie
* Kies **object Identification (begrenzingsvak)** voor projecten waarin de taak een klasse moet toewijzen aan een object binnen een afbeelding, en om een selectie kader op te geven dat het object omheen

Kies **volgende** wanneer u klaar bent om door te gaan.

## <a name="specify-data-to-be-labeled"></a>Geef de gegevens op die moeten worden gelabeld

Als u al een gegevensset hebt gemaakt die uw gegevens bevat, kunt u deze selecteren in de vervolg keuzelijst **een bestaande gegevensset selecteren** . Of kies **een gegevensset maken** om een bestaand Azure-gegevens archief te selecteren of lokale bestanden te uploaden. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Een gegevensset maken op basis van een Azure-gegevens opslag

Hoewel het kiezen van direct uploaden van lokale bestanden prima is voor veel gebruiks gevallen, is [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) zowel robuuster als sneller voor het overdragen van aanzienlijke hoeveel heden gegevens. U wordt aangeraden Azure Storage Explorer als de standaard manier om bestanden te verplaatsen.

Een gegevensset maken op basis van gegevens die u al hebt opgeslagen in Azure Blob-opslag:

1. Kies **een gegevensset maken** en **uit de gegevens opslag** .
1. Een **naam** voor uw gegevensset toewijzen
1. U moet "bestand" kiezen als het **type gegevensset**  
1. Het gegevens archief selecteren 
1. Als uw gegevens zich in een submap van de Blob-opslag bevinden, kiest u **Bladeren** om het pad te selecteren. 
    * U kunt ook `/**` toevoegen na het pad om alle bestanden in de submappen van het geselecteerde pad op te kunnen bevatten
    * `**/*.*` gebruiken voor het toevoegen van alle gegevens in de huidige container en submappen
1. Geef een beschrijving van uw gegevensset op
1. Kies **volgende** 
1. Bevestig de details. U kunt **teruggaan** om de instellingen te wijzigen of kies **maken** om de gegevensset te maken

### <a name="create-a-dataset-by-uploading-data"></a>Een gegevensset maken door gegevens te uploaden

Als u uw gegevens direct wilt uploaden:

1. Kies **een gegevensset maken** en **van lokale bestanden**
1. Een **naam** voor uw gegevensset toewijzen
1. U moet "bestand" kiezen als het **type gegevensset**
1. Als u **Geavanceerde instellingen**kiest, kunt u het gegevens archief, de container en het pad naar uw data aanpassen
1. Kies **Bladeren** om lokale bestanden te selecteren die u wilt uploaden
1. Geef een beschrijving van uw gegevensset op
1. Kies **volgende** 
1. Bevestig de details. U kunt **teruggaan** om de instellingen te wijzigen of kies **maken** om de gegevensset te maken

De gegevens worden geüpload naar de standaard-Blob-opslag (`workspaceblobstore`) van uw Azure ML-werk ruimte.

## <a name="specify-label-classes"></a>Label klassen opgeven

Op de pagina **Label klassen** geeft u de set klassen op die worden gebruikt voor het categoriseren van uw gegevens. Denk aan deze klassen, omdat de nauw keurigheid en snelheid van de labelers worden beïnvloed door de versnelling waarmee ze op de juiste wijze kunnen kiezen. In plaats van de volledige geslacht en soort voor planten of dieren te spellen, is het wellicht beter om veld codes te gebruiken of het genus af te korten. 

Voer één label per rij in met behulp van de knop **+** om een nieuwe rij toe te voegen. Als u meer dan 3 of vier etiketten hebt, maar minder dan 10, kunt u het beste een voor voegsel geven met ' 1: ', ' 2: ', enzovoort. u kunt aan de hand van de cijfer toetsen een aantal sneltoetsen gebruiken om hun werk te versnellen. 

## <a name="describe-the-labeling-task"></a>De taak labelen beschrijven

Het is belang rijk dat u de label taak duidelijk uitspreekt. Op de pagina **instructies voor labelen** kunt u een koppeling maken naar een externe site voor instructies die aan labels moeten worden gepresenteerd. Behoud de instructies op de taak gericht en geschikt voor de doel groep. 

* Wat zijn de labels die ze zien en hoe worden deze gekozen? Is er een referentie tekst waarnaar ze moeten verwijzen?
* Wat moet u doen als er geen label geschikt is? 
* Wat moet u doen als er meerdere labels relevant zijn?
* Welke betrouwbaarheids drempel waarden moeten worden toegepast op een label? Wilt u de ' beste schatting ' als ze niet zeker zijn?
* Wat moet er gebeuren met gedeeltelijk occluded of overlappende objecten?
* Wat moet er gebeuren als een interessant object wordt geknipt door de rand van de afbeelding?
* Wat moeten ze doen als ze van mening zijn dat ze een fout hebben gemaakt bij het verzenden van een afbeelding? 

Met omsluitende kaders zijn andere belang rijke vragen:

* Hoe wordt het begrenzingsvak voor deze taak gedefinieerd? Moeten deze zo nauw keurig mogelijk worden bijgesneden naar het object, of is het een zekere mate van toegestane afstand? 
* Welk niveau van Care en consistentie wilt u verwachten dat de Labeler wordt toegepast voor het definiëren van selectie vakjes?

>[!Note]
> Zorg ervoor dat de Labeler kan kiezen uit de eerste 9 labels met nummer toetsen 1-9. 

## <a name="initialize-the-labeling-project"></a>Het label project initialiseren

Na de initialisatie zijn sommige aspecten van het label project onveranderbaar: u kunt het taak type of de gegevensset niet wijzigen. U mag labels wijzigen en u kunt de URL voor de taak beschrijving wijzigen. Controleer de instellingen zorgvuldig voordat u het project maakt. Zodra u het project hebt verzonden, wordt u teruggeleid naar de start pagina van de **Label** , zodat het project wordt weer gegeven als **initialisatie**. Deze pagina wordt niet automatisch vernieuwd. Als u deze hand matig vernieuwt, wordt het project weer gegeven als **gemaakt**. 

## <a name="manage-teams-and-people"></a>Teams en personen beheren

Een label project krijgt een standaard team en voegt u als een standaardlid toe. Elk label project krijgt een nieuw standaard team, maar teams kunnen tussen projecten worden gedeeld. Projecten kunnen meer dan één team hebben. Het maken van een team doet u door **team toevoegen** te kiezen op de pagina **teams** . 

Personen worden beheerd op de pagina **personen** . U kunt personen toevoegen aan en verwijderen uit hun e-mail adres. Elke Labeler moet worden geverifieerd met behulp van hun micro soft-account of Azure Active Directory als u het gebruikt.  

Zodra u een persoon hebt toegevoegd, kunt u deze toewijzen aan een of meer teams. Ga naar de pagina **teams** , selecteer het specifieke team waarin u geïnteresseerd bent en gebruik vervolgens **personen toewijzen** of **Verwijder mensen** naar wens.

Als u ooit een e-mail naar iedereen in het team wilt verzenden, kunt u dit doen door het team te kiezen om de pagina **Team Details** te openen. Op deze pagina wordt uw e-mail editor door de knop **e-mail team** geopend met de adressen van iedereen in het team.

## <a name="run-and-monitor-the-project"></a>Het project uitvoeren en bewaken

Zodra het project is geïnitialiseerd, wordt het met Azure gestart. Als u op de hoofd pagina voor **labelen** op het project klikt, wordt u naar **Project Details**geleid. Op het tabblad **dash board** ziet u de voortgang van de taak labelen. 

Op het tabblad **gegevens** kunt u uw gegevensset bekijken en gelabelde gegevens bekijken. Als u onjuist gelabelde gegevens ziet, kunt u deze **selecteren** en vervolgens **weigeren**kiezen, waardoor de labels worden verwijderd en de gegevens weer in de niet-gelabelde wachtrij worden geplaatst. 

Op het tabblad **team** kunt u teams aan dit project toewijzen of de toewijzing ervan opheffen. 

Als u het project offline of online wilt halen, kiest u de knop **pause**/**Start** , waarmee u de actieve status van het project kunt in-of uitschakelen.

U kunt gegevens rechtstreeks labelen op de pagina **Project Details** door **Label gegevens**te selecteren. U kunt alleen gegevens labelen wanneer het project wordt uitgevoerd. 

## <a name="export-the-labels"></a>De labels exporteren

U kunt op elk gewenst moment de label gegevens exporteren voor machine learning experimenten. Labels van afbeeldingen kunnen worden geëxporteerd in [Coco-indeling](http://cocodataset.org/#format-data) of als een Azure ml-gegevensset. U vindt de knop **exporteren** op de pagina **Project Details** van het label project.

Het COCO-bestand wordt gemaakt in de standaard-Blob-opslag van de Azure ML-werk ruimte in een map in **export-Coco**. U kunt de geëxporteerde Azure ML-gegevensset openen via de sectie **gegevens sets** van Azure machine learning. De pagina Details van gegevensset biedt ook voorbeeld code voor toegang tot uw labels vanuit Python.

![Geëxporteerde gegevensset](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Volgende stappen

* Label afbeeldingen voor [afbeeldings classificatie of object detectie](how-to-label-images.md)
* Meer informatie over [Azure machine learning en Studio](../compare-azure-ml-to-studio-classic.md)