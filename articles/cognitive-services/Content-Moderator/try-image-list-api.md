---
title: Afbeeldingen met aangepaste lijsten en de API-console beheren - Inhoudsmoderator
titleSuffix: Azure Content Moderator
description: U gebruikt de API Voor lijstbeheer in Azure-inhoudsmoderator om aangepaste lijsten met afbeeldingen te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757195"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Matigen met aangepaste afbeeldingslijsten in de API-console

U gebruikt de [API Voor lijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) in Azure-inhoudsmoderator om aangepaste lijsten met afbeeldingen te maken. Gebruik de aangepaste lijsten met afbeeldingen met de API voor beeldbeheer. De bewerking voor beeldmatiging evalueert uw afbeelding. Als u aangepaste lijsten maakt, wordt deze in de bewerking ook vergeleken met de afbeeldingen in uw aangepaste lijsten. U aangepaste lijsten gebruiken om de afbeelding te blokkeren of toe te staan.

> [!NOTE]
> Er is een maximumlimiet van **5 afbeeldingslijsten** waarbij elke lijst **niet meer dan 10.000 afbeeldingen mag bevatten**.
>

U gebruikt de API Voor lijstbeheer om de volgende taken uit te voeren:

- Een lijst maken.
- Afbeeldingen toevoegen aan een lijst.
- Scherm afbeeldingen tegen de afbeeldingen in een lijst.
- Afbeeldingen uit een lijst verwijderen.
- Een lijst verwijderen.
- Lijstgegevens bewerken.
- De index vernieuwen zodat wijzigingen in de lijst worden gebruikt in een nieuwe scan.

## <a name="use-the-api-console"></a>De API-console gebruiken
Voordat u de API testen in de online console, hebt u uw abonnementssleutel nodig. Dit bevindt zich op het tabblad **Instellingen** in het vak **Ocp-Apim-Subscription-Key.** Zie [Overzicht](overview.md) voor meer informatie.

## <a name="refresh-search-index"></a>Zoekindex vernieuwen

Nadat u wijzigingen hebt aangebracht in een afbeeldingslijst, moet u de index vernieuwen voor wijzigingen die moeten worden opgenomen in toekomstige scans. Deze stap is vergelijkbaar met hoe een zoekmachine op uw bureaublad (indien ingeschakeld) of een webzoekmachine de index voortdurend vernieuwt om nieuwe bestanden of pagina's op te nemen.

