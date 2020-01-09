---
title: 'Snelstartgids: Create Server-Azure Portal-Azure Database for PostgreSQL-één server'
description: Quick Start-hand leiding voor het maken en beheren van een Azure Database for PostgreSQL-één server met behulp van de Azure Portal gebruikers interface.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/25/2019
ms.openlocfilehash: d93c1d81e1434ffdd41297ed54e874c6b71240d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430471"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Snelstart: een Azure Database for PostgreSQL-server maken in Azure Portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstart ontdekt u hoe u in ongeveer vijf minuten een Azure Database for PostgreSQL-server maakt in Azure Portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Open uw webbrowser en ga naar de [portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Een Azure Database for PostgreSQL-server wordt gemaakt met een geconfigureerde set [reken- en opslagresources](./concepts-pricing-tiers.md). De server wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

Volg deze stappen voor het maken van een Azure Database for PostgreSQL-server:
1. Selecteer **een resource maken** (+) in de linkerbovenhoek van de portal.

2. Selecteer **Databases** > **Azure Database for PostgreSQL**.

    ![De Azure Database for PostgreSQL in menu](./media/quickstart-create-database-portal/1-create-database.png)

3. Selecteer de implementatie optie voor **één server** .

   ![Selecteer Azure Database for PostgreSQL-implementatie optie voor één server](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Vul het formulier **basis beginselen** in met de volgende informatie:

    ![Een server maken](./media/quickstart-create-database-portal/create-basics.png)

    Instelling|Voorgestelde waarde|Beschrijving
    ---|---|---
    Abonnement|De naam van uw abonnement|Het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep|*myresourcegroup*| Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.
    Servernaam |*mydemoserver*|Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De domeinnaam *postgres.database.azure.com* wordt toegevoegd aan de naam van de server die u opgeeft. De server mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Hij moet ten minste 3 en mag maximaal 63 tekens bevatten.
    Gegevensbron | *Geen* | Selecteer *geen* als u een volledig nieuwe server wilt maken. (Selecteer *Back-up* als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for PostgreSQL-server).
    Gebruikers naam beheerder |*myadmin*| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor de beheerder mag niet **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** of **public** zijn. De naam mag niet met **pg_** beginnen.
    Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 tot en met 9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
    Locatie|De regio het dichtst bij uw gebruikers| De locatie die zich het dichtst bij uw gebruikers bevindt.
    Versie|De meest recente primaire versie| De nieuwste primaire versie van PostgreSQL, tenzij u specifieke andere vereisten hebt.
    Berekening en opslag | **Algemeen**, **Gen 5**, **2 vCores**, **5 GB**, **7 dagen**, **Geografisch redundant** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **server configureren**. Selecteer vervolgens het tabblad **Algemeen** . *gen 5*, *4 vCores*, *100 GB*en *7 dagen* zijn de standaard waarden voor generatie van **Compute**, **vCore**, **Storage**en **retentie van back-ups**. U kunt deze schuif regelaars behouden als of aanpassen. Als u serverback-ups in geografisch redundante opslag wilt inschakelen, selecteert u **Geografisch redundant** in het gedeelte **Redundantieopties voor back-up**. Selecteer **OK** om deze geselecteerde prijscategorie op te slaan. Deze selecties worden afgebeeld in de volgende schermopname.

   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie.
   > 

    ![Het deelvenster Prijscategorie](./media/quickstart-create-database-portal/2-pricing-tier.png)

5. Selecteer **controleren + maken** om uw selecties te controleren. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.

6. Selecteer het pictogram **Meldingen** (een klok) op de werkbalk om het implementatieproces te bewaken. Na de implementatie kunt u **Vastmaken aan dashboard** selecteren. Hiermee maakt u een tegel voor deze server op uw dashboard in Azure Portal als snelkoppeling naar de **overzichtspagina** van de server. Als u **Naar de resource gaan** selecteert, wordt de **overzichtspagina** van de server weergegeven.

    ![Het deelvenster Meldingen](./media/quickstart-create-database-portal/3-notifications.png)
   
   Standaard word een **postgres**-database op uw server gemaakt. De database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) is een standaarddatabase die kan worden gebruikt door gebruikers, hulpprogramma's en toepassingen van derden. (De andere standaarddatabase is **azure_maintenance**. De functie van de database is het scheiden van beheerde serviceprocessen van acties van de gebruiker. U hebt geen toegang tot deze database.)

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

In Azure Database for PostgreSQL wordt een firewall op serverniveau gemaakt. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij u een firewallregel maakt om de firewall te openen voor specifieke IP-adressen. 

1. Ga naar de server nadat de implementatie is voltooid. U kunt desgewenst naar de server zoeken. Selecteer bijvoorbeeld **Alle resources** in het menu aan de linkerkant. Voer de naam van uw server in, zoals het voor beeld **mydemoserver**, om te zoeken naar de zojuist gemaakte server. Selecteer de naam van de server in de lijst met zoekresultaten. De pagina **Overzicht** wordt geopend voor uw server en biedt opties voor verdere configuratie.
 
    ![Zoeken naar de servernaam](./media/quickstart-create-database-portal/4-locate.png)

