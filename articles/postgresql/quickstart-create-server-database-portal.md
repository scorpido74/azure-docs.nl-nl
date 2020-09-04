---
title: 'Quickstart: Server maken - Azure-portal - Azure Database for PostgreSQL - enkele server'
description: In deze quickstart gaat u een Azure Database for PostgreSQL-server maken en beheren met behulp van de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9b3e1916301d6346defd6ebeab2f3784882f2d5f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798853"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Quickstart: Een Azure Database for PostgreSQL-server maken met behulp van de Azure-portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze quickstart ontdekt u hoe u in ongeveer vijf minuten een enkele Azure Database for PostgreSQL-server maakt in de Azure-portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Open uw webbrowser en ga naar de [portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Een Azure Database for PostgreSQL-server wordt gemaakt met een geconfigureerde set [reken- en opslagresources](./concepts-pricing-tiers.md). De server wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

Ga als volgt te werk om een Azure Database for PostgreSQL-server te maken:

1. Selecteer **Een resource maken** in de linkerbovenhoek van de portal.

2. Selecteer **Databases** > **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > ![Schermopname waarin ‘Azure Database for PostgreSQL’ in het menu wordt weergegeven.](./media/quickstart-create-database-portal/1-create-database.png)

3. Selecteer de implementatieoptie **Enkele server**.

   > [!div class="mx-imgBorder"]
   > ![Schermopname waarin de optie voor enkele server implementeren voor Azure Database for PostgreSQL is geselecteerd](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Vul het formulier **Basisbeginselen** in met de volgende gegevens.

   > [!div class="mx-imgBorder"]
   > ![Schermopname waarin het tabblad Basisbeginselen voor het maken van een enkele server wordt weergegeven.](./media/quickstart-create-database-portal/create-basics.png)

   Instelling|Voorgestelde waarde|Beschrijving
   ---|---|---
   Abonnement|De naam van uw abonnement|Het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
   Resourcegroep|*myresourcegroup*| Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.
   Servernaam |*mydemoserver*|Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De domeinnaam *postgres.database.azure.com* wordt toegevoegd aan de servernaam die u opgeeft. De server mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.
   Gegevensbron | **Geen** | Selecteer **Geen** om een nieuwe server te maken. (Selecteer **Back-up** als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for PostgreSQL-server.)
   Gebruikersnaam van beheerder |*myadmin*| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor de beheerder mag niet **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** of **public** zijn. De naam mag niet met **pg_** beginnen.
   Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Het moet 8 tot 128 tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 t/m 9) en niet-alfanumerieke tekens (bijvoorbeeld !, $, #, %).
   Locatie|De regio het dichtst bij uw gebruikers| De locatie die het dichtst bij uw gebruikers is.
   Versie|De meest recente primaire versie| De nieuwste primaire versie van PostgreSQL, tenzij u specifieke andere vereisten hebt.
   Compute en opslag | **Algemeen**, **Gen 5**, **2 vCores**, **5 GB**, **7 dagen**, **Geografisch redundant** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Server configureren**. Selecteer vervolgens de juiste prijscategorie. Raadpleeg [Prijsgegevens](https://azure.microsoft.com/pricing/details/postgresql/server/) voor meer informatie. Als u serverback-ups in geografisch redundante opslag wilt inschakelen, selecteert u **Geografisch redundant** in het gedeelte **Redundantieopties voor back-up**. Selecteer **OK**.

   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. 
   
5. Selecteer **Beoordelen en maken** om uw selecties te controleren. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.

6. Selecteer het pictogram **Meldingen** (een klok) op de werkbalk om het implementatieproces te bewaken. Nadat de implementatie is voltooid, selecteert u **Ga naar resource** om de pagina **Overzicht** van de server te openen.

Er wordt een lege database, **postgres**, gemaakt. Er is ook een database **azure_maintenance** die wordt gebruikt om de processen van de beheerde service van gebruikersacties te onderscheiden. U kunt de database **azure_maintenance** niet openen.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren
De server die u maakt, is standaard niet openbaar toegankelijk. U moet toegang tot uw IP-adres verlenen. Ga in de Azure-portal naar uw serverresource en selecteer **Verbindingsbeveiliging** in het linkermenu van uw serverresource. Zie [Resources openen](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources) als u niet weet hoe u uw resource moet vinden.

> [!div class="mx-imgBorder"]
> ![Schermopname waarin firewallregels voor verbindingsbeveiliging worden weergegeven.](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Selecteer **Huidig IP-adres van client toevoegen** en selecteer vervolgens **Opslaan**. U kunt meer IP-adressen toevoegen of een IP-bereik opgeven om verbinding met uw server te maken vanaf die IP-adressen. Zie [Firewallregels in Azure Database for PostgreSQL](./concepts-firewall-rules.md) voor meer informatie.
   
> [!NOTE]
> Om connectiviteitsproblemen te vermijden, controleert u of uw netwerk uitgaand verkeer via poort 5432 toestaat. Azure Database for PostgreSQL gebruikt die poort.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Verbinding maken met een Azure Database for PostgreSQL-server met behulp van psql

U kunt [psql](http://postgresguide.com/utilities/psql.html) of [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) (populaire PostgreSQL-clients) gebruiken. In deze quickstart maakt u verbinding met behulp van psql in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) binnen de Azure-portal.

1. Noteer uw servernaam, aanmeldings-id voor de serverbeheerder, wachtwoord en abonnements-id voor de nieuwe server in de sectie **Overzicht** van de server.

2. Open Azure Cloud Shell in de portal door het pictogram in de linkerbovenhoek te selecteren.

   > [!NOTE]
   > Als u Cloud Shell voor de eerste keer opent, wordt er een prompt weergegeven om een resourcegroep en opslagaccount te maken. Dit is een eenmalige stap en ze worden voor alle volgende sessies automatisch toegevoegd. 

   > [!div class="mx-imgBorder"]
   > ![Schermopname waarin servergegevens en het pictogram voor het openen van Azure Cloud Shell worden weergegeven.](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Voer de volgende opdracht uit in de Azure Cloud Shell-terminal. Vervang de waarden door uw servernaam en aanmeldings-id voor de gebruiker met beheerdersrechten. Gebruik de lege database **postgres** met een gebruiker met beheerdersrechten in deze indeling: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Zo ziet het proces eruit in de Cloud Shell-terminal:
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Maak in dezelfde Azure Cloud Shell-terminal een database met de naam **guest**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Schakel de verbindingen over naar de nieuwe **guest**-database.

   ```bash
   \c guest
   ```
6. Typ `\q` en druk vervolgens op Enter om psql te sluiten. 

## <a name="clean-up-resources"></a>Resources opschonen
U hebt een Azure Database for PostgreSQL-server in een resourcegroep gemaakt. Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep of de PostgreSQL-server te verwijderen. 

Zo verwijdert u de resourcegroep:

1. Zoek en selecteer **Resourcegroepen** in de Azure-portal. 
2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
3. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.
4. Typ de naam van de resourcegroep in het bevestigingsvenster en selecteer **Verwijderen**.

Om de server te verwijderen, selecteert u de knop **Verwijderen** op de pagina **Overzicht** van uw server:

> [!div class="mx-imgBorder"]
> ![Schermopname waarin de knop voor het verwijderen van een server wordt weergegeven.](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van exporteren en importeren](./howto-migrate-using-export-and-import.md)
