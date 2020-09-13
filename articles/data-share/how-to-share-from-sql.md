---
title: Gegevens delen en ontvangen van Azure SQL Database en Azure Synapse Analytics
description: Meer informatie over het delen en ontvangen van gegevens van Azure SQL Database en Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: e813921727ee08bf9a76c0a2dbfe15f45fe4db79
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89490068"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Gegevens delen en ontvangen van Azure SQL Database en Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure data share ondersteunt het delen op basis van moment opnamen Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW). In dit artikel wordt uitgelegd hoe u gegevens uit deze bronnen kunt delen en ontvangen.

Azure data share ondersteunt het delen van tabellen of weer gaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW). Gegevens gebruikers kunnen ervoor kiezen om de gegevens in Azure Data Lake Storage Gen2 of Azure Blob Storage te accepteren als CSV-of Parquet-bestand, evenals in Azure SQL Database en Azure Synapse Analytics als tabellen.

Wanneer gegevens worden geaccepteerd in Azure Data Lake Store Gen2 of Azure Blob Storage, overschrijven volledige moment opnamen de inhoud van het doel bestand als dit al bestaat.
Wanneer gegevens in de tabel worden ontvangen en als de doel tabel nog niet bestaat, maakt Azure-gegevens share de SQL-tabel met het bron schema. Als er al een doel tabel met dezelfde naam bestaat, wordt deze verwijderd en overschreven met de laatste volledige moment opname. Incrementele moment opnamen worden momenteel niet ondersteund.

## <a name="share-data"></a>Gegevens delen

### <a name="prerequisites-to-share-data"></a>Vereisten voor het delen van gegevens

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Het e-mailadres voor Azure van de ontvanger (de e-mailalias werkt niet).
* Als de bron-Azure-gegevensopslag zich in een ander Azure-abonnement bevindt dan de opslag die u gebruikt om een Data Share-resource te maken, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="prerequisites-for-sql-source"></a>Vereisten voor SQL-bron

* Een Azure SQL Database of Azure Synapse Analytics (voorheen SQL Data Warehouse) met tabellen en weer gaven die u wilt delen.
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

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Een Data Share-account maken

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

### <a name="create-a-share"></a>Een share maken

1. Ga naar de overzichtspagina van uw gegevensshare.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![EnterShareDetails](./media/enter-share-details.png "Gegevens van share invoeren") 

1. Selecteer **Doorgaan**.

