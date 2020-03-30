---
title: Azure-tabelopslag | Azure Marketplace
description: Leadbeheer configureren in Azure Table-opslag.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280351"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instructies voor loodbeheer voor tabelopslag

In dit artikel wordt beschreven hoe u Azure Table-opslag configureert om verkoopleads te beheren. Met tabelopslag u klantgegevens opslaan en wijzigen.

## <a name="configure-table-storage"></a>Tabelopslag configureren

1. Als u geen Azure-account hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/pricing/free-trial/)aan.
1. Nadat uw account actief is, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)
1. Voer in de Azure-portal de volgende stappen uit:  
    1. Selecteer **+Een resource maken** in het deelvenster aan de linkerkant. Het **nieuwe** deelvenster wordt geopend.
    1. Selecteer **Opslag**in het deelvenster **Nieuw** . Aan de rechterkant wordt een **lijst met aanbevolen** objecten geopend.
    1. Selecteer **Opslagaccount**. Volg vervolgens de instructies bij [Een opslagaccount maken.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    ![Een Azure-opslagaccount maken](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Zie [Quickstart-zelfstudies voor](https://docs.microsoft.com/azure/storage/)meer informatie over opslagaccounts. Zie [Azure-opslagprijzen](https://azure.microsoft.com/pricing/details/storage/)voor prijsinformatie voor prijsbeheer .

1. Wacht tot uw opslagaccount is ingericht, wat meestal enkele minuten duurt. Vervolgens opent u het account vanaf de startpagina van de Azure-portal: Selecteer Alle resources of **Alle bronnen** in het navigatiedeelvenster **weergeven.**

    ![Toegang tot uw Azure-opslagaccount](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Kopieer vanuit het deelvenster Opslagaccount de tekenreeks voor de opslagaccountverbinding voor de sleutel. Plak het in het veld **Verbindingstekenreeks** voor het opslagaccount in de Cloud Partner Portal.

    Voorbeeldverbindingstekenreeks:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure-opslagsleutel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

U [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) of een soortgelijk hulpmiddel gebruiken om de gegevens in uw tabelopslag weer te geven. U er ook gegevens uit exporteren.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Microsoft Flow gebruiken met tabelopslag *(optioneel*)

U [Microsoft Flow](https://docs.microsoft.com/flow/) gebruiken om automatisch meldingen te verzenden wanneer een lead wordt toegevoegd aan uw tabelopslag. Als u geen Microsoft Flow-account hebt, [meldt u zich aan voor een gratis account.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Voorbeeld van leadmelding

In dit voorbeeld ziet u hoe u een basisstroom maakt. De stroom stuurt automatisch een e-mailmelding per uur wanneer er nieuwe leads worden toegevoegd aan uw tabelopslag.

1. Meld u aan bij uw Microsoft Flow-account.
1. Selecteer **Mijn stromen**in het navigatiedeelvenster aan de linkerkant .
1. Selecteer **+Nieuw**in de bovenste navigatiebalk .  
1. Selecteer in de vervolgkeuzelijst **+Maken van leeg**.
1. Selecteer **Maken van leeg**onder Een stroom van leeg **maken**.

   ![Een nieuwe stroom maken vanuit leeg](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Selecteer **Triggers**op de zoekpagina connectors en triggers .
1. Selecteer **Recidief**onder **Triggers**.
1. Houd **in** het venster Herhaling de standaardinstelling van **1** voor **Interval**. Selecteer **in** de vervolgkeuzelijst Frequentie de optie **Uur**.

   >[!NOTE] 
   >In dit voorbeeld wordt een interval van één uur gebruikt. Maar u een interval en frequentie selecteren die het beste past bij uw bedrijfsbehoeften.

   ![Een frequentie van 1 uur instellen voor herhaling](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selecteer **+Nieuwe stap**.
1. Zoek **naar Verleden tijd**en selecteer **Verleden tijd** onder Een **actie kiezen**.

    ![De actie 'verleden tijd' zoeken en selecteren](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Stel in het venster **Verleden tijd** het **interval** in op **1**.  Selecteer in de vervolgkeuzelijst **Tijdeenheid** de optie **Uur**.
    >[!IMPORTANT] 
    >Zorg ervoor dat de eenheid **Interval** en **Tijd** overeenkomt met het interval en de frequentie die u hebt geconfigureerd voor herhaling (stap 8).

    ![Het tijdsinterval get past instellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >U uw stroom op elk gewenst moment controleren om te controleren of elke stap correct is geconfigureerd: Selecteer **Stroomcontrole in** de menubalk Van flow.

In de volgende reeks stappen maakt u verbinding met uw opslagtabel en stelt u de verwerkingslogica in om nieuwe leads te verwerken.

1. Selecteer na de stap **Verleden tijd** de optie **+Nieuwe stap**en zoek vervolgens naar **Entiteiten opmaken**.
1. Selecteer **onder Acties**de optie **Entiteiten opmaken**en selecteer **Geavanceerde opties weergeven**.
1. Vul in het venster **Entiteiten opbrengen** in het venster Entiteiten opbrengen:

   - **Tabel**: de naam van uw tafelopslag. De volgende afbeelding toont "MarketPlaceLeads" ingevoerd:

     ![Een aangepaste waarde kiezen voor azure-tabelnaam](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filterquery:** Wanneer u dit veld selecteert, wordt het pictogram **Verleden tijd** weergegeven in een pop-upvenster. Selecteer **Afgelopen tijd** om deze waarde als tijdstempel te gebruiken om de query te filteren. U ook de volgende functie in het veld plakken:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![De functie filterquery instellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selecteer **Nieuwe stap** om een voorwaarde toe te voegen om uw tabelopslag te scannen op nieuwe leads.

   ![Gebruik 'Nieuwe stap' om een voorwaarde toe te voegen om de opslag van tafelte scannen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Selecteer **in** **het actievenster Kiezen een actie** en selecteer **Vervolgens Voorwaardebeheer**.

     ![Een voorwaardecontrole toevoegen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Selecteer **in** het venster Voorwaarde de optie **Een waarde kiezen**en selecteer **Expressie** in het pop-upvenster.
1. Plak `length(body('Get_entities')?['value'])` in het ***fx-veld.*** Selecteer **OK** om deze functie toe te voegen. 



     ![Een functie toevoegen aan de voorwaarde](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Stel de actie in die u wilt uitvoeren op basis van het resultaat van de voorwaarde.

    1. Selecteren **is groter dan** in de vervolgkeuzelijst.
   1. Voer **0** in als waarde.

     ![Een actie instellen op basis van voorwaarderesultaten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Als de voorwaarde wordt opgelost in 'Als nee', doe dan niets.

    Als de voorwaarde is opgelost in 'Zo ja', activeert u een actie die uw Office 365-account verbindt om een e-mail te verzenden:
   1. Selecteer **Een actie toevoegen**.
   1. Selecteer **Een e-mailbericht verzenden**.
   1. Voer in **het e-mailvenster een e-mail** verzenden gegevens in de volgende velden in:

      - **Aan:** een e-mailadres voor iedereen die de melding ontvangt.
      - **Onderwerp**: een onderwerp voor de e-mail. Bijvoorbeeld: *Nieuwe leads!*
      - **Hoofdtekst:** de tekst die u in elke e-mail wilt opnemen (optioneel). Plak ook `body('Get_entities')?['value']` in als een functie om leadinformatie in te voegen.

        >[!NOTE] 
        >U extra statische of dynamische gegevenspunten in de hoofdtekst van de e-mail invoegen.

      ![E-mail instellen voor leadmelding](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selecteer **Opslaan** om de stroom op te slaan. Microsoft Flow test het automatisch op fouten. Als er geen fouten zijn, wordt uw stroom gestart nadat deze is opgeslagen.

    In de volgende afbeelding ziet u een voorbeeld van hoe de uiteindelijke stroom eruit moet zien.

    [![Definitieve stroomreeks](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Selecteer de afbeelding om deze te vergroten.*)

### <a name="manage-your-flow"></a>Uw stroom beheren

Het is gemakkelijk om uw stroom te beheren nadat deze wordt uitgevoerd. Je hebt volledige controle over je flow. U het bijvoorbeeld stoppen, bewerken, een rungeschiedenis bekijken en analyses opvragen. In de volgende afbeelding ziet u de opties voor stroombeheer.

 ![Opties voor stroombeheer](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

De stroom blijft draaien totdat u **Flow uitschakelen selecteert.**

Als u geen meldingen van lead-e-mail ontvangt, zijn er geen nieuwe leads toegevoegd aan uw tabelopslag.
U ontvangt een e-mail zoals het volgende voorbeeld als er een stroomfout optreedt:

 ![E-mailmelding voor stroomfouten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Volgende stappen

[Klantenleads configureren](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
