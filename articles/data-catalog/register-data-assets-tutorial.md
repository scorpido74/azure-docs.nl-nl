---
title: Gegevensassets registreren in Azure Data Catalog
description: In deze zelfstudie wordt beschreven hoe u gegevenselementen registreert in uw Azure-gegevenscatalogus.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68950233"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Zelfstudie: Gegevenselementen registreren in Azure-gegevenscatalogus

In deze zelfstudie gebruikt u het registratiehulpprogramma om gegevenselementen uit het Azure SQL-databasevoorbeeld te registreren bij de catalogus. Registratie is het proces waarbij belangrijke structurele metagegevens, zoals namen, typen en locaties, worden geëxtraheerd uit de gegevensbron en bijbehorende assets, en worden gekopieerd naar de catalogus. De gegevensbronnen en gegevensassets blijven waar ze zijn, maar de metagegevens worden door de catalogus gebruikt om de gegevensbronnen en gegevensassets eenvoudiger te detecteren en te begrijpen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Gegevensassets registreren 
> * Zoekgegevensassets
> * Aantekeningen toevoegen aan gegevensassets
> * Verbinding maken met gegevensassets
> * Gegevensassets beheren
> * Gegevenselementen verwijderen

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, moet u de [quickstart](register-data-assets-tutorial.md)voltooien.

* Een [Microsoft Azure-abonnement.](https://azure.microsoft.com/)
* U moet uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)hebben.

Als u gegevenscatalogus wilt instellen, moet u de eigenaar of mede-eigenaar zijn van een Azure-abonnement.

## <a name="register-data-assets"></a>Gegevensassets registreren

### <a name="register-a-data-source"></a>Een gegevensbron registreren

U registreert gegevenselementen (tabellen) uit een [Azure SQL-databasevoorbeeld,](../sql-database/sql-database-single-database-get-started.md)maar u elke ondersteunde gegevensbron gebruiken als u liever werkt met gegevens die vertrouwd en relevant zijn voor uw rol. Zie voor een lijst met ondersteunde gegevensbronnen [Ondersteunde gegevensbronnen](data-catalog-dsr.md).

De Azure SQL-databasenaam die we in deze zelfstudie gebruiken, is *RLSTest.*

U nu gegevenselementen registreren uit het Azure SQL-databasevoorbeeld met Azure Data Catalog.

