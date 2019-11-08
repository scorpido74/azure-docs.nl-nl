---
title: Azure-tabel opslag | Azure Marketplace
description: Beheer van leads in azure-tabel opslag configureren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: 21105d72ccd288faf0fed58019e67afe2e1c9d01
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825274"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instructies voor het beheer van de Table-opslag

In dit artikel wordt beschreven hoe u Azure Table Storage configureert om verkoop leads te beheren. Tabel opslag helpt u bij het opslaan en wijzigen van klant gegevens.

## <a name="configure-table-storage"></a>Tabel opslag configureren

1. Als u geen Azure-account hebt, [maakt u een account voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/).
1. Nadat uw account actief is, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).
1. Voer de volgende stappen uit in de Azure Portal:  
    1. Selecteer **+ een resource maken** in het deel venster aan de linkerkant. Het **nieuwe** deel venster wordt geopend.
    1. Selecteer in het deel venster **Nieuw** de optie **opslag**. Aan de rechter kant wordt een lijst met **Aanbevolen** bestanden geopend.
    1. Selecteer een **opslag account**. Volg vervolgens de instructies in [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Een Azure-opslagaccount maken](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Zie Quick Start- [zelf studies](https://docs.microsoft.com/azure/storage/)voor meer informatie over opslag accounts. Zie [prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/)voor informatie over prijzen.

1. Wacht tot uw opslag account is ingericht, wat doorgaans enkele minuten in beslag neemt. Ga vervolgens naar het account vanaf de start pagina van de Azure Portal: Selecteer **alle resources** of **alle resources** in het navigatie deel venster.

    ![Toegang tot uw Azure Storage-account](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Kopieer in het deel venster opslag account het opslag account connection string voor de sleutel. Plak deze in het veld **verbindings reeks** voor het opslag account in de Cloud Partner-Portal.

    Voor beeld connection string:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure-opslag sleutel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

U kunt [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) of een soortgelijk hulp programma gebruiken om de gegevens in uw Table-opslag te bekijken. U kunt ook gegevens uit het bestand exporteren.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Microsoft Flow gebruiken met tabel opslag (*optioneel*)

U kunt [Microsoft flow](https://docs.microsoft.com/flow/) gebruiken om automatisch meldingen te verzenden wanneer een lead wordt toegevoegd aan de tabel opslag. Als u geen Microsoft Flow account hebt, [meldt u zich aan voor een gratis account](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Voor beeld van lead meldingen

In dit voor beeld ziet u hoe u een basis stroom maakt. De stroom verzendt automatisch een e-mail melding per uur wanneer er nieuwe leads worden toegevoegd aan de tabel opslag.

1. Meld u aan bij uw Microsoft Flow-account.
1. Selecteer **mijn stromen**in het navigatie deel venster aan de linkerkant.
1. Selecteer **+ Nieuw**in de bovenste navigatie balk.  
1. Selecteer in de vervolg keuzelijst **+ leeg item maken**.
1. Selecteer onder **een lege stroom maken de**optie **leeg item maken**.

   ![Een nieuwe stroom maken op basis van een lege waarde](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Selecteer op de pagina connectors en triggers zoeken de optie **Triggers**.
1. Onder **Triggers**selecteert u **terugkeer patroon**.
1. In het venster **terugkeer patroon** behoudt u de standaard instelling **1** voor **interval**. Selecteer in de vervolg keuzelijst **frequentie** de optie **uur**.

   >[!NOTE] 
   >In dit voor beeld wordt een interval van één uur gebruikt. U kunt echter een interval en frequentie selecteren die het beste bij uw bedrijfs behoeften passen.

   ![Een frequentie van 1 uur instellen voor het terugkeer patroon](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selecteer **+ nieuwe stap**.
1. Zoek de **tijd ophalen**en selecteer vervolgens **vorige tijd ophalen** onder **Kies een actie**.

    ![Zoek en selecteer de actie ' Get vroegere tijd '](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Stel in het venster **laatste tijd ophalen** het **interval** in op **1**.  Selecteer in de vervolg keuzelijst **tijds eenheid** de optie **uur**.
    >[!IMPORTANT] 
    >Zorg ervoor dat het **interval** en de **tijds eenheid** overeenkomen met het interval en de frequentie die u hebt geconfigureerd voor terugkeer patroon (stap 8).

    ![Het tijds interval voor ophalen instellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >U kunt de stroom op elk gewenst moment controleren om te controleren of elke stap correct is geconfigureerd: Selecteer **flow check** in de menu balk van de stroom.

In de volgende reeks stappen maakt u verbinding met uw opslag tabel en stelt u de verwerkings logica in voor het verwerken van nieuwe leads.

1. Selecteer na de stap voor het **ophalen van tijd** **+ nieuwe stap**en zoek naar **entiteiten ophalen**.
1. Onder **acties**, selecteert u **entiteiten ophalen**en selecteert u vervolgens **Geavanceerde opties weer geven**.
1. Vul in het venster **entiteiten ophalen** de volgende velden in:

   - **Tabel**: de naam van de tabel opslag. In de volgende afbeelding wordt ' MarketPlaceLeads ' weer gegeven:

     ![Kies een aangepaste waarde voor de Azure-tabel naam](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filter query**: wanneer u dit veld selecteert, wordt het pictogram **laatste tijd ophalen** weer gegeven in een pop-upvenster. Selecteer **vorige tijd** als u deze waarde wilt gebruiken als tijds tempel om de query te filteren. U kunt ook de volgende functie in het veld plakken:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![De functie filter query instellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selecteer **nieuwe stap** om een voor waarde toe te voegen om uw tabel opslag te scannen op nieuwe leads.

   ![Nieuwe stap gebruiken om een voor waarde toe te voegen voor het scannen van Table-opslag](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Selecteer in het venster **actie kiezen** de optie **acties**en selecteer vervolgens **besturings element voor waarde**.

     ![Een besturings element voor waarde toevoegen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Selecteer in het venster **voor waarde** **een waarde kiezen**en selecteer vervolgens **expressie** in het pop-upvenster.
1. Plak `length(body('Get_entities')?['value'])` in het ***FX*** -veld. Selecteer **OK** om deze functie toe te voegen. 



     ![Een functie toevoegen aan de voor waarde](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Stel de actie in die moet worden uitgevoerd op basis van het resultaat van de voor waarde.

    1. Select **is groter dan** in de vervolg keuzelijst.
   1. Voer **0** in als waarde.

     ![Een actie instellen op basis van voor waarden resultaten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Als de voor waarde wordt omgezet in ' Indien nee ', moet u niets doen.

    Als de voor waarde wordt omgezet in ' Indien ja ', moet u een actie activeren die uw Office 365-account verbindt met het verzenden van een e-mail bericht:
   1. Selecteer **een actie toevoegen**.
   1. Selecteer **een E-mail verzenden**.
   1. Voer in het venster **een E-mail verzenden** gegevens in de volgende velden in:

      - **Om**: een e-mail adres voor iedereen die de melding ontvangt.
      - **Onderwerp**: een onderwerp voor het e-mail bericht. Bijvoorbeeld: *nieuwe leads!*
      - **Hoofd**tekst: de tekst die u in elk e-mail bericht wilt toevoegen (optioneel). Plak ook in `body('Get_entities')?['value']` als een functie om lead gegevens in te voegen.

        >[!NOTE] 
        >U kunt aanvullende statische of dynamische gegevens punten invoegen in de hoofd tekst van het e-mail bericht.

      ![E-mail voor lead meldingen instellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selecteer **Opslaan** om de stroom op te slaan. Microsoft Flow kunt deze automatisch testen op fouten. Als er fouten zijn, wordt de stroom uitgevoerd nadat deze is opgeslagen.

    In de volgende afbeelding ziet u een voor beeld van hoe de uiteindelijke stroom eruit moet zien.

    [de laatste stroom volgorde ![](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Selecteer de afbeelding om deze te verg Roten.* )

### <a name="manage-your-flow"></a>Uw stroom beheren

Het is eenvoudig om uw stroom te beheren nadat deze is uitgevoerd. U hebt volledige controle over uw stroom. U kunt deze bijvoorbeeld stoppen, bewerken, een uitvoerings geschiedenis bekijken en analyses ophalen. In de volgende afbeelding ziet u de opties voor flow beheer.

 ![Opties voor flow beheer](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

De stroom blijft actief totdat u **deFLOW**uitschakelen selecteert.

Als u geen e-mail meldingen over leads krijgt, zijn er geen nieuwe leads toegevoegd aan uw Table-opslag.
U ontvangt een e-mail bericht zoals in het volgende voor beeld als er een stroom fout optreedt:

 ![E-mail melding voor stroom fout](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Volgende stappen

[Klantenleads ophalen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
