---
title: Importeren en exporteren - Azure Database voor MySQL
description: In dit artikel worden veelvoorkomende manieren uitgelegd om databases te importeren en exporteren in Azure Database voor MySQL, met behulp van hulpprogramma's zoals MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163723"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Uw MySQL-database migreren met import en export
In dit artikel worden twee veelvoorkomende benaderingen uitgelegd voor het importeren en exporteren van gegevens naar een Azure Database voor MySQL-server met behulp van MySQL Workbench. 

## <a name="before-you-begin"></a>Voordat u begint
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een Azure-database voor MySQL-server door [een Azure Database voor MySQL-server maken met Azure-portal](quickstart-create-mysql-server-database-using-azure-portal.md)te volgen.
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) of een andere MySQL-tool van derden om de import/export uit te voeren.

## <a name="use-common-tools"></a>Algemene gereedschappen gebruiken
Gebruik algemene hulpprogramma's en hulpprogramma's zoals MySQL Workbench of mysqldump om op afstand gegevens te verbinden en te importeren of exporteren in Azure Database voor MySQL. 

Gebruik dergelijke hulpprogramma's op uw clientmachine met een internetverbinding om verbinding te maken met Azure Database voor MySQL. Gebruik een SSL-versleutelde verbinding voor de beste beveiligingsprocedures, zoals beschreven in [SSL-connectiviteit configureren in Azure Database voor MySQL.](concepts-ssl-connection-security.md)

U hoeft uw import- en exportbestanden niet te verplaatsen naar een speciale cloudlocatie wanneer u migreert naar Azure Database voor MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Een database maken op de Azure Database voor MySQL-server
Maak een lege database op de Azure Database voor MySQL-server waar u de gegevens wilt migreren. Gebruik een tool zoals MySQL Workbench, Toad of Navicat om de database te maken. De database kan dezelfde naam hebben als de database die de gedumpte gegevens bevat, of u een database maken met een andere naam.

Zoek de verbindingsgegevens in het **overzicht** van uw Azure Database voor MySQL om verbinding te maken.