2. Selecteer **Verbindingsbeveiliging** op de serverpagina.

3. Selecteer onder de kop **Firewallregels** het lege tekstvak in de kolom **Regelnaam** om te beginnen met het maken van de firewallregel. 

   Vul in de vakken een naam in en het eerste en laatste adres van het IP-adresbereik van de clients die toegang moeten krijgen tot uw server. Als het één IP-adres betreft, gebruik dan dezelfde waarde voor het begin-IP-adres en eind-IP-adres.

   ![Firewallregels instellen](./media/quickstart-create-database-portal/5-firewall-2.png)
     

4. Selecteer **Opslaan** op de bovenste werkbalk van de pagina **Verbindingsbeveiliging**. Ga pas verder als u de melding ziet dat de verbindingsbeveiliging is bijgewerkt.

    > [!NOTE]
    > Verbindingen met uw Azure Database voor PostgreSQL-server communiceren via poort 5432. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 5432 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 5432 openstelt.
    >

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Wanneer u de Azure Database for PostgreSQL-server maakt, wordt een standaarddatabase met de naam **postgres** gemaakt. Voor verbinding met uw databaseserver, hebt u de volledige servernaam en aanmeldingsreferenties van de beheerder nodig. U hebt deze waarden mogelijk al eerder in dit artikel genoteerd. Als u dat niet hebt gedaan, kunt u de servernaam en aanmeldingsgegevens gemakkelijk terugvinden op de **overzichtspagina** voor de server in de portal.

Open de pagina **Overzicht** van de server. Noteer de **servernaam** en de **gebruikersnaam van de serverbeheerder**. Beweeg de cursor over elk veld. Het kopieersymbool wordt rechts van de tekst weergegeven. Selecteer het kopieerpictogram indien nodig om de waarden te kopiëren.

 ![De overzichtspagina van de server](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-using-psql"></a>Verbinding maken met de PostgreSQL-database met behulp van psql

Er zijn een aantal toepassingen die kunt gebruiken om verbinding te maken met uw Azure Database voor PostgreSQL-server. Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html) gebruiken om verbinding te maken met een Azure PostgreSQL-server. We gaan nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure PostgreSQL-server.

1. Voer de volgende psql-opdracht uit om verbinding te maken met een Azure-database voor PostgreSQL-server
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Als u liever een URL-pad gebruikt om verbinding te maken met post gres, URL codeert u het @-teken in de gebruikers naam met `%40`. Bijvoorbeeld: de connection string voor psql zou zijn, 
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

   Als de verbinding tot stand is gebracht, wordt er door het hulpprogramma psql een postgres-prompt weergegeven. Hier kunt u sql-opdrachten typen. In de uitvoer van de eerste verbinding kan een waarschuwing worden weergegeven, omdat de psql die u gebruikt mogelijk niet dezelfde versie heeft als de Azure Database for PostgreSQL-server. 

   Voorbeeld van psql-uitvoer:
   ```bash
   psql (9.5.7, server 9.6.2)
   WARNING: psql major version 9.5, server major version 9.6.
    Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=> 
   ```

   > [!TIP]
   > Als de firewall niet is geconfigureerd voor het toestaan van het IP-adres van uw client, wordt het volgende foutbericht weergegeven:
   > 
   > "psql: fataal: Nee pg_hba. conf vermelding voor host `<IP address>`, gebruiker" myadmin ", data base" post gres ", SSL op onherstelbaar: SSL-verbinding is vereist. Please specify SSL options and retry.
   > 
   > Controleer of het IP-adres van de client is toegestaan in de bovenstaande stap voor firewall regels.

2. Maak een lege database met de naam 'mypgsqldb' door bij de prompt de volgende opdracht te typen:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. In de prompt voert u de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

4. Typ `\q` en druk vervolgens op Enter om psql af te sluiten. 

U hebt nu verbinding gemaakt met de Azure Database for PostgreSQL-server via psql en u hebt een lege gebruikersdatabase gemaakt. Ga verder met de volgende sectie om verbinding te maken via een ander algemeen hulpprogramma: pgAdmin.

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Verbinding maken met de PostgreSQL-server met behulp van pgAdmin

