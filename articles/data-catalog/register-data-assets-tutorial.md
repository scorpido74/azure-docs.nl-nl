---
title: Gegevensassets registreren in Azure Data Catalog
description: In deze zelf studie wordt beschreven hoe u gegevensassets registreert in uw Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68950233"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Zelf studie: gegevens assets registreren in Azure Data Catalog

In deze zelf studie gebruikt u het hulp programma voor registratie om gegevensassets te registreren bij het Azure SQL database-voor beeld met de catalogus. Registratie is het proces waarbij belangrijke structurele metagegevens, zoals namen, typen en locaties, worden geëxtraheerd uit de gegevensbron en bijbehorende assets, en worden gekopieerd naar de catalogus. De gegevensbronnen en gegevensassets blijven waar ze zijn, maar de metagegevens worden door de catalogus gebruikt om de gegevensbronnen en gegevensassets eenvoudiger te detecteren en te begrijpen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Gegevensassets registreren 
> * Gegevens assets zoeken
> * Aantekeningen toevoegen aan gegevensassets
> * Verbinding maken met gegevensassets
> * Gegevensassets beheren
> * Gegevensassets verwijderen

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, moet u de [Snelstartgids](register-data-assets-tutorial.md)volt ooien.

* Een [Microsoft Azure](https://azure.microsoft.com/) -abonnement.
* U moet uw eigen Azure Active Directory- [Tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)hebben.

Als u Data Catalog wilt instellen, moet u de eigenaar of mede-eigenaar van een Azure-abonnement zijn.

## <a name="register-data-assets"></a>Gegevensassets registreren

### <a name="register-a-data-source"></a>Een gegevensbron registreren

U registreert gegevensassets (tabellen) uit een [Azure SQL database](../sql-database/sql-database-single-database-get-started.md)-voor beeld, maar u kunt elke ondersteunde gegevens bron gebruiken als u liever met gegevens werkt die vertrouwd en relevant zijn voor uw rol. Zie voor een lijst met ondersteunde gegevensbronnen [Ondersteunde gegevensbronnen](data-catalog-dsr.md).

De naam van de Azure-SQL database die in deze zelf studie wordt gebruikt, is *RLSTest*.

U kunt nu gegevensassets registreren bij het Azure SQL database-voor beeld met behulp van Azure Data Catalog.

1. Ga naar de [Start pagina van Azure Data Catalog](http://azuredatacatalog.com) en selecteer **gegevens publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Selecteer **toepassing starten** om het registratie hulpprogramma te downloaden, te installeren en uit te voeren op uw computer.

   ![Azure Data Catalog - de knop Starten](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Op de pagina **Welkom** selecteert u **Aanmelden** en voert u uw referenties in.

    ![Azure Data Catalog - Welkomstpagina](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Selecteer op de pagina **Microsoft Azure Data Catalog** **SQL Server** en **volgende**.

    ![Azure Data Catalog - gegevensbronnen](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Voer de SQL Server verbindings eigenschappen voor uw Azure SQL database-voor beeld in en selecteer **verbinding maken**.

   ![Azure Data Catalog - SQL Server-verbindingsinstellingen](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registreer de metagegevens van uw gegevensasset. In dit voor beeld registreert u **product** objecten van de Azure SQL database voorbeeld naam ruimte:

    1. Vouw in de boom structuur **Server hiërarchie** uw Azure SQL database-voor beeld uit en selecteer **tabel saleslt**.

    2. Selecteer **product**, **ProductCategory**, **ProductDescription**en **product model** met CTRL + selecteren.

    3. Selecteer de **pijl voor verplaatsen geselecteerd** (**>**). Met deze actie worden alle geselecteerde objecten verplaatst naar de lijst **Te registreren objecten**.

          ![Zelfstudie voor Azure Data Catalog - bladeren door objecten en objecten selecteren](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Selecteer **Preview opnemen** om een snapshotvoorbeeld van de gegevens op te nemen. De moment opname bevat Maxi maal twintig records uit elke tabel en wordt gekopieerd naar de catalogus.

    5. Selecteer **Gegevensprofiel opnemen** om een snapshot van de objectstatistieken op te nemen voor het gegevensprofiel (bijvoorbeeld: minimale, maximale en gemiddelde waarden voor een kolom, het aantal rijen).

    6. Voer in het veld **Tags toevoegen** **verkoop, product, Azure SQL**in. Met deze actie worden zoektags voor deze gegevensassets toegevoegd. Tags zijn voor gebruikers een uitstekende manier om een geregistreerde gegevensbron te zoeken.

    7. Geef de naam op van een **expert** met betrekking tot deze gegevens (optioneel).

          ![Zelfstudie voor Azure Data Catalog - te registreren objecten](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Selecteer **registreren**. Azure Data Catalog registreert uw geselecteerde objecten. In deze oefening worden de geselecteerde objecten uit uw Azure SQL database-voor beeld geregistreerd. Het hulpprogramma voor registratie extraheert metagegevens uit de gegevensasset en kopieert deze gegevens naar de Azure Data Catalog-catalogus. De gegevens blijven waar deze zich momenteel bestaan. Gegevens blijven onder controle van de beheerders en beleids regels van het oorspronkelijke systeem.

          ![Azure Data Catalog - geregistreerde objecten](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Als u uw geregistreerde gegevens bron objecten wilt bekijken, selecteert u **Portal weer geven**. Controleer in de Azure Data Catalog portal of alle vier de tabellen en de data base in de raster weergave worden weer gegeven (Controleer of de zoek balk is uitgeschakeld).

        ![Objecten in de Azure Data Catalog-portal](media/register-data-assets-tutorial/data-catalog-view-portal.png)

In deze oefening hebt u objecten van het Azure SQL database-voor beeld geregistreerd zodat ze gemakkelijk kunnen worden gedetecteerd door gebruikers in uw organisatie.

In de volgende oefening leert u hoe u geregistreerde gegevensassets kunt detecteren.

## <a name="discover-data-assets"></a>Gegevensassets detecteren

Voor detectie in Azure Data Catalog worden twee primaire mechanismen gebruikt: zoeken en filteren.

Zoeken is zowel intuïtief als krachtig. Standaard worden de zoektermen vergeleken met een eigenschap in de catalogus, met inbegrip van door de gebruiker gemaakte aantekeningen.

Filteren is bedoeld als aanvulling op het zoeken. U kunt specifieke kenmerken, zoals experts, gegevensbrontype, objecttype en tags, selecteren om overeenkomende gegevensassets weer te geven en om uw zoekresultaten te beperken tot overeenkomende assets.

Door gebruik te maken van een combi natie van zoeken en filteren kunt u snel navigeren door de gegevens bronnen die zijn geregistreerd bij Azure Data Catalog.

In deze oefening gebruikt u de Azure Data Catalog-portal voor het detecteren van de gegevensassets die u in de vorige stap hebt geregistreerd. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor meer informatie over de zoeksyntaxis.

Hieronder volgen enkele voorbeelden van het detecteren van gegevensassets in de catalogus.  

### <a name="discover-data-assets-with-basic-search"></a>Gegevensassets met een basiszoekopdracht detecteren

Met een basiszoekopdracht kunt u in een catalogus zoeken met behulp van een of meer zoektermen. De resultaten die worden opgehaald, zijn assets die wat betreft een willekeurige eigenschap overeenkomen met een of meer van de opgegeven voorwaarden.

1. Selecteer **Start** in het Azure Data Catalog Portal. Als u de webbrowser hebt gesloten, gaat u naar de [Start pagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Typ `product` in het zoekvak en druk op **ENTER**.

    ![Azure Data Catalog - basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Controleer of alle vier tabellen en de data base in de resultaten worden weer geven. U kunt scha kelen tussen **raster weergave** en **lijst weergave** door knoppen op de werk balk te selecteren, zoals wordt weer gegeven in de volgende afbeelding. U ziet dat het zoekwoord wordt gemarkeerd in de zoekresultaten. Dit gebeurt omdat de optie **Markeren** is **ingeschakeld**. U kunt ook het aantal **resultaten per pagina** in zoekresultaten opgeven.

    ![Azure Data Catalog - resultaten van basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Het deelvenster **Zoekopdrachten** bevindt zich aan de linkerkant. Het deelvenster **Eigenschappen** bevindt zich aan de rechterkant. In het deelvenster **Zoekopdrachten** kunt u zoekcriteria wijzigen en resultaten filteren. In het deelvenster **Eigenschappen** worden de eigenschappen van een geselecteerd object weergegeven in het raster of de lijst.

4. Selecteer **product** in de zoek resultaten. Selecteer de tabbladen **Preview**, **Columns**, **Data profile**en **Documentation** of selecteer de pijl om het onderste deel venster uit te vouwen.  

    ![Azure Data Catalog - onderste deelvenster](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Op het tabblad **Preview** ziet u een voorbeeld van de gegevens in de tabel **Product**.  
5. Selecteer het tabblad **kolommen** om details over kolommen (zoals **naam** en **gegevens type**) te zoeken in de gegevensasset.

6. Selecteer het tabblad **gegevens profiel** om de profile ring van gegevens te bekijken (bijvoorbeeld: het aantal rijen, de grootte van de gegevens of de minimum waarde in een kolom) in de gegevensasset.

### <a name="discover-data-assets-with-property-scoping"></a>Gegevens detecteren door het bereik van eigenschappen te definiëren

Door het bereik van eigenschappen te definiëren, kunt u gegevensassets detecteren waarbij de zoekterm overeenkomt met de opgegeven eigenschap.

1. Schakel het filter **Tabel** onder **Objecttype** in **Filters** uit.  

2. Typ `tags:product` in het zoekvak en druk op **ENTER**. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor alle eigenschappen die u kunt gebruiken voor het zoeken in de gegevenscatalogus.

3. Controleer of de tabellen en de data base in de resultaten worden weer geven.  

    ![Data Catalog - resultaten van het definiëren van het bereik van eigenschappen](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>De zoekopdracht opslaan

1. Voer in het deel venster **Zoek opdrachten** in het gedeelte **huidige zoek opdracht** een naam in voor de zoek opdracht en selecteer **Opslaan**.

    ![Azure Data Catalog - zoekopdracht opslaan](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Bevestig dat de opgeslagen zoekopdracht wordt weergegeven onder **Opgeslagen zoekopdrachten**.

3. Selecteer een van de acties die kunnen worden uitgevoerd op de opgeslagen zoekopdracht (**Naam wijzigen**, **Verwijderen**, **Opslaan als standaard** (zoekopdracht)).

### <a name="grouping-with-parentheses"></a>Groeperen met haakjes

Door te groeperen met behulp van haakjes, kunt u delen van de query groeperen voor logische isolatie, met name in combinatie met Booleaanse operators.

1. Typ `name:product AND (tags:product AND objectType:table)` in het zoekvak en druk op **ENTER**.

2. Bevestig dat u alleen de tabel **Product** in de zoekresultaten ziet.

    ![Azure Data Catalog - zoekopdracht groeperen](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Vergelijkingsoperators

Met vergelijkingsoperators kunt u andere vergelijkingen dan gelijkheid gebruiken voor eigenschappen die de gegevenstypen numeriek en datum hebben.

1. Typ `lastRegisteredTime:>"06/09/2016"` in het zoekvak.

2. Schakel het filter **Tabel** onder **Objecttype** uit.

3. Druk op **Enter**.

4. Controleer of u de tabellen **product**, **ProductCategory**en **ProductDescription** ziet en of de Azure-SQL database die u hebt geregistreerd in de zoek resultaten.

    ![Azure Data Catalog - vergelijking van zoekresultaten](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zie [gegevens assets ontdekken](data-catalog-how-to-discover.md) voor gedetailleerde informatie over het detecteren van gegevensassets. Zie voor meer informatie over de zoek syntaxis [Data Catalog Naslag informatie over de zoek syntaxis](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Aantekeningen toevoegen aan gegevensassets

In deze oefening gebruikt u de Azure Data Catalog Portal om aantekeningen toe te voegen (informatie zoals beschrijvingen, tags of experts) bestaande gegevensassets in de catalogus. De aantekeningen vormen een aanvulling op de structurele meta gegevens die zijn geëxtraheerd uit de gegevens bron tijdens de registratie. Aantekening zorgt ervoor dat de gegevensassets veel eenvoudiger te detecteren en begrijpen.

In deze oefening maakt u aantekeningen voor een enkele gegevensasset (ProductPhoto). U voegt een beschrijvende naam en een beschrijving toe aan de gegevensasset ProductPhoto.  

1. Ga naar de [Start pagina van Azure Data Catalog](https://www.azuredatacatalog.com) en zoek `tags:product` met om de gegevensassets te vinden die u hebt geregistreerd.

2. Selecteer **product model** in Zoek resultaten.  

3. Voer **Productafbeeldingen** in voor **Beschrijvende naam** en **Productfoto’s voor marketingmateriaal** voor de **Beschrijving**.

    ![Azure Data Catalog - beschrijving van ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    De **Beschrijving** helpt anderen om de geselecteerde gegevensasset te detecteren en te begrijpen waarom en hoe ze deze kunnen gebruiken. U kunt ook meer tags toevoegen en kolommen weergeven. U kunt gegevens bronnen zoeken en filteren met behulp van de beschrijvende meta gegevens die u hebt toegevoegd aan de catalogus.

U kunt ook de volgende stappen op deze pagina uitvoeren:

* Experts toevoegen voor de gegevensasset. Selecteer **toevoegen** in het gebied **experts** .

* Tags toevoegen op het niveau van de gegevensset. Selecteer **toevoegen** in het gebied **Tags** . Een label kan een gebruikerstag of een woordenlijsttag zijn. De Standard-editie van Data Catalog bevat een zakelijke woordenlijst waarmee catalogusbeheerders een centrale zakelijke taxonomie kunnen definiëren. Catalogusgebruikers kunnen vervolgens aantekeningen toevoegen aan gegevensassets met behulp van termen in de woordenlijst. Zie voor meer informatie [de zakelijke woorden lijst instellen voor het bepalen van vaste Tags](data-catalog-how-to-business-glossary.md)

* Tags toevoegen op kolomniveau. Selecteer **toevoegen** onder **labels** voor de kolom waarin u aantekeningen wilt maken.

* Beschrijving toevoegen op kolomniveau. Voer een **Beschrijving** in voor een kolom. U kunt ook de metagegevens van de beschrijving bekijken die uit de gegevensbron zijn geëxtraheerd.

* Informatie over **Verzoeken tot toegang** toevoegen die gebruikers laat zien hoe ze toegang kunnen krijgen tot de gegevensasset.
  
* Ga naar het tabblad **Documentatie** en verstrek hier documentatie over de gegevensasset. Met Azure Data Catalog -documentatie kunt u de gegevenscatalogus gebruiken als een opslagplaats voor inhoud om een volledig overzicht te maken van uw gegevensassets.
  
U kunt ook een aantekening toevoegen aan meerdere gegevensassets. U kunt bijvoorbeeld alle geregistreerde gegevensassets selecteren en er een expert voor opgeven.

![Azure Data Catalog - aantekeningen toevoegen aan meerdere gegevensassets](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog ondersteunt een benadering voor aantekeningen via crowdsourcing. Elke Data Catalog gebruiker kan Tags (gebruiker of verklarende woorden lijst), beschrijvingen en andere meta gegevens toevoegen. Door dit te doen, voegen gebruikers perspectief toe aan een gegevensasset en het gebruik ervan en delen zij dat perspectief met andere gebruikers.

Zie [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-how-to-annotate.md) voor gedetailleerde informatie over het toevoegen van aantekeningen aan gegevens.

## <a name="connect-to-data-assets"></a>Verbinding maken met gegevensassets

In deze oefening opent u gegevensassets in geïntegreerde clienthulpprogramma's en een niet-geïntegreerd hulpprogramma (SQL Server Management Studio) met behulp van verbindingsgegevens.

> [!NOTE]
> Het is belangrijk om te weten dat Azure Data Catalog u geen toegang geeft tot de werkelijke gegevensbron. Het wordt gewoon eenvoudiger voor u om ze te detecteren en te begrijpen. Wanneer u verbinding maakt met een gegevensbron, worden uw Windows-referenties gebruikt of wordt u indien nodig om referenties gevraagd. Als u niet eerder toegang hebt gekregen tot de gegevensbron, moet aan u toegang worden verleend voordat u verbinding kunt maken.

### <a name="connect-to-a-data-asset-from-excel"></a>Verbinding maken met een gegevensasset vanuit Excel

1. Selecteer in de zoekresultaten de optie **Product**. Selecteer **openen in** op de werk balk en selecteer **Excel**.

    ![Azure Data Catalog - verbinding maken met gegevensasset](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selecteer **openen** in het pop-upvenster down load. Deze ervaring kan variëren afhankelijk van de browser.

3. Selecteer in het venster **beveiligings melding van micro soft Excel** de optie **inschakelen**.

    ![Azure Data Catalog - beveiligingspop-upvenster van Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Behoud de standaard instellingen in het dialoog venster **gegevens importeren** en selecteer **OK**.

    ![Azure Data Catalog - Excel-gegevens importeren](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Bekijk de gegevensbron in Excel.

    ![Azure Data Catalog - producttabel in Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

In deze oefening hebt u verbinding gemaakt met gegevensasssets die zijn gedetecteerd via Azure Data Catalog. Via de portal van Azure Data Catalog kunt u direct verbinding maken via de clienttoepassingen die zijn geïntegreerd in het menu **Openen in**. U kunt ook verbinding maken met behulp van een andere toepassing naar keuze. Hierbij wordt gebruikgemaakt van de locatiegegevens van de verbinding in de metagegevens van de asset. U kunt bijvoorbeeld SQL Server Management Studio gebruiken om verbinding te maken met de Azure-SQL database om toegang te krijgen tot de gegevens in de gegevensassets die in deze zelf studie zijn geregistreerd.

1. Open **SQL Server Management Studio**.

2. Voer in het dialoogvenster **Verbinding maken met server** de naam in van de server uit het deelvenster **Eigenschappen** in de Azure Data Catalog-portal.

3. Gebruik de juiste verificatieprocedure en aanmeldingsgegevens voor toegang tot de gegevensasset. Gebruik de informatie in het veld **Verzoeken tot toegang** als u geen toegang hebt.

    ![Azure Data Catalog - verzoeken tot toegang](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selecteer **verbindings reeksen weer geven** om de ADO.net-, ODBC-en OLEDB-verbindings reeksen naar het klem bord te bekijken en te kopiëren voor gebruik in uw toepassing.

## <a name="manage-data-assets"></a>Gegevensassets beheren

In deze stap ziet u hoe u beveiliging kunt instellen voor uw gegevensassets. Data Catalog geeft gebruikers geen toegang tot de gegevens zelf. De eigenaar van de gegevensbron bepaalt de toegang tot gegevens.

Met Data Catalog kunt u gegevensbronnen detecteren en de metagegevens bekijken die gerelateerd zijn aan de bronnen die in de catalogus zijn geregistreerd. Mogelijk zijn er echter situaties echter waarin gegevensbronnen alleen zichtbaar moeten zijn voor specifieke gebruikers of specifieke groepen. Voor deze scenario's kunt u Data Catalog gebruiken om eigenaar te worden van geregistreerde gegevensassets en de zicht baarheid van de assets te bepalen.

> [!NOTE]
> De beheermogelijkheden die in deze taak worden beschreven, zijn alleen beschikbaar in de Standard-editie van Azure Data Catalog, niet in de gratis editie.
> In Azure Data Catalog kunt u eigenaar worden van gegevensassets, mede-eigenaren aan gegevensassets toevoegen en de zichtbaarheid van gegevensassets instellen.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Eigenaar worden van gegevensassets en de zichtbaarheid beperken

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com). Typ `tags:cycles` in het tekstvak **Zoeken** en druk op **ENTER**.

2. Selecteer een item in de lijst met resultaten en selecteer **eigenaar worden** op de werk balk.

3. Selecteer **eigendom overnemen**in het gedeelte **beheer** van het deel venster **Eigenschappen** .

    ![Azure Data Catalog - eigenaar worden](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Als u de zicht baarheid wilt beperken, kiest u **eigen aren & deze gebruikers** in het gedeelte met de **zicht baarheid** en selecteert u **toevoegen**. Voer in het tekstvak de e-mailadressen van de betreffende gebruikers in en druk op **ENTER**.

    ![Azure Data Catalog - toegang beperken](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Gegevensassets verwijderen

In deze oefening gebruikt u de portal van Azure Data Catalog om voorbeeldgegevens te verwijderen uit geregistreerde gegevensassets en om gegevensassets te verwijderen uit de catalogus.

In Azure Data Catalog kunt u een individuele asset of meerdere assets verwijderen.

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Typ `tags:cycles` in **het zoekvak** en selecteer **Enter**.

3. Selecteer een item in de lijst met resultaten en selecteer **verwijderen** op de werk balk, zoals wordt weer gegeven in de volgende afbeelding:

    ![Azure Data Catalog - rasteritem verwijderen](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Als u de lijst weergave gebruikt, wordt het selectie vakje aan de linkerkant van het item weer gegeven, zoals in de volgende afbeelding:

    ![Azure Data Catalog - lijstitem verwijderen](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    U kunt ook meerdere gegevensassets selecteren en deze verwijderen zoals op de volgende afbeelding wordt aangegeven:

    ![Azure Data Catalog - meerdere gegevensassets verwijderen](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Het standaardgedrag van de catalogus is om elke gebruiker toe te staan een gegevensbron te registreren en om elke gebruiker toe te staan een geregistreerde gegevensasset te verwijderen. De beheermogelijkheden die in de Standard-editie van Azure Data Catalog zijn opgenomen, bieden aanvullende opties om eigenaar te worden van assets, te beperken wie er assets kunnen detecteren en te beperken wie er assets kunnen verwijderen.

## <a name="summary"></a>Samenvatting

In deze zelfstudie hebt u essentiële mogelijkheden van Azure Data Catalog verkend, inclusief het registreren, annoteren, detecteren en beheren van zakelijke gegevensassets. Nu u de zelfstudie hebt voltooid, is het tijd om aan de slag te gaan. U kunt vandaag nog beginnen de gegevensbronnen waarop u en uw team vertrouwen te registreren en collega's uit te nodigen om de catalogus te gebruiken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ondersteunde gegevensbronnen](data-catalog-dsr.md)
