---
title: 'Zelfstudie: Delen buiten uw organisatie - Azure Data Share'
description: 'Zelfstudie: Gegevens delen met klanten en partners met behulp van Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/28/2020
ms.openlocfilehash: 4f3c9d739f959670700182baf80c4a08e53d8587
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259061"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Zelfstudie: Gegevens delen met Azure Data Share  

In deze zelfstudie leert u hoe u een nieuwe Azure Data Share instelt en hoe u uw gegevens met klanten en partners buiten uw Azure-organisatie kunt delen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevensshare.
> * Voeg gegevenssets toe aan uw gegevensshare.
> * Schakel een schema voor momentopnamen in voor uw gegevensshare. 
> * Voeg ontvangers toe aan uw gegevensshare. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Het e-mailadres voor Azure van de ontvanger (de e-mailalias werkt niet).
* Als de bron-Azure-gegevensopslag zich in een ander Azure-abonnement bevindt dan de opslag die u gebruikt om een Data Share-resource te maken, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="share-from-a-storage-account"></a>Delen vanaf een opslagaccount:

* Een Azure Storage-account: U kunt een gratis [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) aanmaken als u nog niet een hebt
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol Inzender.
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar. 


### <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL:

* Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) met tabellen en weergaven die u wilt delen.
* Machtiging om naar de databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol Inzender.
* Machtiging voor de gegevensshare om toegang te krijgen tot de datawarehouse. U kunt dit doen via de volgende stappen: 
    1. Stel uzelf in als de Azure Active Directory-beheerder voor de SQL-server.
    1. Maak verbinding met Azure SQL Database/Data Warehouse met behulp van Azure Active Directory.
    1. Gebruik Queryeditor (preview) om het volgende script uit te voeren en de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader. U moet verbinding maken met behulp van Active Directory en niet via SQL Server-verificatie. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Een Azure SQL Database-gebruiker met db_datareader-toegang om door de tabellen en/of weergaven die u wilt delen te navigeren en ze te selecteren. 

* Toegang tot de firewall van SQL Server via het IP-adres van de client. U kunt dit doen via de volgende stappen: 
    1. Ga in SQL Server in de Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik op de wisselknop **Aan** om toegang tot Azure-services toe te staan.
    1. Klik op **+ IP-adres van client toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen. 

### <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
* Een Azure Data Explorer-cluster met databases die u wilt delen.
* Machtiging om naar het Azure Data Explorer-cluster te schrijven, aanwezig in *Microsoft.Kusto/clusters/write*. Deze machtiging maakt onderdeel uit van de rol Inzender.
* Machtiging om roltoewijzing toe te voegen aan het Azure Data Explorer-cluster, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een Data Share-account maken

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Selecteer de menuknop in de linkerbovenhoek van de portal, en selecteer vervolgens **Een resource maken** (+).

1. Zoek naar *Data Share*.

1. Selecteer Data Share en selecteer **Maken**.

1. Vul de basisgegevens van uw Azure Data Share-resource in met de volgende informatie. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevensshare-account.|
    | Resourcegroep | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
    | Naam | *datashareaccount* | Geef een naam op voor uw gegevensshare-account. |
    | | |

1. Selecteer **Beoordelen en maken** en vervolgens **Maken** om uw gegevensshare-account in te richten. Het inrichten van een nieuw gegevensshare-account duurt doorgaans 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="create-a-share"></a>Een share maken

1. Ga naar de overzichtspagina van uw gegevensshare.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![EnterShareDetails](./media/enter-share-details.png "Gegevens van share invoeren") 

1. Selecteer **Doorgaan**.

1. Selecteer **Gegevenssets toevoegen** om gegevenssets toe te voegen aan de share. 

    ![Gegevenssets toevoegen aan de share](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Welke lijst met typen gegevensset wordt weergegeven, is afhankelijk van het type share (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. Als u deelt vanuit een Azure SQL Database of Azure SQL Data Warehouse, wordt u om SQL-referenties gevraagd. Voer de verificatie uit met behulp van de gebruiker die u hebt gemaakt als onderdeel van de vereisten.

    ![AddDatasets](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer Gegevenssets toevoegen. 

    ![SelectDatasets](./media/select-datasets.png "Gegevenssets selecteren")    

1. Voer op het tabblad Ontvangers de e-mailadressen in van uw gegevensgebruiker door + Ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**.

1. Als u het type share momentopname hebt geselecteerd, kunt u het momentopnameschema configureren om updates van uw gegevens naar uw gegevensgebruiker te sturen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad Beoordelen en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**.

Uw Azure-gegevensshare is nu gemaakt en de ontvanger van uw gegevensshare kan nu uw uitnodiging accepteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare maakt en ontvangers uitnodigt. Voor meer informatie over hoe een gegevensgebruiker een gegevensshare kan accepteren en ontvangen, gaat u door naar de zelfstudie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md). 
