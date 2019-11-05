---
title: 'Zelf studie: & ontvangen gegevens accepteren-Azure-gegevens share'
description: Zelf studie-gegevens accepteren en ontvangen met behulp van Azure-gegevens share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499337"
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

### <a name="receive-data-into-a-storage-account"></a>Gegevens ontvangen in een opslag account: 

* Een Azure Storage account: als u er nog geen hebt, kunt u een [Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)maken. 
* Machtiging voor het toevoegen van roltoewijzing aan het opslag account dat aanwezig is in de machtiging *micro soft. Authorization/roltoewijzingen/schrijven* . Deze machtiging bevindt zich in de rol van eigenaar. 
* Registratie van resource provider voor micro soft. DataShare. Raadpleeg de documentatie van [Azure resource providers](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) voor informatie over hoe u dit doet. 

> [!IMPORTANT]
> Als u een Azure-gegevens share wilt accepteren en ontvangen, moet u eerst de resource provider micro soft. DataShare registreren. u moet eigenaar zijn van het opslag account waarin u gegevens accepteert. Volg de instructies in [Troubleshooting Azure data share](data-share-troubleshoot.md) om de resource provider van de gegevens share te registreren en voeg uzelf toe als eigenaar van het opslag account. 

### <a name="receive-data-into-a-sql-based-source"></a>Gegevens ontvangen in een bron op basis van SQL:

* Machtiging voor de gegevens share MSI om toegang te krijgen tot de Azure SQL Database of Azure SQL Data Warehouse. U kunt dit doen door de volgende stappen uit te voeren: 
    1. Stel uzelf in als de Azure Active Directory beheerder voor de-server.
    1. Maak verbinding met de Azure SQL Database/data warehouse met behulp van Azure Active Directory.
    1. Gebruik de query-editor (preview) om het volgende script uit te voeren om de gegevens share MSI toe te voegen als db_owner. U moet verbinding maken met behulp van Active Directory en niet SQL Server-verificatie. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Houd er rekening mee dat de *< share_acc_name >* de naam is van uw gegevens share-account. Als u nog geen gegevens share-account hebt gemaakt, kunt u later terugkeren naar deze vereiste.         

* Client-IP SQL Server toegang tot Firewall: dit kunt u doen door de volgende stappen uit te voeren: 1. Navigeer naar *firewalls en virtuele netwerken* 1. Klik op de **aan/uit** om toegang tot Azure-Services toe te staan. 

Zodra deze vereisten zijn voltooid, bent u klaar om gegevens te ontvangen in uw SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Uitnodiging openen

Controleer uw postvak in voor een uitnodiging van uw gegevens provider. De uitnodiging is van Microsoft Azure, met de titel **uitnodiging voor Azure data share van <yourdataprovider@domain.com>** . Noteer de naam van de share om ervoor te zorgen dat u de juiste share accepteert als er meerdere uitnodigingen zijn. 

Selecteer on- **uitnodiging weer geven** om uw uitnodiging in azure te bekijken. Hiermee gaat u naar de weer gave van de ontvangen shares.

![Moment](./media/invitations.png "Lijst met uitnodigingen") 

Selecteer de share die u wilt weer geven. 

## <a name="accept-invitation"></a>Uitnodiging accepteren
Zorg ervoor dat alle velden worden gecontroleerd, met inbegrip **van de gebruiks voorwaarden**. Als u akkoord gaat met de gebruiksrecht overeenkomst, moet u het selectie vakje inschakelen om aan te geven dat u akkoord gaat. 

![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

Onder *doel gegevens share account*selecteert u het abonnement en de resource groep waarin u uw gegevens share gaat implementeren. 

Selecteer voor het veld **gegevens share account** de optie **nieuwe maken** als u geen bestaand gegevens share-account hebt. Anders selecteert u een bestaand gegevens share-account waarnaar u uw gegevens share wilt accepteren. 

Voor het veld *ontvangen share naam* kunt u de standaard waarde die wordt opgegeven door de gegevens opgeven, of een nieuwe naam voor de ontvangen share opgeven. 

![Doel gegevens share account](./media/target-data-share.png "Doel gegevens share account") 

Zodra u akkoord gaat met de gebruiks voorwaarden en een locatie hebt opgegeven voor uw share, selecteert u op *accepteren en configureren*. Als u deze optie kiest, wordt een abonnement voor delen gemaakt en wordt u gevraagd een doel opslag account te selecteren waarnaar uw gegevens moeten worden gekopieerd. 

![Opties accepteren](./media/accept-options.png "Opties accepteren") 

Als u de uitnodiging nu wilt accepteren maar uw opslag op een later tijdstip wilt configureren, selecteert u *accepteren en later configureren*. Met deze optie kunt u later uw doel opslag account configureren. Zie [de pagina uw opslag account configureren](how-to-configure-mapping.md) voor gedetailleerde stappen voor het hervatten van de configuratie van de gegevens share om uw opslag later te configureren. 

Als u de uitnodiging niet wilt accepteren, selecteert u *afwijzen*. 

## <a name="configure-storage"></a>Opslag configureren
Onder *instellingen voor doel opslag*selecteert u het abonnement, de resource groep en het opslag account waarnaar u uw gegevens wilt ontvangen. 

![Instellingen voor doel opslag](./media/target-storage-settings.png "Doel opslag") 

Zorg ervoor dat u de moment opname-instellingen inschakelt om regel matig vernieuwingen van uw gegevens te ontvangen. Houd er rekening mee dat u alleen een schema voor het instellen van moment opnamen kunt zien als uw gegevens provider deze in de gegevens share heeft opgenomen. 

![Moment opname-instellingen](./media/snapshot-settings.png "Moment opname-instellingen") 

Selecteer *Opslaan*. 

> [!IMPORTANT]
> Als u op SQL gebaseerde gegevens ontvangt en u deze gegevens wilt ontvangen in een bron op basis van SQL, gaat u naar onze [een toewijzings handleiding voor gegevensset configureren](how-to-configure-mapping.md) voor meer informatie over het configureren van een SQL Server als bestemming voor uw gegevensset. 

## <a name="trigger-a-snapshot"></a>Een moment opname activeren

U kunt een moment opname activeren op het tabblad ontvangen shares-> Details door **moment opname activeren**te selecteren. Hier kunt u een volledige of incrementele moment opname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevens provider ontvangt, selecteert u volledig kopiëren. 

![Moment opname activeren](./media/trigger-snapshot.png "Moment opname activeren") 

Wanneer de status van de laatste uitvoering is *geslaagd*, opent u het opslag account om de ontvangen gegevens weer te geven. 

Selecteer op **gegevens sets**om te controleren welk opslag account u hebt gebruikt. 

![Consumer gegevens sets](./media/consumer-datasets.png "Toewijzing dataset van consument") 

## <a name="view-history"></a>Geschiedenis weergeven
Als u een geschiedenis van uw moment opnamen wilt weer geven, gaat u naar ontvangen shares-> geschiedenis. Hier vindt u een geschiedenis van alle moment opnamen die zijn gegenereerd voor de afgelopen 60 dagen. 

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie leert u hoe u een Azure-gegevens share accepteert en ontvangt. Voor meer informatie over concepten van Azure data share gaat u verder met [concepten: Azure data share terminologie](terminology.md).