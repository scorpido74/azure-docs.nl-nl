---
title: 'Zelf studie: delen buiten uw organisatie-Azure-gegevens share'
description: 'Zelf studie: gegevens delen met klanten en partners met behulp van de Azure-gegevens share'
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083047"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Zelf studie: gegevens delen met Azure data share  

In deze zelf studie leert u hoe u een nieuwe Azure-gegevens share instelt en hoe u uw gegevens met klanten en partners buiten uw Azure-organisatie gaat delen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevensshare.
> * Voeg gegevenssets toe aan uw gegevensshare.
> * Maak een momentopname schema voor uw gegevens share. 
> * Voeg ontvangers toe aan uw gegevensshare. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Het e-mail adres van uw ontvanger voor Azure (met hun e-mail alias werkt niet).
* Als de bron-Azure-gegevens opslag zich in een ander Azure-abonnement bevindt dan de service die u gebruikt om een gegevens share bron te maken, registreert u de [resource provider micro soft. DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waar het Azure-gegevens archief zich bevindt. 

### <a name="share-from-a-storage-account"></a>Delen vanuit een opslag account:

* Een Azure Storage account: als u er nog geen hebt, kunt u een [Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken
* Toestemming om te schrijven naar het opslag account dat aanwezig is in *micro soft. Storage/Storage accounts/write*. Deze machtiging bevindt zich in de rol Inzender.
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account dat aanwezig is in *micro soft. autorisatie/roltoewijzingen/schrijven*. Deze machtiging bevindt zich in de rol van eigenaar. 


### <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL:

* Een Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) met tabellen en weer gaven die u wilt delen.
* Toestemming om te schrijven naar de data bases op SQL Server, die aanwezig zijn in *micro soft. SQL/servers/data bases/schrijven*. Deze machtiging bevindt zich in de rol Inzender.
* Machtiging voor de gegevens share om toegang te krijgen tot het Data Warehouse. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Stel uzelf in als de Azure Active Directory beheerder voor de SQL-Server.
    1. Maak verbinding met de Azure SQL Database/data warehouse met behulp van Azure Active Directory.
    1. Gebruik de query-editor (preview) om het volgende script uit te voeren om de door de resource beheerde identiteit als db_datareader toe te voegen. U moet verbinding maken met behulp van Active Directory en niet SQL Server-verificatie. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Houd er rekening mee dat de *< share_acc_name >* de naam van uw gegevens share bron is. Als u nog geen resource voor gegevens delen hebt gemaakt, kunt u later terug naar deze vereiste.  

* Een Azure SQL Database gebruiker met ' db_datareader ' toegang om te navigeren en de tabellen en/of weer gaven te selecteren die u wilt delen. 

* Client-IP SQL Server toegang tot Firewall. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Ga in SQL Server in Azure Portal naar *firewalls en virtuele netwerken*
    1. Klik op de **aan/uit** om toegang tot Azure-Services toe te staan.
    1. Klik op **+ client IP toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald wanneer u de volgende keer dat u SQL-gegevens deelt van Azure Portal. U kunt ook een IP-bereik toevoegen. 

### <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
* Een Azure Data Explorer-cluster met data bases die u wilt delen.
* Toestemming om te schrijven naar Azure Data Explorer cluster, dat aanwezig is in *micro soft. Kusto/clusters/schrijven*. Deze machtiging bevindt zich in de rol Inzender.
* Machtiging voor het toevoegen van roltoewijzing aan het Azure Data Explorer-cluster, dat aanwezig is in *micro soft. autorisatie/roltoewijzingen/schrijven*. Deze machtiging bevindt zich in de rol van eigenaar.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een gegevens share-account maken

Een Azure-gegevens share bron maken in een Azure-resource groep.

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

1. Zoeken naar *gegevens share*.

1. Selecteer gegevens share en selecteer **maken**.

1. Vul de basis gegevens van uw Azure-gegevens share bron in met de volgende gegevens. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Naam | *datashareacount* | Geef een naam op voor uw gegevens share-account. |
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

1. Vul de details in voor uw gegevens share. Geef een naam, type share, beschrijving van share-inhoud en gebruiks voorwaarden op (optioneel). 

    ![EnterShareDetails](./media/enter-share-details.png "Share gegevens invoeren") 

1. Selecteer **door gaan**

1. Selecteer gegevens **sets toevoegen**om gegevens sets toe te voegen aan uw gegevens share. 

    ![Gegevenssets](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. U ziet een andere lijst met typen gegevensset, afhankelijk van het share type (moment opname of in-place) dat u in de vorige stap hebt geselecteerd. Als u het delen van een Azure SQL Database of Azure SQL Data Warehouse, wordt u gevraagd om een aantal SQL-referenties op te vragen. Verificatie met behulp van de gebruiker die u hebt gemaakt als onderdeel van de vereisten.

    ![AddDatasets](./media/add-datasets.png "Gegevens sets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer gegevens sets toevoegen. 

    ![SelectDatasets](./media/select-datasets.png "Gegevens sets selecteren")    

1. Voer op het tabblad ontvangers in de e-mail adressen van uw gegevens consument in door + ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **door gaan**

1. Als u het type snap shot share hebt geselecteerd, kunt u het momentopname schema configureren om updates van uw gegevens aan uw gegevens consument toe te voegen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Moment opnamen inschakelen") 

1. Selecteer een begin tijd en een herhalings interval. 

1. Selecteer **door gaan**

1. Controleer op het tabblad controleren en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**

Uw Azure-gegevens share is nu gemaakt en de ontvanger van uw gegevens share is nu klaar om uw uitnodiging te accepteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie leert u hoe u een Azure-gegevens share maakt en ontvangers uitnodigt. Ga door naar de zelf studie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md) voor meer informatie over hoe een gegevens gebruiker een gegevens share kan accepteren en ontvangen. 
