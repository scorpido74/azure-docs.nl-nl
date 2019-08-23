---
title: Azure-tabel | Azure Marketplace
description: Beheer van leads voor Azure-tabel configureren.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902635"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Beheer van leads configureren met behulp van een Azure-tabel

Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum voor het ontvangen van Azure Marketplace-en AppSource-leads, kunt u een Azure-tabel gebruiken om deze leads af te handelen. U kunt de gegevens vervolgens exporteren en importeren in uw CRM-systeem. De instructies in dit artikel begeleidt u stapsgewijs door het proces van het maken van een Azure Storage-account en een Azure-tabel onder dat account. Daarnaast kunt u een nieuwe stroom maken met behulp van Microsoft Flow om een e-mail bericht te verzenden wanneer uw aanbieding een lead ontvangt.

## <a name="configure-azure-table"></a>Azure-tabel configureren

1. Als u geen Azure-account hebt, kunt u [een gratis proef account maken](https://azure.microsoft.com/pricing/free-trial/).
2. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).
3. Maak in de Azure Portal een opslag account met behulp van de volgende procedure.  
    1. Selecteer **+ een resource maken** in de menu balk links.  Het **nieuwe** deel venster (Blade) wordt aan de rechter kant weer gegeven.
    2. Selecteer **opslag** in het deel venster **Nieuw** .  Er wordt een **Aanbevolen** lijst aan de rechter kant weer gegeven.
    3. Selecteer **opslag account** om te beginnen met het maken van het account.  Volg de instructies in het artikel [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Stappen voor het maken van een Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Selecteer voor meer informatie over opslag accounts [Snelstartgids zelf studie](https://docs.microsoft.com/azure/storage/).  Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

4. Wacht tot uw opslag account is ingericht, een proces dat doorgaans een paar minuten in beslag neemt.  Ga vervolgens naar uw opslag account vanaf de **Start** pagina van de Azure portal door **alle resources weer geven** te selecteren of door **alle resources** te selecteren in de linkernavigatiebalk van de Azure Portal.

    ![Toegang tot uw Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Selecteer in het deel venster opslag account de optie **toegangs sleutels** en kopieer de *verbindings reeks* waarde voor de sleutel. Sla deze waarde op als dit de waarde voor de *verbindings reeks voor het opslag account* is die u moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen. 

    Een voor beeld van een verbinding Sting is:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure-opslag sleutel](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Selecteer in het deel venster opslag account de optie **tabellen** en selecteer **+ tabel** om een tabel te maken. Voer een naam in voor de tabel en selecteer **OK**. Sla deze waarde op wanneer u deze nodig hebt als u een MS-flow wilt configureren voor het ontvangen van e-mail meldingen wanneer leads worden ontvangen.

    ![Azure-tabellen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    U kunt [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) of een ander hulp programma gebruiken om de gegevens in uw opslag tabel te bekijken. U kunt ook de gegevens in de Azure-tabel exporteren. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Beschrijving Microsoft Flow gebruiken met een Azure-tabel  

U kunt [Microsoft flow](https://docs.microsoft.com/flow/) gebruiken om meldingen te automatiseren telkens wanneer een lead wordt toegevoegd aan een Azure-tabel. Als u geen account hebt, kunt u [zich aanmelden voor een gratis account](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Voor beeld van lead meldingen

Gebruik dit voor beeld als richt lijn voor het maken van een eenvoudige stroom waarmee automatisch een e-mail melding wordt verzonden wanneer een nieuwe lead wordt toegevoegd aan een Azure-tabel. In dit voor beeld wordt een terugkeer patroon ingesteld om elk uur lead gegevens te verzenden als de tabel opslag wordt bijgewerkt.

1. Meld u aan bij uw Microsoft Flow-account.
2. Selecteer **mijn stromen**op de linkernavigatiebalk.
3. Selecteer **+ Nieuw**op de bovenste navigatie balk.  
4. Selecteer in de vervolg keuzelijst **+ gepland: leeg**

   ![Mijn stromen * * + gepland-van leeg * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  In het venster *een geplande stroom maken* onder *herhalen elke* selecteert u ' 1 ' voor interval en uur voor frequentie. Geef ook de stroom een naam als u wilt. Selecteer **Maken**.

    >[!Note]
    >Hoewel in dit voor beeld een interval van 1 uur wordt gebruikt, kunt u het interval en de frequentie selecteren die het beste past bij uw bedrijfs behoeften.

    ![Bouw een geplande stroom.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selecteer **+ nieuwe stap**.
7. Zoek in het venster *Kies een actie* op ' Get naactieve tijd ' en selecteer vervolgens **vorige tijd ophalen** onder acties.

   ![Kies een actie.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Stel in het venster **laatste tijd ophalen** het **interval** in op 1. Selecteer in de vervolg keuzelijst **tijds eenheid** de optie **uur**.

    >[!Important]
    >Zorg ervoor dat deze interval en tijds eenheid overeenkomen met het interval en de frequentie die u in stap 5 voor het terugkeer patroon hebt geconfigureerd.

    ![Het tijds interval voor ophalen instellen](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>U kunt de stroom op elk gewenst moment controleren om te controleren of elke stap correct is geconfigureerd. Als u de stroom wilt controleren, selecteert u **stroom controle** in de menu balk van de stroom.

In de volgende reeks stappen maakt u verbinding met uw Azure-tabel en stelt u de verwerkings logica in om nieuwe leads te verwerken.

9. Selecteer na de stap voor het ophalen van tijd **+ nieuwe stap**en zoek naar ' entiteiten ophalen ' in het venster *een actie kiezen* .
10. Onder **acties**, selecteert u **entiteiten ophalen (Azure Table Storage)** .
11. Geef in het venster **Azure Table Storage** informatie op voor de volgende velden en selecteer **maken**:

    * *Verbindings naam* : Geef een beschrijvende naam op voor de verbinding die u tussen deze stroom en de Azure-tabel tot stand brengt.
    * *Naam van opslag account* : Geef de naam op van het opslag account voor uw Azure-tabel. U kunt dit vinden op de pagina **toegangs sleutels** van het opslag account.
    * *Gedeelde opslag sleutel* : Geef de sleutel waarde voor uw opslag account voor uw Azure-tabel op. U kunt dit vinden op de pagina **toegangs sleutels** van het opslag account.

        ![Azure-tabel opslag.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Nadat u op maken hebt geklikt, wordt het venster *entiteiten ophalen* weer geven. Selecteer **Geavanceerde opties weer geven** en geef informatie op voor de volgende velden:

       * *Tabel* : Selecteer de naam van uw Azure-Table Storage (uit stap 6 van instructies over het configureren van een Azure-tabel). In de volgende scherm opname wordt de prompt weer gegeven wanneer de tabel ' marketplaceleads ' is geselecteerd voor dit voor beeld.

            ![Azure Table-entiteiten ophalen.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filter query* – Selecteer dit veld en plak deze functie in het veld:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table Get entities-filter Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Nu u klaar bent met het instellen van de verbinding met de Azure-tabel, selecteert u **nieuwe stap** om een voor waarde toe te voegen voor het scannen van de Azure-tabel voor nieuwe leads. 

13. Selecteer in het venster **actie kiezen** de optie **acties**en selecteer vervolgens het **besturings element voor waarde**.

    ![Azure-tabel: Kies een actie.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Selecteer in het venster **voor waarde** het veld **een waarde kiezen** en selecteer vervolgens **expressie** in het pop-upvenster.

15. In `length(body('Get_entities')?['value'])` het ***FX*** -veld plakken. Selecteer **OK** om deze functie toe te voegen. 

16. Het instellen van de voor waarde volt ooien:
    1. Selecteer ' is groter dan ' in de vervolg keuzelijst.
    2. 0 invoeren als waarde

        ![Azure-tabel-voor waarde.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

In de volgende stappen gaat u de actie instellen die moet worden uitgevoerd op basis van het resultaat van de voor waarde.

* Als de voor waarde wordt omgezet in **als Nee**, moet u niets doen.
* Als de voor waarde wordt omgezet in **Indien ja**, moet u een actie activeren die uw Office 365-account verbindt om een e-mail te verzenden. 

17. Selecteer **een actie toevoegen** onder **als ja**.

    ![Azure-tabel-voor waarde, * * als Ja * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selecteer **een E-mail verzenden (Office 365 Outlook)** .

    ![Azure-tabel-voor waarde, * * als Ja * *, e-mail verzenden.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Als u een andere e-mail provider wilt gebruiken, zoekt en selecteert u in plaats daarvan een e-mail melding (E-mail) verzenden als actie. De instructies laten zien hoe u met Office 365 Outlook kunt configureren, maar de instructies zijn vergelijkbaar voor een andere e-mail provider.

19. Geef in het **Office 365 Outlook** -venster informatie op voor de volgende velden:

    1. Voer een e-mail adres **in** voor iedereen die deze melding ontvangt.
    1. **Onderwerp** : Geef een onderwerp op voor het e-mail bericht. Bijvoorbeeld: Nieuwe leads.
    1. **Hoofd** tekst: Voeg de tekst toe die u in elk e-mail bericht wilt toevoegen (optioneel) en plak `body('Get_entities')?['value']`deze in de hoofd tekst.

    >[!Note]
    >U kunt aanvullende statische of dynamische gegevens punten invoegen naar de hoofd tekst van deze e-mail.

    ![Azure Table-condition, * * if Ja * *, Office 365 Outlook-venster.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selecteer **Opslaan** om de stroom op te slaan. Microsoft Flow wordt de stroom automatisch getest op fouten. Als er fouten zijn, wordt de stroom uitgevoerd nadat deze is opgeslagen.

In de volgende scherm opname ziet u een voor beeld van hoe de uiteindelijke stroom eruit moet zien.

![Een voor beeld van de laatste stroom.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Uw stroom beheren

Het is eenvoudig om uw stroom te beheren nadat deze is uitgevoerd. U hebt volledige controle over uw stroom. U kunt deze bijvoorbeeld stoppen, bewerken, een uitvoerings geschiedenis bekijken en analyses ophalen. Op de volgende scherm afbeelding ziet u de opties die beschikbaar zijn voor het beheren van een stroom. 

 ![Een stroom beheren](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

De stroom blijft actief totdat u deze stopt met de optie **stroom** uitschakelen.

Als u geen e-mail meldingen over leads krijgt, betekent dit dat er nieuwe leads zijn toegevoegd aan de Azure-tabel. Als er stroom fouten zijn, krijgt u een e-mail zoals het voor beeld in de volgende scherm opname.

 ![E-mail melding voor stroom fout](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel

Wanneer u klaar bent voor het configureren van de informatie voor lead beheer voor uw aanbieding in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
2. Selecteer **verbinding maken** in het gedeelte Lead beheer.
3. Selecteer in het pop-upvenster verbindings Details de optie **Azure-tabel** voor de **doel locatie**van de Lead en plak de Connection String uit het Azure Storage-account dat u hebt gemaakt door de eerdere stappen in het **opslag account** te volgen Connection String aan.
4. Selecteer **Opslaan**. 

>[!Note]
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.

Wanneer leads worden gegenereerd, stuurt micro soft leads naar de Azure-tabel. Als u een stroom hebt geconfigureerd, wordt er ook een e-mail bericht verzonden naar het e-mail adres dat u hebt geconfigureerd.

![Leadbeheer](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Lead beheer-verbindings Details](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Lead beheer-opslag account voor verbindings Details](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

