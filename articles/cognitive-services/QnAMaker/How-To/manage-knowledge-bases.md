---
title: Beheer kennisbanken - QnA Maker
description: Met QnA Maker u uw kennisbanken beheren door toegang te bieden tot de instellingen en inhoud van de kennisbank.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756798"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Kennisbank maken en instellingen beheren

Met QnA Maker u uw kennisbanken beheren door toegang te bieden tot de kennisbankinstellingen en gegevensbronnen.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
> * Een [QnA Maker-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) die is gemaakt in de Azure-portal. Onthoud uw Azure Active Directory ID, Abonnement, QnA-bronnaam die u hebt geselecteerd toen u de bron maakte.

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

1. Meld u aan bij de [QnAMaker.ai](https://QnAMaker.ai) portal met uw Azure-referenties.

1. Selecteer in de QnA Maker-portal de optie **Een kennisbank maken.**

1. Sla **stap 1** op de pagina **Maken** over als u al uw QnA Maker-bron hebt.

    Als u de resource nog niet hebt gemaakt, selecteert u **Een QnA-service maken**. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Onthoud uw Azure Active Directory ID, Abonnement, QnA-bronnaam die u hebt geselecteerd toen u de bron maakte.

    Wanneer u klaar bent met het maken van de bron in de Azure-portal, keert u terug naar de QnA Maker-portal, vernieuwt u de browserpagina en gaat u verder naar **stap 2.**

1. Selecteer in **stap 3**uw Active directory, abonnement, service (resource) en de taal voor alle kennisbanken die in de service zijn gemaakt.

   ![Schermafbeelding van het selecteren van een QnA Maker-servicekennisbank](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. In **stap 3,** noem `My Sample QnA KB`uw kennisbank .

1. Configureer in **stap 4**de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Multi-turn extractie uit URL's, .pdf- of .docx-bestanden inschakelen.**|Geselecteerd|
    |**Standaardantwoordtekst**| `Quickstart - default answer not found.`|
    |**+ URL toevoegen**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selecteer **Professional**|

1. Selecteer uw **KB maken**in **stap 5.**

    Het extractieproces duurt enkele ogenblikken om het document te lezen en vragen en antwoorden te identificeren.

    Nadat QnA Maker met succes de kennisbank heeft gemaakt, wordt de **Knowledge base** pagina geopend. U de inhoud van de kennisbank op deze pagina bewerken.

## <a name="edit-knowledge-base"></a>Knowledge base bewerken

1.  Selecteer **Mijn kennisbases** in de bovenste navigatiebalk.

       U alle services die u hebt gemaakt of met u hebt gedeeld, gesorteerd in de aflopende volgorde van de **laatst gewijzigde** datum.

       ![Mijn kennisbanken](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecteer een bepaalde kennisbank om deze aan te bewerkingen.

1.  Selecteer **Instellingen**. De volgende lijst bevat velden die u wijzigen.

       |Doel|Actie|
       |--|--|
       |URL toevoegen|U nieuwe URL's toevoegen om nieuwe FAQ-inhoud toe te voegen aan Knowledge Base door te klikken op **De koppeling Kennisbank beheren > '+ URL toevoegen'.**|
       |URL verwijderen|U bestaande URL's verwijderen door het pictogram Verwijderen, de prullenbak, te selecteren.|
       |Inhoud vernieuwen|Als u wilt dat uw kennisbank de nieuwste inhoud van bestaande URL's crawlt, schakelt u het selectievakje **Vernieuwen** in. Hiermee wordt de kennisbank één keer bijgewerkt met de nieuwste URL-inhoud. Dit is niet het instellen van een regelmatig schema van updates.|
       |Bestand toevoegen|U een ondersteund bestandsdocument toevoegen om deel uit te maken van een kennisbank door **Kennisbank beheren**te selecteren en vervolgens **bestand + Bestand toevoegen te** selecteren|
    |Importeren|U ook een bestaande kennisbank importeren door de knop **Kennisbasis importeren** te selecteren. |
    |Update|Het bijwerken van de kennisbank is afhankelijk van **de prijscategorie voor beheer** die wordt gebruikt tijdens het maken van de QnA Maker-service die is gekoppeld aan uw kennisbank. U de beheerlaag ook bijwerken vanuit azure-portal indien nodig.

  1. Zodra u klaar bent met het aanbrengen van wijzigingen in de kennisbank, selecteert u **Opslaan en trainen** in de rechterbovenhoek van de pagina om de wijzigingen voort te zetten.

       ![Opslaan en trainen](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Als u de pagina verlaat voordat **u Opslaan en trainen selecteert,** gaan alle wijzigingen verloren.



## <a name="manage-large-knowledge-bases"></a>Grote kennisbanken beheren

* **Gegevensbrongroepen**: De QnA's worden gegroepeerd op de gegevensbron waaruit ze zijn geëxtraheerd. U de gegevensbron uitvouwen of samenvouwen.

    ![Gebruik de gegevensbronbalk van QnA Maker om vragen en antwoorden van gegevensbronnen samen te vouwen en uit te vouwen](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Kennisbank zoeken**: U de kennisbank doorzoeken door het tekstvak bovenaan de Knowledge Base-tabel in te typen. Klik op Enter om te zoeken naar de inhoud van de vraag, het antwoord of de metagegevens. Klik op het X-pictogram om het zoekfilter te verwijderen.

    ![Gebruik het zoekvak QnA Maker boven de vragen en antwoorden om de weergave te reduceren tot alleen filterovereenkomende items](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Pagination**: Snel door databronnen gaan om grote kennisbanken te beheren

    ![Gebruik de QnA Maker-paginatie-functies boven de vragen en antwoorden om pagina's met vragen en antwoorden te doorlopen](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Kennisbanken verwijderen

Het verwijderen van een kennisbank (KB) is een permanente operatie. Het kan niet ongedaan worden gemaakt. Voordat u een kennisbank verwijderde, moet u de kennisbank exporteren via de pagina **Instellingen** van de QnA Maker-portal.

Als u uw kennisbank deelt met medewerkers,](collaborate-knowledge-base.md) en deze vervolgens verwijdert, verliest iedereen de toegang tot de KB.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [de taal](language-knowledge-base.md) van alle kennisbanken in een resource.

* QnA-paren bewerken
* Azure-resources beheren die worden gebruikt door QnA Maker