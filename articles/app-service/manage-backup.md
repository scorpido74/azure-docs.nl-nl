---
title: Een back-up maken van een app
description: Meer informatie over het maken van back-ups van uw apps in Azure App Service. Handmatige of geplande back-ups uitvoeren. Back-ups aanpassen door de bijgevoegde database op te splitsen.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b812ae10b3462dbeff05c8a67e7ebb725281e7e8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535754"
---
# <a name="back-up-your-app-in-azure"></a>Back-up maken van uw app in Azure
Met de functie Back-up en herstel in [Azure App Service](overview.md) u eenvoudig handmatig of volgens een planning app-back-ups maken. U de back-ups tot onbepaalde tijd kunnen behouden. U de app herstellen naar een momentopname van een vorige status door de bestaande app over te schrijven of te herstellen naar een andere app.

Zie Een app herstellen in [Azure](web-sites-restore.md)voor informatie over het herstellen van een app vanuit back-up.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Wat wordt een back-up
App Service kan een back-up maken van de volgende gegevens naar een Azure-opslagaccount en -container die u hebt geconfigureerd voor het gebruik van uw app. 

* App-configuratie
* Bestandsinhoud
* Database die is verbonden met uw app

De volgende databaseoplossingen worden ondersteund met een back-upfunctie: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Elke back-up is een volledige offline kopie van uw app, geen incrementele update.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Vereisten en beperkingen
* Voor de functie Back-up en herstel moet het app-serviceplan zich in **de** standaardlaag of **de premiumlaag** bevindt. Zie [Een app opschalen in Azure](manage-scale-up.md)voor meer informatie over het schalen van uw App Service-abonnement naar een hogere laag. **Premium-laag** maakt een groter aantal dagelijkse back-ups mogelijk dan **de standaardlaag.**
* U hebt een Azure-opslagaccount en -container nodig in hetzelfde abonnement als de app waarvan u een back-up wilt maken. Zie overzicht van [Azure-opslagaccounts](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over Azure-opslagaccounts.
* Back-ups kunnen maximaal 10 GB aan app- en database-inhoud bevatten. Als de back-upgrootte deze limiet overschrijdt, krijgt u een foutmelding.
* Back-ups van AZURE Database met TLS voor MySQL worden niet ondersteund. Als een back-up is geconfigureerd, krijgt u mislukte back-ups.
* Back-ups van AZURE Database met TLS voor PostgreSQL worden niet ondersteund. Als een back-up is geconfigureerd, krijgt u mislukte back-ups.
* In-app MySQL-databases worden automatisch geback-upt zonder enige configuratie. Als u handmatiginstellingen maakt voor in-app MySQL-databases, zoals het toevoegen van verbindingstekenreeksen, werken de back-ups mogelijk niet correct.
* Het gebruik van een opslagaccount met firewall als bestemming voor uw back-ups wordt niet ondersteund. Als een back-up is geconfigureerd, krijgt u mislukte back-ups.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Een handmatige back-up maken
1. Ga in de [Azure-portal](https://portal.azure.com)naar de pagina van uw app en selecteer **Back-ups**. De pagina **Back-ups** wordt weergegeven.

    ![Pagina Back-ups](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Als u het volgende bericht ziet, klikt u erop om uw App Service-abonnement te upgraden voordat u doorgaan met back-ups.
    > Zie [Een app opschalen in Azure](manage-scale-up.md)voor meer informatie.
    > ![Opslagaccount kiezen](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. Selecteer **Back-up** op de pagina **Back-up is niet geconfigureerd. Klik hier om back-ups voor uw app te configureren.**

    ![Klik op Configureren](./media/manage-backup/configure-start.png)

3. Klik op de pagina **Back-upconfiguratie** op **Opslag die niet is geconfigureerd** om een opslagaccount te configureren.

    ![Opslagaccount kiezen](./media/manage-backup/configure-storage.png)

4. Kies uw back-upbestemming door een **opslagaccount** en **container**te selecteren. Het opslagaccount moet deel uitmaken van hetzelfde abonnement als de app waaru een back-up van wilt maken. Als u dat wenst, u een nieuw opslagaccount of een nieuwe container op de respectievelijke pagina's aanmaken. Klik op **Selecteren**als u klaar bent.

5. Klik op de pagina **Back-upconfiguratie** die nog open is, u **back-updatabase**configureren en vervolgens de databases selecteren die u wilt opnemen in de back-ups (SQL-database of MySQL) en vervolgens op **OK**klikken.

    ![Opslagaccount kiezen](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Als u een database in deze lijst wilt weergeven, moet de verbindingstekenreeks bestaan in het gedeelte **Verbindingstekenreeksen** van de pagina **Toepassingsinstellingen** voor uw app. 
    >
    > In-app MySQL-databases worden automatisch geback-upt zonder enige configuratie. Als u handmatiginstellingen maakt voor in-app MySQL-databases, zoals het toevoegen van verbindingstekenreeksen, werken de back-ups mogelijk niet correct.
    > 
    > 

6. Klik op de pagina **Back-upconfiguratie** op **Opslaan**.
7. Klik op de pagina **Back-ups** op **Back-up**.

    ![Knop Back-upnow](./media/manage-backup/manual-backup.png)

    U ziet een voortgangsbericht tijdens het back-upproces.

Zodra het opslagaccount en de container zijn geconfigureerd, u op elk gewenst moment een handmatige back-up starten.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Geautomatiseerde back-ups configureren
1. Stel op de pagina **Back-upconfiguratie** **geplande back-up** in **op Aan**. 

    ![Geautomatiseerde back-ups inschakelen](./media/manage-backup/scheduled-backup.png)

2. Configureer het back-upschema naar wens en selecteer **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Gedeeltelijke back-ups configureren
Soms wilt u niet alles in uw app back-ups maken. Enkele voorbeelden:

* U [stelt wekelijkse back-ups](#configure-automated-backups) van uw app in die statische inhoud bevat die nooit verandert, zoals oude blogberichten of afbeeldingen.
* Uw app heeft meer dan 10 GB aan inhoud (dat is het maximale bedrag dat u tegelijk back-upsmaken).
* U wilt geen back-up maken van de logboekbestanden.

Met gedeeltelijke back-ups u precies kiezen welke bestanden u wilt back-ups maken.

> [!NOTE]
> Individuele databases in de back-up kan worden 4GB max, maar de totale maximale grootte van de back-up is 10GB

### <a name="exclude-files-from-your-backup"></a>Bestanden uitsluiten van uw back-up
Stel dat u een app hebt die logboekbestanden en statische afbeeldingen bevat die eenmaal een back-up zijn gemaakt en die niet zullen veranderen. In dergelijke gevallen u uitsluiten dat deze mappen en bestanden worden opgeslagen in uw toekomstige back-ups. Als u bestanden en mappen wilt uitsluiten `_backup.filter` van `D:\home\site\wwwroot` uw back-ups, maakt u een bestand in de map van uw app. Geef de lijst op met bestanden en mappen die u in dit bestand wilt uitsluiten. 

U uw bestanden openen `https://<app-name>.scm.azurewebsites.net/DebugConsole`door te navigeren naar. Meld u desgevraagd aan bij uw Azure-account.

Identificeer de mappen die u wilt uitsluiten van uw back-ups. U wilt bijvoorbeeld de gemarkeerde map en bestanden filteren.

![Afbeeldingenmap](./media/manage-backup/kudu-images.png)

Maak een `_backup.filter` bestand aangeroepen en zet de voorgaande lijst in het bestand, maar verwijder `D:\home`. Lijst één map of bestand per regel. Dus de inhoud van het bestand moet zijn:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Upload `_backup.filter` bestand `D:\home\site\wwwroot\` naar de map van uw site met [ftp](deploy-ftp.md) of een andere methode. Als u dat wenst, u `DebugConsole` het bestand rechtstreeks maken met Kudu en de inhoud daar invoegen.

Voer back-ups uit op dezelfde manier als normaal gesproken, [handmatig](#create-a-manual-backup) of [automatisch.](#configure-automated-backups) Nu zijn alle bestanden en mappen `_backup.filter` die zijn opgegeven in is uitgesloten van de toekomstige back-ups gepland of handmatig gestart. 

> [!NOTE]
> U herstelt gedeeltelijke back-ups van uw site op dezelfde manier als [een gewone back-up.](web-sites-restore.md) Het herstelproces doet het juiste.
> 
> Wanneer een volledige back-up wordt hersteld, wordt alle inhoud op de site vervangen door wat er in de back-up staat. Als er een bestand op de site staat, maar niet in de back-up, wordt het verwijderd. Maar wanneer een gedeeltelijke back-up wordt hersteld, blijft alle inhoud die zich in een van de mappen op de zwarte lijst bevindt, of een bestand op de zwarte lijst, zoals het is.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Hoe back-ups worden opgeslagen
Nadat u een of meer back-ups voor uw app hebt gemaakt, zijn de back-ups zichtbaar op **de** containerpagina van uw opslagaccount en uw app. In het opslagaccount bestaat elke`.zip` back-up uit een `.xml` bestand dat de `.zip` back-upgegevens bevat en een bestand dat een manifest van de bestandsinhoud bevat. U deze bestanden uitpakken en doorbladeren als u toegang wilt krijgen tot uw back-ups zonder daadwerkelijk een app-herstel uit te voeren.

De databaseback-up voor de app wordt opgeslagen in de hoofdmap van het .zip-bestand. Voor een SQL-database is dit een BACPAC-bestand (geen bestandsextensie) en kan worden geïmporteerd. Zie [Een BACPAC-bestand importeren om een nieuwe gebruikersdatabase te maken](https://technet.microsoft.com/library/hh710052.aspx)als u een SQL-database wilt maken op basis van de BACPAC-export.

> [!WARNING]
> Als u een van de bestanden in uw bestanden in de **container back-ups** van uw website wijzigt, kan de back-up ongeldig worden en dus niet-herstelbaar.
> 
> 

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U back-upbeheer automatiseren met scripts met behulp van Azure [CLI](/cli/azure/install-azure-cli) of [Azure PowerShell.](/powershell/azure/overview)

Zie voor monsters:

- [Azure CLI-voorbeelden](samples-cli.md)
- [Azure PowerShell-voorbeelden](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Volgende stappen
Zie [Een app herstellen in Azure](web-sites-restore.md)voor informatie over het herstellen van een app vanuit een back-up. 
