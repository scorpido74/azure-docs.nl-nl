---
title: Micro Focus CICS BankDemo instellen voor Micro Focus Enterprise Developer 4.0 op Azure Virtual Machines
description: Voer de Micro Focus BankDemo-toepassing uit op Azure Virtual Machines (VM's) om te leren microfocus-enterpriseserver en enterprise ontwikkelaar te gebruiken.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411072"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Micro Focus CICS BankDemo instellen voor Micro Focus Enterprise Developer 4.0 op Azure

Wanneer u Micro Focus Enterprise Server 4.0 en Enterprise Developer 4.0 inStelt op Azure, u implementaties van IBM z/OS-workloads testen. In dit artikel ziet u hoe u CICS BankDemo instellen, een voorbeeldtoepassing die wordt geleverd met Enterprise Developer.

CICs staat voor Customer Information Control System, het transactieplatform dat wordt gebruikt door veel van de online mainframetoepassingen. De BankDemo-applicatie is ideaal om te leren hoe Enterprise Server en Enterprise Developer werken en hoe u een daadwerkelijke toepassing beheren en implementeren, compleet met green screen terminals.

> [!NOTE]
> Binnenkort beschikbaar: instructies voor het instellen van [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) op Azure VM's.

## <a name="prerequisites"></a>Vereisten

- Een VM met [Enterprise Developer](set-up-micro-focus-azure.md). Houd er rekening mee dat Enterprise Developer een volledig exemplaar van Enterprise Server heeft staan voor ontwikkelings- en testdoeleinden. Dit exemplaar is de instantie van Enterprise Server die wordt gebruikt voor de demo.

- [SQL Server 2017 Express-editie](https://www.microsoft.com/sql-server/sql-server-editions-express). Download en installeer het op de Enterprise Developer VM. Enterprise Server vereist een database voor het beheer van CICS-regio's en de BankDemo-toepassing maakt ook gebruik van een SQL Server-database genaamd BANKDEMO. In deze demo wordt ervan uitgegaan dat u SQL Server Express voor beide databases gebruikt. Selecteer bij het installeren de basisinstallatie.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS wordt gebruikt voor het beheren van de databases en het uitvoeren van een T-SQL-script. Download en installeer het op de Enterprise Developer VM.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) met het nieuwste servicepack of [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), dat je gratis downloaden.

- Rumba Desktop of een andere 3270 emulator.

## <a name="configure-the-windows-environment"></a>De Windows-omgeving configureren

Nadat u Enterprise Developer 4.0 op de VM hebt geïnstalleerd, moet u het exemplaar van Enterprise Server configureren dat bij de vm wordt geleverd. Om dat te doen, moet u een paar extra Windows-functies als volgt installeren.

1. Gebruik RDP om u aan te melden bij de door u gemaakte Enterprise Server 4.0 VM.

2. Klik **op het** pictogram Zoeken naast de knop **Start** en typ **Windows-functies**. De wizard Rollen en onderdelen toevoegen van serverbeheer wordt geopend.

3. Selecteer **De Functie Webserver (IIS)** en controleer vervolgens de volgende opties:

    - Webbeheerhulpprogramma's
    - Compatibiliteit met IIS 6-beheer (selecteer alle beschikbare functies)
    - IIS-beheerconsole
    - IIS-beheerscripts en -hulpprogramma's
    - IIS-beheerservice

4. Selecteer **World Wide Web Services**en controleer de volgende opties:

     Functies voor toepassingsontwikkeling:
    - .NET-uitbreidbaarheid
    - ASP.NET
    - Algemene HTTP-functies: alle beschikbare functies toevoegen
    - Gezondheid en diagnose: alle beschikbare functies toevoegen
    - Veiligheid:
        - Basisverificatie
        - Windows-verificatie

5. Selecteer **de Windows Process Activation Service** en alle kinderen.

6. Schakel microsoft **.NET framework 3.5.1**op **voor functies**en controleer de volgende opties:

    - Activering van windows Communication Foundation HTTP-activering
    - Windows Communication Foundation Niet-HTTP-activering

7. Schakel microsoft **.NET framework 4.6**op **voor functies**en controleer de volgende opties:

   - Pipe-activering met naam
   - TCP-activering
   - TCP-poort delen

     ![Wizard Rollen en functies toevoegen: functieservices](media/01-demo-roles.png)

8. Wanneer u alle opties hebt geselecteerd, klikt u op **Volgende** om te installeren.

9. Ga na de **Windows-functies \> naar \> Systeem- en Beveiligingsbeheerhulpprogramma's**van het Configuratiescherm en selecteer **Services**. Scroll naar beneden en zorg ervoor dat de volgende services worden uitgevoerd en ingesteld op **Automatisch:**

    - **NetTcpPortSharing**
    - **Net.Pipe-listeneradapter**
    - **Net.tcp-listeneradapter**

10. Als u IIS- en WAS-ondersteuning wilt configureren, zoekt u in het menu **Opdrachtprompt voor Micro Focus Enterprise Developer (64-bits)** en wordt u uitgevoerd als **administrator.**

11. Typ **wassetup –i** en druk op **Enter**.

12. Nadat het script is uitgevoerd, u het venster sluiten.

## <a name="configure-the-local-system-account-for-sql-server"></a>Het lokale systeemaccount voor SQL Server configureren

Sommige Enterprise Server-processen moeten zich kunnen aanmelden bij SQL Server en databases en andere objecten kunnen maken. Deze processen maken gebruik van het lokale systeemaccount, dus u moet sysadmin-bevoegdheid aan dat account geven.

1. Start de **SSMS** en klik op **Verbinding maken** om verbinding te maken met de lokale SQLEXPRESS-server met Windows-verificatie. Het moet beschikbaar zijn in de lijst **Servernaam.**

2. Vouw links de **map Beveiliging** uit en selecteer **Aanmeldingen**.

3. Selecteer **NT\\AUTHORITY SYSTEM** en selecteer **Eigenschappen**.

4. Selecteer **Serverrollen** en controleer **sysadmin**.

     ![Venster SSMS Object Explorer: aanmeldingseigenschappen](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>De BankDemo-database en al zijn objecten maken

1. Open **Windows Explorer** en navigeer naar **C:\\\\Gebruikers\\\\\\\\Openbare\\\\documenten Micro Focus\\\\Enterprise Developer\\Samples Mainframe CICS DotNet BankDemo SQL**.

2. Kopieer de inhoud van **BankDemoCreateAll.SQL-bestand** naar uw klembord.

3. Open **SSMS**. Klik rechts op **Server** en selecteer **Nieuwe query**.

4. Plak de inhoud van het klembord in het vak **Nieuwe query.**

5. Voer de SQL uit door op **Uitvoeren** te klikken vanaf het tabblad **Opdracht** boven de query.

De query moet worden uitgevoerd zonder fouten. Wanneer deze is voltooid, beschikt u over de voorbeelddatabase voor de BankDemo-toepassing.

![SQLQuery1.sql-uitvoer](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Controleren of de databasetabellen en -objecten zijn gemaakt

1. Klik met de rechtermuisknop op de **BANKDEMO-database** en selecteer **Vernieuwen**.

2. Vouw de **database** uit en selecteer **Tabellen**. Je zou zoiets als het volgende moeten zien.

     ![BANKDEMO-tabel uitgebreid in Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>De toepassing bouwen in Enterprise Developer

1. Open Visual Studio en meld je aan.

2. Selecteer onder de optie **Bestandmenu** **Project/Oplossing**openen, navigeer naar **C:\\Gebruikers\\\\Openbare Documenten\\Micro Focus\\Enterprise\\Developer Samples\\Mainframe\\CICS\\DotNet\\BankDemo**en selecteer het **sln-bestand.**

3. Neem de tijd om de objecten te onderzoeken. COBOL-programma's worden weergegeven in Solution Explorer met de CBL-extensie, samen met CopyBooks (CPY) en JCL.

4. Klik met de rechtermuisknop op het **BankDemo2-project** en selecteer **Instellen als opstartproject**.

    > [!NOTE]
    > Het BankDemo Project maakt gebruik van HCOSS (Host Compatibility Option for SQL Server), dat niet wordt gebruikt voor deze demo.

5. Klik in **Solution Explorer**met de rechtermuisknop op het **BankDemo2-project** en selecteer **Bouwen**.

    > [!NOTE]
    > Bouwen op oplossingsniveau leidt tot fouten, omdat HCOSS niet is geconfigureerd.

6. Wanneer het project is gebouwd, onderzoekt u het venster **Uitvoer.** Het zou eruit moeten zien als in de onderstaande afbeelding.

     ![Uitvoervenster met geslaagde build](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>De BankDemo-toepassing implementeren in de regiodatabase

1. Open een opdrachtprompt voor Enterprise Developer (64 bits) als beheerder.

2. Navigeer naar de **\\%PUBLIC%\\Documents\\\\Micro\\Focus\\Enterprise\\Developer samples\\Mainframe CICS DotNet BankDemo**.

3. Voer bij de opdrachtprompt **bankdemodbdeploy** uit en voeg de parameter op voor de database die moet worden geïmplementeerd, bijvoorbeeld:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Zorg ervoor dat u een voorwaartse slash\\(/) niet een achterwaartse slash() gebruikt. Dit script draait een tijdje.

![Beheer: opdrachtprompt voor enterprise-ontwikkelaars](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>De BankDemo-regio maken in enterpriseadministrator voor .NET

1. Open de **Enterprise Server voor de gebruikersinterface van .NET Administration.**

2. Als u de MMC-module wilt starten, kiest u in het menu **Start** van Windows de optie **Micro Focus Enterprise Developer Configuration \> \> Enterprise Server voor .NET-beheerder**. (Kies voor Windows Server de optie **Micro Focus Enterprise Developer \> Enterprise Server voor .NET-beheerder).**

3. Vouw de container **Regio's** uit in het linkerdeelvenster en klik vervolgens met de rechtermuisknop op **CICS**.

4. Selecteer **Regio definiëren** om een nieuwe CICS-regio te maken, **bankdemo**genaamd, gehost in de (lokale) database.

5. Geef de instantie databaseserver op **Volgende**en voer de regionaam **BANKDEMO**in .

     ![Dialoogvenster Regio definiëren](media/07-demo-cics.png)

6. Als u het regiodefinitiebestand voor de database met meerdere regio's wilt selecteren, zoekt u **\_regiobankdemo\_db.config** in **C:\\\\Users Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Regio definiëren - Regionaam: BANKDEMO](media/08-demo-cics.png)

7. Klik op **Voltooien**.

## <a name="create-xa-resource-definitions"></a>XA-brondefinities maken

1. Vouw in het linkerdeelvenster van de gebruikersinterface van **de Enterprise Server voor .NET Administration** het **systeem**uit en vervolgens **XA-brondefinities**. Met deze instelling bepaalt u hoe de regio werkt met Enterprise Server en de toepassingsdatabases.

2. Klik met de rechtermuisknop op **XA-brondefinities** en selecteer **Serverinstantie toevoegen**.

3. Selecteer **databaseservice-instantie**in de vervolgkeuzelijst . Het zal de lokale machine SQLEXPRESS.

4. Selecteer de instantie van onder de **xa-brondefinities (machinename\\sqlexpress)** container en klik op **Toevoegen**.

5. Selecteer **Database XA Resource Definition** en typ **BANKDEMO** voor **naam** en **regio**.

     ![Nieuw scherm Database XA Resource Definition](media/09-demo-xa.png)

6. Klik op de ellipsen (**...**) om de wizard Verbindingstekenreeks weer te nemen. Typ **voor Servernaam** **\\(lokaal) SQLEXPRESS**. Selecteer **Windows-verificatie**voor **Aanmelden**. Typ **BANKDEMO** voor databasenaam

     ![Scherm Verbindingstekenreeks bewerken](media/10-demo-string.png)

7. Test de verbinding.

## <a name="start-the-bankdemo-region"></a>Start de BANKDEMO Regio

> [!NOTE]
> De eerste stap is belangrijk: u moet de regio zo instellen dat de XA-resourcedefinitie die u zojuist hebt gemaakt, wordt gebruikt.

1. Navigeer naar het **BANDEMO CICS-gebied** onder de **container Regio's**en selecteer **vervolgens Regioopstartbestand bewerken** in het deelvenster **Acties.** Schuif omlaag naar de SQL-eigenschappen en voer **bankdemo** in voor de Naam van de **XA-bron**of gebruik de ellips om deze te selecteren.

2. Klik **op** het pictogram Opslaan om de wijzigingen op te slaan.

3. Klik met de rechtermuisknop op **HET CICS-gebied BANKDEMO** in het **deelvenster Console** en selecteer **Start/Stop-regio**.

4. Selecteer Start onder aan het vak **Start/Stopgebied** dat in het middelste deelvenster wordt **weergegeven.** Na een paar seconden begint de regio.

     ![SQL-start/stop,, vak](media/11-demo-sql.png)

     ![CICS Regio BANKDEMO - Gestart scherm](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Een listener maken

Maak een listener voor de TN3270-sessies die toegang hebben tot de BankDemo-toepassing.

1. Vouw **configuratie-editors** uit in het linkerdeelvenster en selecteer **Listener**.

2. Klik **op** het pictogram Bestand openen en selecteer het bestand **seelistener.exe.config.** Dit bestand wordt bewerkt en wordt geladen elke keer dat Enterprise Server wordt gestart.

3. Let op de twee regio's die eerder zijn gedefinieerd (ESDEMO en JCLDEMO).

4. Als u een nieuwe regio voor BANKDEMO wilt maken, klikt u met de rechtermuisknop op **Regio ',** en selecteert u **Regio toevoegen**.

5. Selecteer **BANKDEMO-regio**.

6. Voeg een TN3270-kanaal toe door met de rechtermuisknop op **BANKDEMO-regio** te klikken en **Kanaal toevoegen te**selecteren.

7. Voer **TN3270**voor **Naam**in . Voer **voor Port** **9024**in . De ESDEMO applicatie maakt gebruik van poort 9230 dus je moet een andere poort gebruiken.

8. Als u het bestand wilt opslaan, klikt u op het pictogram **Opslaan** of kiest **u Bestand** \> **opslaan**.

9. Als u de listener wilt starten, klikt u op het pictogram **Listener starten** of kiest u Listener Met **opties** \> **starten**.

     ![Venster Listenerconfiguratieeditor](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Rumba configureren om toegang te krijgen tot de BankDemo-toepassing

Het laatste wat je moet doen is het configureren van een 3270 sessie met rumba, een 3270 emulator. Met deze stap u toegang krijgen tot de BankDemo-toepassing via de luisteraar die u hebt gemaakt.

1. Start Rumba Desktop in het menu Windows **Start.**

2. Selecteer **TN3270**onder het **menu-item Verbindingen** .

3. Klik **op Invoegen** en typ **127.0.0.1** voor het IP-adres en **9024** voor de door de gebruiker gedefinieerde poort.

4. Klik onder aan het dialoogvenster op **Verbinden**. Er verschijnt een zwart CICS-scherm.

5. Typ **bank** om het eerste 3270-scherm weer te geven voor de BankDemo-toepassing.

6. Typ voor gebruikersnaam **B0001** en typ voor het wachtwoord alles. Het eerste scherm BANK20 wordt geopend.

![Mainframe Display](media/14-demo.png)
![Welkomstscherm Mainframe Display - Rumba - Subsysteem Demonstratie scherm](media/15-demo.png)

Gefeliciteerd! U voert nu een CICS-toepassing uit in Azure met behulp van Micro Focus Enterprise Server.

## <a name="next-steps"></a>Volgende stappen

- [Enterprise Server uitvoeren in Docker-containers op Azure](run-enterprise-server-container.md)
- [Mainframemigratie - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtuele machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Problemen oplossen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe to Azure migration Demyststifying mainframe to Azure migration Demyststifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
