---
title: Tekst met aangepaste termenlijsten beheren - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Gebruik de API Lijstbeheer om aangepaste lijsten te maken met termen die u gebruiken met de API voor tekstbeheer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75382120"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Matigen met aangepaste termenlijsten in de API-console

De standaardlijst met algemene termen van Azure Content Moderator is voldoende voor de meeste moderatietaken voor inhoudsbeheer. Het is echter mogelijk dat u inhoud moet controleren op termen die specifiek zijn voor uw organisatie. Zo is het bijvoorbeeld mogelijk dat u namen van concurrenten wilt taggen voor nader onderzoek. 

Gebruik de [API Lijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) om aangepaste lijsten te maken met termen die u gebruiken met de API voor tekstbeheer. De **tekst - Schermbewerking** scant uw tekst op godslastering en vergelijkt tekst ook met aangepaste en gedeelde blocklists.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.
>

U de API voor lijstbeheer gebruiken om de volgende taken uit te voeren:
- Een lijst maken.
- Termen toevoegen aan een lijst.
- Termen vergelijken met de termen in een lijst.
- Termen verwijderen uit een lijst.
- Een lijst verwijderen.
- Lijstgegevens bewerken.
- De index vernieuwen zodat wijzigingen in de lijst worden gebruikt in een nieuwe scan.

## <a name="use-the-api-console"></a>De API-console gebruiken

Voordat u de API testen in de online console, hebt u uw abonnementssleutel nodig. Deze sleutel bevindt zich op het tabblad **Instellingen** in het vak **Ocp-Apim-Subscription-Key.** Zie [Overzicht](overview.md) voor meer informatie.

## <a name="refresh-search-index"></a>Zoekindex vernieuwen

Nadat u wijzigingen hebt aangebracht in een termenlijst, moet u de index vernieuwen voor wijzigingen die moeten worden opgenomen in toekomstige scans. Deze stap is vergelijkbaar met hoe een zoekmachine op uw bureaublad (indien ingeschakeld) of een webzoekmachine de index voortdurend vernieuwt om nieuwe bestanden of pagina's op te nemen.

1. Selecteer **termlistaallijsten**in de [API voor termenlijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het linkermenu termlijsten en selecteer vervolgens **Zoekindex vernieuwen**. 

   De **pagina TermLists - Vernieuwen van zoekindex** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Termlijsten - Regioselectie zoekindexpagina vernieuwen](images/test-drive-region.png)

   De **term lists - Refresh Search Index** API console opens.

3. Voer in het vak **listId** de lijst-id in. Voer uw abonnementssleutel in en selecteer **Verzenden**.

   ![Api voor termenlijsten - inhoudsvak zoekindexconsoleantwoord vernieuwen](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Een termenlijst maken
1. Ga naar de [API-verwijzing term listbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   De **term lijsten - Pagina Maken** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Termenlijsten - Selectie paginaregio maken](images/test-drive-region.png)

   De **term lists - API-console maken** wordt geopend.
 
3. Voer in het vak **Ocp-Apim-Subscription-Key** uw abonnementssleutel in.

4. Voer in het vak **Hoofdaanvraag** waarden in voor **Naam** (bijvoorbeeld MyList) en **Beschrijving**.

   ![Termenlijsten - Hoofdnaam en beschrijving consoleaanvraag maken](images/try-terms-list-create-1.png)

5. Gebruik tijdelijke aanduidingen voor sleutelsleutels om meer beschrijvende metagegevens aan uw lijst toe te wijzen.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Voeg lijstmetagegevens toe als sleutelwaardeparen en niet als werkelijke termen.
 
6. Selecteer **Verzenden**. Uw lijst wordt gemaakt. Let op de **id-waarde** die aan de nieuwe lijst is gekoppeld. U hebt deze ID nodig voor andere functies voor lijstbeheer op andere termen.

   ![Termlijsten - Het vak Inhoud van consolerespons maken toont de lijst-id](images/try-terms-list-create-2.png)
 
7. Termen toevoegen aan Mijn lijst. Selecteer in het linkermenu onder **Term**de optie **Term toevoegen**. 

   De pagina **Term - Term toevoegen** wordt geopend. 

8. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Term - Term pagina regio selectie toevoegen](images/test-drive-region.png)

   De **term - Term** API-console toevoegen wordt geopend.
 