pgAdmin is een open-source hulpprogramma voor PostgreSQL. U kunt pgAdmin installeren vanaf de [pgAdmin-website](https://www.pgadmin.org/). Mogelijk gebruikt u een andere versie van pgAdmin dan de versie in deze snelstart. Raadpleeg de documentatie van pgAdmin als u extra hulp nodig hebt.

1. Start de toepassing pgAdmin op uw clientcomputer.

2. Ga naar **Object** op de werkbalk, wijs **Create** aan met de muisaanwijzer en selecteer **Server**.

3. In het dialoogvenster **Create - Server** gaat u naar het tabblad **General** en voert u een unieke beschrijvende naam in voor de server, zoals **mydemoserver**.

    ![Het tabblad General](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. In het dialoogvenster **Create - Server** gaat u naar het tabblad **Connection** en vult u de tabel met instellingen in.

   ![Het tabblad Connection](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    Parameter pgAdmin |Waarde|Beschrijving
    ---|---|---
    Host name/address | Servernaam | De servernaam die u hebt gebruikt toen u eerder de Azure Database for PostgreSQL-server hebt gemaakt. De server in ons voorbeeld is **mydemoserver.postgres.database.azure.com**. Gebruik de FQDN (Fully Qualified Domain Name) ( **\*. postgres.database.azure.com**) zoals weergegeven in het voorbeeld. Als u de servernaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. 
    Port | 5432 | De poort die moet worden gebruikt wanneer u verbinding maakt met de Azure Database for PostgreSQL-server. 
    Onderhoudsdatabase | *postgres* | De door het systeem gegenereerde standaarddatabasenaam.
    Gebruikersnaam | Aanmeldingsnaam van serverbeheerder | De aanmeldingsnaam van de serverbeheerder die u hebt opgegeven toen u de Azure Database for PostgreSQL-server eerder hebt gemaakt. Als u de gebruikersnaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. De indeling is *username\@servername*.
    Wachtwoord | Uw beheerderswachtwoord | Het wachtwoord dat u hebt gekozen toen u eerder in deze Quick Start de server maakte.
    Rol | Leeg laten | U hoeft op dit moment geen rolnaam op te geven. Laat het veld leeg.
    SSL-modus | *Require* | U kunt de SSL-modus instellen op het tabblad SSL van pgAdmin. Standaard worden alle Azure Database for PostgreSQL-servers gemaakt waarbij SSL-afdwinging is ingeschakeld. Raadpleeg [SSL afdwingen](./concepts-ssl-connection-security.md) als u geforceerd SSL wilt uitschakelen.
    
5. Selecteer **Opslaan**.

6. Vouw in het linkerdeelvenster **Browser** het knooppunt **Server** uit. Selecteer uw server, bijvoorbeeld **mydemoserver**, om verbinding mee te maken.

7. Vouw het knooppunt Servers uit en vouw vervolgens **Databases** eronder uit. De lijst moet uw bestaande *postgres*-database bevatten, plus eventuele andere databases die u hebt gemaakt. U kunt met Azure Database for PostgreSQL meerdere databases per server maken.

8. Klik met de rechter muisknop op **data bases**, selecteer het menu **maken** en selecteer vervolgens **Data Base**.

9. Voer de gewenste database naam in het **database** veld in, zoals **mypgsqldb2**.

10. Selecteer bij **Owner** de eigenaar voor de database uit de keuzelijst. Selecteer de aanmeldings naam van de server beheerder, zoals het voor beeld, **mijn beheerder**.

    ![Een database maken in pgadmin](./media/quickstart-create-database-portal/11-pgadmin-database.png)

11. Klik op **Save** om een nieuwe, lege database te maken.

12. In het deelvenster **Browser** kunt u de database die u hebt gemaakt, zien in de lijst met databases onder de naam van uw server.


## <a name="clean-up-resources"></a>Resources opschonen
De resources die u hebt gemaakt in de Quick Start kunt u op een van de volgende twee manieren opschonen. U kunt de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) verwijderen, met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de serverresource.

> [!TIP]
> Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om ook deze Quick Starts te volgen, moet u geen resources opschonen die u in deze Quick Start hebt gemaakt. Als u niet wilt doorgaan, gebruikt u de volgende stappen om resources te verwijderen die tijdens deze Quick Start in de portal zijn gemaakt.

De hele resourcegroep verwijderen, met inbegrip van de nieuwe server:
1. Zoek de resourcegroep in de portal. Selecteer **Resourcegroepen** in het menu aan de linkerkant. Selecteer vervolgens de naam van de resourcegroep, zoals in het voorbeeld **myresourcegroup**.

2. Selecteer **Verwijderen** op de pagina van de resourcegroep. Voer de naam van uw resource groep in, zoals het voor beeld **myresourcegroup**, in het tekstvak om de verwijdering te bevestigen. Selecteer **Verwijderen**.

Alleen de zojuist gemaakte server verwijderen:
1. Als de server niet is geopend, zoekt u de server in de portal. Selecteer **Alle resources** in het menu aan de linkerkant. Zoek vervolgens naar de server die u hebt gemaakt.

2. Selecteer **Verwijderen** op de pagina **Overzicht**.

    ![De knop Verwijderen](./media/quickstart-create-database-portal/12-delete.png)

3. Controleer de naam van de server die u gaat verwijderen en kijk welke databases eveneens worden verwijderd. Voer de naam van uw server in het tekstvak in, zoals in het voor beeld **mydemoserver**. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