![De verbindingsgegevens zoeken in de Azure-portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Voeg de verbindingsgegevens toe aan MySQL Workbench.

![MySQL Workbench-verbindingstekenreeks](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Bepalen wanneer import- en exporttechnieken moeten worden gebruikt in plaats van een dump en herstellen
Gebruik MySQL-hulpprogramma's om databases in Azure MySQL-database te importeren en exporteren in de volgende scenario's. In andere scenario's u profiteren van het gebruik van de [dump- en herstelbenadering.](concepts-migrate-dump-restore.md) 

- Wanneer u selectief een paar tabellen moet kiezen om uit een bestaande MySQL-database te importeren in Azure MySQL-database, u het beste de import- en exporttechniek gebruiken.  Door dit te doen, u onnodige tabellen van de migratie weglaten om tijd en resources te besparen. Gebruik bijvoorbeeld de `--include-tables` `--exclude-tables` of schakelaar met [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) en de `--tables` schakelaar met [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wanneer u de andere databaseobjecten dan tabellen verplaatst, maakt u deze objecten expliciet. Neem beperkingen op (primaire sleutel, externe sleutel, indexen), weergaven, functies, procedures, triggers en andere databaseobjecten die u wilt migreren.
- Wanneer u gegevens migreert uit andere externe gegevensbronnen dan een MySQL-database, maakt u platte bestanden en importeert u deze met [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Zorg ervoor dat alle tabellen in de database de InnoDB-opslagengine gebruiken wanneer u gegevens laadt in Azure Database voor MySQL. Azure Database voor MySQL ondersteunt alleen de InnoDB-opslagengine, dus het ondersteunt geen alternatieve opslagengines. Als uw tabellen alternatieve opslagengines vereisen, moet u deze converteren naar de InnoDB-engineindeling voordat de migratie naar Azure Database voor MySQL wordt gebruikt. 

Als u bijvoorbeeld een WordPress- of web-app hebt die de MyISAM-engine gebruikt, converteert u de tabellen eerst door de gegevens te migreren naar InnoDB-tabellen. Vervolgens herstellen naar Azure Database voor MySQL. Gebruik de `ENGINE=INNODB` component om de engine in te stellen voor het maken van een tabel en vervolgens de gegevens vóór de migratie over te zetten naar de compatibele tabel. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestatieaanbevelingen voor import en export
-   Maak geclusterde indexen en primaire sleutels voordat gegevens worden geladen. Gegevens laden in primaire sleutelvolgorde. 
-   Stel het maken van secundaire indexen uit tot nadat gegevens zijn geladen. Maak alle secundaire indexen na het laden. 
-   Schakel beperkingen voor het laden van buitenlandse sleutelfactoren uit. Het uitschakelen van buitenlandse key checks levert aanzienlijke prestatiewinst op. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te garanderen.
-   Gegevens parallel laden. Vermijd te veel parallellisme waardoor u een resourcelimiet bereikt en beheer resources met behulp van de statistieken die beschikbaar zijn in de Azure-portal. 
-   Gebruik indien van toepassing partitietabellen.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importeren en exporteren met MySQL Workbench
Er zijn twee manieren om gegevens te exporteren en te importeren in MySQL Workbench. Elk dient een ander doel. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Wizards voor het exporteren en importeren van tabelgegevens uit het contextmenu van de objectbrowser
![MySQL Workbench-wizards in het contextmenu van de objectbrowser](./media/concepts-migrate-import-export/p1.png)

De wizards voor tabelgegevens ondersteunen import- en exportbewerkingen met CSV- en JSON-bestanden. Ze bevatten verschillende configuratieopties, zoals scheidingstekens, kolomselectie en coderingsselectie. U elke wizard uitvoeren tegen lokale of op afstand verbonden MySQL-servers. De importactie omvat tabel-, kolom- en typetoewijzing. 

U deze wizards openen in het contextmenu van de objectbrowser door met de rechtermuisknop op een tabel te klikken. Kies vervolgens **de wizard Tabelgegevens exporteren** of wizard **Tabelgegevens importeren**. 

#### <a name="table-data-export-wizard"></a>Wizard Tabelgegevens exporteren
In het volgende voorbeeld wordt de tabel geëxporteerd naar een CSV-bestand: 
1. Klik met de rechtermuisknop op de tabel van de database die moet worden geëxporteerd. 
2. Selecteer **wizard Tabelgegevens exporteren**. Selecteer de kolommen die moeten worden geëxporteerd, rijverschuiving (indien aanwezig) en tel (indien aanwezig). 
3. Klik **op** de pagina Gegevens selecteren voor exporteren op **Volgende**. Selecteer het bestandspad, CSV of JSON-bestandstype. Selecteer ook de lijnscheidingsteken, methode voor het omsluiten van tekenreeksen en veldscheidingsteken. 
4. Klik op de **locatiepagina Uitvoerbestand selecteren** op **Volgende**. 
5. Klik **op** de pagina Gegevens exporteren op **Volgende**.

#### <a name="table-data-import-wizard"></a>Wizard Tabelgegevens importeren
In het volgende voorbeeld wordt de tabel uit een CSV-bestand geïmporteerd:
1. Klik met de rechtermuisknop op de tabel van de database die moet worden geïmporteerd. 
2. Blader naar en selecteer het CSV-bestand dat moet worden geïmporteerd en klik op **Volgende**. 
3. Schakel de doeltabel (nieuw of bestaand) in en schakel de **tabel Afkappen in of** uit voordat u de invoer importeert. Klik op **Volgende**.
4. Selecteer codering en de kolommen die moeten worden geïmporteerd en klik op **Volgende**. 
5. Klik **op** de pagina Gegevens importeren op **Volgende**. De wizard importeert de gegevens dienovereenkomstig.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Wizards voor SQL-gegevens exporteren en importeren uit het deelvenster Navigator
Gebruik een wizard om SQL te exporteren of te importeren die is gegenereerd vanuit MySQL Workbench of gegenereerd vanuit de opdracht mysqldump. Toegang tot deze wizards in het deelvenster **Navigator** of door **Server** te selecteren in het hoofdmenu. Selecteer vervolgens **Gegevens exporteren** of **Gegevens importeren**. 

#### <a name="data-export"></a>Gegevens exporteren
![MySQL Workbench-gegevens exporteren met het deelvenster Navigator](./media/concepts-migrate-import-export/p2.png)

U het tabblad **Gegevensexporteren** gebruiken om uw MySQL-gegevens te exporteren. 
1. Selecteer elk schema dat u wilt exporteren, kies optioneel specifieke schemaobjecten/tabellen uit elk schema en genereer de export. Configuratieopties omvatten exporteren naar een projectmap of een op zichzelf staand SQL-bestand, dumpen opgeslagen routines en gebeurtenissen of slaan tabelgegevens over. 
 
   U ook **een resultaatset exporteren** gebruiken om een specifiek resultaat dat is ingesteld in de SQL-editor te exporteren naar een andere indeling, zoals CSV, JSON, HTML en XML. 
3. Selecteer de databaseobjecten die u wilt exporteren en configureer de bijbehorende opties.
4. Klik **op Vernieuwen** om de huidige objecten te laden.
5. Open het tabblad **Geavanceerde opties** optioneel om de exportbewerking te verfijnen. Voeg bijvoorbeeld tabelvergrendelingen toe, gebruik vervangen in plaats van invoegen instructies en citeer-id's met tekens terugtikken.
6. Klik **op Exporteren starten** om het exportproces te starten.


#### <a name="data-import"></a>Gegevens importeren
![MySQL Workbench-gegevens importeren met Management Navigator](./media/concepts-migrate-import-export/p3.png)

U het tabblad **Gegevensimporteren** gebruiken om geëxporteerde gegevens te importeren of te herstellen uit de bewerking gegevensexport of vanuit de opdracht mysqldump. 
1. Kies de projectmap of het op zichzelf staande SQL-bestand, kies het schema dat u wilt importeren of kies **Nieuw** om een nieuw schema te definiëren. 
2. Klik **op Importeren starten** om het importproces te starten.

## <a name="next-steps"></a>Volgende stappen
- Lees als een andere migratiebenadering [Uw MySQL-database migreren met dumpen en herstellen in Azure Database voor MySQL.](concepts-migrate-dump-restore.md)
- Zie de [databasemigratiehandleiding](https://aka.ms/datamigration)voor meer informatie over het migreren van databases naar Azure Database voor MySQL. 
