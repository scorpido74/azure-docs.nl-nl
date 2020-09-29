---
title: Hoe u Jupyter-notebooks uitvoert in uw werkruimte
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van een Jupyter Notebook zonder uw werk ruimte te verlaten in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: 46435ef773e90234538bb755e20035990bbf1066
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460030"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Hoe u Jupyter-notebooks uitvoert in uw werkruimte


Meer informatie over het rechtstreeks uitvoeren van uw Jupyter-notebook in uw werk ruimte in Azure Machine Learning Studio. Hoewel u [Jupyter](https://jupyter.org/) of [jjupyterlab](https://jupyterlab.readthedocs.io)kunt starten, kunt u uw notitie blokken ook bewerken en uitvoeren zonder de werk ruimte te verlaten.

Bekijk hoe u het volgende kunt doen:

* Jupyter-notitie blokken maken in uw werk ruimte
* Een experiment uitvoeren vanuit een notebook
* De notitieblok omgeving wijzigen
* Details vinden van de reken instanties die worden gebruikt voor het uitvoeren van uw notitie blokken

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Notitie blokken maken

Maak in uw Azure Machine Learning-werk ruimte een nieuw Jupyter-notitie blok en ga aan de slag. Het zojuist gemaakte notitie blok wordt opgeslagen in de standaard werkruimte opslag. Dit notitie blok kan worden gedeeld met iedereen die toegang heeft tot de werk ruimte. 

Een nieuw notitie blok maken: 

1. Open uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).
1. Selecteer aan de linkerkant **notitie blokken**. 
1. Selecteer het pictogram  **nieuw bestand maken** boven de lijst **gebruikers bestanden** in de sectie **mijn bestanden** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Nieuw bestand maken":::

1. Geef het bestand een naam. 
1. Voor Jupyter Notebook-bestanden selecteert u **notebook** als het bestands type.
1. Selecteer een bestands directory.
1. Selecteer **Maken**.

U kunt ook tekst bestanden maken.  Selecteer **tekst** als het bestands type en voeg de extensie toe aan de naam (bijvoorbeeld myfile.py of myfile.txt)  

U kunt ook mappen en bestanden, met inbegrip van notitie blokken, uploaden met de hulp middelen boven aan de pagina met notitie blokken.  De notitie blokken en de meeste tekst bestands typen worden weer gegeven in de sectie voor beeld.  Er is geen voor beeld beschikbaar voor de meeste andere bestands typen.

> [!IMPORTANT]
> Inhoud in notitie blokken en scripts kan mogelijk gegevens van uw sessies lezen en toegang krijgen tot gegevens zonder uw organisatie in Azure.  Laad alleen bestanden van vertrouwde bronnen. Zie [Aanbevolen procedures voor veilige code](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)voor meer informatie.

### <a name="clone-samples"></a>Voor beelden klonen

Uw werk ruimte bevat een map met voor **beelden** met notebooks die zijn ontworpen om u te helpen de SDK te verkennen en als voor beeld voor uw eigen machine learning projecten te gebruiken.  U kunt deze notitie blokken klonen in uw eigen map in uw opslag container voor werk ruimten.  

