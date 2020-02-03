---
title: 'Zelf studie: & ontvangen gegevens accepteren-Azure-gegevens share'
description: Zelf studie-gegevens accepteren en ontvangen met behulp van Azure-gegevens share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f2acb89597ef877543a2c4cc46f395aede41034b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964495"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Zelf studie: gegevens accepteren en ontvangen met behulp van Azure-gegevens share  

In deze zelf studie leert u hoe u een uitnodiging voor gegevens delen kunt accepteren met behulp van Azure data share. U leert hoe u gegevens ontvangt die met u worden gedeeld, en hoe u een regel matig Vernieuwings interval kunt instellen om ervoor te zorgen dat u altijd de meest recente moment opname hebt van de gegevens die met u worden gedeeld. 

> [!div class="checklist"]
> * Een uitnodiging voor een Azure-gegevens share accepteren
> * Een Azure data share-account maken
> * Een bestemming voor uw gegevens opgeven
> * Een abonnement op uw gegevens share maken voor geplande vernieuwing

## <a name="prerequisites"></a>Vereisten
Voordat u een uitnodiging voor een gegevens share kunt accepteren, moet u een aantal Azure-resources inrichten, die hieronder worden weer gegeven. 

Zorg ervoor dat alle vereisten zijn voltooid voordat u een uitnodiging voor een gegevens share accepteert. 

* Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een uitnodiging voor een gegevens share: een uitnodiging van Microsoft Azure met een onderwerp met de titel ' uitnodiging voor Azure-gegevens delen van **<yourdataprovider@domain.com>** '.
* Registreer de resource provider micro soft. DataShare. Volg de instructies in [Troubleshooting Azure data share](data-share-troubleshoot.md) om de resource provider van de gegevens share te registreren.

### <a name="receive-data-into-a-storage-account"></a>Gegevens ontvangen in een opslag account: 

* Een Azure Storage account: als u er nog geen hebt, kunt u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)maken. 
* Toestemming om te schrijven naar het opslag account dat aanwezig is in *micro soft. Storage/Storage accounts/write*. Deze machtiging bevindt zich in de rol Inzender. 
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account dat aanwezig is in *micro soft. autorisatie/roltoewijzingen/schrijven*. Deze machtiging bevindt zich in de rol van eigenaar.  

### <a name="receive-data-into-a-sql-based-source"></a>Gegevens ontvangen in een bron op basis van SQL:

* Toestemming om te schrijven naar data bases op de SQL-Server, die aanwezig is in *micro soft. SQL/servers/data bases/schrijven*. Deze machtiging bevindt zich in de rol Inzender. 
* Machtiging voor de beheerde identiteit van de gegevens share bron om toegang te krijgen tot de Azure SQL Database of Azure SQL Data Warehouse. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Stel uzelf in als de Azure Active Directory beheerder voor de SQL-Server.
    1. Maak verbinding met de Azure SQL Database/data warehouse met behulp van Azure Active Directory.
    1. Gebruik de query-editor (preview) om het volgende script uit te voeren om de door de gegevens share beheerde identiteit toe te voegen als een db_datareader, db_datawriter, db_ddladmin. U moet verbinding maken met behulp van Active Directory en niet SQL Server-verificatie. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Houd er rekening mee dat de *< share_acc_name >* de naam van uw gegevens share bron is. Als u nog geen resource voor gegevens delen hebt gemaakt, kunt u later terug naar deze vereiste.         

* Client-IP SQL Server toegang tot Firewall. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Ga in SQL Server in Azure Portal naar *firewalls en virtuele netwerken*
    1. Klik op de **aan/uit** om toegang tot Azure-Services toe te staan.
    1. Klik op **+ client IP toevoegen** en klik op **Opslaan**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald wanneer u de volgende keer dat u SQL-gegevens deelt van Azure Portal. U kunt ook een IP-bereik toevoegen. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Gegevens ontvangen in een Azure Data Explorer-cluster: 

* Een Azure Data Explorer-cluster in hetzelfde Azure-Data Center als het Data Explorer cluster van de gegevens provider: als u er nog geen hebt, kunt u een [azure Data Explorer-cluster](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)maken. Als u het Azure-Data Center van het cluster van de gegevens provider niet weet, kunt u het cluster later in het proces maken.
* Toestemming om te schrijven naar het Azure Data Explorer-cluster, dat aanwezig is in *micro soft. Kusto/clusters/schrijven*. Deze machtiging bevindt zich in de rol Inzender. 
* Machtiging voor het toevoegen van roltoewijzing aan het Azure Data Explorer-cluster, dat aanwezig is in *micro soft. autorisatie/roltoewijzingen/schrijven*. Deze machtiging bevindt zich in de rol van eigenaar. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Uitnodiging openen

