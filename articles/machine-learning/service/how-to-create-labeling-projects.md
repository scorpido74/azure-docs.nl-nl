---
title: Een project voor gegevens label maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en uitvoeren van labels projecten voor het labelen van gegevens voor machine learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: bdedc3ee0c76eafc4b4bc95116fb5be846f4bee5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978233"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Een gegevens label project maken en labels exporteren 

Het labelen van Voluminous-gegevens in machine learning projecten is vaak een hoofd. Projecten met een computer vision-onderdeel, zoals afbeeldings classificatie of object detectie, hebben doorgaans labels nodig voor duizenden afbeeldingen.
 
[Azure machine learning](https://ml.azure.com/) biedt u een centrale locatie voor het maken, beheren en bewaken van etiket projecten. Gebruik deze functie om gegevens, labels en team leden te coördineren om op efficiënte wijze label taken te beheren. Machine Learning ondersteunt de classificatie van afbeeldingen, ofwel meerdere labels, meerdere klassen en object-id's samen met gebonden vakken.

Machine Learning volgt de voortgang en onderhoudt de wachtrij met onvolledige label taken. Labels hebben geen Azure-account nodig om deel te nemen. Nadat ze zijn geverifieerd met uw Microsoft-account of [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), kunnen ze net zo veel labels krijgen als in hun tijd.

In Machine Learning kunt u het project starten en stoppen, mensen en teams toevoegen en verwijderen en de voortgang controleren. U kunt gelabelde gegevens in de COCO-indeling exporteren of als een Azure Machine Learning-gegevensset.

> [!Important]
> Er worden op dit moment alleen afbeeldings classificatie en object identificatie labeling voor projecten ondersteund. Daarnaast moeten de gegevens kopieën beschikbaar zijn in een Azure Blob-gegevens opslag. (Als u geen bestaande gegevens opslag hebt, kunt u afbeeldingen uploaden tijdens het maken van het project.) 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een project maken
> * De gegevens en structuur van het project opgeven
> * De teams en personen beheren die aan het project werken
> * Het project uitvoeren en bewaken
> * De labels exporteren


## <a name="prerequisites"></a>Vereisten

* De gegevens die u wilt labelen, hetzij in lokale bestanden of in azure Storage.
* De set labels die u wilt Toep assen.
* De instructies voor het labelen.
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werk ruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Een label project maken

Labels van projecten worden beheerd vanuit Azure Machine Learning. U gebruikt de pagina **Label projecten** om uw projecten en personen te beheren. Aan een project zijn een of meer teams toegewezen en aan een team zijn een of meer personen toegewezen.

Als uw gegevens al in Azure Blob-opslag aanwezig zijn, moet u deze beschikbaar maken als een gegevens opslag voordat u het label project maakt. Zie [gegevens opslag maken en registreren](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores)voor meer informatie.

Selecteer **project toevoegen**om een project te maken. Geef het project een passende naam en selecteer het **taak type labelen**.

![Wizard voor het maken van labels](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Kies **afbeeldings classificatie meerdere labels** voor projecten wanneer u *een of meer* labels wilt Toep assen van een set klassen naar een afbeelding. Zo kan een foto van een hond worden gelabeld met zowel *honden* als *Daytime*.
* Kies **afbeeldings classificatie meerdere klassen** voor projecten wanneer u slechts *één klasse* van een reeks klassen wilt Toep assen op een installatie kopie.
* Kies **object identificatie (begrenzingsvak)** voor projecten wanneer u een klasse en een selectie kader wilt toewijzen aan elk object binnen een afbeelding.

Selecteer **volgende** als u klaar bent om door te gaan.

## <a name="specify-the-data-to-label"></a>Geef de gegevens op die u wilt labelen

Als u al een gegevensset hebt gemaakt die uw gegevens bevat, selecteert u deze in de vervolg keuzelijst **een bestaande gegevensset selecteren** . Of selecteer **een gegevensset maken** voor het gebruik van een bestaande Azure-gegevens opslag of voor het uploaden van lokale bestanden.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Een gegevensset maken op basis van een Azure-gegevens opslag

In veel gevallen is het prima om alleen lokale bestanden te uploaden. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) biedt echter een snellere en robuuste manier om grote hoeveel heden gegevens over te dragen. U wordt aangeraden Storage Explorer als de standaard manier om bestanden te verplaatsen.

Een gegevensset maken op basis van gegevens die u al hebt opgeslagen in Azure Blob-opslag:

1. Selecteer **een gegevensset maken** > **uit gegevens opslag**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies **bestand** als **type gegevensset**.  
1. Selecteer de gegevens opslag.
1. Als uw gegevens zich in een submap in uw Blob-opslag bevinden, kiest u **Bladeren** om het pad te selecteren.
    * Voeg "/* *" toe aan het pad om alle bestanden in de submappen van het geselecteerde pad op te kunnen bevatten.
    * Voeg "* */* . *" toe om alle gegevens in de huidige container en de submappen ervan op te halen.
1. Geef een beschrijving op voor de gegevensset.
1. Selecteer **Next**.
1. Bevestig de details. Selecteer **vorige** om de instellingen te wijzigen of **maken** om de gegevensset te maken.

### <a name="create-a-dataset-from-uploaded-data"></a>Een gegevensset maken op basis van geüploade gegevens

Uw gegevens rechtstreeks uploaden:

1. Selecteer **een gegevensset maken** > **van lokale bestanden**.
1. Wijs een **naam** toe aan uw gegevensset.
1. Kies "bestand" als **type gegevensset**.
1. *Optioneel:* Selecteer **Geavanceerde instellingen** voor het aanpassen van de gegevens opslag, container en het pad naar uw data.
1. Selecteer **Bladeren** om de lokale bestanden te selecteren die u wilt uploaden.
1. Geef een beschrijving op voor de gegevensset.
1. Selecteer **Next**.
1. Bevestig de details. Selecteer **vorige** om de instellingen te wijzigen of **maken** om de gegevensset te maken.

De gegevens worden geüpload naar de standaard-BLOB Store ("workspaceblobstore") van uw Machine Learning-werk ruimte.

## <a name="specify-label-classes"></a>Label klassen opgeven

Geef op de pagina **Label klassen** de set klassen op om uw gegevens te categoriseren. Doe dit zorgvuldig omdat de nauw keurigheid en snelheid van uw labelers worden beïnvloed door de mogelijkheid om te kiezen uit de klassen. In plaats van de volledige geslacht en soort voor planten of dieren te spellen, moet u bijvoorbeeld een veld code gebruiken of het genus afkorten.

Voer één label per rij in. Gebruik de knop **+** om een nieuwe rij toe te voegen. Als u meer dan 3 of vier etiketten hebt, maar minder dan 10, wilt u mogelijk de namen met cijfers ("1:", "2:") als voor voegsel toevoegen, zodat de labelers de cijfer toetsen kunnen gebruiken om hun werk te versnellen.

## <a name="describe-the-labeling-task"></a>De taak labelen beschrijven

Het is belang rijk om de label taak duidelijk te verklaren. Op de pagina **Label instructies** kunt u een koppeling naar een externe site toevoegen voor het labelen van instructies. Behoud de instructies op de taak gericht en geschikt voor de doel groep. Houd rekening met de volgende vragen:

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

>[!NOTE]
> Houd er rekening mee dat de labelers de eerste 9 Labels kunnen selecteren met behulp van nummer toetsen 1-9.

## <a name="initialize-the-labeling-project"></a>Het label project initialiseren

Nadat het label project is geïnitialiseerd, zijn sommige aspecten van het project onveranderbaar. U kunt het taak type of de gegevensset niet wijzigen. U *kunt* labels en de URL voor de taak beschrijving wijzigen. Controleer de instellingen zorgvuldig voordat u het project maakt. Nadat u het project hebt verzonden, keert u terug naar de start pagina van de **Label** , waarmee het project als **initialisatie**wordt weer gegeven. Deze pagina wordt niet automatisch vernieuwd. Daarom moet u na een onderbreking de pagina hand matig vernieuwen om de status van het project weer te **geven.**

## <a name="manage-teams-and-people"></a>Teams en personen beheren

Elk label project dat u maakt, krijgt standaard een nieuw team met u als lid. Teams kunnen ook tussen projecten worden gedeeld. En projecten kunnen meer dan één team hebben. Als u een team wilt maken, selecteert u **team toevoegen** op de pagina **teams** .

U beheert personen op de pagina **personen** . Personen toevoegen en verwijderen op e-mail adres. Elke Labeler moet worden geverifieerd via uw Microsoft-account of Azure Active Directory als u deze gebruikt.  

Nadat u een persoon hebt toegevoegd, kunt u die persoon toewijzen aan een of meer teams: Ga naar de pagina **teams** , selecteer het team en selecteer vervolgens **personen toewijzen** of **personen verwijderen**.

Als u een e-mail bericht wilt verzenden naar het team, selecteert u het team om de pagina **Team Details** weer te geven. Selecteer op deze pagina **e-mail team** om een e-mail concept te openen met de adressen van iedereen in het team.

## <a name="run-and-monitor-the-project"></a>Het project uitvoeren en bewaken

Nadat u het project hebt geïnitialiseerd, wordt het met Azure gestart. Selecteer het project op de hoofd pagina **labelen** om naar **Project Details**te gaan. Op het tabblad **dash board** ziet u de voortgang van de taak labelen.

Op het tabblad **gegevens** kunt u uw gegevensset bekijken en gelabelde gegevens bekijken. Als u onjuist gelabelde gegevens ziet, selecteert u deze en kiest u **weigeren**, waardoor de labels worden verwijderd en de gegevens weer in de niet-gelabelde wachtrij worden geplaatst.

Gebruik het tabblad **team** om teams toe te wijzen of te verwijderen aan het project.

Selecteer de knop **pause**/**Start** om het project te onderbreken of opnieuw te starten. U kunt alleen gegevens labelen wanneer het project wordt uitgevoerd.

U kunt gegevens rechtstreeks labelen op de pagina **Project Details** door **Label gegevens**te selecteren.

## <a name="export-the-labels"></a>De labels exporteren

U kunt de label gegevens voor Machine Learning experimenten op elk gewenst moment exporteren. Labels van afbeeldingen kunnen worden geëxporteerd in [Coco-indeling](http://cocodataset.org/#format-data) of als een Azure machine learning-gegevensset. Gebruik de knop **exporteren** op de pagina **Project Details** van het label project.

Het COCO-bestand wordt gemaakt in de standaard-Blob-opslag van de Azure Machine Learning werk ruimte in een map in *export-Coco*. U kunt toegang krijgen tot de geëxporteerde Azure Machine Learning-gegevensset in het gedeelte **gegevens sets** van machine learning. De pagina Details gegevensset biedt ook voorbeeld code voor toegang tot uw labels vanuit Python.

![Geëxporteerde gegevensset](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Volgende stappen

* Label afbeeldingen voor [afbeeldings classificatie of object detectie](how-to-label-images.md)
* Meer informatie over [Azure machine learning en machine learning Studio (klassiek)](../compare-azure-ml-to-studio-classic.md)