1. Ga naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en selecteer Gegevens **publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. selecteer **Toepassing starten** om het registratiegereedschap op uw computer te downloaden, te installeren en uit te voeren.

   ![Azure Data Catalog - de knop Starten](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Selecteer **op** de welkomstpagina **Aanmelden** en voer uw referenties in.

    ![Azure Data Catalog - Welkomstpagina](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Selecteer **SQL Server** en **Volgende**op de pagina Microsoft Azure **Data Catalog** .

    ![Azure Data Catalog - gegevensbronnen](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Voer de SQL Server-verbindingseigenschappen in voor uw Azure SQL-databasevoorbeeld en selecteer **CONNECT**.

   ![Azure Data Catalog - SQL Server-verbindingsinstellingen](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registreer de metagegevens van uw gegevensasset. In dit voorbeeld registreert u **productobjecten** uit de bivaknaamruimte van de Azure SQL-database:

    1. Vouw in de structuur **Serverhiërarchie** het voorbeeld van uw Azure SQL-database uit en selecteer **SalesLT**.

    2. Selecteer **Product,** **Productcategorie,** **Productbeschrijving**en **Productmodel** met Ctrl+select.

    3. selecteer de pijl met**>** **verplaatsing** ( ). Met deze actie worden alle geselecteerde objecten verplaatst naar de lijst **Te registreren objecten**.

          ![Zelfstudie voor Azure Data Catalog - bladeren door objecten en objecten selecteren](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Selecteer **Preview opnemen** om een snapshotvoorbeeld van de gegevens op te nemen. De momentopname bevat maximaal 20 records uit elke tabel en wordt gekopieerd naar de catalogus.

    5. Selecteer **Gegevensprofiel opnemen** om een snapshot van de objectstatistieken op te nemen voor het gegevensprofiel (bijvoorbeeld: minimale, maximale en gemiddelde waarden voor een kolom, het aantal rijen).

    6. Voer in het veld **Tags toevoegen** **verkoop, product, azure sql**in. Met deze actie worden zoektags voor deze gegevensassets toegevoegd. Tags zijn voor gebruikers een uitstekende manier om een geregistreerde gegevensbron te zoeken.

    7. Geef de naam op van een **expert** met betrekking tot deze gegevens (optioneel).

          ![Zelfstudie voor Azure Data Catalog - te registreren objecten](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Selecteer **REGISTER**. Azure Data Catalog registreert uw geselecteerde objecten. In deze oefening worden de geselecteerde objecten uit uw Azure SQL-databasevoorbeeld geregistreerd. Het hulpprogramma voor registratie extraheert metagegevens uit de gegevensasset en kopieert deze gegevens naar de Azure Data Catalog-catalogus. De gegevens blijven waar het momenteel blijft. De gegevens blijven onder controle van de beheerders en het beleid van het oorsprongssysteem.

          ![Azure Data Catalog - geregistreerde objecten](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Als u uw geregistreerde gegevensbronobjecten wilt zien, selecteert u **Portal weergeven**. Controleer in de azure-gegevenscatalogusportal of u alle vier de tabellen en de database in de rasterweergave ziet (controleer of de zoekbalk leeg is).

        ![Objecten in de Azure Data Catalog-portal](media/register-data-assets-tutorial/data-catalog-view-portal.png)

In deze oefening registreerde u objecten uit het Azure SQL-databasevoorbeeld, zodat ze eenvoudig kunnen worden gedetecteerd door gebruikers in uw hele organisatie.

In de volgende oefening leert u hoe u geregistreerde gegevensassets kunt detecteren.

## <a name="discover-data-assets"></a>Gegevensassets detecteren

Voor detectie in Azure Data Catalog worden twee primaire mechanismen gebruikt: zoeken en filteren.

Zoeken is zowel intuïtief als krachtig. Standaard worden de zoektermen vergeleken met een eigenschap in de catalogus, met inbegrip van door de gebruiker gemaakte aantekeningen.

Filteren is bedoeld als aanvulling op het zoeken. U kunt specifieke kenmerken, zoals experts, gegevensbrontype, objecttype en tags, selecteren om overeenkomende gegevensassets weer te geven en om uw zoekresultaten te beperken tot overeenkomende assets.

Door een combinatie van zoeken en filteren te gebruiken, u snel navigeren in de gegevensbronnen die zijn geregistreerd bij Azure Data Catalog.

In deze oefening gebruikt u de Azure Data Catalog-portal voor het detecteren van de gegevensassets die u in de vorige stap hebt geregistreerd. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor meer informatie over de zoeksyntaxis.

Hieronder volgen enkele voorbeelden van het detecteren van gegevensassets in de catalogus.  

### <a name="discover-data-assets-with-basic-search"></a>Gegevensassets met een basiszoekopdracht detecteren

Met een basiszoekopdracht kunt u in een catalogus zoeken met behulp van een of meer zoektermen. De resultaten die worden opgehaald, zijn assets die wat betreft een willekeurige eigenschap overeenkomen met een of meer van de opgegeven voorwaarden.

1. selecteer **Start** in de Azure Data Catalog-portal. Als u de webbrowser hebt gesloten, gaat u naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Typ `product` in het zoekvak en druk op **ENTER**.

    ![Azure Data Catalog - basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Controleer of u alle vier de tabellen en de database in de resultaten ziet. U schakelen tussen **rasterweergave** en **lijstweergave** door knoppen op de werkbalk te selecteren, zoals in de volgende afbeelding wordt weergegeven. U ziet dat het zoekwoord wordt gemarkeerd in de zoekresultaten. Dit gebeurt omdat de optie **Markeren** is **ingeschakeld**. U kunt ook het aantal **resultaten per pagina** in zoekresultaten opgeven.

    ![Azure Data Catalog - resultaten van basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Het deelvenster **Zoekopdrachten** bevindt zich aan de linkerkant. Het deelvenster **Eigenschappen** bevindt zich aan de rechterkant. In het deelvenster **Zoekopdrachten** kunt u zoekcriteria wijzigen en resultaten filteren. In het deelvenster **Eigenschappen** worden de eigenschappen van een geselecteerd object weergegeven in het raster of de lijst.

4. selecteer **Product** in de zoekresultaten. selecteer de tabbladen **Voorbeeld**, **Kolommen,** **Gegevensprofiel**en **Documentatie** of selecteer de pijl om het onderste deelvenster uit te vouwen.  

    ![Azure Data Catalog - onderste deelvenster](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Op het tabblad **Preview** ziet u een voorbeeld van de gegevens in de tabel **Product**.  
5. selecteer het tabblad **Kolommen** om details over kolommen (zoals **naam** en **gegevenstype)** in het gegevenselement te vinden.

6. selecteer het tabblad **Gegevensprofiel** om de profilering van gegevens te zien (bijvoorbeeld: aantal rijen, grootte van gegevens of minimumwaarde in een kolom) in het gegevenselement.

### <a name="discover-data-assets-with-property-scoping"></a>Gegevens detecteren door het bereik van eigenschappen te definiëren

Door het bereik van eigenschappen te definiëren, kunt u gegevensassets detecteren waarbij de zoekterm overeenkomt met de opgegeven eigenschap.

1. Schakel het filter **Tabel** onder **Objecttype** in **Filters** uit.  

2. Typ `tags:product` in het zoekvak en druk op **ENTER**. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor alle eigenschappen die u kunt gebruiken voor het zoeken in de gegevenscatalogus.

3. Controleer of u de tabellen en de database in de resultaten ziet.  

    ![Data Catalog - resultaten van het definiëren van het bereik van eigenschappen](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>De zoekopdracht opslaan

1. Voer in het deelvenster **Zoekopdrachten** in de sectie **Huidig zoeken** een naam in voor de zoekopdracht en selecteer **Opslaan**.

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

3. Druk op **ENTER**.

4. Controleer of u de tabellen **Product,** **Productcategorie**en **ProductDescription** en de Azure SQL-database ziet die u hebt geregistreerd in zoekresultaten.

    ![Azure Data Catalog - vergelijking van zoekresultaten](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zie [Gegevensassets ontdekken](data-catalog-how-to-discover.md) voor gedetailleerde informatie over het ontdekken van gegevensassets. Zie [Syntaxis](/rest/api/datacatalog/#search-syntax-reference)van de zoekfunctie voor zoeken naar zoeken voor meer informatie over de syntaxis van zoeken naar gegevens .

## <a name="annotate-data-assets"></a>Aantekeningen toevoegen aan gegevensassets

In deze oefening gebruikt u de Azure Data Catalog-portal om bestaande gegevenselementen in de catalogus te annoteren (informatie toevoegen, zoals beschrijvingen, tags of experts). De annotaties vullen de structurele metagegevens aan die tijdens de registratie uit de gegevensbron zijn gehaald. Annotatie maakt de gegevensactiva veel gemakkelijker te ontdekken en te begrijpen.

In deze oefening maakt u aantekeningen voor een enkele gegevensasset (ProductPhoto). U voegt een beschrijvende naam en een beschrijving toe aan de gegevensasset ProductPhoto.  

1. Ga naar de startpagina van Azure `tags:product` Data [Catalog](https://www.azuredatacatalog.com) en zoek met de gegevenselementen die u hebt geregistreerd.

2. **selecteer ProductModel** in zoekresultaten.  

3. Voer **Productafbeeldingen** in voor **Beschrijvende naam** en **Productfoto’s voor marketingmateriaal** voor de **Beschrijving**.

    ![Azure Data Catalog - beschrijving van ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    De **Beschrijving** helpt anderen om de geselecteerde gegevensasset te detecteren en te begrijpen waarom en hoe ze deze kunnen gebruiken. U kunt ook meer tags toevoegen en kolommen weergeven. U gegevensbronnen zoeken en filteren met behulp van de beschrijvende metagegevens die u aan de catalogus hebt toegevoegd.

U ook de volgende stappen op deze pagina uitvoeren:

* Experts toevoegen voor de gegevensasset. selecteer **Toevoegen** in het gebied **Experts.**

* Tags toevoegen op het niveau van de gegevensset. selecteer **Toevoegen** in het gebied **Labels.** Een label kan een gebruikerstag of een woordenlijsttag zijn. De Standard-editie van Data Catalog bevat een zakelijke woordenlijst waarmee catalogusbeheerders een centrale zakelijke taxonomie kunnen definiëren. Catalogusgebruikers kunnen vervolgens aantekeningen toevoegen aan gegevensassets met behulp van termen in de woordenlijst. Zie Voor meer informatie [hoe u de bedrijfswoordenlijst voor beheerde tagging](data-catalog-how-to-business-glossary.md) instellen

* Tags toevoegen op kolomniveau. selecteer **Toevoegen** onder **Labels** voor de kolom die u wilt annoteren.

* Beschrijving toevoegen op kolomniveau. Voer een **Beschrijving** in voor een kolom. U kunt ook de metagegevens van de beschrijving bekijken die uit de gegevensbron zijn geëxtraheerd.

* Informatie over **Verzoeken tot toegang** toevoegen die gebruikers laat zien hoe ze toegang kunnen krijgen tot de gegevensasset.
  
* Ga naar het tabblad **Documentatie** en verstrek hier documentatie over de gegevensasset. Met Azure Data Catalog -documentatie kunt u de gegevenscatalogus gebruiken als een opslagplaats voor inhoud om een volledig overzicht te maken van uw gegevensassets.
  
U kunt ook een aantekening toevoegen aan meerdere gegevensassets. U kunt bijvoorbeeld alle geregistreerde gegevensassets selecteren en er een expert voor opgeven.

![Azure Data Catalog - aantekeningen toevoegen aan meerdere gegevensassets](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog ondersteunt een benadering voor aantekeningen via crowdsourcing. Elke gebruiker van de gegevenscatalogus kan tags (gebruiker of woordenlijst), beschrijvingen en andere metagegevens toevoegen. Door dit te doen, gebruikers toevoegen perspectief op een data-asset en het gebruik ervan, en delen dat perspectief met andere gebruikers.

Zie [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-how-to-annotate.md) voor gedetailleerde informatie over het toevoegen van aantekeningen aan gegevens.

## <a name="connect-to-data-assets"></a>Verbinding maken met gegevensassets

In deze oefening opent u gegevensassets in geïntegreerde clienthulpprogramma's en een niet-geïntegreerd hulpprogramma (SQL Server Management Studio) met behulp van verbindingsgegevens.

> [!NOTE]
> Het is belangrijk om te weten dat Azure Data Catalog u geen toegang geeft tot de werkelijke gegevensbron. Het wordt gewoon eenvoudiger voor u om ze te detecteren en te begrijpen. Wanneer u verbinding maakt met een gegevensbron, worden uw Windows-referenties gebruikt of wordt u indien nodig om referenties gevraagd. Als u niet eerder toegang hebt gekregen tot de gegevensbron, moet aan u toegang worden verleend voordat u verbinding kunt maken.

### <a name="connect-to-a-data-asset-from-excel"></a>Verbinding maken met een gegevensasset vanuit Excel

1. Selecteer in de zoekresultaten de optie **Product**. selecteer **Openen op** de werkbalk en selecteer **Excel**.

    ![Azure Data Catalog - verbinding maken met gegevensasset](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selecteer **Openen** in het pop-upvenster downloaden. Deze ervaring kan variëren afhankelijk van de browser.

3. Selecteer in het venster **beveiligingsbericht van Microsoft Excel** de optie **Inschakelen**.

    ![Azure Data Catalog - beveiligingspop-upvenster van Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Bewaar de standaardinstellingen in het dialoogvenster **Gegevens importeren** en selecteer **OK**.

    ![Azure Data Catalog - Excel-gegevens importeren](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Bekijk de gegevensbron in Excel.

    ![Azure Data Catalog - producttabel in Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

In deze oefening hebt u verbinding gemaakt met gegevensasssets die zijn gedetecteerd via Azure Data Catalog. Via de portal van Azure Data Catalog kunt u direct verbinding maken via de clienttoepassingen die zijn geïntegreerd in het menu **Openen in**. U kunt ook verbinding maken met behulp van een andere toepassing naar keuze. Hierbij wordt gebruikgemaakt van de locatiegegevens van de verbinding in de metagegevens van de asset. U SQL Server Management Studio bijvoorbeeld gebruiken om verbinding te maken met de Azure SQL-database om toegang te krijgen tot de gegevens in de gegevenselementen die in deze zelfstudie zijn geregistreerd.

1. Open **SQL Server Management Studio**.

2. Voer in het dialoogvenster **Verbinding maken met server** de naam in van de server uit het deelvenster **Eigenschappen** in de Azure Data Catalog-portal.

3. Gebruik de juiste verificatieprocedure en aanmeldingsgegevens voor toegang tot de gegevensasset. Gebruik de informatie in het veld **Verzoeken tot toegang** als u geen toegang hebt.

    ![Azure Data Catalog - verzoeken tot toegang](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selecteer **Verbindingstekenreeksen weergeven** om ADO.NET- ODBC- en OLEDB-verbindingstekenreeksen naar het klembord te weergeven en te kopiëren voor gebruik in uw toepassing.

## <a name="manage-data-assets"></a>Gegevensassets beheren

In deze stap ziet u hoe u beveiliging kunt instellen voor uw gegevensassets. Data Catalog geeft gebruikers geen toegang tot de gegevens zelf. De eigenaar van de gegevensbron bepaalt de toegang tot gegevens.

Met Data Catalog kunt u gegevensbronnen detecteren en de metagegevens bekijken die gerelateerd zijn aan de bronnen die in de catalogus zijn geregistreerd. Mogelijk zijn er echter situaties echter waarin gegevensbronnen alleen zichtbaar moeten zijn voor specifieke gebruikers of specifieke groepen. Voor deze scenario's u Gegevenscatalogus gebruiken om eigenaar te worden van geregistreerde gegevensassets en de zichtbaarheid van de activa die u bezit te beheren.

> [!NOTE]
> De beheermogelijkheden die in deze taak worden beschreven, zijn alleen beschikbaar in de Standard-editie van Azure Data Catalog, niet in de gratis editie.
> In Azure Data Catalog kunt u eigenaar worden van gegevensassets, mede-eigenaren aan gegevensassets toevoegen en de zichtbaarheid van gegevensassets instellen.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Eigenaar worden van gegevensassets en de zichtbaarheid beperken

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com). Typ `tags:cycles` in het tekstvak **Zoeken** en druk op **ENTER**.

2. selecteer een item in de resultatenlijst en selecteer **Eigendom nemen** op de werkbalk.

3. Selecteer **Eigendom overnemen**in de sectie **Beheer** van het deelvenster **Eigenschappen** .

    ![Azure Data Catalog - eigenaar worden](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Als u de zichtbaarheid wilt beperken, kiest u **Eigenaren & deze gebruikers** in de sectie **Zichtbaarheid** en selecteert u **Toevoegen**. Voer in het tekstvak de e-mailadressen van de betreffende gebruikers in en druk op **ENTER**.

    ![Azure Data Catalog - toegang beperken](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Gegevensassets verwijderen

In deze oefening gebruikt u de portal van Azure Data Catalog om voorbeeldgegevens te verwijderen uit geregistreerde gegevensassets en om gegevensassets te verwijderen uit de catalogus.

In Azure Data Catalog kunt u een individuele asset of meerdere assets verwijderen.

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Voer **Search** enter `tags:cycles` in het tekstvak Zoeken in en selecteer **ENTER**.

3. Selecteer een item in de resultatenlijst en selecteer **Verwijderen** op de werkbalk zoals weergegeven in de volgende afbeelding:

    ![Azure Data Catalog - rasteritem verwijderen](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Als u de lijstweergave gebruikt, bevindt het selectievakje zich links van het item, zoals in de volgende afbeelding wordt weergegeven:

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