1. Selecteer **Gegevenssets toevoegen** om gegevenssets toe te voegen aan de share. 

    ![Gegevenssets toevoegen aan de share](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Welke lijst met typen gegevensset wordt weergegeven, is afhankelijk van het type share (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. 

    ![AddDatasets](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Selecteer uw SQL-Server, geef referenties op en selecteer **volgende** om naar het object te navigeren dat u wilt delen en selecteer gegevens sets toevoegen. 

    ![SelectDatasets](./media/select-datasets-sql.png "Gegevenssets selecteren")    

1. Voer op het tabblad Ontvangers de e-mailadressen in van uw gegevensgebruiker door + Ontvanger toevoegen te selecteren. 

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**.

1. Als u het type share momentopname hebt geselecteerd, kunt u het momentopnameschema configureren om updates van uw gegevens naar uw gegevensgebruiker te sturen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad Beoordelen en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**.

Uw Azure-gegevensshare is nu gemaakt en de ontvanger van uw gegevensshare kan nu uw uitnodiging accepteren. 

## <a name="receive-data"></a>Gegevens ontvangen

### <a name="prerequisites-to-receive-data"></a>Vereisten voor het ontvangen van gegevens
Voordat u een uitnodiging voor gegevensshares kunt accepteren, moet u een aantal Azure-resources inrichten. Deze worden hieronder weergegeven. 

Zorg ervoor dat alle vereisten zijn voltooid voordat u een uitnodiging voor gegevensshares accepteert. 

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Data Share-uitnodiging: Een uitnodiging van Microsoft Azure met een onderwerp met de titel: Azure Data Share-uitnodiging van **<yourdataprovider@domain.com>** .
* Registreer de [Microsoft.DataShare-resourceprovider](concepts-roles-permissions.md#resource-provider-registration) in het Azure-abonnement waarin u een Data Share-resource gaat maken, en in het Azure-abonnement waarin uw Azure-doelgegevensarchieven zich bevinden.

### <a name="prerequisites-for-target-storage-account"></a>Vereisten voor het doel-opslag account
Als u ervoor kiest om gegevens te ontvangen in Azure Storage, hieronder volgt de lijst met vereisten.

* Een Azure Storage-account: Hier kunt u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) maken als u nog geen account hebt. 
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging om roltoewijzing toe te voegen aan het opslagaccount, aanwezig in *Microsoft.Authorization/role assignments/write*. Deze machtiging maakt onderdeel uit van de rol Eigenaar.  

### <a name="prerequisites-for-sql-target"></a>Vereisten voor SQL-doel
Als u ervoor kiest om gegevens te ontvangen in Azure SQL Database, wordt in azure Synapse Analytics hieronder de lijst met vereisten weer gegeven.

* Machtiging om naar databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol Inzender. 
* Machtiging voor de beheerde identiteit van de gegevens share bron om toegang te krijgen tot de Azure SQL Database of Azure Synapse Analytics. U kunt dit doen via de volgende stappen: 
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

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Uitnodiging openen

1. U kunt de uitnodiging openen vanuit uw e-mail of rechtstreeks vanuit de Azure-portal. 

   Als u de uitnodiging wilt openen vanuit uw e-mail, gaat u in uw Postvak IN naar de uitnodiging van de gegevensprovider. De uitnodiging is van Microsoft Azure en heeft de titel **Azure Data Share-uitnodiging van <yourdataprovider@domain.com>** . Klik op **Uitnodiging weergeven** om de uitnodiging te bekijken in Azure. 

   Als u de uitnodiging rechtstreeks vanuit de Azure-portal wilt openen, gaat u in de Azure-portal naar **Data Share-uitnodigingen**. Hiermee kunt u de lijst met Data Share-uitnodigingen openen.

   ![Lijst met uitnodigingen](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer de share die u wilt bekijken. 

### <a name="accept-invitation"></a>Uitnodiging accepteren
1. Zorg ervoor dat u alle velden controleert, met inbegrip van de **Gebruiksvoorwaarden**. Als u akkoord gaat met de gebruiksvoorwaarden, moet u het vakje inschakelen om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Selecteer onder *Data Share-doelaccount* het abonnement en de resourcegroep waarin u de Data Share-gegevens wilt implementeren. 

   Selecteer voor het veld **Data Share-account** de optie **Nieuwe maken** als u geen bestaand Data Share-account hebt. Anders selecteert u een bestaand Data Share-account waarin u de informatie uit uw gegevensshare wilt accepteren. 

   In het veld **Ontvangen sharenaam** kunt u de standaardwaarde laten staan die is opgegeven door de gegevensprovider. U kunt er ook voor kiezen een nieuwe naam op te geven voor de ontvangen share. 

   Zodra u akkoord bent gegaan met de gebruiksvoorwaarden en een Data Share-account hebt opgegeven voor het beheren van de ontvangen share, selecteert u **Accepteren en configureren**. Er wordt een shareabonnement gemaakt. 

   ![Opties voor accepteren](./media/accept-options.png "Opties voor accepteren") 

   U ziet de ontvangen share nu in uw Data Share-account. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *Weigeren*. 

### <a name="configure-received-share"></a>Ontvangen share configureren
Volg de onderstaande stappen om te configureren waar u gegevens wilt ontvangen.

1. Selecteer het tabblad **Gegevenssets**. Schakel het selectievakje in van de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **+ Toewijzen aan doel** om een doelgegevensarchief te kiezen. 

   ![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

1. Selecteer een doel gegevens archief waarvan u wilt dat de gegevens binnenkomen. Alle gegevensbestanden of tabellen in het doelgegevensarchief met hetzelfde pad en dezelfde naam worden overschreven. 

   ![Doelopslagaccount](./media/dataset-map-target-sql.png "Doel gegevens archief") 

1. Als u wilt delen met behulp van momentopnamen, en de gegevensprovider een schema heeft opgesteld voor het maken van momentopnamen om de gegevens regelmatig bij te werken, kunt u het schema voor het maken van momentopnamen ook inschakelen door het tabblad **Schema voor momentopnamen** te selecteren. Schakel het selectievakje naast het schema voor momentopnamen in en selecteer **+ Inschakelen**.

   ![Schema voor momentopnamen inschakelen](./media/enable-snapshot-schedule.png "Schema voor momentopnamen inschakelen")

### <a name="trigger-a-snapshot"></a>Een momentopname activeren
Deze stappen zijn alleen van toepassing bij delen op basis van momentopnamen.

1. U kunt een momentopname activeren door **Momentopname activeren** te selecteren op het tabblad **Details**. Hier kunt u een volledige of incrementele momentopname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevensprovider ontvangt, selecteert u volledig kopiëren. Voor SQL-bronnen wordt alleen volledige moment opname ondersteund.

   ![Momentopname activeren](./media/trigger-snapshot.png "Momentopname activeren") 

1. Wanneer de status van de laatste uitvoering *Geslaagd* is, gaat u naar het doelgegevensarchief om de ontvangen gegevens te bekijken. Selecteer **Gegevenssets** en klik op de koppeling in het doelpad. 

   ![Gegevenssets van consumenten](./media/consumer-datasets.png "Toewijzing voor gegevenssets van consumenten") 

### <a name="view-history"></a>Geschiedenis weergeven
Deze stap geldt alleen voor delen op basis van momentopnamen. Selecteer het tabblad **Geschiedenis** om de geschiedenis van uw momentopnamen weer te geven. Hier vindt u een geschiedenis van alle momentopnamen die zijn gegenereerd voor de afgelopen dertig dagen. 

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u gegevens kunt delen en ontvangen van een opslag account met behulp van de Azure data share-service. Voor meer informatie over het delen van andere gegevens bronnen gaat u verder met [ondersteunde gegevens archieven](supported-data-stores.md).

