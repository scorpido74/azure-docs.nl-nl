---
title: Een persoonsmodel aanpassen met de website Video-indexer
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een persoonsmodel met de website Video-indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499979"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Een persoonsmodel aanpassen met de website Video-indexer

Video Indexer ondersteunt herkenning van beroemdheden voor video-inhoud. De functie voor herkenning van beroemdheden omvat ongeveer een miljoen gezichten op basis van veelgevraagde gegevensbronnen zoals IMDB, Wikipedia en top LinkedIn-beïnvloeders. Zie [Een persoonsmodel aanpassen in Video-indexervoor](customize-person-model-overview.md)voor een gedetailleerd overzicht.

U de website Video Indexer gebruiken om gezichten te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U de API ook gebruiken, zoals beschreven in [Een persoonsmodel aanpassen met API's](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centraal beheer van persoonsmodellen in uw account

1. Als u de persoonsmodellen in uw account wilt bekijken, bewerken en verwijderen, bladert u naar de website van video-indexer en meldt u zich aan.

2. Selecteer de knop voor het aanpassen van inhoudsmodellen in de rechterbovenhoek van de pagina.

    ![Aanpassing van inhoudsmodel](./media/customize-face-model/content-model-customization.png)

3. Selecteer het tabblad Personen.

    Je ziet het standaardpersoonsmodel in je account. Het standaardpersoonsmodel bevat alle gezichten die u hebt bewerkt of gewijzigd in de inzichten van uw video's waarvoor u tijdens het indexeren geen aangepast persoonsmodel hebt opgegeven.

    Als u andere persoonsmodellen hebt gemaakt, worden deze ook op deze pagina weergegeven.

    ![Aanpassing van inhoudsmodel](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Een nieuw persoonsmodel maken

1. Selecteer de knop **+ Model toevoegen.**

    ![Een nieuw persoonsmodel toevoegen](./media/customize-face-model/add-new-person.png)

2. Voer de naam van het model in en selecteer de knop controleren naast de naam.

    ![Een nieuw persoonsmodel toevoegen](./media/customize-face-model/add-new-person2.png)

    U hebt een nieuw persoonsmodel gemaakt. U nu gezichten toevoegen aan het nieuwe model Persoon.

3. Selecteer de knop lijstmenu en kies **+ Persoon toevoegen**.

    ![Een nieuw persoonsmodel toevoegen](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Een nieuwe persoon toevoegen aan een persoonsmodel

> [!NOTE]
> Met Video Indexer u meerdere personen met dezelfde naam toevoegen aan een persoonsmodel. Het is echter aan te raden om unieke namen te geven aan elke persoon in uw model voor bruikbaarheid en duidelijkheid.

1. Als u een nieuw gezicht aan een persoonsmodel wilt toevoegen, selecteert u de knop lijstmenu naast het persoonsmodel waaraan u het gezicht wilt toevoegen.

1. Selecteer **+ Persoon toevoegen** in het menu.

    ![Een nieuw gezicht aan de persoon toevoegen](./media/customize-face-model/add-new-face.png)

    Een pop-up vraagt u om de gegevens van de persoon in te vullen. Typ de naam van de persoon en selecteer de controleknop.

    ![Een nieuw gezicht aan de persoon toevoegen](./media/customize-face-model/add-new-face2.png)

    U vervolgens kiezen uit uw verkenner of de gezichtsafbeeldingen van het gezicht slepen en neerzetten. VideoIndexer neemt alle standaardbestandstypen (bijvoorbeeld JPG, PNG en meer).

    Video Indexer kan gebeurtenissen van deze persoon detecteren in de toekomstige video's die u indexeert en de huidige video's die u al had geïndexeerd, met behulp van het persoonsmodel waaraan u dit nieuwe gezicht hebt toegevoegd. Het kan enige tijd duren voordat de persoon in uw huidige video's wordt erkend, omdat dit een batchproces is.

## <a name="rename-a-person-model"></a>De naam van een persoonsmodel wijzigen

U de naam van elk persoonsmodel in uw account wijzigen, inclusief het model Standaardpersoon. Zelfs als u de naam van uw standaardpersoonsmodel wijzigt, wordt het nog steeds weergegeven als het standaardpersoonsmodel in uw account.

1. Selecteer de knop lijstmenu naast het model Persoon waarvan u de naam wilt wijzigen.
2. Selecteer **Naam wijzigen** in het menu.

    ![De naam van een persoonsmodel wijzigen](./media/customize-face-model/rename-person.png)

3. Selecteer de huidige naam van het model en typ uw nieuwe naam.

    ![De naam van een persoonsmodel wijzigen](./media/customize-face-model/rename-person2.png)

4. Selecteer de controleknop voor een andere naam van uw model.

## <a name="delete-a-person-model"></a>Een persoonsmodel verwijderen

U elk persoonsmodel verwijderen dat u in uw account hebt gemaakt. U het standaardpersoonsmodel echter niet verwijderen.

1. Selecteer **Verwijderen** in het menu.

    ![Een persoonsmodel verwijderen](./media/customize-face-model/delete-person.png)

    Er verschijnt een pop-up en laat u weten dat met deze actie het persoonsmodel en alle personen en bestanden die het bevat, worden verwijderd. Deze actie kan niet ongedaan worden gemaakt.

    ![Een persoonsmodel verwijderen](./media/customize-face-model/delete-person2.png)

1. Als u dit zeker weet, selecteert u opnieuw verwijderen.

> [!NOTE]
> De bestaande video's die zijn geïndexeerd met behulp van dit (nu verwijderde) persoonsmodel, ondersteunen niet de mogelijkheid om de namen van de gezichten die in de video worden weergegeven bij te werken. Je de namen van gezichten in deze video's pas bewerken nadat je ze opnieuw hebt geïndexeerd met een ander persoonsmodel. Als u opnieuw indexeert zonder een persoonsmodel op te geven, wordt het standaardmodel gebruikt.

## <a name="manage-existing-people-in-a-person-model"></a>Bestaande personen beheren in een persoonsmodel

Als u de inhoud van een van uw persoonsmodellen wilt bekijken, selecteert u de pijl naast de naam van het persoonsmodel. De drop-down toont u alle mensen in dat specifieke Persoon model. Als u de knop lijstmenu naast elk van de personen selecteert, ziet u opties beheren, hernoemen en verwijderen.  

![Een nieuw gezicht aan de persoon toevoegen](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>De naam van een persoon wijzigen

1. Als u de naam van een persoon in uw persoonsmodel wilt wijzigen, selecteert u de knop lijstmenu en kiest **u Naam wijzigen** in het lijstmenu.
1. Selecteer de huidige naam van de persoon en typ uw nieuwe naam.
1. Selecteer de controleknop en de persoon krijgt een nieuwe naam.

### <a name="delete-a-person"></a>Een persoon verwijderen

1. Als u een persoon uit uw persoonsmodel wilt verwijderen, selecteert u de knop lijstmenu en kiest **u Verwijderen** in het lijstmenu.
1. Een pop-up vertelt u dat deze actie de persoon zal verwijderen en dat deze actie niet ongedaan kan worden gemaakt.
1. Selecteer Opnieuw **verwijderen** en hiermee wordt de persoon uit het persoonsmodel verwijderd.

### <a name="manage-a-person"></a>Een persoon beheren

Als u **Beheren**selecteert, ziet u alle gezichten waaruit dit persoonsmodel wordt getraind. Deze gezichten zijn afkomstig van gebeurtenissen van die persoon in video's die dit persoonsmodel gebruiken of van afbeeldingen die u handmatig hebt geüpload.

U meer gezichten aan de persoon toevoegen door **afbeeldingen toevoegen**te selecteren.

U het deelvenster Beheren gebruiken om de naam van de persoon te wijzigen en de persoon uit het persoonsmodel te verwijderen.

## <a name="use-a-person-model-to-index-a-video"></a>Een persoonsmodel gebruiken om een video te indexeren

U een persoonsmodel gebruiken om uw nieuwe video te indexeren door het persoonsmodel toe te wijsen tijdens het uploaden van de video.

Ga als volgt te werk om uw persoonsmodel in een nieuwe video te gebruiken:

1. Selecteer de knop **Uploaden** boven aan de pagina.

    ![Een persoonsmodel uploaden](./media/customize-face-model/upload.png)

1. Zet je videobestand in de cirkel of blader naar je bestand.
1. Selecteer de pijl **Geavanceerde opties.**

    ![Een persoonsmodel uploaden](./media/customize-face-model/upload2.png)

1. Selecteer de vervolgkeuzelijst en selecteer het persoonsmodel dat u hebt gemaakt.

    ![Een persoonsmodel uploaden](./media/customize-face-model/upload3.png)

1. Selecteer de optie **Uploaden** onder aan de pagina en je nieuwe video wordt geïndexeerd met je persoonsmodel.

Als u tijdens het uploaden geen persoonsmodel opgeeft, indexeert Video Indexer de video met het standaardpersoonsmodel in uw account.

## <a name="use-a-person-model-to-reindex-a-video"></a>Een persoonsmodel gebruiken om een video opnieuw te indexeren

Als u een persoonsmodel wilt gebruiken om een video in uw verzameling opnieuw te indexeren, gaat u naar uw accountvideo's op de startpagina van Video Indexer en houdt u de plaats in van de naam van de video die u opnieuw wilt indexeren.

U ziet opties om uw video te bewerken, te verwijderen en opnieuw te indexeren.

1. Selecteer de optie om uw video opnieuw te indexeren.

    ![Het model Persoon gebruiken om een video opnieuw te indexeren](./media/customize-face-model/reindex.png)

    Je nu het persoonsmodel selecteren waarmee je je video opnieuw wilt indexeren.
1. Selecteer de vervolgkeuzelijst en selecteer het persoonsmodel dat u wilt gebruiken.

    ![Het model Persoon gebruiken om een video opnieuw te indexeren](./media/customize-face-model/reindex2.png)

1. Selecteer de knop **Opnieuw indexeren** en uw video wordt opnieuw geïndexeerd met uw persoonsmodel.

Alle nieuwe bewerkingen die u aandeed op de gezichten die zijn gedetecteerd en herkend in de video die u zojuist opnieuw hebt geïndexeerd, worden opgeslagen in het persoonsmodel dat u hebt gebruikt om de video opnieuw te indexeren.

## <a name="managing-people-in-your-videos"></a>Mensen beheren in je video's

U de gezichten beheren die worden gedetecteerd en mensen die worden herkend in de video's die u indexeert door gezichten te bewerken en te verwijderen.

Als u een gezicht verwijdert, verwijdert u een specifiek gezicht uit de inzichten van de video.

Als u een gezicht bewerkt, wordt de naam van een gezicht dat wordt gedetecteerd en mogelijk herkend in uw video. Wanneer u een gezicht in uw video bewerkt, wordt die naam opgeslagen als een persoonsvermelding in het persoonsmodel dat tijdens het uploaden en indexeren aan de video is toegewezen.

Als u tijdens het uploaden geen persoonsmodel aan de video toewijst, wordt uw bewerking opgeslagen in het standaardpersoonsmodel van uw account.

### <a name="edit-a-face"></a>Een gezicht bewerken

> [!NOTE]
> Als een persoonsmodel twee of meer verschillende personen met dezelfde naam heeft, u die naam niet taggen in de video's die dat persoonsmodel gebruiken. U alleen wijzigingen aanbrengen in personen die die naam delen op het tabblad Personen van de pagina inhoudsmodel in VideoIndexer. Daarom is het raadzaam om unieke namen te geven aan elke persoon in uw persoonsmodel.

1. Ga naar de Video Indexer-website en meld u aan.
1. Zoek naar een video die je wilt bekijken en bewerken in je account.
1. Als u een gezicht in uw video wilt bewerken, gaat u naar het tabblad Inzichten en selecteert u het potloodpictogram in de rechterbovenhoek van het venster.

    ![Een gezicht bewerken in je video](./media/customize-face-model/edit-face.png)

1. Selecteer een van de gedetecteerde gezichten en wijzig hun namen in 'Onbekende #X' (of de naam die eerder aan het gezicht is toegewezen).
1. Nadat u de nieuwe naam hebt intypen, selecteert u het controlepictogram naast de nieuwe naam. Deze actie slaat de nieuwe naam op en herkent en noemt alle gebeurtenissen van dit gezicht in uw andere huidige video's en in de toekomstige video's die u uploadt. Het herkennen van het gezicht in uw andere huidige video's kan enige tijd duren voordat het van kracht wordt, omdat dit een batchproces is.

Als u een gezicht een naam geeft met de naam van een bestaande persoon in het persoonsmodel dat de video gebruikt, worden de gedetecteerde gezichtsafbeeldingen van deze video van die persoon samengevoegd met wat er al in het model bestaat. Als u een naam geeft aan een gezicht met een nieuwe naam, wordt een nieuw persoonsitem gemaakt in het persoonsmodel dat de video gebruikt.

![Een gezicht bewerken in je video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Een gezicht verwijderen

Als u een gedetecteerd gezicht in uw video wilt verwijderen, gaat u naar het deelvenster Insights en selecteert u het potloodpictogram in de rechterbovenhoek van het deelvenster. Selecteer de optie **Verwijderen** onder de naam van het gezicht. Met deze actie wordt het gedetecteerde gezicht uit de video verwijderd. Het gezicht van de persoon wordt nog steeds gedetecteerd in de andere video's waarin het wordt weergegeven, maar je het gezicht ook uit die video's verwijderen nadat ze zijn geïndexeerd.

De persoon, als ze een naam hadden gekregen, blijft ook bestaan in het persoonsmodel dat is gebruikt om de video te indexeren waarvan u het gezicht hebt verwijderd, tenzij u de persoon specifiek uit het persoonsmodel verwijdert.

![Een gezicht in je video verwijderen](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Volgende stappen

[Persoonsmodel aanpassen met API's](customize-person-model-with-api.md)
