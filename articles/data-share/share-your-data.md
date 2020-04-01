---
title: 'Zelfstudie: Delen buiten uw organisatie - Azure-gegevensshare'
description: Zelfstudie - Gegevens delen met klanten en partners met Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083047"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Zelfstudie: Gegevens delen met Azure-gegevensshare  

In deze zelfstudie leert u hoe u een nieuwe Azure-gegevensshare instelt en begint met het delen van uw gegevens met klanten en partners buiten uw Azure-organisatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een gegevensshare.
> * Voeg gegevenssets toe aan uw gegevensshare.
> * Schakel een momentopnameschema in voor uw gegevensaandeel. 
> * Voeg ontvangers toe aan uw gegevensshare. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Het Azure-inlog-e-mailadres van uw ontvanger (het gebruik van de e-mailalias werkt niet).
* Als de bron Azure-gegevensarchief zich in een ander Azure-abonnement bevindt dan het abonnement dat u gebruikt om gegevensshare-bron te maken, registreert u de [Microsoft.DataShare-bronprovider](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin het Azure-gegevensarchief zich bevindt. 

### <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount:

* Een Azure Storage-account: als u er nog geen hebt, u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken
* Toestemming om te schrijven naar het opslagaccount, dat aanwezig is in *Microsoft.Storage/storageAccounts/write*. Deze machtiging bestaat in de rol Inzender.
* Toestemming om roltoewijzing toe te voegen aan het opslagaccount, dat aanwezig is in *Microsoft.Authorization/roltoewijzingen/schrijven*. Deze machtiging bestaat in de rol Eigenaar. 


### <a name="share-from-a-sql-based-source"></a>Delen vanuit een SQL-bron:

* Een Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) met tabellen en weergaven die u wilt delen.
* Toestemming om te schrijven naar de databases op SQL-server, die aanwezig is in *Microsoft.Sql/servers/databases/write.* Deze machtiging bestaat in de rol Inzender.
* Toestemming voor het delen van gegevens om toegang te krijgen tot het gegevensmagazijn. Dit kan via de volgende stappen: 
    1. Stel jezelf in als azure active directory-beheerder voor de SQL-server.
    1. Maak verbinding met het Azure SQL Database/Data Warehouse met Azure Active Directory.
    1. Gebruik Queryeditor (voorbeeld) om het volgende script uit te voeren om de beheerde identiteit van de gegevenssharebron toe te voegen als db_datareader. U moet verbinding maken met Active Directory en niet met SQL Server-verificatie. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Houd er rekening mee dat de *<share_acc_name>* de naam is van uw bron voor gegevensshare. Als u nog geen Data Share-bron hebt gemaakt, u later terugkomen op deze vereiste.  

* Een Azure SQL Database-gebruiker met 'db_datareader' toegang om te navigeren en de tabellen en/of weergaven te selecteren die u wilt delen. 

* Client IP SQL Server Firewall-toegang. Dit kan via de volgende stappen: 
    1. Navigeer in SQL-server in Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik **op** de schakelaar om toegang tot Azure Services toe te staan.
    1. Klik **op +Client-IP toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald wanneer u de volgende SQL-gegevens van Azure-portal deelt. U ook een IP-bereik toevoegen. 

### <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
* Een Azure Data Explorer-cluster met databases die u wilt delen.
* Toestemming om te schrijven naar Azure Data Explorer-cluster, dat aanwezig is in *Microsoft.Kusto/clusters/write*. Deze machtiging bestaat in de rol Inzender.
* Toestemming om roltoewijzing toe te voegen aan het Azure Data Explorer-cluster, dat aanwezig is in *Microsoft.Authorization/roltoewijzingen/schrijven*. Deze machtiging bestaat in de rol Eigenaar.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Een account voor gegevensshare maken

Maak een Azure Data Share-bron in een Azure-brongroep.

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

1. Zoeken naar *gegevensdelen*.

1. Selecteer Gegevensdelen en Selecteer **Maken**.

1. Vul de basisgegevens van uw Azure Data Share-bron in met de volgende gegevens. 

     **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Name | *datashareacount* | Geef een naam op voor uw account voor gegevensshare. |
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw account voor gegevensshare.|
    | Resourcegroep | *test-resource-groep* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *VS - oost 2* | Selecteer een regio voor uw account voor gegevensshare.
    | | |

1. Selecteer **Maken** om uw gegevensshare-account in te richten. Het inrichten van een nieuwe rekening voor gegevensshare duurt doorgaans ongeveer 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

## <a name="create-a-data-share"></a>Een gegevensaandeel maken

1. Navigeer naar de pagina Overzicht van gegevensdelen.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Start het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de gegevens in voor uw gegevensaandeel. Geef een naam, sharetype, beschrijving van de inhoud van delen en gebruiksvoorwaarden op (optioneel). 

    ![EnterShareDetails](./media/enter-share-details.png "Details van delen invoeren") 

1. Selecteer **Doorgaan**

1. Als u gegevenssets wilt toevoegen aan uw gegevensshare, selecteert u **Gegevenssets toevoegen**. 

    ![Gegevenssets](./media/datasets.png "Gegevenssets")

1. Selecteer het gegevenssettype dat u wilt toevoegen. U ziet een andere lijst met gegevenssettypen, afhankelijk van het sharetype (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. Als u deelt vanuit een Azure SQL Database of Azure SQL Data Warehouse, wordt u gevraagd om bepaalde SQL-referenties. Verifieer met de gebruiker die u hebt gemaakt als onderdeel van de vereisten.

    ![Gegevenssets toevoegen](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Navigeer naar het object dat u wilt delen en selecteer 'Gegevenssets toevoegen'. 

    ![Gegevenssets selecteren](./media/select-datasets.png "Gegevenssets selecteren")    

1. Voer op het tabblad Geadresseerden de e-mailadressen van uw gegevensconsument in door '+ Geadresseerde toevoegen' te selecteren. 

    ![Geadresseerden toevoegen](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**

1. Als u het type momentopnameshare hebt geselecteerd, u momentopnameschema configureren om uw gegevens aan uw gegevensconsument te voorzien. 

    ![Snapshots inschakelen](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begin- en herhalingsinterval. 

1. Selecteer **Doorgaan**

1. Controleer op het tabblad Controleren + Maken de inhoud van uw pakket, Instellingen, Geadresseerden en Synchronisatie-instellingen. Selecteer **Maken**

Uw Azure-gegevensaandeel is nu gemaakt en de ontvanger van uw gegevensaandeel is nu klaar om uw uitnodiging te accepteren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare maakt en ontvangers uitnodigt. Ga door met de zelfstudie [gegevens accepteren en ontvangen](subscribe-to-data-share.md) om meer te weten te komen over hoe een gegevensconsument een gegevensshare kan accepteren en ontvangen. 
