---
title: Azure-tabel | Azure Marketplace
description: Leadbeheer configureren voor Azure Table.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a36c411b9ababc42adb51d82a316df4252c01e24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251977"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Leadbeheer configureren met behulp van een Azure-tabel

Als uw CRM-systeem (Customer Relationship Management) niet expliciet wordt ondersteund in het Partnercentrum voor het ontvangen van Azure Marketplace- en AppSource-leads, u een Azure-tabel gebruiken om deze leads te verwerken. U er vervolgens voor kiezen om de gegevens te exporteren en te importeren in uw CRM-systeem. De instructies in dit artikel leiden u door het proces van het maken van een Azure Storage-account en een Azure-tabel onder dat account. Bovendien u een nieuwe stroom maken met Microsoft Flow om een e-mailmelding te verzenden wanneer uw aanbieding een lead ontvangt.

## <a name="configure-azure-table"></a>Azure-tabel configureren

1. Als u geen Azure-account hebt, u [een gratis proefaccount maken.](https://azure.microsoft.com/pricing/free-trial/)
2. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)
3. Maak in de Azure-portal een opslagaccount met de volgende procedure.  
    1. Selecteer **+Een resource maken** op de linkermenubalk.  Het **nieuwe** deelvenster (mes) wordt rechts weergegeven.
    2. Selecteer **Opslag** in het deelvenster **Nieuw.**  **Een lijst met aanbevolen** gegevens wordt rechts weergegeven.
    3. Selecteer **Opslagaccount** om het aanmaken van een account te starten.  Volg de instructies in het artikel [Een opslagaccount aanmaken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Stappen om een Azure-opslagaccount te maken](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Selecteer [Snelstart-zelfstudie voor](https://docs.microsoft.com/azure/storage/)meer informatie over opslagaccounts .  Zie [opslagprijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over opslagprijzen.

4. Wacht tot uw opslagaccount is ingericht, een proces dat doorgaans enkele minuten duurt.  Ga vervolgens naar uw opslagaccount vanaf de **startpagina** van de Azure-portal door **Alle resources weergeven** te selecteren of **alle bronnen** te selecteren op de linkernavigatiemenubalk van de Azure-portal.

    ![Toegang tot uw Azure-opslagaccount](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Selecteer **access-sleutels** in het deelvenster Access en kopieer de waarde van de *verbindingstekenreeks* voor de sleutel. Sla deze waarde op, omdat dit de waarde van de *opslagaccountverbinding is* die u in de publicatieportal moet verstrekken om leads voor uw marktplaatsaanbieding te ontvangen. 

    Een voorbeeld van een verbindingssteek is:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure-opslagsleutel](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Selecteer **tabellen** in het deelvenster Opslagaccount en selecteer **+Tabel** om een tabel te maken. Voer een naam in voor uw tabel en selecteer **OK**. Sla deze waarde op zoals u deze nodig hebt als u een MS Flow wilt configureren om e-mailmeldingen te ontvangen wanneer leads worden ontvangen.

    ![Azure-tabellen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    U [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) of een ander hulpmiddel gebruiken om de gegevens in uw opslagtabel weer te geven. U de gegevens ook exporteren in de Azure-tabel. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Optioneel) Microsoft Flow gebruiken met een Azure-tabel  

U [Microsoft Flow](https://docs.microsoft.com/flow/) gebruiken om meldingen te automatiseren telkens wanneer een lead wordt toegevoegd aan azure-tabel. Wanneer je nog geen account hebt kun je je [gratis registreren.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Voorbeeld van leadmelding

Gebruik dit voorbeeld als een handleiding om een eenvoudige stroom te maken die automatisch een e-mailmelding verzendt wanneer een nieuwe lead wordt toegevoegd aan een Azure-tabel. In dit voorbeeld wordt een herhaling ingesteld om elk uur leadgegevens te verzenden als de tabelopslag wordt bijgewerkt.

1. Meld u aan bij uw Microsoft Flow-account.
2. Selecteer op de linkernavigatiebalk **Mijn stromen**.
3. Selecteer **+ Nieuw**op de bovenste navigatiebalk .  
4. Selecteer in de vervolgkeuzelijst **+ Gepland - van leeg**

   ![Mijn stromen **+ Gepland - van leeg**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.    Selecteer op *het venster Een geplande stroom bouwen* onder *Elke* selectie "1" voor interval en "uur" voor frequentie. Geef de stroom ook een naam als je wilt. Selecteer **Maken**.

>[!Note]
>Hoewel in dit voorbeeld een interval van 1 uur wordt gebruikt, u het interval en de frequentie selecteren die het beste is voor uw bedrijfsbehoeften.

![Bouw een geplande stroom.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selecteer **+ Nieuwe stap**.
7. Selecteer in het *zoekvenster Kiezen een actievenster* naar 'verleden tijd' en selecteer **Verleden tijd** onder Acties.

   ![Kies een actie.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Stel in het venster **Verleden tijd** het **interval** in op 1. Selecteer in de vervolgkeuzelijst **Tijdeenheid** de optie **Uur**.

    >[!Important]
    >Zorg ervoor dat deze interval- en tijdeenheid overeenkomt met het interval en de frequentie die u hebt geconfigureerd voor Herhaling in stap 5.

    ![Verloop van tijdsinterval instellen](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>U uw stroom op elk gewenst moment controleren om te controleren of elke stap correct is geconfigureerd. Als u uw stroom wilt controleren, selecteert u **Stroomcontrole** in de menubalk Stroom.

In de volgende reeks stappen maakt u verbinding met uw Azure-tabel en stelt u de verwerkingslogica in om nieuwe leads te verwerken.

9. **Selecteer**na de stap Verleden tijd en zoek naar 'Entiteiten opvragen' in het *actievenster Kiezen voor een actie.*
10. Selecteer **onder Acties**de optie **Entiteiten opdoen (Azure Table Storage)**.
11.    Geef in het venster **Azure Table Storage** informatie op voor de volgende velden en selecteer **Maken:**
* *Verbindingsnaam* - geef een betekenisvolle naam voor de verbinding die u tot stand legt tussen deze stroom en de Azure Table.
* *Naam van het opslagaccount* : geef de naam op van het opslagaccount voor uw Azure-tabel. U dit vinden op de pagina **Toegangssleutels van** het opslagaccount.
* *Gedeelde opslagsleutel* : geef de sleutelwaarde voor uw winkelaccount voor uw Azure-tabel. U dit vinden op de pagina **Toegangssleutels van** het opslagaccount.
    ![Azure-tabelopslag.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

Nadat u op Maken hebt geklikt, ziet u een venster *Entiteiten opdoen.* Selecteer hier **Geavanceerde opties weergeven** en informatie geven voor de volgende velden:
* *Tabel* : selecteer de naam van uw Azure Table Storage (uit stap 6 van instructies voor het configureren van een Azure-tabel). De volgende schermopname toont de prompt wanneer de tabel 'marketplaceleads' voor dit voorbeeld is geselecteerd.
    ![Azure Table krijgt entiteiten.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

* *Filterquery* - Selecteer dit veld en plak `Timestamp gt datetime'@{body('Get_past_time')}'` ![deze functie in het veld: Azure Table get tities - Filter Query.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Nu u de verbinding met de Azure-tabel hebt ingesteld, selecteert u **Nieuwe stap** om een voorwaarde toe te voegen om de Azure-tabel te scannen op nieuwe leads. 

13. Selecteer **in** **het actievenster Kiezen een actie** en selecteer vervolgens het **besturingselement Voorwaarde**.

    ![Azure Table - Kies een actie.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Selecteer **in** het venster Voorwaarde het veld **Een waarde kiezen** en selecteer **Expressie** in het pop-upvenster.

15. Plak `length(body('Get_entities')?['value'])` in het ***fx-veld.*** Selecteer **OK** om deze functie toe te voegen. 

16. Ga als u klaar bent met het instellen van de voorwaarde:
    1. Selecteer 'is groter dan' in de vervolgkeuzelijst.
    2. Voer 0 in als de waarde

        ![Azure Table - Voorwaarde.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

In de volgende stappen stelt u de actie in die u moet uitvoeren op basis van het resultaat van de aandoening.

* Als de voorwaarde wordt opgelost **om als nee,** niets doen.
* Als de voorwaarde is opgelost naar **Zo ja,** activeert u een actie die uw Office 365-account verbindt om een e-mail te verzenden. 

17. Selecteer **Een actie toevoegen** onder Als **ja**.

    ![Azure Table - Voorwaarde, **Zo ja**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selecteer **Een e-mail verzenden (Office 365 Outlook)**.

    ![Azure Table - Voorwaarde, **Zo ja**, stuur e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Als u een andere e-mailprovider wilt gebruiken en een e-mailmelding (E-mail) wilt verzenden als actie. In de instructies ziet u hoe u configureert met Office 365 Outlook, maar de instructies zijn vergelijkbaar voor een andere e-mailprovider.

19. Geef in het **Office 365 Outlook-venster** informatie op voor de volgende velden:

    1. **Naar** - Voer een e-mailadres in voor iedereen die deze melding ontvangt.
    1. **Onderwerp** - Geef een onderwerp voor de e-mail. Bijvoorbeeld: Nieuwe leads!
    1. **Hoofdtekst** - Voeg de tekst toe die u in elke `body('Get_entities')?['value']`e-mail wilt opnemen (optioneel) en plak vervolgens in de hoofdtekst.

    >[!Note]
    >U extra statische of dynamische gegevenspunten invoegen in de hoofdtekst van deze e-mail.

    ![Azure Table - Voorwaarde, **Zo ja**, Office 365 Outlook-venster.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selecteer **Opslaan** om de stroom op te slaan. Microsoft Flow test de stroom automatisch op fouten. Als er geen fouten zijn, wordt uw stroom gestart nadat deze is opgeslagen.

De volgende schermopname toont een voorbeeld van hoe de uiteindelijke stroom eruit moet zien.

![Een voorbeeld van de uiteindelijke stroom.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Uw stroom beheren

Het beheren van uw stroom nadat deze wordt uitgevoerd, is eenvoudig. Je hebt volledige controle over je flow. U het bijvoorbeeld stoppen, bewerken, een rungeschiedenis bekijken en analyses opvragen. De volgende schermopname toont de opties die beschikbaar zijn om een stroom te beheren. 

 ![Een stroom beheren](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

De stroom blijft draaien totdat u deze stopt met de optie **Stroom uitschakelen.**

Als u geen lead-e-mailmeldingen ontvangt, betekent dit dat er geen nieuwe leads zijn toegevoegd aan de Azure-tabel. Als er stroomfouten zijn, ontvangt u een e-mail zoals het voorbeeld in de volgende schermopname.

 ![E-mailmelding voor stroomfouten](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Uw aanbieding configureren om leads naar de Azure-tabel te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, voert u de onderstaande stappen uit:

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.
2. Selecteer **Verbinding maken** onder de sectie Leadbeheer.
3. Selecteer in het pop-upvenster Verbindingsdetails de optie **Azure Table** voor de **leadbestemming**en plak in de verbindingstekenreeks van het Azure-opslagaccount dat u hebt gemaakt door eerdere stappen te volgen in het **veld Tekenreeks voor accountverbinding voor opslag.**
4. **E-mail contact** opnemen : e-mails verstrekken aan mensen in uw bedrijf die e-mailmeldingen moeten ontvangen wanneer een nieuwe lead wordt ontvangen. U meerdere e-mails verstrekken door ze te scheiden met puntkomma.
5. Selecteer **Ok**.

Klik op de knop Valideren om ervoor te zorgen dat u met succes verbinding hebt gemaakt met een leadbestemming. Als dit lukt, heb je een testlead in de hoofdbestemming.

>[!Note]
>U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen.

Wanneer leads worden gegenereerd, stuurt Microsoft leads naar de Azure-tabel. Als u een stroom hebt geconfigureerd, wordt er ook een e-mail verzonden naar het e-mailadres dat u hebt geconfigureerd.

![Leadbeheer](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Leadbeheer - verbindingsgegevens](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Leadbeheer - opslagaccount voor verbindingsgegevens](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

