---
title: Back-up maken van app-Azure App Service
description: Meer informatie over het maken van back-ups van uw apps in Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e82a39b049c39cb4facfbc38b5e9020bb46de99b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637970"
---
# <a name="back-up-your-app-in-azure"></a>Back-up maken van uw app in Azure
Met de functie voor het maken en terugzetten van back-ups in [Azure app service](overview.md) kunt u eenvoudig hand matig app-back-ups maken of volgens een planning.  U kunt back-ups zo configureren dat ze tot een onbeperkte tijd worden bewaard. U kunt de app herstellen naar een moment opname van een vorige status door de bestaande app te overschrijven of te herstellen naar een andere app.

Zie [een app herstellen in azure](web-sites-restore.md)voor meer informatie over het herstellen van een app vanuit een back-up.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Waarvan wordt een back-up gemaakt?
App Service kunt een back-up maken van de volgende gegevens naar een Azure-opslag account en een container die u hebt geconfigureerd voor gebruik van uw app. 

* App-configuratie
* Bestandsinhoud
* Data base verbonden met uw app

De volgende database oplossingen worden ondersteund met de back-upfunctie: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Elke back-up is een volledige offline kopie van uw app, niet een incrementele update.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Vereisten en beperkingen
* Voor de functie voor back-up en herstellen moet het App Service-abonnement zich in de laag **Standard** of **Premium** . Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie over het schalen van uw app service-abonnement voor het gebruik van een hogere laag.  
  Met de **Premium** -laag kunt u een groter aantal dagelijkse back-ups maken dan met de **Standard** -laag.
