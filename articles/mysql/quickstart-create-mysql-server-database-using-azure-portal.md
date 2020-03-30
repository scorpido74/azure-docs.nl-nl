---
title: 'Snelstart: een server maken - Azure-portal - Azure-database voor MySQL'
description: Dit artikel leidt u stapsgewijs door de Azure Portal zodat u snel, in ongeveer vijf minuten, een voorbeeld van een Azure-database voor MySQL-server kunt maken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 3/20/2020
ms.openlocfilehash: 50a3eebaa18674a0d0a7e9da88b8d3386e5016c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067806"
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Een Azure-database voor MySQL-server maken met behulp van Azure Portal

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstart ontdekt u hoe u in ongeveer vijf minuten een Azure Database for MySQL-server maakt in Azure Portal.  

Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Open uw webbrowser en ga naar [Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken
U maakt een Azure Database voor MySQL-server met een gedefinieerde set [reken- en opslagresources](./concepts-compute-unit-and-storage.md). De server wordt gemaakt binnen een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

Volg deze stappen voor het maken van een Azure Database voor MySQL-server:

1. Selecteer **Een resource** (+) maken in de linkerbovenhoek van de portal.

2. Selecteer **Azure** > **Database databases voor MySQL**. U **MySQL** ook invoeren in het zoekvak om de service te vinden.

   ![De optie Azure Database voor MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Vul het formulier voor de gegevens van de nieuwe server als volgt in:
   
   ![Formulier voor het maken van een server](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving** 
    ---|---|---
    Servernaam | Unieke servernaam | Voer een unieke naam in die uw Azure Database voor MySQL-server identificeert. Bijvoorbeeld: mydemoserver. De domeinnaam *mysql.database.azure.com* wordt toegevoegd aan de servernaam die u opgeeft. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
    Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | *myresourcegroep* | Geef een nieuwe of bestaande resourcegroepnaam op.
    Bron selecteren | *Leeg* | Selecteer *Leeg* om een nieuwe server te maken. (Selecteer *Back-up* als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for MySQL-server).
    Aanmeldgegevens van serverbeheerder | myadmin | Een aanmeldingsaccount die moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor beheerders kan niet **worden azure_superuser,** **beheerder**, **beheerder**, **root,** **gast**of **openbaar**.
    Wachtwoord | *Uw keuze* | Geef een nieuw wachtwoord op voor het beheerdersaccount voor de server. Het wachtwoord moet tussen 8 en 128 tekens lang zijn. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
    Wachtwoord bevestigen | *Uw keuze*| Bevestig het wachtwoord voor het beheerdersaccount.
    Locatie | *De regio die het dichtst bij uw gebruikers staat*| Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
    Versie | *De meest recente primaire versie*| De meest recente primaire versie (tenzij u specifieke vereisten hebt en een andere versie moet gebruiken).
    Prijscategorie | **Algemeen**, **Gen 5**, **2 vCores**, **5 GB**, **7 dagen**, **Geografisch redundant** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Prijsniveau**. Selecteer vervolgens het tabblad *Gen 5* **Algemeen doel,** *4 vCores*, *100 GB*en *7 dagen* zijn de standaardwaarden voor **Compute Generation**, **vCore**, **Storage**en Backup **Retention Period**. U kunt deze schuifregelaars laten zoals ze zijn. Als u uw serverback-ups wilt inschakelen in georedundante opslag, selecteert u **Geografisch redundant** in de **redundantieopties voor back-up .** Selecteer **OK** om deze geselecteerde prijscategorie op te slaan. Deze selecties worden afgebeeld in de volgende schermopname.
  
   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie.
   > 

   ![Server maken - het venster Prijscategorie](./media/quickstart-create-mysql-server-database-using-azure-portal/3-pricing-tier.png)

4. Selecteer **Controleren + maken** om de server in te richten. Dit kan maximaal 20 minuten duren.
   
5. Selecteer **Meldingen** op de werkbalk (pictogram van een klok) om het implementatieproces te bewaken.
   
   Standaard worden de volgende databases gemaakt voor de server: **information_schema**, **mysql**, **performance_schema** en **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

De service Azure Database voor MySQL maakt een firewall op serverniveau. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij er een firewallregel wordt gemaakt om de firewall te openen voor specifieke IP-adressen. 

1. Ga naar de server nadat de implementatie is voltooid. U kunt indien nodig naar de server zoeken. Selecteer bijvoorbeeld **Alle resources** in het menu aan de linkerkant. Voer vervolgens de servernaam in, zoals het voorbeeld **mydemoserver,** om te zoeken naar uw nieuw gemaakte server. Selecteer de naam van de server in de lijst met zoekresultaten. De pagina **Overzicht** wordt geopend voor uw server en biedt opties voor verdere configuratie.

2. Selecteer **verbindingsbeveiliging**op de serverpagina .

3. Selecteer onder de kop **Firewallregels** het lege tekstvak in de kolom **Regelnaam** om te beginnen met het maken van de firewallregel. Voer het exacte bereik van IP-adressen in van de clients die toegang moeten hebben tot deze server.
   
   ![Verbindingsbeveiliging - Firewallregels](./media/quickstart-create-mysql-server-database-using-azure-portal/5-firewall-2.png)



4. Selecteer **Opslaan** op de bovenste werkbalk van de pagina **Verbindingsbeveiliging**. Ga pas verder als u de melding ziet dat de update is voltooid. 

   > [!NOTE]
   > Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 openstelt.
   > 

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen
Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U hebt deze waarden mogelijk al eerder in dit artikel genoteerd. Als u dat niet hebt gedaan, kunt u de servernaam en aanmeldingsgegevens eenvoudig terugvinden op de pagina **Overzicht** of ** Eigenschappen** van de server in Azure Portal.

Ga hiervoor als volgt te werk: 

1. Open de pagina **Overzicht** van de server. Noteer de **servernaam** en de **gebruikersnaam van de serverbeheerder**. 

2. Beweeg de muisaanwijzer de cursor over elk veld. Het kopieerpictogram wordt rechts van de tekst weergegeven. Selecteer het kopieerpictogram indien nodig om de waarden te kopiëren.

In dit voorbeeld wordt de servernaam **mydemoserver.mysql.database.azure.com**en is de aanmelding voor serverbeheerders **myadmin\@mydemoserver.**

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Verbinding maken met MySQL via het opdrachtregelprogramma mysql
Maak verbinding met de server met behulp van het opdrachtregelprogramma **mysql.exe**. U kunt MySQL [hier](https://dev.mysql.com/downloads/) downloaden en vervolgens op uw computer installeren. 

1. Gebruik de volgende indeling om verbinding te maken met een Azure Database voor MySQL-server via het hulpprogramma mysql:

    ```bash
    mysql --host <fully qualified server name> --user <server admin login name>@<server name> -p
    ```

    Met de volgende opdracht maakt u bijvoorbeeld verbinding met onze voorbeeldserver:

    ```bash
    mysql --host mydemoserver.mysql.database.azure.com --user myadmin@mydemoserver -p
    ```

    mysql-parameter |Voorgestelde waarde|Beschrijving
    ---|---|---
    --host | *Servernaam* | De servernaam die u eerder hebt gebruikt toen u de Azure Database voor MySQL-server hebt gemaakt. De server in ons voorbeeld is **mydemoserver.mysql.database.azure.com**. Gebruik de volledig gekwalificeerde domeinnaam**\*(.mysql.database.azure.com)** zoals in het voorbeeld wordt weergegeven. Als u de servernaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. 
    --user | *Aanmeldingsnaam van serverbeheerder* |De gebruikersnaam van de serverbeheerder die u hebt opgegeven toen u de Azure Database voor MySQL-server eerder hebt gemaakt. Als u de gebruikersnaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. De indeling is *\@gebruikersnaam servernaam*.
    -p | *Wacht tot er om wordt gevraagd* |Geef het wachtwoord op dat u hebt opgegeven tijdens het maken van de server als u hierom wordt gevraagd. Houd er rekening mee dat de ingevoerde wachtwoordtekens niet worden weergegeven bij de bash-prompt terwijl u de tekens typt. Nadat u het wachtwoord hebt ingevoerd, selecteert u **Enter**.

   Als de verbinding tot stand is gebracht, geeft het hulpprogramma mysql de prompt `mysql>` weer voor het typen van opdrachten. 

   Dit is een voorbeeld van mysql-uitvoer:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Als de firewall niet is geconfigureerd voor het toestaan van het IP-adres van uw client, wordt het volgende foutbericht weergegeven:
    >
    > ERROR 2003 (28000): Client with IP address 123.456.789.0 is not allowed to access the server.
    >
    > U kunt deze fout oplossen door ervoor te zorgen dat de serverconfiguratie overeenkomt met de stappen in de sectie 'Een serverfirewallregel configureren' van het artikel.

4. Controleer of de verbinding werkt door de status van de server weer te geven door `status` te typen bij de prompt mysql>.

    ```sql
    status
    ```

   > [!TIP]
   > Zie [hoofdstuk 4.5.1 in de Engelstalige naslaghandleiding van MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) voor aanvullende opdrachten.

5.  Maak een lege database door bij de prompt **mysql>** de volgende opdracht te typen:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Het uitvoeren van de opdracht kan even duren. 

    Op een Azure Database voor MySQL-server kunt u een of meerdere databases maken. U kunt kiezen voor het maken van één database per server om gebruik te maken van alle resources of voor meerdere databases om de resources te delen. Er geldt geen limiet voor het aantal databases dat u kunt maken, maar meerdere databases delen wel dezelfde serverresources. 

6. Vraag de databases op bij de prompt **mysql>** door de volgende opdracht te typen:

    ```sql
    SHOW DATABASES;
    ```

7.  Typ `\q` en druk vervolgens op **Enter** om het hulpprogramma mysql af te sluiten. 

U hebt nu verbinding gemaakt met de Azure Database voor MySQL-server en een lege gebruikersdatabase gemaakt. Ga verder met de volgende sectie voor een vergelijkbare oefening. In de volgende oefening wordt verbinding gemaakt met dezelfde server, maar met behulp van een andere veelgebruikt hulpprogramma, MySQL Workbench.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Verbinding maken met de server met het GUI-hulpprogramma MySQL Workbench
Als u verbinding met de server wilt maken met behulp van het GUI-hulpprogramma MySQL Workbench, voert u de volgende stappen uit:

1.    Open de toepassing MySQL Workbench op uw clientcomputer. U kunt MySQL Workbench downloaden en installeren vanaf [deze website](https://dev.mysql.com/downloads/workbench/).

2. Maak een nieuwe verbinding. Selecteer het pluspictogram (+) naast de kop **MySQL-verbindingen.**

3. Voer in het dialoogvenster **Nieuwe verbinding instellen** de gegevens van de serververbinding in op het tabblad **Parameters.** Vervang de hostnaam, de gebruikersnaam en het wachtwoord door uw eigen waarden.

   ![Nieuwe verbinding instellen](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Instelling |Voorgestelde waarde|Beschrijving van veld|
    |---|---|---|
     Verbindingsnaam | Demo connection | Een label voor deze verbinding. |
    Verbindingsmethode | Standard (TCP/IP) | Standard (TCP/IP) is voldoende. |
    Hostnaam | *Servernaam* | De servernaam die u hebt gebruikt toen u eerder de Azure Database voor MySQL-server hebt gemaakt. De server in ons voorbeeld is **mydemoserver.mysql.database.azure.com**. Gebruik de volledig gekwalificeerde domeinnaam**\*(.mysql.database.azure.com)** zoals in het voorbeeld wordt weergegeven. Als u de servernaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen.|
     Poort | 3306 | De poort om verbinding te maken met uw Azure Database voor MySQL-server. |
    Gebruikersnaam |  *Aanmeldingsnaam van serverbeheerder* | De aanmeldingsgegevens van de serverbeheerder die u hebt opgegeven toen u de Azure Database voor MySQL-server eerder hebt gemaakt. Onze voorbeeld gebruikersnaam is **myadmin\@mydemoserver**. Als u de gebruikersnaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. De indeling is *\@gebruikersnaam servernaam*.
    Wachtwoord | *Uw wachtwoord* | Selecteer **Store in Vault** om het wachtwoord op te slaan. |

4. Selecteer **Verbinding testen** om te controleren of alle parameters juist zijn geconfigureerd. Selecteer vervolgens **OK** om de verbinding op te slaan. 

    > [!NOTE]
    > SSL wordt standaard afgedwongen op uw server. Om verbinding te kunnen maken, is hiervoor aanvullende configuratie nodig. Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure-database voor MySQL) voor meer informatie. Als u SSL wilt uitschakelen voor deze Quick Start, gaat u naar Azure Portal. Ga vervolgens naar de pagina Verbindingsbeveiliging om de wisselknop **SSL afdwingen** op Uit te zetten.

## <a name="clean-up-resources"></a>Resources opschonen
De resources die u hebt gemaakt in de Quick Start kunt u op twee manieren opschonen. U kunt de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) verwijderen, met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de ene server-resource.

> [!TIP]
> Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om ook deze Quick Starts te volgen, moet u geen resources opschonen die u in deze Quick Start hebt gemaakt. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze Quick Start zijn gemaakt.
>

Ga als volgt te werk om de hele resourcegroep te verwijderen, inclusief de nieuwe server:

1.    Zoek de resourcegroep in Azure Portal. Selecteer **Resourcegroepen** in het menu aan de linkerkant en selecteer vervolgens de naam van uw resourcegroep (zoals **myresourcegroup** in ons voorbeeld).

2.    Selecteer **Verwijderen** op de pagina van de resourcegroep. Voer vervolgens de naam van uw resourcegroep (zoals onze **voorbeeldgroep myresourcegroep)** in het vak in om verwijdering te bevestigen en selecteer **Verwijderen**.

Ga als volgt te werk om alleen de zojuist gemaakte server te verwijderen:

1. Zoek de server in Azure Portal als de server nog niet is geopend. Selecteer **Alle bronnen**in het menu aan de linkerkant in de Azure-portal . Zoek vervolgens naar de server die u hebt gemaakt.

2. Selecteer **Verwijderen** op de pagina **Overzicht**. 

   ![Azure Database voor MySQL-server - server verwijderen](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3. Controleer de naam van de server die u gaat verwijderen en kijk welke databases eveneens worden verwijderd. Voer uw servernaam in het vak in (zoals onze **voorbeeldmydemoserver).** Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste Azure-database voor MySQL-database ontwerpen](./tutorial-design-database-using-portal.md)

