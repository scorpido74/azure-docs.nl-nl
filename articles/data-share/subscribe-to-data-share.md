---
title: 'Zelfstudie: Gegevens accepteren en ontvangen - Azure Data Share'
description: 'Zelfstudie: Gegevens accepteren en ontvangen met Azure Data Share'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: ce47bc5e880f15eaa1bbf07477673d2475e5a10a
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489915"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Zelfstudie: Gegevens accepteren en ontvangen met Azure Data Share  

In deze zelfstudie leert u hoe u een uitnodiging voor gegevensshares met behulp van Azure Data Share accepteert. U leert hoe u gegevens ontvangt die met u worden gedeeld, en hoe u een regelmatig vernieuwingsinterval kunt instellen om ervoor te zorgen dat u altijd de meest recente momentopname hebt van de gegevens die met u worden gedeeld. 

> [!div class="checklist"]
> * Een Azure Data Share-uitnodiging accepteren
> * Maak een Azure Data Share-account
> * Geef een doel op voor de gegevens
> * Maak een abonnement voor uw gegevensshare voor geplande vernieuwing

## <a name="prerequisites"></a>Vereisten
Voordat u een uitnodiging voor gegevensshares kunt accepteren, moet u een aantal Azure-resources inrichten. Deze worden hieronder weergegeven. 

