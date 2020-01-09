---
title: 'Zelf studie: delen buiten uw organisatie-Azure-gegevens share'
description: 'Zelf studie: gegevens delen met klanten en partners met behulp van de Azure-gegevens share'
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 8749f7dee2ceeb09e37cc97d4e5bfe76c52e2da6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438741"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Zelf studie: gegevens delen met Azure data share  

In deze zelf studie leert u hoe u een nieuwe Azure-gegevens share instelt en hoe u uw gegevens met klanten en partners buiten uw Azure-organisatie gaat delen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevensshare.
> * Voeg gegevenssets toe aan uw gegevensshare.
> * Maak een synchronisatieschema mogelijk voor uw gegevensshare. 
> * Voeg ontvangers toe aan uw gegevensshare. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Het e-mail adres van uw ontvanger voor Azure (met hun e-mail alias werkt niet).

### <a name="share-from-a-storage-account"></a>Delen vanuit een opslag account:

* Een Azure Storage account: als u er nog geen hebt, kunt u een [Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account dat aanwezig is in de machtiging *micro soft. Authorization/roltoewijzingen/schrijven* . Deze machtiging bevindt zich in de rol van eigenaar. 

### <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL:

* Een Azure SQL Database of Azure SQL Data Warehouse met tabellen en weer gaven die u wilt delen.
* Machtiging voor de gegevens share om toegang te krijgen tot het Data Warehouse. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Stel uzelf in als de Azure Active Directory beheerder voor de-server.
    1. Maak verbinding met de Azure SQL Database/data warehouse met behulp van Azure Active Directory.
    1. Gebruik de query-editor (preview) om het volgende script uit te voeren om de gegevens share-MSI als db_owner toe te voegen. U moet verbinding maken met behulp van Active Directory en niet SQL Server-verificatie. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Houd er rekening mee dat de *< share_acc_name >* de naam is van uw gegevens share-account. Als u nog geen gegevens share-account hebt gemaakt, kunt u later terugkeren naar deze vereiste.  

* Een [Azure SQL database gebruiker met `db_owner` toegang](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users) om te navigeren en de tabellen en/of weer gaven te selecteren die u wilt delen. 

* Client-IP SQL Server toegang tot Firewall: dit kunt u doen door de volgende stappen uit te voeren: 1. Navigeer naar *firewalls en virtuele netwerken* 1. Klik op de **aan/uit** om toegang tot Azure-Services toe te staan. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een gegevens share-account maken

Een Azure-gegevens share bron maken in een Azure-resource groep.

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

1. Zoeken naar *gegevens share*.

1. Selecteer gegevens share en selecteer **maken**.

1. Vul de basis gegevens van uw Azure-gegevens share bron in met de volgende gegevens. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Name | *datashareacount* | Geef een naam op voor uw gegevens share-account. |
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevens share-account.|
    | Resourcegroep | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *VS-Oost 2* | Selecteer een regio voor uw gegevens share-account.
    | | |

1. Selecteer **maken** om uw gegevens share-account in te richten. Het inrichten van een nieuw gegevens share account duurt doorgaans ongeveer 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="create-a-data-share"></a>Een gegevens share maken

1. Ga naar de pagina overzicht van gegevens delen.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw gegevens share. Geef een naam, beschrijving van de share-inhoud en gebruiks voorwaarden op (optioneel). 

    ![EnterShareDetails](./media/enter-share-details.png "Share gegevens invoeren") 

1. Selecteer **door gaan**

1. Selecteer gegevens **sets toevoegen**om gegevens sets toe te voegen aan uw gegevens share. 

    ![Gegevenssets](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Als u gegevens deelt vanuit een Azure SQL Database of Azure SQL Data Warehouse, wordt u gevraagd om een aantal SQL-referenties op te vragen. Verificatie met behulp van de gebruiker die u hebt gemaakt als onderdeel van de vereisten.

    ![AddDatasets](./media/add-datasets.png "Gegevens sets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer gegevens sets toevoegen. 

    ![SelectDatasets](./media/select-datasets.png "Gegevens sets selecteren")    

1. Voer op het tabblad ontvangers in de e-mail adressen van uw gegevens consument in door + ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **door gaan**

1. Als u wilt dat uw gegevens gebruiker incrementele updates van uw gegevens kan ophalen, moet u het schema voor de moment opname inschakelen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Moment opnamen inschakelen") 

1. Selecteer een begin tijd en een herhalings interval. 

1. Selecteer **door gaan**

1. Controleer op het tabblad controleren en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**

Uw Azure-gegevens share is nu gemaakt en de ontvanger van uw gegevens share is nu klaar om uw uitnodiging te accepteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie leert u hoe u een Azure-gegevens share maakt en ontvangers uitnodigt. Ga door naar de zelf studie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md) voor meer informatie over hoe een gegevens gebruiker een gegevens share kan accepteren en ontvangen. 