1. Controleer uw postvak in voor een uitnodiging van uw gegevens provider. De uitnodiging is van Microsoft Azure, met de titel **uitnodiging voor Azure data share van <yourdataprovider@domain.com>** . Noteer de naam van de share om ervoor te zorgen dat u de juiste share accepteert als er meerdere uitnodigingen zijn. 

1. Selecteer on- **uitnodiging weer geven** om uw uitnodiging in azure te bekijken. Hiermee gaat u naar de weer gave van de ontvangen shares.

   ![Moment](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer de share die u wilt weer geven. 

## <a name="accept-invitation"></a>Uitnodiging accepteren
1. Zorg ervoor dat alle velden worden gecontroleerd, met inbegrip **van de gebruiks voorwaarden**. Als u akkoord gaat met de gebruiksrecht overeenkomst, moet u het selectie vakje inschakelen om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Onder *doel gegevens share account*selecteert u het abonnement en de resource groep waarin u uw gegevens share gaat implementeren. 

   Selecteer voor het veld **gegevens share account** de optie **nieuwe maken** als u geen bestaand gegevens share-account hebt. Anders selecteert u een bestaand gegevens share-account waarnaar u uw gegevens share wilt accepteren. 

   Voor het veld **ontvangen share naam** kunt u de standaard waarde die wordt opgegeven door de gegevens opgeven, of een nieuwe naam voor de ontvangen share opgeven. 

   ![Doel gegevens share account](./media/target-data-share.png "Doel gegevens share account") 

1. Zodra u akkoord gaat met de gebruiks voorwaarden en een locatie hebt opgegeven voor uw share, selecteert u op *accepteren en configureren*. Er wordt een share abonnement gemaakt.

   Voor het delen op basis van moment opnamen vraagt het volgende scherm u een doel-opslag account te selecteren waarnaar uw gegevens moeten worden gekopieerd. 

   ![Opties accepteren](./media/accept-options.png "Opties accepteren") 

   Als u de uitnodiging nu wilt accepteren, maar uw doel gegevens archief op een later tijdstip wilt configureren, selecteert u *accepteren en later configureren*. Ga naar de pagina [gegevensset configureren](how-to-configure-mapping.md) voor gedetailleerde stappen voor het hervatten van de configuratie van de gegevens share om verder te gaan met het configureren van uw opslag. 

   Zie de pagina [gegevensset-toewijzingen configureren](how-to-configure-mapping.md) voor gedetailleerde stappen voor het hervatten van de configuratie van de gegevens share voor in-place delen. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *afwijzen*. 

## <a name="configure-storage"></a>Opslag configureren
1. Onder *instellingen voor doel opslag*selecteert u het abonnement, de resource groep en het opslag account waarnaar u uw gegevens wilt ontvangen. 

   ![Instellingen voor doel opslag](./media/target-storage-settings.png "Doel opslag") 

1. Als u regel matig een update van uw gegevens wilt ontvangen, moet u de moment opname-instellingen inschakelen. Houd er rekening mee dat u alleen een schema voor het instellen van moment opnamen kunt zien als uw gegevens provider deze in de gegevens share heeft opgenomen. 

   ![Moment opname-instellingen](./media/snapshot-settings.png "Moment opname-instellingen") 

1. Selecteer *Opslaan*. 

> [!IMPORTANT]
> Als u op SQL gebaseerde gegevens ontvangt en deze gegevens wilt ontvangen in een bron op basis van SQL, gaat u naar [een toewijzings handleiding voor een gegevensset configureren](how-to-configure-mapping.md) voor meer informatie over het configureren van een SQL Server als bestemming voor uw gegevensset. 

## <a name="trigger-a-snapshot"></a>Een moment opname activeren
Deze stappen zijn alleen van toepassing op het delen op basis van moment opnamen.

1. U kunt een moment opname activeren op het tabblad ontvangen shares-> Details door **moment opname activeren**te selecteren. Hier kunt u een volledige of incrementele moment opname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevens provider ontvangt, selecteert u volledig kopiëren. 

   ![Moment opname activeren](./media/trigger-snapshot.png "Moment opname activeren") 

1. Wanneer de status van de laatste uitvoering is *geslaagd*, gaat u naar doel gegevens archief om de ontvangen gegevens weer te geven. Selecteer **gegevens sets**en klik op de koppeling in het doelpad. 

   ![Consumer gegevens sets](./media/consumer-datasets.png "Toewijzing dataset van consument") 

## <a name="view-history"></a>Geschiedenis weergeven
Als u een geschiedenis van uw moment opnamen wilt weer geven, gaat u naar ontvangen shares-> geschiedenis. Hier vindt u een geschiedenis van alle moment opnamen die zijn gegenereerd voor de afgelopen 60 dagen. 

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u een Azure-gegevens share kunt accepteren en ontvangen. Voor meer informatie over concepten van Azure data share gaat u verder met [concepten: Azure data share terminologie](terminology.md).