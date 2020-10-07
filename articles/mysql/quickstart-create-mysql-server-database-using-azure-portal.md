---
title: 'Quickstart: Een server maken - Azure Portal - Azure Database for MySQL'
description: Dit artikel leidt u stapsgewijs door de Azure Portal zodat u snel, in ongeveer vijf minuten, een voorbeeld van een Azure-database voor MySQL-server kunt maken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90906556"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Quickstart: Een Azure Database for MySQL-server maken met behulp van Azure Portal

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstart ontdekt u hoe u in ongeveer vijf minuten een Azure Database for MySQL-server maakt in Azure Portal.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Open uw webbrowser en ga naar [Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken
U maakt een Azure Database voor MySQL-server met een gedefinieerde set [reken- en opslagresources](./concepts-compute-unit-and-storage.md). De server wordt gemaakt binnen een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

Volg deze stappen voor het maken van een Azure Database voor MySQL-server:

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).

2. Selecteer **Databases** > **Azure Database voor MySQL**. U kunt ook **MySQL** in het zoekvak typen om de service te vinden.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="De optie Azure Database for MySQL":::

3. Vul het formulier voor de gegevens van de nieuwe server als volgt in:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="De optie Azure Database for MySQL":::

**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld** 
---|---|---
Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
Resourcegroep | *myresourcegroup* | Geef een nieuwe of bestaande resourcegroepnaam op. De resourcegroep kan worden gebruikt om uw afhankelijkheden in te delen die deel uitmaken van één project.
Servernaam | Unieke servernaam | Kies een unieke naam ter identificatie van de Azure Database for MySQL-server. Bijvoorbeeld 'mysqldbserver'. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
Gegevensbron |*Geen* | Selecteer *Geen* om een nieuwe server te maken. (Selecteer *Back-up* als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for MySQL-server).
Aanmeldgegevens van serverbeheerder | myadmin | Voer een gebruikersnaam in voor de serverbeheerder. Het is niet toegestaan om **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** voor de gebruikersnaam van de beheerder te gebruiken.
Wachtwoord | *Uw keuze* | Geef een nieuw wachtwoord op voor het beheerdersaccount voor de server. Het wachtwoord moet 8 tot 128 tekens lang zijn en mag bestaan uit een combinatie van hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
Wachtwoord bevestigen | *Uw keuze*| Bevestig het wachtwoord voor het beheerdersaccount.
Locatie | *De regio het dichtst bij uw gebruikers*| Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
Versie | *De meest recente primaire versie*| De meest recente primaire versie (tenzij u specifieke vereisten hebt en een andere versie moet gebruiken).
Compute en opslag | **Algemeen**, **Gen 5**, **2 vCores**, **5 GB**, **7 dagen**, **Geografisch redundant** |De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Server configureren**. Selecteer vervolgens de juiste prijscategorie. Zie [de pagina met prijzen](https://azure.microsoft.com/pricing/details/mysql/) voor meer informatie. Als u serverback-ups in geografisch redundante opslag wilt inschakelen, selecteert u **Geografisch redundant** in het gedeelte **Redundantieopties voor back-up**. Selecteer **OK**.

   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. 

4. Selecteer **Beoordelen en maken** om de server in te richten. Dit kan maximaal 20 minuten duren.
   
5. Selecteer **Meldingen** op de werkbalk (pictogram van een klok) om het implementatieproces te bewaken.
   
Standaard worden de volgende databases gemaakt voor de server: **information_schema**, **mysql**, **performance_schema** en **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren
De server die wordt gemaakt, is standaard beveiligd met een firewall en is niet openbaar toegankelijk. Om toegang tot uw IP-adres te geven, gaat u in Azure Portal naar uw serverresource en selecteert u **Verbindingsbeveiliging** in het linkermenu van uw serverresource. Als u niet weet hoe u uw resource moet vinden, gaat u naar [Een resource openen](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="De optie Azure Database for MySQL":::
   
Selecteer **Huidig IP-adres van client toevoegen** en selecteer daarna **Opslaan**. U kunt extra IP-adressen toevoegen of een IP-bereik opgeven om verbinding met uw server te maken vanaf die IP-adressen. Zie [Firewallregels beheren op een Azure Database for MySQL-server](./concepts-firewall-rules.md) voor meer informatie

> [!NOTE]
> Controleer of uw netwerk uitgaand verkeer toestaat via poort 3306, die wordt gebruikt door Azure Database for MySQL om connectiviteitsproblemen te vermijden.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Verbinding maken met Azure Database for MySQL-server met behulp van de mysql-opdrachtregelclient
U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) kiezen om vanuit uw lokale omgeving verbinding met de server te maken. In deze quickstart voeren we **mysql.exe** in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) uit om verbinding te maken met de server.

1. Start Azure Cloud Shell in de portal door op het gemarkeerde pictogram in de linkerbovenhoek te klikken. Noteer uw servernaam, aanmeldings-id voor de serverbeheerder, wachtwoord en abonnement voor de nieuwe server in de sectie **Overzicht**, zoals hieronder is weergegeven.

    >[!NOTE]
    >Als u Cloud Shell voor de eerste keer start, wordt er een prompt weergegeven om een resourcegroep en opslagaccount te maken. Dit is een eenmalige stap die voor alle volgende sessies automatisch wordt toegevoegd. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="De optie Azure Database for MySQL":::
2. Voer deze opdracht uit in de Azure Cloud Shell-terminal. Vervang de waarden door uw servernaam en aanmeldings-id voor de gebruiker met beheerdersrechten. Voor de gebruikersnaam van de beheerder is @\<servername> vereist, zoals hieronder wordt weergegeven voor Azure Database for MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Zo zie het proces eruit in de Cloud Shell-terminal
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. Maak in dezelfde Azure Cloud Shell-terminal een database **guest** 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Wijzigen in database **gast**
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Typ ```quit``` en druk vervolgens op Enter om mysql af te sluiten.   

## <a name="clean-up-resources"></a>Resources opschonen
U hebt een Azure Database for MySQL-server in een resourcegroep gemaakt.  Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep of de MySQL-server te verwijderen. Voer de volgende stappen uit om de resourcegroep te verwijderen:
1. Zoek en selecteer **Resourcegroepen** in de Azure-portal. 
2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
3. Selecteer op de pagina Overzicht van uw resourcegroep de optie **Resourcegroep verwijderen**.
4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

Om uw server te verwijderen, klikt u op de knop **Verwijderen** op de pagina **Overzicht** van uw server, zoals hieronder weergegeven:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="De optie Azure Database for MySQL":::

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
>[Een PHP-app in Windows compileren met MySQL](../app-service/app-service-web-tutorial-php-mysql.md)
>[PHP-app op Linux compileren met MySQL](../app-service/containers/tutorial-php-mysql-app.md)
>[Op Java gebaseerde Spring-app compileren met MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
