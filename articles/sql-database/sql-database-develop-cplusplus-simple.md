---
title: Verbinding maken met SQL Database met C en C++
description: Gebruik de voorbeeldcode in deze snelle start om een moderne toepassing met C++ te bouwen en ondersteund door een krachtige relationele database in de cloud met Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: ed8e5eaa0ff9b58f80473b052aacfb9f01d45055
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529208"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Verbinding maken met SQL Database met C en C++

Dit bericht is bedoeld voor C- en C++-ontwikkelaars die verbinding proberen te maken met Azure SQL DB. Het is onderverdeeld in secties, zodat u springen naar de sectie die het beste vangt uw interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Voorwaarden voor de C/C++ tutorial

Zorg ervoor dat u over de volgende zaken beschikt:

* Een actief Azure-account. Als u nog geen abonnement hebt, kunt u zich registreren voor een [gratis Azure-proefversie](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). U moet de C++-taalcomponenten installeren om dit voorbeeld te bouwen en uit te voeren.
* [Visual Studio Linux Ontwikkeling](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Als u op Linux ontwikkelt, moet u ook de Visual Studio Linux-extensie installeren.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database en SQL Server op virtuele machines
Azure SQL is gebouwd op Microsoft SQL Server en is ontworpen om een krachtige, performante en schaalbare service te bieden. Er zijn veel voordelen aan het gebruik van SQL Azure via uw eigen database die op locatie wordt uitgevoerd. Met SQL Azure hoeft u uw database niet te installeren, in te stellen, te onderhouden of te beheren, maar alleen de inhoud en de structuur van uw database. Typische dingen waar we ons zorgen over maken met databases zoals fouttolerantie en redundantie zijn allemaal ingebouwd.

Azure heeft momenteel twee opties voor het hosten van SQL-serverworkloads: Azure SQL-database, database als service en SQL-server op Virtual Machines (VM). We zullen niet in detail treden over de verschillen tussen deze twee, behalve dat Azure SQL-database uw beste kans is voor nieuwe cloudgebaseerde toepassingen om te profiteren van de kostenbesparingen en prestatieoptimalisatie die cloudservices bieden. Als u overweegt uw on-premises toepassingen te migreren of uit te breiden naar de cloud, kan SQL Server op azure virtual machine beter voor u werken. Om het eenvoudig te houden voor dit artikel, maken we een Azure SQL-database.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologieën voor gegevenstoegang: ODBC en OLE DB
Verbinding maken met Azure SQL DB is niet anders en momenteel zijn er twee manieren om verbinding te maken met databases: ODBC (Open Database-connectiviteit) en OLE DB (Object Linking and Embedding database). In de afgelopen jaren heeft Microsoft zich aangesloten bij [ODBC voor native relationele toegang tot gegevens.](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/) ODBC is relatief eenvoudig, en ook veel sneller dan OLE DB. Het enige voorbehoud hier is dat ODBC maakt gebruik van een oude C-stijl API.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Stap 1: Uw Azure SQL-database maken
Zie de [pagina Aan de slag](sql-database-single-database-get-started.md) voor meer informatie over het maken van een voorbeelddatabase.  U deze korte video van twee minuten volgen om een Azure [SQL-database](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) te maken met behulp van de Azure-portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Stap 2: Verbindingstekenreeks oppakken
Nadat uw Azure SQL-database is ingericht, moet u de volgende stappen uitvoeren om verbindingsgegevens te bepalen en uw client-IP toe te voegen voor firewalltoegang.

Ga in [Azure-portal](https://portal.azure.com/)naar de ODBC-verbindingstekenreeks van de Azure SQL-database met de **tekenreeksen voor databaseverbindingen weergeven** die worden weergegeven als onderdeel van de overzichtssectie voor uw database:

![ODBCConnection-tekenreeks](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopieer de inhoud van de **ODBC-tekenreeks (Inclusief Node.js) [SQL-verificatie].** We gebruiken deze tekenreeks later om verbinding te maken met onze C++ ODBC-command-line-tolk. Deze tekenreeks bevat details zoals het stuurprogramma, de server en andere parameters voor databaseverbindingen.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Stap 3: Uw IP toevoegen aan de firewall
Ga naar de firewallsectie voor uw databaseserver en voeg uw [client-IP](sql-database-configure-firewall-settings.md) toe aan de firewall met behulp van deze stappen om ervoor te zorgen dat we een succesvolle verbinding tot stand kunnen brengen:

![YourIPWindow toevoegen](./media/sql-database-develop-cplusplus-simple/ip.png)

Op dit moment hebt u uw Azure SQL DB geconfigureerd en bent u klaar om verbinding te maken vanuit uw C++-code.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Stap 4: Verbinding maken vanuit een Windows C/C++-toepassing
U eenvoudig verbinding maken met uw [Azure SQL DB met ODBC op Windows met behulp van dit voorbeeld](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) dat wordt gebouwd met Visual Studio. Het voorbeeld implementeert een ODBC-opdrachtregeltolk die kan worden gebruikt om verbinding te maken met onze Azure SQL DB. In dit voorbeeld wordt een DSN-bestand (Database source file) gebruikt als een command-line argument of de verbose verbindingstekenreeks die we eerder hebben gekopieerd uit de Azure-portal. Breng de eigenschappenpagina voor dit project naar boven en plak de verbindingstekenreeks als een opdrachtargument zoals hier wordt weergegeven:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Zorg ervoor dat u de juiste verificatiegegevens voor uw database opgeeft als onderdeel van die tekenreeks voor databaseverbindingen.

Start de applicatie om het te bouwen. U ziet in het volgende venster een geldige verbinding. U zelfs enkele basisSQL-opdrachten uitvoeren, zoals **de tabel maken** om uw databaseconnectiviteit te valideren:

![SQL-opdrachten](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

U ook een DSN-bestand maken met behulp van de wizard die wordt gestart wanneer er geen opdrachtargumenten worden opgegeven. We raden u aan deze optie ook te proberen. U dit DSN-bestand gebruiken voor automatisering en het beveiligen van uw verificatie-instellingen:

![DSN-bestand maken](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gefeliciteerd! U hebt nu met succes verbinding gemaakt met Azure SQL met Behulp van C++ en ODBC op Windows. U blijven lezen om hetzelfde te doen voor Linux-platform ook.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Stap 5: Verbinding maken vanuit een Linux C/C++ applicatie
Mocht je het nieuws nog niet gehoord hebben, dan kun je met Visual Studio nu ook c++ Linux applicatie ontwikkelen. U lezen over dit nieuwe scenario in de [Visual C ++ voor Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blog. Om te bouwen voor Linux, moet je een externe machine waar je Linux distro wordt uitgevoerd. Als u er geen beschikbaar hebt, u er snel een instellen met [Linux Azure Virtual-machines.](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Voor deze tutorial, laten we aannemen dat je een Ubuntu 16.04 Linux distributie ingesteld. De stappen hier moeten ook gelden voor Ubuntu 15.10, Red Hat 6 en Red Hat 7.

In de volgende stappen worden de bibliotheken geïnstalleerd die nodig zijn voor SQL en ODBC voor uw distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Start Visual Studio. Voeg onder Tools -> Opties -> Cross Platform -> Connection Manager een verbinding toe aan uw Linux-vak:

![Opties voor hulpprogramma's](./media/sql-database-develop-cplusplus-simple/tools.png)

Nadat verbinding via SSH is gemaakt, maakt u een Sjabloon Leeg project (Linux):

![Nieuwe projectsjabloon](./media/sql-database-develop-cplusplus-simple/template.png)

U vervolgens een [nieuw C-bronbestand](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)toevoegen en deze vervangen door deze inhoud. Met behulp van de ODBC API's SQLAllocHandle, SQLSetConnectAttr en SQLDriverConnect moet u in staat zijn om een verbinding met uw database te initialiseren en tot stand te brengen.
Net als bij het Windows ODBC-voorbeeld moet u de SQLDriverConnect-aanroep vervangen door de details van uw databaseverbindingstekenreeksparameters die eerder uit de Azure-portal zijn gekopieerd.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Het laatste wat u moet doen voordat u samenstelt, is **odbc** toevoegen als afhankelijkheid van de bibliotheek:

![ODBC toevoegen als invoerbibliotheek](./media/sql-database-develop-cplusplus-simple/lib.png)

Als u uw toepassing wilt starten, brengt u de Linux-console naar boven in het menu **Foutopsporing:**

![Linux-console](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Als uw verbinding is gelukt, moet u nu de huidige databasenaam zien die in de Linux-console wordt afgedrukt:

![Linux-consolevensteruitvoer](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gefeliciteerd! U hebt de zelfstudie voltooid en nu verbinding maken met uw Azure SQL DB van C++ op Windows- en Linux-platforms.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Download de volledige C/C++ zelfstudieoplossing
De GetStarted-oplossing die alle voorbeelden bevat in dit artikel vindt u op GitHub:

* [ODBC C++ Windows-voorbeeld](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Download het Voorbeeld van Windows C++ ODBC om verbinding te maken met Azure SQL
* [ODBC C++ Linux-voorbeeld](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Download het Linux C++ ODBC-voorbeeld om verbinding te maken met Azure SQL

## <a name="next-steps"></a>Volgende stappen
* Het [overzicht van SQL-databaseontwikkeling bekijken](sql-database-develop-overview.md)
* Meer informatie over de [ODBC API Reference](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Aanvullende bronnen
* [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Bekijk alle [mogelijkheden van SQL Database](https://azure.microsoft.com/services/sql-database/)

