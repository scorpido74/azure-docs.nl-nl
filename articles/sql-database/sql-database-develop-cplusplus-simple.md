---
title: Verbinding maken met SQL Database met behulp van C en C++
description: Gebruik de voorbeeld code in deze Quick Start om een moderne toepassing te bouwen met C++ en te worden ondersteund door een krachtige relationele data base in de Cloud met Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529208"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Verbinding maken met SQL Database met behulp van C en C++

Dit bericht is bedoeld om C-en C++-ontwikkel aars om verbinding te maken met Azure SQL DB. Het wordt onderverdeeld in secties, zodat u naar de sectie kunt gaan die uw interesse het beste vastlegt.

## <a name="prerequisites-for-the-cc-tutorial"></a>Vereisten voor de C/C++-zelf studie

Zorg ervoor dat u over de volgende zaken beschikt:

* Een actief Azure-account. Als u nog geen abonnement hebt, kunt u zich registreren voor een [gratis Azure-proefversie](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). U moet de C++-taal onderdelen installeren om dit voor beeld te bouwen en uit te voeren.
* [Ontwikkeling van Visual Studio Linux](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Als u op Linux ontwikkelt, moet u ook de Visual Studio Linux-extensie installeren.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database en SQL Server op virtuele machines
Azure SQL is gebaseerd op Microsoft SQL Server en is ontworpen om een hoge Beschik baarheid, een krachtige en schaal bare service te bieden. Er zijn veel voor delen voor het gebruik van SQL Azure over uw eigen data base die on-premises wordt uitgevoerd. Met SQL Azure hoeft u uw data base niet te installeren, in te stellen, te onderhouden of te beheren, maar alleen de inhoud en de structuur van uw data base. Typische dingen die u zorgen maakt voor data bases zoals fout tolerantie en redundantie zijn allemaal ingebouwd.

Azure heeft momenteel twee opties voor het hosten van SQL Server-workloads: Azure SQL database, Database as a Service en SQL Server op Virtual Machines (VM). We zullen geen details over de verschillen tussen deze twee zien, behalve dat Azure SQL database uw beste treffer is voor nieuwe Cloud toepassingen om te profiteren van de kosten besparingen en prestatie optimalisatie die Cloud Services bieden. Als u overweegt om uw on-premises toepassingen te migreren of uit te breiden naar de Cloud, is SQL Server op Azure virtual machine mogelijk beter voor u. Voor dit artikel gaan we een Azure-SQL database maken.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologieën voor gegevens toegang: ODBC en OLE DB
Verbinding maken met Azure SQL DB is niet anders en er zijn momenteel twee manieren om verbinding te maken met data bases: ODBC (Open Data Base Connectivity) en OLE DB (object linking and Embedding data base). In de afgelopen jaren is micro soft afgestemd op [ODBC voor native relationele gegevens toegang](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC is relatief eenvoudig en ook veel sneller dan OLE DB. Het enige voor behoud hier is dat ODBC een oude C-style-API gebruikt.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Stap 1: uw Azure SQL Database maken
Zie de [pagina](sql-database-single-database-get-started.md) aan de slag voor meer informatie over het maken van een voorbeeld database.  U kunt ook deze [korte video van twee minuten](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) volgen om een Azure-SQL database te maken met behulp van de Azure Portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Stap 2: connection string ophalen
Nadat uw Azure-SQL database is ingericht, moet u de volgende stappen uitvoeren om verbindings gegevens te bepalen en uw client-IP toe te voegen voor toegang tot de firewall.

Ga in [Azure Portal](https://portal.azure.com/)naar uw Azure SQL database ODBC-Connection String met behulp van de **verbindings reeksen Data Base weer geven** die worden weer gegeven als onderdeel van de sectie Overzicht voor uw Data Base:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopieer de inhoud van de **ODBC (inclusief node. js) [SQL-verificatie]** teken reeks. We gebruiken deze teken reeks later om verbinding te maken via de opdracht regel-interpreter van de C++ ODBC. Deze teken reeks bevat details zoals het stuur programma, de server en andere database verbindings parameters.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Stap 3: uw IP toevoegen aan de firewall
Ga naar de sectie Firewall voor uw database server en voeg het [IP-adres van de client toe aan de firewall met behulp van de volgende stappen](sql-database-configure-firewall-settings.md) om ervoor te zorgen dat er een geslaagde verbinding kan worden gemaakt:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Op dit moment hebt u uw Azure SQL-data base geconfigureerd en kunt u verbinding maken vanuit uw C++-code.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Stap 4: verbinding maken vanaf een Windows C/C++-toepassing
U kunt eenvoudig verbinding maken met uw [Azure SQL-data base met behulp van ODBC in Windows met behulp van dit voor beeld](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) dat is gebaseerd op Visual Studio. Het voor beeld implementeert een ODBC-opdracht regel programma dat kan worden gebruikt om verbinding te maken met onze Azure SQL-data base. In dit voor beeld wordt een DSN-bestand (data base source name File) gebruikt als een opdracht regel argument of de uitgebreide connection string die u eerder hebt gekopieerd uit de Azure Portal. Breng de eigenschappen pagina voor dit project en plak de connection string als een opdracht argument, zoals hier wordt weer gegeven:

![DSN-Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Zorg ervoor dat u de juiste verificatie gegevens voor uw data base opgeeft als onderdeel van de data base connection string.

Start de toepassing om deze te bouwen. U ziet nu het volgende venster met de validatie van een geslaagde verbinding. U kunt zelfs een aantal eenvoudige SQL-opdrachten uitvoeren zoals **Create Table** om uw database verbinding te valideren:

![SQL-opdrachten](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

U kunt ook een DSN-bestand maken met behulp van de wizard die wordt gestart wanneer er geen opdracht argumenten worden gegeven. U wordt aangeraden deze optie ook te proberen. U kunt dit DSN-bestand voor automatisering gebruiken en uw verificatie-instellingen beschermen:

![DSN-bestand maken](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gefeliciteerd! U hebt nu verbinding gemaakt met Azure SQL via C++ en ODBC in Windows. U kunt ook door gaan met lezen om hetzelfde te doen voor Linux-platform.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Stap 5: verbinding maken vanuit een Linux C/C++-toepassing
Als u het nieuws nog niet hebt gehoord, kunt u met Visual Studio nu ook de C++ Linux-toepassing ontwikkelen. Meer informatie over dit nieuwe scenario vindt u in de [ontwikkel blog van Visual C++ voor Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . Als u voor Linux wilt bouwen, hebt u een externe computer nodig waarop uw Linux-distributie wordt uitgevoerd. Als u er nog geen hebt, kunt u snel een snelle set instellen met behulp van [virtuele Linux Azure-machines](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Voor deze zelf studie gaan we ervan uit dat u een Ubuntu 16,04 Linux-distributie hebt ingesteld. Deze stappen moeten ook van toepassing zijn op Ubuntu 15,10, Red Hat 6 en Red Hat 7.

In de volgende stappen worden de bibliotheken geïnstalleerd die nodig zijn voor SQL en ODBC voor uw distributie:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Start Visual Studio. Voeg onder extra-> opties-> Kruis platform-> verbindings beheer een verbinding toe aan uw Linux-vak:

![Extra opties](./media/sql-database-develop-cplusplus-simple/tools.png)

Nadat de verbinding via SSH tot stand is gebracht, maakt u een sjabloon voor een leeg project (Linux):

![Nieuwe project sjabloon](./media/sql-database-develop-cplusplus-simple/template.png)

Vervolgens kunt u een [Nieuw C-bron bestand toevoegen en dit vervangen door deze inhoud](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Met behulp van de ODBC Api's SQLAllocHandle, SQLSetConnectAttr en SQLDriverConnect moet u een verbinding met uw data base kunnen initialiseren en tot stand brengen.
Net als bij het voor beeld van Windows ODBC moet u de SQLDriverConnect-aanroep vervangen door de gegevens uit de data base connection string para meters die u eerder van de Azure Portal hebt gekopieerd.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Het laatste wat u moet doen vóór het compileren is om **ODBC** toe te voegen als een bibliotheek afhankelijkheid:

![ODBC toevoegen als een invoer bibliotheek](./media/sql-database-develop-cplusplus-simple/lib.png)

Als u uw toepassing wilt starten, gaat u naar de Linux-console vanuit het menu **fout opsporing** :

![Linux-console](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Als de verbinding is geslaagd, ziet u nu de huidige database naam in de Linux-console:

![Venster uitvoer Linux-console](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gefeliciteerd! U hebt de zelf studie voltooid en kunt nu verbinding maken met uw Azure SQL DB vanuit C++ op Windows-en Linux-platforms.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>De volledige oplossing C/C++-zelf studie ophalen
U kunt de GetStarted-oplossing vinden die alle voor beelden in dit artikel bevat op GitHub:

* Voor [beeld van ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29): down load het Windows C++ ODBC-voor beeld om verbinding te maken met Azure SQL
* Voor [beeld van ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29): down load het ODBC-voor beeld Linux c++ om verbinding te maken met Azure SQL

## <a name="next-steps"></a>Volgende stappen
* Bekijk het [overzicht](sql-database-develop-overview.md) van de SQL database-ontwikkeling
* Meer informatie over de [ODBC API-verwijzing](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Aanvullende bronnen
* [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Bekijk alle [mogelijkheden van SQL Database](https://azure.microsoft.com/services/sql-database/)

