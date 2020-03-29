---
title: Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken
description: Meer informatie over het gebruik van Azure Data Lake Tools voor Visual Studio Code voor het maken, testen en uitvoeren van U-SQL-scripts.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60508891"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken

Lees in dit artikel hoe u Azure Data Lake Tools for Visual Studio Code (VS-code) gebruiken om U-SQL-scripts te maken, te testen en uit te voeren. De informatie wordt ook behandeld in de volgende video:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Vereisten

Azure Data Lake Tools voor VS Code ondersteunt Windows, Linux en macOS.U-SQL local run en local debug werkt alleen in Windows.

- [Visual Studio-code](https://www.visualstudio.com/products/code-vs.aspx)

Voor MacOS en Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake-hulpprogramma's installeren

Nadat u de vereisten hebt geïnstalleerd, u Azure Data Lake Tools voor VS-code installeren.

**Azure Data Lake-hulpprogramma's installeren**

1. Open Visual Studio Code.
2. Selecteer **Extensies** in het linkerdeelvenster. Voer **Azure Data Lake Tools** in het zoekvak in.
3. Selecteer **Installeren** naast **Azure Data Lake Tools**. 

   ![Selecties voor het installeren van Data Lake-hulpprogramma's](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Na enkele seconden wordt de knop **Installeren** gewijzigd in **Opnieuw laden.**
4. Selecteer **Opnieuw laden** om de extensie Azure Data Lake Tools **te** activeren.
5. Selecteer **Venster opnieuw laden** om te bevestigen. U **Azure Data Lake Tools zien** in het deelvenster **Extensies.**

 
## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake-hulpprogramma's activeren
Maak een .usql-bestand of open een bestaand .usql-bestand om de extensie te activeren. 


## <a name="work-with-u-sql"></a>Werken met U-SQL

Als u met U-SQL wilt werken, moet u een U-SQL-bestand of een map openen.

**Het voorbeeldscript openen**

Open het opdrachtpalet (Ctrl+Shift+P) en voer **ADL in: Voorbeeldscript openen**. Hiermee wordt een ander voorbeeld van dit voorbeeld geopend. U ook een script bewerken, configureren en verzenden in dit exemplaar.

**Een map openen voor uw U-SQL-project**

1. Selecteer in Visual Studio Code het menu **Bestand** en selecteer **Map openen**.
2. Geef een map op en selecteer **Map selecteren**.
3. Selecteer het menu **Bestand** en selecteer **Nieuw**. Er wordt een Untitled-1-bestand aan het project toegevoegd.
4. Voer de volgende code in het bestand Untitled-1 in:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Het script maakt een bestand van departments.csv met enkele gegevens in de map /output.

5. Sla het bestand op als **myUSQL.usql** in de geopende map.

**Een U-SQL-script compileren**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2. Voer **ADL: Compileren Script**. De compileerresultaten worden weergegeven in het venster **Uitvoer.** U ook met de rechtermuisknop op een scriptbestand klikken en vervolgens **ADL: Compileren script** selecteren om een U-SQL-taak samen te stellen. Het compilatieresultaat wordt weergegeven in het deelvenster **Uitvoer.**
 
**Een U-SQL-script verzenden**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2. Voer **ADL in: Taak verzenden**. U ook met de rechtermuisknop op een scriptbestand klikken en vervolgens **ADL: Taak verzenden selecteren.** 

Nadat u een U-SQL-taak hebt verzonden, worden de indieningslogboeken weergegeven in het venster **Uitvoer** in VS-code. De taakweergave wordt in het rechterdeelvenster weergegeven. Als de indiening succesvol is, wordt de url van de taak ook weergegeven. U de taak-URL openen in een webbrowser om de status van de realtime taak bij te houden. 

Op het tabblad **OVERZICHT** van de taakweergave ziet u de taakgegevens. De belangrijkste functies zijn het opnieuw indienen van een script, het dupliceren van een script en openen in de portal. Op het tabblad **GEGEVENS** van de taakweergave u verwijzen naar de invoerbestanden, uitvoerbestanden en bronbestanden. Bestanden kunnen worden gedownload naar de lokale computer.

![Tabblad Overzicht in de taakweergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Tabblad Gegevens in de taakweergave](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**De standaardcontext instellen**

U de standaardcontext instellen om deze instelling toe te passen op alle scriptbestanden als u geen parameters voor bestanden afzonderlijk hebt ingesteld.

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2. Voer **ADL in: Stel standaardcontext in**. Of klik met de rechtermuisknop op de scripteditor en selecteer **ADL: Standaardcontext instellen**.
3. Kies het gewenste account, database en schema. De instelling wordt opgeslagen in het configuratiebestand xxx_settings.json.

   ![Account, database en schemaset als standaardcontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Scriptparameters instellen**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2. Voer **ADL in: Scriptparameters instellen**.
3. Het bestand xxx_settings.json wordt geopend met de volgende eigenschappen:

   - **account:** een Azure Data Lake Analytics-account onder uw Azure-abonnement dat nodig is om U-SQL-taken samen te stellen en uit te voeren. U moet het computeraccount configureren voordat u U-SQL-taken compileert en uitvoert.
   - **database**: Een database onder uw account. De standaardinstelling is **stramien**.
   - **schema**: Een schema onder uw database. De standaard instelling is **dbo**.
   - **optioneelInstellingen:**
        - **prioriteit**: Het prioriteitsbereik is 1 tot 1000, met 1 als hoogste prioriteit. De standaardwaarde is **1000**.
        - **mateOfParallelisme**: Het parallellisme bereik is van 1 tot 150. De standaardwaarde is de maximale parallellisme die is toegestaan in uw Azure Data Lake Analytics-account. 

   ![Inhoud van het JSON-bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Nadat u de configuratie hebt opgeslagen, worden de account-, database- en schemagegevens weergegeven op de statusbalk in de linkerbenedenhoek van het bijbehorende .usql-bestand als u geen standaardcontext hebt ingesteld.

**Git negeren instellen**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2. Voer **ADL in: Git negeren instellen**.

   - Als u geen **.gitIgnore-bestand** in uw vs-code-werkmap hebt, wordt er een bestand met de naam **.gitIgnore** gemaakt in uw map. Er worden standaard vier items (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) in het bestand toegevoegd. U indien nodig meer updates uitvoeren.
   - Als u al een **.gitIgnore-bestand** in uw VS-code-map hebt, voegt het hulpprogramma vier items **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj)** toe aan uw **.gitIgnore-bestand** als de vier items niet in het bestand zijn opgenomen.

   ![Items in het bestand .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Werken met code-behind bestanden: C Sharp, Python en R

Azure Data Lake Tools ondersteunt meerdere aangepaste codes. Zie [U-SQL ontwikkelen met Python, R en C Sharp voor Azure Data Lake Analytics in VS-code voor](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)instructies.

## <a name="work-with-assemblies"></a>Werken met assemblages

Zie [U-SQL-samenstellingen ontwikkelen voor Azure Data Lake Analytics-taken voor](data-lake-analytics-u-sql-develop-assemblies.md)informatie over het ontwikkelen van assemblages.

U Data Lake Tools gebruiken om aangepaste codeverzamelingen te registreren in de Data Lake Analytics-catalogus.

**Een vergadering registreren**

U de assemblage registreren via de **ADL: Register Assembly** of **ADL: Register Assembly (Advanced)** opdracht.

**Registreren via de opdracht ADL: Assemblee registreren**
1.  Selecteer Ctrl+Shift+P om het opdrachtpalet te openen.
2.  Voer **ADL: Register Assembly**. 
3.  Geef het lokale assemblagepad op. 
4.  Selecteer een Data Lake Analytics-account.
5.  Selecteer een database.

Het portaal wordt geopend in een browser en geeft het assemblageregistratieproces weer.  

Een handigere manier om de opdracht **ADL: Register Assembly** te activeren, is door met de rechtermuisknop op het .dll-bestand in Verkenner te klikken. 

**Registreren via de opdracht ADL: Register Assembly (Advanced)**
1.  Selecteer Ctrl+Shift+P om het opdrachtpalet te openen.
2.  Voer **ADL: Register Assembly (Advanced)**. 
3.  Geef het lokale assemblagepad op. 
4.  Het JSON-bestand wordt weergegeven. Controleer en bewerk indien nodig de afhankelijkheden en resourceparameters van de assemblage. Instructies worden weergegeven in het venster **Uitvoer.** Als u naar de assemblageregistratie wilt gaan, slaat u (Ctrl+S) het JSON-bestand op.

    ![JSON-bestand met assemblageafhankelijkheden en resourceparameters](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools detecteert automatisch of de DLL afhankelijkheden voor assemblage smaakt. De afhankelijkheden worden weergegeven in het JSON-bestand nadat ze zijn gedetecteerd. 
>- U uw DLL-resources (bijvoorbeeld .txt, .png en .csv) uploaden als onderdeel van de assemblageregistratie. 

Een andere manier om de **opdracht ADL: Register Assembly (Advanced)** te activeren, is door met de rechtermuisknop op het .dll-bestand in Verkenner te klikken. 

De volgende U-SQL-code laat zien hoe u een assemblage aanroepen. In het monster wordt de verzamelnaam *getest.*


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>U-SQL local run en lokale foutopsporing voor Windows-gebruikers gebruiken
U-SQL local run test uw lokale gegevens en valideert uw script lokaal voordat uw code wordt gepubliceerd naar Data Lake Analytics. U de lokale foutopsporingsfunctie gebruiken om de volgende taken te voltooien voordat uw code wordt verzonden naar Data Lake Analytics: 
- Debug je C# code-behind. 
- Stap door de code. 
- Valideer uw script lokaal.

De lokale run- en lokale foutopsporingsfunctie werkt alleen in Windows-omgevingen en wordt niet ondersteund op macOS- en Linux-gebaseerde besturingssystemen.

Zie [U-SQL local run en local debug met Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)voor instructies over lokale run en lokale foutopsporing.


## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voordat u U-SQL-scripts compileren en uitvoeren in Data Lake Analytics, moet u verbinding maken met uw Azure-account.

<b id="sign-in-by-command">Verbinding maken met Azure met een opdracht</b>

1.  Selecteer Ctrl+Shift+P om het opdrachtpalet te openen. 
2.  Voer **ADL in: Log in.** De inloggegevens verschijnen rechtsonder.

    ![De opdracht Aanmelding invoeren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Melding over aanmelding en verificatie](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Selecteer **& openen kopiëren** om de [aanmeldingswebpagina](https://aka.ms/devicelogin)te openen . Plak de code in het vak en selecteer **Doorgaan**.

    ![Opmeldwebpagina](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Volg de instructies om in te loggen vanaf de webpagina. Wanneer u verbonden bent, wordt uw Azure-accountnaam weergegeven op de statusbalk in de linkerbenedenhoek van het VS-codevenster. 

> [!NOTE] 
>- Data Lake Tools meldt u automatisch in de volgende keer als u zich niet afmeldt.
>- Als uw account twee factoren heeft ingeschakeld, raden we u aan telefoonverificatie te gebruiken in plaats van een pincode te gebruiken.


Als u zich wilt afmelden, voert u de opdracht **ADL: Afmelden in.**

**Verbinding maken met Azure vanuit de verkenner**

Azure **DATALAKE**uitvouwen, **aanmelden bij Azure**selecteren en vervolgens stap 3 en stap 4 van Verbinding maken met Azure volgen met een [opdracht](#sign-in-by-command).

![Selectie 'Aanmelden bij Azure' in de verkenner](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Je je niet afmelden bij de ontdekkingsreiziger. Zie [Verbinding maken met Azure met een opdracht](#sign-in-by-command)om u af te melden.


## <a name="create-an-extraction-script"></a>Een extractiescript maken 
U een extractiescript maken voor .csv-, .tsv- en .txt-bestanden met behulp van de opdracht **ADL: EXTRACT Script maken** of vanuit de Azure Data Lake-verkenner.

**Een extractiescript maken met een opdracht**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen en voer **ADL in: EXTRACT Script maken**.
2. Geef het volledige pad op voor een Azure Storage-bestand en selecteer de Enter-toets.
3. Selecteer één account.
4. Selecteer een scheidingsteken om het bestand te extraheren voor een .txt-bestand. 

![Proces voor het maken van een extractiescript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Het extractiescript wordt gegenereerd op basis van uw vermeldingen. Voor een script dat de kolommen niet kan detecteren, kiest u een van de twee opties. Zo niet, dan wordt er slechts één script gegenereerd.

![Resultaat van het maken van een extractiescript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Een extractiescript maken vanuit de verkenner**

Een andere manier om het extractiescript te maken, is via het menu met de rechtermuisknop (snelkoppeling) in het csv-, .tsv- of .txt-bestand in Azure Data Lake Store of Azure Blob-opslag.

![Opdracht 'EXTRACTscript maken' in het snelmenu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integreren met Azure Data Lake Analytics via een opdracht

U hebt toegang tot Azure Data Lake Analytics-bronnen om accounts weer te geven, metagegevens te openen en analytics-taken weer te geven. 

**De Azure Data Lake Analytics-accounts weergeven onder uw Azure-abonnement**

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen.
2. Voer **ADL in: Accounts weergeven**. De accounts worden weergegeven in het deelvenster **Uitvoer.**

**Toegang krijgen tot azure Data Lake Analytics-metagegevens**

1.  Selecteer Ctrl+Shift+P en voer **ADL in: Lijsttabellen**.
2.  Selecteer een van de Data Lake Analytics-accounts.
3.  Selecteer een van de Data Lake Analytics-databases.
4.  Selecteer een van de schema's. U de lijst met tabellen bekijken.

**Azure Data Lake Analytics-taken weergeven**
1.  Open het opdrachtpalet (Ctrl+Shift+P) en selecteer **ADL: Taken weergeven**. 
2.  Selecteer een Data Lake Analytics of een lokaal account. 
3.  Wacht tot de lijst met vacatures wordt weergegeven voor het account.
4.  Selecteer een taak in de lijst met vacatures. Data Lake Tools opent de taakweergave in het rechterdeelvenster en geeft bepaalde informatie weer in de VS-code-uitvoer.

    ![Lijst met vacatures](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integreren met Azure Data Lake Store via een opdracht

U opdrachten met betrekking tot Azure Data Lake Store gebruiken om:
 - [Blader door de Azure Data Lake Store-bronnen](#list-the-storage-path) 
 - [Een voorbeeld van het Azure Data Lake Store-bestand bekijken](#preview-the-storage-file) 
 - Het bestand rechtstreeks uploaden naar Azure Data Lake Store in VS-code
 - Het bestand rechtstreeks downloaden vanuit Azure Data Lake Store in VS-code

### <a name="list-the-storage-path"></a>Het opslagpad weergeven 

**Het opslagpad weergeven in het opdrachtpalet**

1. Klik met de rechtermuisknop op de scripteditor en selecteer **ADL: Lijstpad**.
2. Kies de map in de lijst of selecteer **Een pad invoeren** of Bladeren vanaf het **hoofdpad**. (We gebruiken **Een pad invoeren** als voorbeeld.) 
3. Selecteer uw Data Lake Analytics-account.
4. Blader naar of voer het pad van de opslagmap in (bijvoorbeeld /output/).  

Het opdrachtpalet bevat de padgegevens op basis van uw vermeldingen.

![Resultaten van opslagpaden](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Een handigere manier om het relatieve pad weer te geven is via het snelmenu.

**Het opslagpad weergeven via het snelmenu**

Klik met de rechtermuisknop op de padtekenreeks en selecteer **Lijstpad**.

!['Pad weergeven' in het snelmenu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Een voorbeeld van het opslagbestand bekijken

1. Klik met de rechtermuisknop op de scripteditor en selecteer **ADL: voorbeeldbestand**.
2. Selecteer uw Data Lake Analytics-account. 
3. Voer een azure-bestandspad in (bijvoorbeeld /output/SearchLog.txt). 

Het bestand wordt geopend in VS-code.

![Stappen en resultaat voor het bekijken van het opslagbestand](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Een andere manier om een voorbeeld van het bestand te bekijken, is via het snelmenu op het volledige pad van het bestand of het relatieve pad van het bestand in de scripteditor. 

### <a name="upload-a-file-or-folder"></a>Een bestand of map uploaden

1. Klik met de rechtermuisknop op de scripteditor en selecteer **Bestand uploaden** of **Map uploaden**.
2. Kies één bestand of meerdere bestanden als u **Bestand uploaden**hebt geselecteerd of kies de hele map als u **Map uploaden**hebt geselecteerd. Selecteer vervolgens **Uploaden**. 
3. Kies de opslagmap in de lijst of selecteer **Een pad invoeren** of Bladeren vanaf het **hoofdpad**. (We gebruiken **Een pad invoeren** als voorbeeld.) 
4. Selecteer uw Data Lake Analytics-account. 
5. Blader naar of voer het pad van de opslagmap in (bijvoorbeeld /output/). 
6. Selecteer **Huidige map kiezen** om uw uploadbestemming op te geven.

![Stappen en resultaat voor het uploaden van een bestand of map](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Een andere manier om bestanden naar opslag te uploaden is via het snelmenu op het volledige pad van het bestand of het relatieve pad van het bestand in de scripteditor.

U [de uploadstatus controleren.](#check-storage-tasks-status)


### <a name="download-a-file"></a>Bestand downloaden 
U een bestand downloaden met behulp van de opdracht **ADL: Download Bestand** of **ADL: Download Bestand (Geavanceerd)**.

**Een bestand downloaden via de opdracht ADL: Download File (Advanced)**
1. Klik met de rechtermuisknop op de scripteditor en selecteer **Bestand downloaden (geavanceerd)**.
2. VS-code geeft een JSON-bestand weer. U bestandspaden invoeren en meerdere bestanden tegelijk downloaden. Instructies worden weergegeven in het venster **Uitvoer.** Als u het bestand of de bestanden wilt downloaden, slaat u het JSON-bestand op (Ctrl+S).

    ![JSON-bestand met paden voor het downloaden van bestanden](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

In het venster **Uitvoer** wordt de status van het downloaden van bestanden weergegeven.

![Uitvoervenster met downloadstatus](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

U [de downloadstatus controleren.](#check-storage-tasks-status)

**Een bestand downloaden via de opdracht ADL: Bestands downloaden**

1. Klik met de rechtermuisknop op de scripteditor, selecteer **Bestand downloaden**en selecteer vervolgens de doelmap in het dialoogvenster **Map selecteren.**
2. Kies de map in de lijst of selecteer **Een pad invoeren** of Bladeren vanaf het **hoofdpad**. (We gebruiken **Een pad invoeren** als voorbeeld.) 
3. Selecteer uw Data Lake Analytics-account. 
4. Blader naar of voer het pad van de opslagmap in (bijvoorbeeld /output/) en kies vervolgens een bestand dat u wilt downloaden.

![Stappen en resultaat voor het downloaden van een bestand](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Een andere manier om opslagbestanden te downloaden is via het snelmenu op het volledige pad van het bestand of het relatieve pad van het bestand in de scripteditor.

U [de downloadstatus controleren.](#check-storage-tasks-status)

### <a name="check-storage-tasks-status"></a>De status van opslagtaken controleren
De upload- en downloadstatus wordt weergegeven op de statusbalk. Selecteer de statusbalk en vervolgens wordt de status weergegeven op het tabblad **UITVOER.**

![Statusbalk en uitvoerdetails](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integreren met Azure Data Lake Analytics vanuit de verkenner

Nadat u zich hebt aangemeld, worden alle abonnementen voor uw Azure-account weergegeven in het linkerdeelvenster, onder **AZURE DATALAKE**. 

![Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Gegevens Lake Analytics-metagegevensnavigatie

Uw Azure-abonnement uitbreiden. Onder het knooppunt **U-SQL Databases** u door uw U-SQL-database bladeren en mappen bekijken zoals **Schema's,** **Referenties,** **Verzamelingen**, **Tabellen**en **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Beheer van gegevenslake analytics-metagegevens

**U-SQL-databases**uitbreiden . U een database, schema, tabel, tabeltype, index of statistiek maken door met de rechtermuisknop op het bijbehorende knooppunt te klikken en vervolgens Script te selecteren **om te maken** in het snelmenu. Bewerk het script op de geopende scriptpagina op basis van uw behoeften. Dien de taak vervolgens in door er met de rechtermuisknop op te klikken en **ADL: Job verzenden te**selecteren. 

Nadat u klaar bent met het maken van het item, klikt u met de rechtermuisknop op het knooppunt en selecteert u **Vernieuwen** om het item weer te geven. U het item ook verwijderen door er met de rechtermuisknop op te klikken en vervolgens **Verwijderen**te selecteren.

![Opdracht 'Script om te maken' in het snelmenu in de Data Lake-verkenner](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Scriptpagina voor het nieuwe item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registratie van Data Lake Analytics-assemblage

U een verzameling registreren in de bijbehorende database door met de rechtermuisknop op het knooppunt **Assembleies** te klikken en vervolgens **De assemblage register**te selecteren.

![Opdracht 'Assemblage registreren' in het snelmenu voor het knooppunt Assembleies](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integreren met Azure Data Lake Store vanuit de verkenner

Blader naar **Data Lake Store:**

- U met de rechtermuisknop op het mapknooppunt klikken en vervolgens de opdrachten **Vernieuwen,** **Verwijderen,** **Uploaden,** **Mappen uploaden,** **Relatief pad kopiëren**en Volledig pad **kopiëren** gebruiken in het snelmenu.

   ![Opdrachten voor snelmenu's voor een mapknooppunt in de Data Lake-verkenner](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- U met de rechtermuisknop op het bestandsknooppunt klikken en vervolgens de opdrachten **Voorbeeld**, **Downloaden**, **Verwijderen**, **EXTRACTscript maken** (alleen beschikbaar voor CSV-, TSV- en TXT-bestanden), Relatieve **pad kopiëren**en Volledige **pad kopiëren** in het snelmenu gebruiken.

   ![Opdrachten voor snelmenu's voor een bestandsknooppunt in de Data Lake-verkenner](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integreren met Azure Blob-opslag vanuit de verkenner

Blader naar Blob-opslag:

- U met de rechtermuisknop op het blobcontainerknooppunt klikken en vervolgens de opdrachten **Blobcontainer vernieuwen, Blobcontainer verwijderen**en **Blob uploaden** gebruiken in het snelmenu. **Refresh**

   ![Snelmenuopdrachten voor een blobcontainerknooppunt onder Blob-opslag](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- U met de rechtermuisknop op het mapknooppunt klikken en vervolgens de opdrachten Blob **vernieuwen** en **uploaden** gebruiken in het snelmenu.

   ![Opdrachten voor snelmenu's voor een mapknooppunt onder Blob-opslag](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- U met de rechtermuisknop op het bestandsknooppunt klikken en vervolgens de opdrachten **Voorbeeld/Bewerken**, **Downloaden**, **Verwijderen**, **EXTRACTScript maken** (alleen beschikbaar voor CSV-, TSV- en TXT-bestanden), Relatieve **pad kopiëren**en **Volledige padkopiëren** gebruiken in het snelmenu.

    ![Opdrachten voor snelmenu's voor een bestandsknooppunt onder Blob-opslag](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>De Data Lake-verkenner openen in de portal
1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen.
2. Voer **Open Web Azure Storage Explorer** in of klik met de rechtermuisknop op een relatief pad of het volledige pad in de scripteditor en selecteer Vervolgens Web Azure Storage Explorer **openen**.
3. Selecteer een Data Lake Analytics-account.

Data Lake Tools opent het Azure Storage-pad in de Azure-portal. U het pad vinden en een voorbeeld van het bestand bekijken vanaf het web.

## <a name="additional-features"></a>Extra functies

Data Lake Tools voor VS Code ondersteunt de volgende functies:

-   **IntelliSense automatisch aanvullen:** suggesties worden weergegeven in pop-upvensters rond items zoals trefwoorden, methoden en variabelen. Verschillende pictogrammen vertegenwoordigen verschillende soorten objecten:

    - Scala-gegevenstype
    - Complex gegevenstype
    - Ingebouwde UDT's
    - .NET-verzamelingen en -klassen
    - C#-expressies
    - Ingebouwde C# UDF's, UDO's en UDAAGs 
    - U-SQL-functies
    - U-SQL-vensterfuncties
 
    ![IntelliSense-objecttypen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense automatisch aanvullen op data lake analytics-metagegevens:** Data Lake Tools downloadt de gegevensmetagegevens van Data Lake Analytics lokaal. De IntelliSense-functie vult automatisch objecten uit de metagegevens van Data Lake Analytics. Deze objecten omvatten de database, schema, tabel, weergave, tabelwaardefunctie, procedures en C#-samenstellingen.
 
    ![IntelliSense-metagegevens](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Foutmarkering IntelliSense**: Data Lake Tools onderstreept bewerkingsfouten voor U-SQL en C#. 
-   **Syntaxishoogtepunten:** Hulpmiddelen voor Gegevensmeer gebruikt kleuren om items zoals variabelen, trefwoorden, gegevenstypen en functies te onderscheiden. 

    ![Syntaxis met verschillende kleuren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> We raden u aan een upgrade te geven naar Azure Data Lake Tools voor Visual Studio-versie 2.3.3000.4 of hoger. De vorige versies zijn niet meer beschikbaar om te downloaden en zijn nu afgeschaft.  
   
## <a name="next-steps"></a>Volgende stappen
- [U-SQL ontwikkelen met Python, R en C Sharp voor Azure Data Lake Analytics in VS-code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL local run en local debug met Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Zelfstudie: Aan de slag met Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Zelfstudie: U-SQL-scripts ontwikkelen met Behulp van Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
