---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevensshares met Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964223"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Veelvoorkomende problemen in Azure-gegevensshare oplossen 

In dit artikel ziet u hoe u veelvoorkomende problemen voor Azure Data Share oplossen. 

## <a name="azure-data-share-invitations"></a>Azure-uitnodigingen voor gegevensdelen 

In sommige gevallen, wanneer een nieuwe gebruiker op **Uitnodiging accepteren** klikt vanaf de verzonden e-mailuitnodiging, kan deze worden gepresenteerd met een lege lijst met uitnodigingen. 

![Geen uitnodigingen](media/no-invites.png)

Dit kan te wijten zijn aan de volgende redenen:

* **Azure Data Share-service is niet geregistreerd als resourceprovider van een Azure-abonnement in de Azure-tenant.** U ondervindt dit probleem als er geen gegevensshare-bron in uw Azure-tenant zit. Wanneer u een Azure Data Share-bron maakt, wordt de resourceprovider automatisch geregistreerd in uw Azure-abonnement. U de service Gegevensdelen ook handmatig registreren na deze stappen. U moet de azure-bijdrager hebben om deze stappen uit te voeren.

    1. Navigeer in de Azure-portal naar **Abonnementen**
    1. Selecteer het abonnement dat u wilt gebruiken om Azure Data Share-bron te maken
    1. Klik op **Resource Providers**
    1. Zoeken naar **Microsoft.DataShare**
    1. Klik **op Registreren** 

    U moet de [RBAC-rol azure-inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) hebben om deze stappen uit te voeren. 

* **Uitnodiging wordt verzonden naar uw e-mailalias in plaats van uw Azure-aanmeldingsmail.** Als u de Azure Data Share-service hebt geregistreerd of al een gegevensshare-bron in de Azure-tenant hebt gemaakt, maar de uitnodiging nog steeds niet zien, is dit misschien omdat de provider uw e-mailalias als ontvanger heeft ingevoerd in plaats van uw Azure-aanmeldingsadres. Neem contact op met uw gegevensprovider en zorg ervoor dat deze de uitnodiging naar uw Azure-inloge-e-mailadres heeft verzonden en niet uw e-mailalias.

* **De uitnodiging is al geaccepteerd.** De koppeling in de e-mail brengt u naar de pagina Uitnodiging voor gegevensdelen in azure-portal, waarin alleen in behandeling zijnde uitnodigingen worden vermeld. Als u de uitnodiging al hebt geaccepteerd, wordt deze niet meer weergegeven op de pagina Uitnodiging voor gegevensdelen. Ga naar uw Data Share-bron waarmee u de uitnodiging hebt geaccepteerd om ontvangen aandelen te bekijken en uw doelconfiguratie van Azure Data Explorer-cluster te configureren.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fout bij het maken of ontvangen van een nieuw aandeel

'Kan geen gegevenssets toevoegen'

'Kan gegevenssets niet in kaart brengen'

'Kan Data Share resource x geen toegang tot y verlenen'

"Je hebt niet de juiste machtigingen om x"

'We kunnen geen schrijfmachtigingen voor Azure Data Share-account toevoegen aan een of meer van uw geselecteerde bronnen'

Als u een van de bovenstaande fouten ontvangt bij het maken van een nieuwe share- of toewijzingsgegevenssetgegevens, kan dit te wijten zijn aan onvoldoende machtigingen voor het Azure-gegevensarchief. Zie [Rollen en vereisten](concepts-roles-permissions.md) voor vereiste machtigingen. 

U hebt schrijftoestemming nodig om gegevens uit een Azure-gegevensarchief te delen of te ontvangen, die doorgaans in de rol Inzender bestaan. 

Als dit de eerste keer is dat u gegevens uit het Azure-gegevensarchief deelt of ontvangt, hebt u ook *Microsoft.Autorisatie/roltoewijzingen/schrijfmachtigingen* nodig, die doorgaans bestaat in de rol Eigenaar. Zelfs als u de Azure-gegevensarchiefbron hebt gemaakt, wordt u niet automatisch de eigenaar van de bron. Met de juiste toestemming verleent de Azure Data Share-service automatisch de beheerde identiteitstoegang van de gegevenssharebron tot het gegevensarchief. Dit proces kan enkele minuten duren om van kracht te worden. Als u door deze vertraging een storing ondervindt, probeert u het binnen enkele minuten opnieuw.

Sql-gebaseerd delen vereist extra machtigingen. Zie Het oplossen van SQL-gebaseerde delen voor meer informatie.

## <a name="troubleshooting-sql-based-sharing"></a>Problemen met delen op basis van SQL oplossen

"Gebruiker x bestaat niet in SQL-database"

Als u deze fout ontvangt bij het toevoegen van een gegevensset van een SQL-bron, kan dit zijn omdat u geen gebruiker hebt gemaakt voor de beheerde identiteit van Azure Data Share op uw SQL Server.  Voer het volgende script uit om dit probleem op te lossen:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Als u deze fout ontvangt wanneer u de gegevensset in kaart brengt naar een SQL-doel, kan dit zijn omdat u geen gebruiker hebt gemaakt voor de beheerde identiteit van Azure Data Share op uw SQL Server.  Voer het volgende script uit om dit probleem op te lossen:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Houd er rekening mee dat de *<share_acc_name>* de naam is van uw bron voor gegevensshare.      

Zorg ervoor dat u alle vereisten hebt gevolgd die in [Deel uw gegevens](share-your-data.md) worden vermeld en accepteer en ontvang de [zelfstudie met gegevens.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>Momentopname is mislukt
Momentopname kan mislukken als gevolg van verschillende redenen. U een gedetailleerd foutbericht vinden door op de begintijd van de momentopname te klikken en vervolgens op de status van elke gegevensset. 

Als het foutbericht gerelateerd is aan toestemming, controleert u dat de service Gegevensdelen de vereiste toestemming heeft. Zie [Rollen en vereisten](concepts-roles-permissions.md) voor meer informatie. Als dit de eerste keer is dat u een momentopname maakt, kan het enkele minuten duren voordat de gegevenssharebron toegang krijgt tot het Azure-gegevensarchief. Wacht een paar minuten en probeer het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen. 

Ga voor meer informatie over het ontvangen van gegevens verder naar de zelfstudie [voor het accepteren en ontvangen van gegevens.](subscribe-to-data-share.md)