* U hebt een Azure-opslag account en een container nodig in hetzelfde abonnement als de app waarvan u een back-up wilt maken. Zie [overzicht van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-account-overview)voor meer informatie over Azure Storage-accounts.
* Back-ups kunnen Maxi maal 10 GB aan app-en Data Base-inhoud hebben. Als de back-upgrootte deze limiet overschrijdt, wordt er een fout bericht weer geven.
* Back-ups van SSL ingeschakeld Azure Database for MySQL worden niet ondersteund. Als er een back-up is geconfigureerd, ontvangt u mislukte back-ups.
* Back-ups van SSL ingeschakeld Azure Database for PostgreSQL worden niet ondersteund. Als er een back-up is geconfigureerd, ontvangt u mislukte back-ups.
* Voor MySQL-data bases in-app wordt automatisch een back-up gemaakt zonder configuratie. Als u hand matige instellingen maakt voor MySQL-data bases in-app, zoals het toevoegen van verbindings reeksen, werken de back-ups mogelijk niet correct.
* Het gebruik van een opslag account waarvoor Firewall is ingeschakeld als bestemming voor uw back-ups wordt niet ondersteund. Als er een back-up is geconfigureerd, ontvangt u mislukte back-ups.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Een handmatige back-up maken
1. Ga in het [Azure Portal](https://portal.azure.com)naar de pagina van uw app en selecteer **back-ups**. De pagina **back-ups** wordt weer gegeven.
   
    ![Pagina Back-ups][ChooseBackupsPage]
   
   > [!NOTE]
   > Als het volgende bericht wordt weer gegeven, klikt u hierop om uw App Service-abonnement bij te werken voordat u kunt door gaan met back-ups.
   > Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie.  
   > ![Opslag account kiezen](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Klik op de pagina **back-up** op![ **configureren**
Klik op configureren](./media/web-sites-backup/ClickConfigure1.png)
3. Klik op de pagina **back-upconfiguratie** op **opslag: Niet geconfigureerd** voor het configureren van een opslag account.
   
    ![Opslagaccount kiezen][ChooseStorageAccount]
4. Kies het doel van de back-up door een **opslag account** en **container**te selecteren. Het opslag account moet deel uitmaken van hetzelfde abonnement als de app waarvan u een back-up wilt maken. Als u wilt, kunt u een nieuw opslag account of een nieuwe container maken op de respectieve pagina's. Wanneer u klaar bent, klikt u op **selecteren**.
   
    ![Opslagaccount kiezen](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Op de pagina **back-upconfiguratie** die nog geopend is, kunt u de **back-updatabase**configureren en vervolgens de data bases selecteren die u wilt toevoegen in de back-ups (SQL database of MySQL). Klik vervolgens op **OK**.  
   
    ![Opslagaccount kiezen](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Als u een data base in deze lijst wilt weer geven, moet de connection string bestaan in de sectie **verbindings reeksen** van de pagina **Toepassings instellingen** voor uw app. 
   >
   > Voor MySQL-data bases in-app wordt automatisch een back-up gemaakt zonder configuratie. Als u hand matige instellingen maakt voor MySQL-data bases in-app, zoals het toevoegen van verbindings reeksen, werken de back-ups mogelijk niet correct.
   > 
   > 
6. Klik op de pagina **back-upconfiguratie** op **Opslaan**.    
7. Klik op de pagina **back-ups** op **back-up**.
   
    ![Knop BackUpNow][BackUpNow]
   
    Er wordt een voortgangs bericht weer gegeven tijdens het back-upproces.

Zodra het opslag account en de container zijn geconfigureerd, kunt u op elk gewenst moment een hand matige back-up starten.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatische back-ups configureren
1. Stel op de pagina **back-upconfiguratie** de **geplande back-up** in **op aan**. 
   
    ![Opslagaccount kiezen](./media/web-sites-backup/05ScheduleBackup1.png)
2. Opties voor back-upschema worden weer gegeven, de **geplande back-up** ingesteld **op aan**, vervolgens het back-upschema configureren en op **OK**klikken.
   
    ![Automatische back-ups inschakelen][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Gedeeltelijke back-ups configureren
Soms wilt u niet een back-up maken van alles in uw app. Enkele voorbeelden:

* U [stelt wekelijkse back-ups](#configure-automated-backups) van uw app in die statische inhoud bevatten die nooit wordt gewijzigd, zoals oude blog berichten of installatie kopieën.
* Uw app heeft meer dan 10 GB aan inhoud (dat is de maximale hoeveelheid waarover u per keer een back-up kunt maken).
* U wilt geen back-up van de logboek bestanden maken.

Met gedeeltelijke back-ups kunt u precies bepalen van welke bestanden u een back-up wilt maken.

> [!NOTE]
> Afzonderlijke data bases in de back-up kunnen 4 GB Maxi maal zijn, maar de totale maximale grootte van de back-up is 10 GB

### <a name="exclude-files-from-your-backup"></a>Bestanden uitsluiten van de back-up
Stel dat u een app hebt die logboek bestanden en statische installatie kopieën bevat die één keer een back-up hebben en niet zullen worden gewijzigd. In dergelijke gevallen kunt u deze mappen en bestanden uitsluiten van opslag in uw toekomstige back-ups. Als u bestanden en mappen wilt uitsluiten van uw back-ups `_backup.filter` , maakt u `D:\home\site\wwwroot` een bestand in de map van uw app. Geef de lijst met bestanden en mappen op die u wilt uitsluiten van dit bestand. 

Een eenvoudige manier om uw bestanden te openen, is door kudu te gebruiken. Klik op **geavanceerde hulp middelen-> go-** instelling voor uw web-app om toegang te krijgen tot kudu.

![Kudu met behulp van portal][kudu-portal]

Bepaal de mappen die u wilt uitsluiten van uw back-ups.  Stel dat u de gemarkeerde map en bestanden wilt filteren.

![Map afbeeldingen][ImagesFolder]

Maak een bestand met `_backup.filter` de naam en plaats de voor gaande lijst in het bestand `D:\home`, maar verwijder het. Eén map of bestand per regel weer geven. De inhoud van het bestand moet dus het volgende zijn:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Upload `_backup.filter` het bestand naar `D:\home\site\wwwroot\` de map van uw site met behulp van [FTP](deploy-ftp.md) of een andere methode. Als u wilt, kunt u het bestand rechtstreeks maken met behulp van kudu `DebugConsole` en de inhoud daar invoegen.

Voer back-ups op dezelfde manier uit als u normaal gesp roken [hand matig](#create-a-manual-backup) of [automatisch](#configure-automated-backups)uitvoert. Alle bestanden en mappen die zijn opgegeven in `_backup.filter` , worden nu uitgesloten van de toekomstige back-ups die zijn gepland of hand matig zijn gestart. 

> [!NOTE]
> U kunt gedeeltelijke back-ups van uw site op dezelfde manier herstellen als [een gewone back-up](web-sites-restore.md). Het herstel proces heeft het recht.
> 
> Wanneer een volledige back-up wordt hersteld, wordt alle inhoud op de site vervangen door de naam van de back-up. Als een bestand zich op de site bevindt, maar niet bij de back-up wordt verwijderd. Maar wanneer een gedeeltelijke back-up wordt hersteld, blijft alle inhoud die zich bevindt in een van de genoteerde directory's of een bestand in de zwarte lijst, in de staat.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Hoe back-ups worden opgeslagen
Nadat u een of meer back-ups voor uw app hebt gemaakt, zijn de back-ups zichtbaar op de pagina **containers** van uw opslag account en uw app. In het opslag account bestaat elke back-up uit`.zip` een bestand met de back-upgegevens `.xml` en een bestand dat een manifest van `.zip` de bestands inhoud bevat. U kunt deze bestanden uitpakken en door bladeren als u toegang wilt krijgen tot uw back-ups zonder een app te herstellen.

De back-up van de Data Base voor de app wordt opgeslagen in de hoofdmap van het zip-bestand. Voor een SQL database is dit een BACPAC-bestand (geen bestands extensie) en kan het worden geïmporteerd. Als u een SQL database wilt maken op basis van de BACPAC-export, raadpleegt [u een BACPAC-bestand importeren om een nieuwe gebruikers database te maken](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Het wijzigen van een van de bestanden in de **websitebackups** -container kan ertoe leiden dat de back-up ongeldig wordt en daarom niet kan worden herstorable.
> 
> 

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt back-upbeheer automatiseren met scripts met behulp van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/overview).

Zie voor voor beelden:

- [Azure CLI-voorbeelden](samples-cli.md)
- [Voorbeelden van Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Volgende stappen
Zie [een app herstellen in azure](web-sites-restore.md)voor meer informatie over het herstellen van een app vanuit een back-up. 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

