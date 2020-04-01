---
title: 'Zelfstudie: Gegevens & ontvangen - Azure-gegevensshare'
description: Zelfstudie - Gegevens accepteren en ontvangen met Azure-gegevensshare
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083100"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Zelfstudie: Gegevens accepteren en ontvangen met Azure-gegevensshare  

In deze zelfstudie leert u hoe u een uitnodiging voor gegevensshare accepteert met Azure Data Share. U leert hoe u gegevens ontvangt die met u worden gedeeld en hoe u een regelmatig vernieuwingsinterval inschakelen om ervoor te zorgen dat u altijd de meest recente momentopname van de gegevens die met u worden gedeeld, hebt. 

> [!div class="checklist"]
> * Een uitnodiging voor Azure Data Share accepteren
> * Een Azure Data Share-account maken
> * Een bestemming voor uw gegevens opgeven
> * Een abonnement op uw gegevensaandeel maken voor geplande vernieuwing

## <a name="prerequisites"></a>Vereisten
Voordat u een uitnodiging voor gegevensshare accepteren, moet u een aantal Azure-bronnen inrichten, die hieronder worden vermeld. 

Zorg ervoor dat alle vereisten volledig zijn voordat u een uitnodiging voor gegevensshare accepteert. 

* Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een uitnodiging voor gegevensshare: een uitnodiging van Microsoft Azure **<yourdataprovider@domain.com>** met een onderwerp met de titel 'Uitnodiging voor azure-gegevensdelen van ''.
* Registreer de [Microsoft.DataShare-bronprovider](concepts-roles-permissions.md#resource-provider-registration) in het Azure-abonnement waar u een Data Share-bron en het Azure-abonnement maakt waarin uw doelAzure-gegevensopslag zich bevindt.

### <a name="receive-data-into-a-storage-account"></a>Ontvang gegevens in een opslagaccount: 

* Een Azure Storage-account: als u er nog geen hebt, u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)maken. 
* Toestemming om te schrijven naar het opslagaccount, dat aanwezig is in *Microsoft.Storage/storageAccounts/write*. Deze machtiging bestaat in de rol Inzender. 
* Toestemming om roltoewijzing toe te voegen aan het opslagaccount, dat aanwezig is in *Microsoft.Authorization/roltoewijzingen/schrijven*. Deze machtiging bestaat in de rol Eigenaar.  

### <a name="receive-data-into-a-sql-based-source"></a>Ontvang gegevens in een SQL-gebaseerde bron:

* Toestemming om naar databases op de SQL-server te schrijven, die aanwezig is in *Microsoft.Sql/servers/databases/write.* Deze machtiging bestaat in de rol Inzender. 
* Toestemming voor de beheerde identiteit van de gegevenssharebron om toegang te krijgen tot de Azure SQL Database of Azure SQL Data Warehouse. Dit kan via de volgende stappen: 
    1. Stel jezelf in als azure active directory-beheerder voor de SQL-server.
    1. Maak verbinding met het Azure SQL Database/Data Warehouse met Azure Active Directory.
    1. Gebruik Queryeditor (voorbeeld) om het volgende script uit te voeren om de beheerde identiteit voor gegevensdelen toe te voegen als een 'db_datareader, db_datawriter, db_ddladmin'. U moet verbinding maken met Active Directory en niet met SQL Server-verificatie. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Houd er rekening mee dat de *<share_acc_name>* de naam is van uw bron voor gegevensshare. Als u nog geen Data Share-bron hebt gemaakt, u later terugkomen op deze vereiste.         

* Client IP SQL Server Firewall-toegang. Dit kan via de volgende stappen: 
    1. Navigeer in SQL-server in Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik **op** de schakelaar om toegang tot Azure Services toe te staan.
    1. Klik **op +Client-IP toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald wanneer u de volgende keer gegevens ontvangt in een SQL-doel van Azure-portal. U ook een IP-bereik toevoegen. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Gegevens ontvangen in een Azure Data Explorer-cluster: 

* Een Azure Data Explorer-cluster in hetzelfde Azure-datacenter als het Data Explorer-cluster van de gegevensprovider: als u er nog geen hebt, u een [Azure Data Explorer-cluster maken.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) Als u het Azure-datacenter van het cluster van de gegevensprovider niet kent, u het cluster later in het proces maken.
* Toestemming om te schrijven naar het Azure Data Explorer-cluster, dat aanwezig is in *Microsoft.Kusto/clusters/write*. Deze machtiging bestaat in de rol Inzender. 
* Toestemming om roltoewijzing toe te voegen aan het Azure Data Explorer-cluster, dat aanwezig is in *Microsoft.Authorization/roltoewijzingen/schrijven*. Deze machtiging bestaat in de rol Eigenaar. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Uitnodiging openen