Zie [zelf studie: uw eerste ml-experiment maken](tutorial-1st-experiment-sdk-setup.md#azure)voor een voor beeld.

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Bestanden van Git en versie van mijn bestanden gebruiken

U kunt toegang krijgen tot alle Git-bewerkingen via een Terminal venster. Alle Git-bestanden en-mappen worden opgeslagen in het bestands systeem van de werk ruimte.

> [!NOTE]
> Voeg uw bestanden en mappen toe aan de map **~/cloudfiles/code/users** , zodat deze in al uw Jupyter-omgevingen zichtbaar zijn.

Voor toegang tot de terminal:

1. Open uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).
1. Selecteer aan de linkerkant **notitie blokken**.
1. Selecteer een notitie blok dat zich in de sectie **gebruikers bestanden** aan de linkerkant bevindt.  Als u nog geen notitie blokken hebt, maakt u eerst [een notitie blok](#create)
1. Selecteer een **Compute** -doel of maak een nieuw en wacht tot het wordt uitgevoerd.
1. Selecteer het pictogram **Open Terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Nieuw bestand maken":::

1. Als u het pictogram niet ziet, selecteert u de **..** . rechts van het berekenings doel en selecteert u vervolgens **Terminal openen**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Nieuw bestand maken":::


Meer informatie over [het klonen van Git-opslag plaatsen in het bestands systeem van de werk ruimte](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Kopiëren en plakken in Terminal

> * Windows: `Ctrl-Insert` kopiëren en gebruiken `Ctrl-Shift-v` of `Shift-Insert` Plakken.
> * Mac OS: `Cmd-c` om te kopiëren en `Cmd-v` te plakken.
> * FireFox/IE ondersteunt mogelijk geen juiste Klembord machtigingen.

### <a name="share-notebooks-and-other-files"></a>Notitie blokken en andere bestanden delen

Kopieer en plak de URL om een notitie blok of bestand te delen.  Alleen andere gebruikers van de werk ruimte hebben toegang tot deze URL.  Meer informatie over [het verlenen van toegang tot uw werk ruimte](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Een notitie blok bewerken

Als u een notitie blok wilt bewerken, opent u een notitie blok dat zich bevindt in het gedeelte **gebruikers bestanden** van uw werk ruimte. Klik op de cel die u wilt bewerken. 

U kunt het notitie blok bewerken zonder verbinding te maken met een reken instantie.  Wanneer u de cellen in het notitie blok wilt uitvoeren, selecteert of maakt u een reken instantie.  Als u een gestopt Compute-exemplaar selecteert, wordt het automatisch gestart wanneer u de eerste cel uitvoert.

Wanneer een reken instantie wordt uitgevoerd, kunt u ook de voltooiing van code gebruiken, mogelijk gemaakt door [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), in een python-notebook.

U kunt Jupyter of Jjupyterlab ook starten via de werk balk van het notitie blok.  Azure Machine Learning biedt geen updates en corrigeert fouten van Jupyter of Jjupyterlab, omdat deze open source-producten zijn buiten de grenzen van Microsoft Ondersteuning.

### <a name="use-intellisense"></a>IntelliSense gebruiken

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) is een hulp programma voor het volt ooien van de code die een aantal functies omvat: lijst leden, parameter info, snelle informatie en volledig woord. Deze functies helpen u meer te weten te komen over de code die u gebruikt, het bijhouden van de para meters die u typt en het toevoegen van aanroepen aan eigenschappen en methoden met slechts enkele toetsaanslagen.  

Wanneer u code typt, gebruikt u Ctrl + spatie om IntelliSense te activeren.

### <a name="clean-your-notebook-preview"></a>Uw notitie blok opschonen (preview-versie)

> [!IMPORTANT]
> De functie verzamelen is momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Tijdens het maken van een notitie blok gaat u meestal naar cellen die u hebt gebruikt voor het verkennen van gegevens of het opsporen van fouten. De functie *verzamelen* helpt u bij het produceren van een schone notebook zonder deze vreemde cellen.

1. Voer alle notebook-cellen uit.
1. Selecteer de cel met de code die u voor het nieuwe notitie blok wilt uitvoeren. Bijvoorbeeld de code die een experiment verzendt of de code die een model registreert.
1. Selecteer het pictogram voor **verzamelen** dat wordt weer gegeven op de werk balk van de cel.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Nieuw bestand maken":::
1. Voer de naam in voor het nieuwe ' verzamelde ' notitie blok.  

Het nieuwe notitie blok bevat alleen code cellen, waarbij alle cellen zijn vereist voor het produceren van dezelfde resultaten als de cel die u hebt geselecteerd voor het verzamelen van gegevens.

### <a name="save-and-checkpoint-a-notebook"></a>Een notitie blok opslaan en controle punten

Azure Machine Learning een controlepunt bestand maakt wanneer u een *ipynb*-   bestand maakt.

Selecteer in de werk balk notebook het menu en vervolgens **bestand &gt; opslaan en controle punt** om het notitie blok hand matig op te slaan. er wordt dan een controlepunt bestand toegevoegd dat is gekoppeld aan het notitie blok.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Nieuw bestand maken":::

Elk notitie blok wordt elke 30 seconden autobespaard.Met automatisch opslaan worden alleen het eerste *ipynb*   -bestand, niet het controlepunt bestand, bijgewerkt.
 
Selecteer **controle punten** in het menu van het notitie blok om een benoemd controle punt te maken en de notitie blok terug te zetten naar een opgeslagen controle punt.


### <a name="useful-keyboard-shortcuts"></a>Nuttige sneltoetsen

|Toetsenbord  |Bewerking  |
|---------|---------|
|SHIFT + ENTER     |  Een cel uitvoeren       |
|Ctrl + spatie | IntelliSense activeren |
|CTRL + M (Windows)     |  Tabblad overvulling in notebook in-of uitschakelen.       |
|CTRL + SHIFT + M (Mac & Linux)     |    Tabblad overvulling in notebook in-of uitschakelen.     |
|Tabblad (wanneer tab-trap is ingeschakeld) | Een ' \t '-teken toevoegen (inspringing)
|Tabblad (als tab-trap is uitgeschakeld) | De focus wijzigen naar het volgende focusable item (de knop cel verwijderen, de knop uitvoeren, enzovoort)

## <a name="delete-a-notebook"></a>Een notebook verwijderen 

U *kunt* de voor **beelden** van notitie blokken niet verwijderen.  Deze notitie blokken maken deel uit van de studio en worden bijgewerkt telkens wanneer een nieuwe SDK wordt gepubliceerd.  

U *kunt* notitie blokken met **gebruikers bestanden** op een van de volgende manieren verwijderen:

* Selecteer in de Studio de **..** . aan het einde van een map of bestand.  Zorg ervoor dat u een ondersteunde browser (micro soft Edge, Chrome of Firefox) gebruikt.
* Selecteer op elke werk balk van een notitie blok de optie [**Terminal openen**](#terminal)  om toegang te krijgen tot het Terminal venster voor het reken exemplaar.
* In Jupyter of Jjupyterlab met hun hulp middelen.

## <a name="run-an-experiment"></a>Een experiment uitvoeren

Als u een experiment wilt uitvoeren vanuit een notebook, maakt u eerst verbinding met een actief [reken exemplaar](concept-compute-instance.md). Als u geen reken instantie hebt, gebruikt u deze stappen om er een te maken: 

1. Selecteer **+** in de werk balk van het notitie blok. 
2. Geef de berekening een naam en kies een grootte voor de **virtuele machine**. 
3. Selecteer **Maken**.
4. Het reken exemplaar is automatisch verbonden met het notitie blok en u kunt nu uw cellen uitvoeren.

Alleen u kunt de compute-instanties zien en gebruiken die u maakt.  Uw **gebruikers bestanden** worden afzonderlijk van de virtuele machine opgeslagen en worden gedeeld door alle reken instanties in de werk ruimte.

### <a name="view-logs-and-output"></a>Logboeken en uitvoer weer geven

Gebruik [notebook widgets](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) om de voortgang van de uitvoering en logboeken weer te geven. Een widget is asynchroon en levert updates totdat de training is voltooid. Azure Machine Learning widgets worden ook ondersteund in Jupyter en JupterLab.

## <a name="change-the-notebook-environment"></a>De notitieblok omgeving wijzigen

Op de werk balk van notitie blokken kunt u de omgeving wijzigen waarop uw notitie blok wordt uitgevoerd.  

Met deze acties worden de status van het notitie blok of de waarden van variabelen in het notitie blok niet gewijzigd:

|Bewerking  |Resultaat  |
|---------|---------| --------|
|De kernel stoppen     |  Stopt elke actieve cel. Als u een cel uitvoert, wordt de kernel automatisch opnieuw gestart. |
|Naar een andere sectie van de werk ruimte navigeren     |     Actieve cellen worden gestopt. |

Met deze acties wordt de status van het notitie blok opnieuw ingesteld en worden alle variabelen in het notitie blok opnieuw ingesteld.

|Bewerking  |Resultaat  |
|---------|---------| --------|
| De kernel wijzigen | Notebook maakt gebruik van nieuwe kernel |
| Scha kelen tussen compute    |     In de notitie blok wordt automatisch de nieuwe Compute gebruikt. |
| Berekening opnieuw instellen | Start opnieuw wanneer u een cel probeert uit te voeren |
| Compute stoppen     |    Er worden geen cellen uitgevoerd  |
| Notitie blok openen in Jupyter of Jjupyterlab     |    Notitie blok geopend op een nieuw tabblad.  |

### <a name="add-new-kernels"></a>Nieuwe kernels toevoegen

Het notitie blok vindt automatisch alle Jupyter-kernels die zijn geïnstalleerd op het verbonden Compute-exemplaar.  Een kernel toevoegen aan het reken exemplaar:

1. Selecteer [**Terminal openen**](#terminal) op de werk balk van het notitie blok.
1. Gebruik het Terminal venster om een nieuwe omgeving te maken.  De onderstaande code maakt bijvoorbeeld `newenv` :
    ```shell
    conda create --name newenv
    ```
1. Activeer de omgeving.  Bijvoorbeeld na het maken van `newenv` :

    ```shell
    conda activate newenv
    ```
1. PIP-en ipykernel-pakket installeren in de nieuwe omgeving en een kernel maken voor die Conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Een van de [beschik bare Jupyter-kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kan worden geïnstalleerd.

### <a name="status-indicators"></a>Status indicatoren

Een indicator naast de vervolg keuzelijst voor **berekeningen** toont de status.  De status wordt ook weer gegeven in de vervolg keuzelijst zelf.  

|Kleur |Compute-status |
|---------|---------| 
| Green | Compute running |
| Red |Kan niet berekenen | 
| Zwart | Berekenen gestopt |
|  Licht blauw |Berekenen maken, starten, opnieuw starten, instellen |
|  Grijs |Berekening verwijderen, stoppen |

Een indicator naast de vervolg keuzelijst **kernel** toont de status.

|Kleur |Kernel-status |
|---------|---------|
|  Green |Kernel verbonden, niet-actief, bezet|
|  Grijs |Kernel niet verbonden |

## <a name="find-compute-details"></a>Berekenings details zoeken 

Meer informatie over de compute-exemplaren vindt u in [Studio](https://ml.azure.com)op de **Compute** -pagina.

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste experiment uitvoeren](tutorial-1st-experiment-sdk-train.md)
* [Back-up van de bestands opslag maken met moment opnamen](../storage/files/storage-snapshots-files.md)
