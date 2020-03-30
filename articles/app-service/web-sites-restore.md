---
title: App herstellen vanuit back-up
description: Meer informatie over het herstellen van uw app vanaf een back-up. Bepaalde gekoppelde databases kunnen samen met de app in één bewerking worden hersteld.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 1295080d0eec7a4e88029cdadd85863f5f40d034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689245"
---
# <a name="restore-an-app-in-azure"></a>Een app in Azure herstellen
In dit artikel ziet u hoe u een app herstellen in [Azure App Service](../app-service/overview.md) waarvan u eerder een back-up hebt gemaakt (zie Een [back-up van uw app maken in Azure](manage-backup.md)). U uw app herstellen met de gekoppelde databases on-demand naar een vorige status of een nieuwe app maken op basis van een van de back-ups van uw oorspronkelijke app. Azure App Service ondersteunt de volgende databases voor back-ups en herstel:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Herstellen van back-ups is beschikbaar voor apps die worden uitgevoerd in **de standaard-** en **premiumlaag.** Zie [Een app opschalen in Azure](manage-scale-up.md)voor informatie over het opschalen van uw app. **Premium-laag** maakt het mogelijk om een groter aantal dagelijkse back-ups uit te voeren dan **de standaardlaag.**

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Een app herstellen vanuit een bestaande back-up
1. Klik **op** de pagina Instellingen van uw app in de Azure-portal op **Back-ups** om de pagina **Back-ups** weer te geven. Klik vervolgens op **Herstellen**.
   
    ![Kies nu herstellen][ChooseRestoreNow]
2. Selecteer **op** de pagina Herstellen eerst de back-upbron.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    De **app-back-upoptie** toont u alle bestaande back-ups van de huidige app en u er eenvoudig een selecteren.
    Met de optie **Opslag** u een back-upZIP-bestand selecteren uit een bestaand Azure Storage-account en een container in uw abonnement.
    Als u een back-up van een andere app probeert te herstellen, gebruikt u de optie **Opslag.**
3. Geef vervolgens de bestemming voor het herstellen van de app op in **Bestemming herstellen**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Als u **Overschrijven**kiest, worden alle bestaande gegevens in uw huidige app gewist en overschreven. Voordat u op **OK**klikt, moet u ervoor zorgen dat dit precies is wat u wilt doen.
   > 
   > 
   
   > [!WARNING]
   > Als de App-service gegevens naar de database schrijft terwijl u deze herstelt, kan dit leiden tot symptomen zoals schending van primaire sleutel en gegevensverlies. Er wordt voorgesteld om de appservice eerst te stoppen voordat u de database herstelt.
   > 
   > 
   
    U **Bestaande app** selecteren om de app-back-up te herstellen naar een andere app in dezelfde brongroep. Voordat u deze optie gebruikt, moet u al een andere app in uw brongroep hebben gemaakt met het spiegelen van de databaseconfiguratie naar de app-back-up. U ook een **nieuwe** app maken om uw inhoud naar te herstellen.

4. Klik op **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Een back-up downloaden of verwijderen uit een opslagaccount
1. Selecteer **opslagaccounts**op de hoofdpagina **bladeren** van de Azure-portal . Er wordt een lijst met uw bestaande opslagaccounts weergegeven.
2. Selecteer het opslagaccount met de back-up die u wilt downloaden of verwijderen. De pagina voor het opslagaccount wordt weergegeven.
3. Selecteer op de pagina opslagaccount de gewenste container
   
    ![Bekijk containers][ViewContainers]
4. Selecteer back-upbestand dat u wilt downloaden of verwijderen.
   
    ![BekijkContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klik op **Downloaden** of **Verwijderen,** afhankelijk van wat u wilt doen.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Een herstelbewerking controleren
Als u details wilt zien over het succes of falen van de bewerking voor het herstellen van de app, navigeert u naar de pagina **Activiteitslogboek** in de Azure-portal.  
 

Scroll naar beneden om de gewenste herstelbewerking te vinden en klik om deze te selecteren.

Op de pagina details worden de beschikbare informatie weergegeven met betrekking tot de herstelbewerking.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U back-upbeheer automatiseren met scripts met behulp van Azure [CLI](/cli/azure/install-azure-cli) of [Azure PowerShell.](/powershell/azure/overview)

Zie voor monsters:

- [Azure CLI-voorbeelden](samples-cli.md)
- [Azure PowerShell-voorbeelden](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