1. Controleer uw postvak IN op een uitnodiging van uw gegevensprovider. De uitnodiging is afkomstig van Microsoft Azure, getiteld **Azure Data Share invitation from <yourdataprovider@domain.com> **. Let op de naam van het aandeel om ervoor te zorgen dat u het juiste aandeel accepteert als er meerdere uitnodigingen zijn. 

1. Selecteer in **Uitnodiging weergeven** om uw uitnodiging in Azure weer te geven. Dit brengt u naar uw weergave Ontvangen aandelen.

   ![Uitnodigingen](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer het aandeel dat u wilt bekijken. 

## <a name="accept-invitation"></a>Uitnodiging accepteren
1. Controleer of alle velden worden beoordeeld, inclusief de **gebruiksvoorwaarden.** Als u akkoord gaat met de gebruiksvoorwaarden, moet u het vakje aanvinken om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Selecteer *onder Account Voor het delen van doelgegevens*de abonnements- en brongroep waarop u uw gegevensaandeel implementeert. 

   Selecteer Voor het **veld Account gegevensdelen** de optie **Nieuw maken** als u geen bestaand gegevensshare-account hebt. Selecteer anders een bestaand Data Share-account waaru uw gegevensaandeel in wilt accepteren. 

   Voor het veld **Ontvangen sharename** u de standaardopgegeven door de gegevensopgeven of een nieuwe naam opgeven voor het ontvangen aandeel. 

   ![Doelgegevensshare-account](./media/target-data-share.png "Doelgegevensshare-account") 

1. Zodra u akkoord bent gegaan met de gebruiksvoorwaarden en een locatie voor uw aandeel hebt opgegeven, selecteert u *Accepteren en configureren*. Er wordt een aandelenabonnement gemaakt.

   Voor delen op basis van momentopnamen wordt u in het volgende scherm gevraagd een doelopslagaccount te selecteren waarnaar uw gegevens moeten worden gekopieerd. 

   ![Opties accepteren](./media/accept-options.png "Opties accepteren") 

   Als u de uitnodiging nu liever accepteert, maar uw doelgegevensarchief op een later tijdstip configureert, selecteert u *Accepteren en later configureren.* Als u uw opslag later wilt blijven configureren, raadpleegt u de pagina [Gegevenstoewijzingen configureren](how-to-configure-mapping.md) voor gedetailleerde stappen over het hervatten van de configuratie van uw gegevensshare. 

   Zie pagina [Gegevenstoewijzingen configureren](how-to-configure-mapping.md) voor gedetailleerde stappen voor het hervatten van de configuratie van uw gegevensshare voor het delen op de plaats. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *Weigeren*. 

## <a name="configure-storage"></a>Opslag configureren
1. Selecteer *onder Instellingen voor doelopslag*het abonnements-, bron- en opslagaccount waaru uw gegevens wilt ontvangen. 

   ![Instellingen voor doelopslag](./media/target-storage-settings.png "Doelopslag") 

1. Als u uw gegevens regelmatig wilt bijwerken, moet u de momentopnameinstellingen inschakelen. Houd er rekening mee dat u alleen een momentopname-instellingsschema ziet als uw gegevensprovider deze heeft opgenomen in het gegevensaandeel. 

   ![Momentopname-instellingen](./media/snapshot-settings.png "Momentopname-instellingen") 

1. Selecteer *Opslaan*. 

> [!IMPORTANT]
> Als u SQL-gebaseerde gegevens ontvangt en die gegevens wilt ontvangen in een SQL-gebaseerde bron, gaat u naar het configureren van een handleiding voor [het toewijzen van gegevenssettoewijzing](how-to-configure-mapping.md) om te leren hoe u een SQL Server configureert als bestemming voor uw gegevensset. 

## <a name="trigger-a-snapshot"></a>Een momentopname activeren
Deze stappen zijn alleen van toepassing op delen op basis van momentopnamen.

1. U een momentopname activeren op het tabblad Ontvangen shares -> Details door **momentopname Trigger te**selecteren. Hier u een volledige of incrementele momentopname van uw gegevens activeren. Als het de eerste keer is dat u gegevens ontvangt van uw gegevensprovider, selecteert u volledige kopie. 

   ![Momentopname activeren](./media/trigger-snapshot.png "Momentopname activeren") 

1. Wanneer de status van de laatste run *is geslaagd,* gaat u naar het doelgegevensarchief om de ontvangen gegevens weer te geven. Selecteer **Gegevenssets**en klik op de koppeling in het doelpad. 

   ![Gegevenssets voor consumenten](./media/consumer-datasets.png "Toewijzing van consumentengegevenssets") 

## <a name="view-history"></a>Geschiedenis weergeven
Als u een geschiedenis van uw momentopnamen wilt weergeven, navigeert u naar ontvangen shares > Geschiedenis. Hier vindt u een geschiedenis van alle momentopnamen die zijn gegenereerd voor de afgelopen 60 dagen. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare accepteren en ontvangen. Ga voor meer informatie over azure data share-concepten verder naar [Concepten: Azure Data Share Terminology](terminology.md).