1. Selecteer in de [API-verwijzing voor afbeeldingslijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het linkermenu **afbeeldingslijsten**en selecteer vervolgens **Zoekindex vernieuwen**.

   De **pagina Afbeeldingslijsten - Zoekindex vernieuwen** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 
 
    ![Afbeeldingslijsten - Regioselectie zoekindexpagina vernieuwen](images/test-drive-region.png)

    De **afbeeldingslijsten - Update de API-console zoekindex** wordt geopend.

3. Voer in het vak **listId** de lijst-id in. Voer uw abonnementssleutel in en selecteer **Verzenden**.

   ![Afbeeldingslijsten - inhoudsvak zoekindexconsole vernieuwen](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Een afbeeldingslijst maken

1. Ga naar de [API-verwijzing voor imagelistbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   De **afbeeldingslijsten - Pagina maken** wordt geopend. 

3. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft.

   ![Afbeeldingslijsten - Selectie van paginaregio's maken](images/test-drive-region.png)

   De **afbeeldingslijsten - API-console maken** wordt geopend.
 
4. Voer in het vak **Ocp-Apim-Subscription-Key** uw abonnementssleutel in.

5. Voer in het vak **Hoofdaanvraag** waarden in voor **Naam** (bijvoorbeeld MyList) en **Beschrijving**.

   ![Afbeeldingslijsten - Hoofdnaam en beschrijving consoleaanvraag maken](images/try-terms-list-create-1.png)

6. Gebruik tijdelijke aanduidingen voor sleutelsleutels om meer beschrijvende metagegevens aan uw lijst toe te wijzen.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Voeg lijstmetagegevens toe als sleutelwaardeparen en niet de werkelijke afbeeldingen.
 
7. Selecteer **Verzenden**. Uw lijst wordt gemaakt. Let op de **id-waarde** die aan de nieuwe lijst is gekoppeld. U hebt deze ID nodig voor andere functies voor het beheer van de afbeeldingslijst.

   ![Afbeeldingslijsten - Het vak Inhoud van consolerespons maken toont de lijst-id](images/try-terms-list-create-2.png)
 
8. Voeg vervolgens afbeeldingen toe aan MyList. Selecteer **Afbeelding**in het linkermenu en selecteer **Afbeelding toevoegen**.

   De **pagina Afbeelding - Afbeelding toevoegen** wordt geopend. 

9. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft.

   ![Afbeelding - Regioselectie afbeeldingspagina toevoegen](images/test-drive-region.png)

   De **image - Image** API-console toevoegen wordt geopend.
 
10. Voer in het vak **listId** de lijst-id in die u hebt gegenereerd en voer vervolgens de URL in van de afbeelding die u wilt toevoegen. Voer uw abonnementssleutel in en selecteer **Verzenden**.

11. Als u wilt controleren of de afbeelding aan de lijst is toegevoegd, selecteert u in het linkermenu **Afbeelding**en selecteert u **Alle afbeeldings-id's opdoen**.

    De **Image - Get All Image Ids** API-console wordt geopend.
  
12. Voer in het vak **listId** de lijst-id in en voer vervolgens uw abonnementssleutel in. Selecteer **Verzenden**.

    ![Afbeelding - Inhoudsvak Voor alle afbeeldings-id's van de console Antwoord bevat de afbeeldingen die u hebt ingevoerd](images/try-image-list-create-11.png)
 
10. Voeg nog een paar afbeeldingen toe. Nu u een aangepaste lijst met afbeeldingen hebt gemaakt, probeert u [afbeeldingen te evalueren](try-image-api.md) met behulp van de aangepaste afbeeldingslijst. 

## <a name="delete-images-and-lists"></a>Afbeeldingen en lijsten verwijderen

Het verwijderen van een afbeelding of een lijst is eenvoudig. U de API gebruiken om de volgende taken uit te voeren:

- Een installatiekopie verwijderen. (**Afbeelding - Verwijderen**)
- Verwijder alle afbeeldingen in een lijst zonder de lijst te verwijderen. (**Afbeelding - Alle afbeeldingen verwijderen)**
- Een lijst en alle inhoud verwijderen. (**Afbeeldingslijsten - Verwijderen**)

In dit voorbeeld wordt één afbeelding verwijderd:

1. Selecteer afbeelding seinen in de [API voor afbeeldingslijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het linkermenu en selecteer **Vervolgens** **Verwijderen**. 

   De **pagina Afbeelding - Verwijderen** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Afbeelding - Paginaregioselectie verwijderen](images/test-drive-region.png)
 
   De **api-console Image - Delete** wordt geopend.
 
3. Voer in het vak **listId** de id van de lijst in om een afbeelding uit te verwijderen.  Dit is het nummer dat is geretourneerd in de **image - Get All Image Ids-console** voor MyList. Voer vervolgens de **ImageId** van de afbeelding in om te verwijderen. 

In ons voorbeeld is de lijst-id **58953**, de waarde voor **ContentSource**. De afbeeldings-ID is **59021**, de waarde voor **ContentIds**.

1. Voer uw abonnementssleutel in en selecteer **Verzenden**.

1. Als u wilt controleren of de afbeelding is verwijderd, gebruikt u de console **Afbeeldings-id's op.**
 
## <a name="change-list-information"></a>Lijstgegevens wijzigen

U de naam en beschrijving van een lijst bewerken en metagegevens toevoegen.

1. Selecteer in de [API-verwijzing voor api-beheer van afbeeldingslijst](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het linkermenu **afbeeldingslijsten**en selecteer **Vervolgens Details bijwerken**. 

   De **pagina Afbeeldingslijsten - Details bijwerken** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft.  

    ![Afbeeldingslijsten - Details paginaregioselectie bijwerken](images/test-drive-region.png)

    De **afbeeldingslijsten - Api-console Details bijwerken** wordt geopend.
 
3. Voer in het vak **listId** de lijst-id in en voer vervolgens uw abonnementssleutel in.

4. Voer in het vak **Hoofdaanvraag** uw bewerkingen uit en selecteer vervolgens de knop **Verzenden** op de pagina.

   ![Afbeeldingslijsten - Details console aanvraag-hoofdbewerkingen bijwerken](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of begin met de [afbeeldingslijsten .NET snelstart](image-lists-quickstart-dotnet.md) om te integreren met uw toepassing.