Zorg ervoor dat alle vereisten zijn voltooid voordat u een uitnodiging voor gegevensshares accepteert. 

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Data Share-uitnodiging: Een uitnodiging van Microsoft Azure met een onderwerp met de titel: Azure Data Share-uitnodiging van **<yourdataprovider@domain.com>** .
* Registreer de [Microsoft.DataShare-resourceprovider](concepts-roles-permissions.md#resource-provider-registration) in het Azure-abonnement waarin u een Data Share-resource gaat maken, en in het Azure-abonnement waarin uw Azure-doelgegevensarchieven zich bevinden.

### <a name="receive-data-into-a-storage-account"></a>Gegevens ontvangen in een opslagaccount: 

* Een Azure Storage-account: Hier kunt u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken als u nog geen account hebt. 
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar.  

### <a name="receive-data-into-a-sql-based-target"></a>Gegevens ontvangen in een op SQL gebaseerd doel:

* Machtiging om naar databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot Azure SQL Database of Azure Synapse Analytics. U kunt dit doen via de volgende stappen: 
    1. Stel uzelf in als de Azure Active Directory-beheerder voor de SQL-server.
    1. Maak verbinding met Azure SQL Database/Data Warehouse met behulp van Azure Active Directory.
    1. Gebruik Queryeditor (preview) om het volgende script uit te voeren om de beheerde Data Share-identiteit toe te voegen als db_datareader, db_datawriter, db_ddladmin. U moet verbinding maken met behulp van Active Directory en niet via SQL Server-verificatie. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.         

* Toegang tot de firewall van SQL Server via het IP-adres van de client. U kunt dit doen via de volgende stappen: 
    1. Ga in SQL Server in de Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik op de wisselknop **Aan** om toegang tot Azure-services toe te staan.
    1. Klik op **+ IP-adres van client toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald wanneer u de volgende keer gegevens ontvangt in een SQL-doel vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Gegevens ontvangen in een Azure Data Explorer-cluster: 

* Een Azure Data Explorer-cluster in hetzelfde Azure-datacenter als het Data Explorer-cluster van de gegevensprovider: Hier kunt u een [Azure Data Explorer-account](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) maken als u nog geen account hebt. Als u het Azure-datacenter van het cluster van de gegevensprovider niet weet, kunt u het cluster later in het proces maken.
* Machtiging om naar het Azure Data Explorer-cluster te schrijven, aanwezig in *Microsoft.Kusto/clusters/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging om roltoewijzing toe te voegen aan het Azure Data Explorer-cluster, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Uitnodiging openen

1. U kunt de uitnodiging openen vanuit uw e-mail of rechtstreeks vanuit de Azure-portal. 

   Als u de uitnodiging wilt openen vanuit uw e-mail, gaat u in uw Postvak IN naar de uitnodiging van de gegevensprovider. De uitnodiging is van Microsoft Azure en heeft de titel **Azure Data Share-uitnodiging van <yourdataprovider@domain.com>** . Klik op **Uitnodiging weergeven** om de uitnodiging te bekijken in Azure. 

   Als u de uitnodiging rechtstreeks vanuit de Azure-portal wilt openen, gaat u in de Azure-portal naar **Data Share-uitnodigingen**. Hiermee kunt u de lijst met Data Share-uitnodigingen openen.

   ![Lijst met uitnodigingen](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer de share die u wilt bekijken. 

## <a name="accept-invitation"></a>Uitnodiging accepteren
1. Zorg ervoor dat u alle velden controleert, met inbegrip van de **Gebruiksvoorwaarden**. Als u akkoord gaat met de gebruiksvoorwaarden, moet u het vakje inschakelen om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Selecteer onder *Data Share-doelaccount* het abonnement en de resourcegroep waarin u de Data Share-gegevens wilt implementeren. 

   Selecteer voor het veld **Data Share-account** de optie **Nieuwe maken** als u geen bestaand Data Share-account hebt. Anders selecteert u een bestaand Data Share-account waarin u de informatie uit uw gegevensshare wilt accepteren. 

   In het veld **Ontvangen sharenaam** kunt u de standaardwaarde laten staan die is opgegeven door de gegevensprovider. U kunt er ook voor kiezen een nieuwe naam op te geven voor de ontvangen share. 

   Zodra u akkoord bent gegaan met de gebruiksvoorwaarden en een Data Share-account hebt opgegeven voor het beheren van de ontvangen share, selecteert u **Accepteren en configureren**. Er wordt een shareabonnement gemaakt. 

   ![Opties voor accepteren](./media/accept-options.png "Opties voor accepteren") 

   U ziet de ontvangen share nu in uw Data Share-account. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *Weigeren*. 

## <a name="configure-received-share"></a>Ontvangen share configureren
Volg de onderstaande stappen om te configureren waar u gegevens wilt ontvangen.

1. Selecteer het tabblad **Gegevenssets**. Schakel het selectievakje in van de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **+ Toewijzen aan doel** om een doelgegevensarchief te kiezen. 

   ![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

1. Selecteer het type doelgegevensarchief waarin u de gegevens wilt opslaan. Alle gegevensbestanden of tabellen in het doelgegevensarchief met hetzelfde pad en dezelfde naam worden overschreven. 

   Selecteer voor in-place delen een gegevensarchief op de opgegeven locatie. De locatie is het Azure-datacenter waar het brongegevensarchief van de gegevensprovider zich bevindt. Zodra de gegevensset is toegewezen, kunt u de koppeling in het doelpad volgen om toegang te krijgen tot de gegevens.

   ![Doelopslagaccount](./media/dataset-map-target-sql.png "Doelopslag") 

1. Als u wilt delen met behulp van momentopnamen, en de gegevensprovider een schema heeft opgesteld voor het maken van momentopnamen om de gegevens regelmatig bij te werken, kunt u het schema voor het maken van momentopnamen ook inschakelen door het tabblad **Schema voor momentopnamen** te selecteren. Schakel het selectievakje naast het schema voor momentopnamen in en selecteer **+ Inschakelen**.

   ![Schema voor momentopnamen inschakelen](./media/enable-snapshot-schedule.png "Schema voor momentopnamen inschakelen")

## <a name="trigger-a-snapshot"></a>Een momentopname activeren
Deze stappen zijn alleen van toepassing bij delen op basis van momentopnamen.

1. U kunt een momentopname activeren door **Momentopname activeren** te selecteren op het tabblad **Details**. Hier kunt u een volledige of incrementele momentopname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevensprovider ontvangt, selecteert u volledig kopiëren. 

   ![Momentopname activeren](./media/trigger-snapshot.png "Momentopname activeren") 

1. Wanneer de status van de laatste uitvoering *Geslaagd* is, gaat u naar het doelgegevensarchief om de ontvangen gegevens te bekijken. Selecteer **Gegevenssets** en klik op de koppeling in het doelpad. 

   ![Gegevenssets van consumenten](./media/consumer-datasets.png "Toewijzing voor gegevenssets van consumenten") 

## <a name="view-history"></a>Geschiedenis weergeven
Deze stap geldt alleen voor delen op basis van momentopnamen. Selecteer het tabblad **Geschiedenis** om de geschiedenis van uw momentopnamen weer te geven. Hier vindt u een geschiedenis van alle momentopnamen die zijn gegenereerd voor de afgelopen dertig dagen. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een Azure-gegevensshare accepteert en ontvangt. Voor meer informatie over Azure Data Share-concepten gaat u naar [Concepten: Azure Data Share-terminologie](terminology.md).