9. Voer in het vak **listId** de lijst-id in die u hebt gegenereerd en selecteer een waarde voor **taal**. Voer uw abonnementssleutel in en selecteer **Verzenden**.

   ![Term - Term console query parameters toevoegen](images/try-terms-list-create-3.png)
 
10. Als u wilt controleren of de term aan de lijst is toegevoegd, selecteert u in het linkermenu **Term**en selecteert u **Alle termen opmaken**. 

    De **term - Download Alle termen** API-console wordt geopend.

11. Voer in het vak **listId** de lijst-id in en voer vervolgens uw abonnementssleutel in. Selecteer **Verzenden**.

12. Controleer in het vak **Inhoud van antwoord** de voorwaarden die u hebt ingevoerd.

    ![Term - Inhoudsvak Antwoord voor alle termen op alle voorwaarden krijgen, bevat de voorwaarden die u hebt ingevoerd](images/try-terms-list-create-4.png)
 
13. Voeg nog een paar termen toe. Nu u een aangepaste lijst met termen hebt gemaakt, probeert u [bepaalde tekst te scannen](try-text-api.md) met behulp van de aangepaste termenlijst. 

## <a name="delete-terms-and-lists"></a>Termen en lijsten verwijderen

U kunt eenvoudig term of lijsten verwijderen. U gebruikt de API om de volgende taken uit te voeren:

- Een term verwijderen. (**Term - Verwijderen**)
- Alle termen in een lijst verwijderen zonder de lijst te verwijderen. (**Term - Alle voorwaarden verwijderen**)
- Een lijst en alle inhoud verwijderen. (**Term Lijsten - Verwijderen**)

In dit voorbeeld wordt één woord verwijderd.

1. Selecteer term [list management API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het linkermenu **Term**en selecteer **Vervolgens Verwijderen**. 

   De **term - Delete** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Term - Paginaregioselectie verwijderen](images/test-drive-region.png)

   De **term - API-console verwijderen** wordt geopend.
  
3. Voer in het vak **listId** de id in van de lijst waaruit u een term wilt verwijderen. Deze ID is het nummer (in ons voorbeeld, **122**) dat wordt geretourneerd in de **term lijsten - Details console** voor MyList. Voer de term in en selecteer een taal.
 
   ![Term - Consolequeryparameters verwijderen](images/try-terms-list-delete-1.png)

4. Voer uw abonnementssleutel in en selecteer **Verzenden**.

5. Als u wilt controleren of de term is verwijderd, gebruikt u de **term lijsten - Alle** console ophalen.

   ![Termlijsten - Het vak Inhoud van alle consolerespons krijgt, geeft aan dat de term is verwijderd](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Lijstgegevens wijzigen

U de naam en beschrijving van een lijst bewerken en metagegevens toevoegen.

1. Selecteer **termlistaallijsten**in de [API voor termenlijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het linkermenu termlijsten en selecteer **vervolgens Details bijwerken**. 

   De pagina **Termenlijsten - Details bijwerken** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Termlijsten - Details paginaregioselectie bijwerken](images/test-drive-region.png)

   De **term lists - Update Details** API console wordt geopend.

3. Voer in het vak **listId** de lijst-id in en voer vervolgens uw abonnementssleutel in.

4. Voer **in** het hoofdvak Van toepassing van het verzoek uw bewerkingen uit en selecteer **Verzenden**.

   ![Term lijsten - Details console Aanvraag bodybewerkingen bijwerken](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of begin met de [termlijsten .NET snelstart](term-lists-quickstart-dotnet.md) om te integreren met uw toepassing.
