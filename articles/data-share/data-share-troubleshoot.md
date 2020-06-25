---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevens shares met Azure data share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 510787b395388f0dc895b9646aee8208013f3b42
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322044"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Veelvoorkomende problemen in azure data share oplossen 

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met Azure data share oplost. 

## <a name="azure-data-share-invitations"></a>Uitnodigingen voor Azure-gegevens delen 

In sommige gevallen wordt er een lege lijst met uitnodigingen weer gegeven wanneer een nieuwe gebruiker op **uitnodiging accepteren** klikt vanuit de uitnodiging voor het verzenden van e-mail. 

![Geen uitnodigingen](media/no-invites.png)

Dit kan de volgende oorzaken hebben:

* **De Azure data share-service is niet geregistreerd als resource provider van een Azure-abonnement in de Azure-Tenant.** Dit probleem treedt op als er geen gegevens delen resource aanwezig is in uw Azure-Tenant. Wanneer u een Azure-gegevens share-resource maakt, wordt de resource provider automatisch geregistreerd in uw Azure-abonnement. U kunt de gegevens share service ook hand matig registreren door de volgende stappen uit te voeren. U moet de rol Azure contributor hebben om deze stappen te volt ooien.

    1. Ga in het Azure Portal naar **abonnementen**
    1. Selecteer het abonnement dat u wilt gebruiken voor het maken van een Azure data share-resource
    1. Klik op **resource providers**
    1. Zoeken naar **micro soft. DataShare**
    1. Klik op **Registreren** 

    U moet de [rol van Azure INZENDER RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) hebben om deze stappen te kunnen volt ooien. 

* **Er wordt een uitnodiging verzonden naar uw e-mail alias in plaats van uw Azure-aanmeldings-e-mail.** Als u de Azure data share-service hebt geregistreerd of al een gegevens share bron hebt gemaakt in de Azure-Tenant, maar de uitnodiging nog steeds niet ziet, kan het zijn dat de provider uw e-mail alias heeft ingevoerd als ontvanger in plaats van uw e-mail adres voor aanmelding bij Azure. Neem contact op met uw gegevens provider en zorg ervoor dat ze de uitnodiging hebben verzonden naar uw e-mail adres voor Azure-aanmelding en niet uw e-mail alias.

* **De uitnodiging is al geaccepteerd.** Via de koppeling in het e-mail bericht gaat u naar de pagina uitnodiging voor gegevens delen in Azure Portal. deze lijst bevat alleen openstaande uitnodigingen. Als u de uitnodiging al hebt geaccepteerd, wordt deze niet meer weer gegeven op de pagina uitnodiging voor gegevens delen. Ga door naar uw gegevens share resource die u hebt gebruikt om de uitnodiging te accepteren voor het weer geven van ontvangen shares en het configureren van uw doel-Azure-Data Explorer cluster instelling.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fout bij het maken of ontvangen van een nieuwe share

Kan geen gegevens sets toevoegen

Fout bij het toewijzen van gegevens sets

' Kan geen gegevens share bron x toegang verlenen tot y '

U hebt niet de juiste machtigingen voor x

"Er kunnen geen schrijf machtigingen voor het Azure data share-account worden toegevoegd aan een of meer van de geselecteerde resources"

Als u een van de bovenstaande fouten ontvangt bij het maken van een nieuwe share of het toewijzen van gegevens sets, kan dit worden veroorzaakt door onvoldoende machtigingen voor de Azure-gegevens opslag. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor de vereiste machtigingen. 

U hebt schrijf machtigingen nodig om gegevens te delen of te ontvangen van een Azure-gegevens archief, dat meestal voor komt in de rol Inzender. 

Als dit de eerste keer is dat u gegevens uit het Azure-gegevens archief deelt of ontvangt, hebt u ook *micro soft. Authorization/Role Assignments/write* Permission, die meestal voor komt in de rol van eigenaar. Zelfs als u de Azure-gegevens opslag resource hebt gemaakt, wordt de eigenaar van de resource niet automatisch door de bron. Met de juiste machtigingen verleent de Azure data share-service automatisch de beheerde identiteits toegang van de gegevens share bron aan het gegevens archief. Het kan enkele minuten duren voordat dit proces is doorgevoerd. Probeer het over enkele minuten opnieuw als u fouten ondervindt als gevolg van deze vertraging.

Voor delen op basis van SQL zijn aanvullende machtigingen vereist. Zie problemen met het delen van SQL oplossen voor meer informatie.

## <a name="troubleshooting-sql-based-sharing"></a>Problemen met delen op basis van SQL oplossen

"Gebruiker x bestaat niet in SQL Database"

Als deze fout optreedt bij het toevoegen van een gegevensset van een bron op basis van SQL, kan dat zijn omdat u geen gebruiker hebt gemaakt voor de door Azure data share beheerde identiteit in SQL Database.  Voer het volgende script uit om dit probleem op te lossen:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Als deze fout optreedt bij het toewijzen van dataset aan een op SQL gebaseerd doel, kan dat zijn omdat u geen gebruiker hebt gemaakt voor de door Azure data share beheerde identiteit op uw SQL Server.  Voer het volgende script uit om dit probleem op te lossen:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Houd er rekening mee dat de *<share_acc_name>* de naam van uw gegevens share bron is.      

Zorg ervoor dat u alle vereisten hebt gevolgd die worden vermeld in [uw gegevens delen](share-your-data.md) en zelf studie voor [gegevens accepteren en ontvangen](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>De moment opname is mislukt
De moment opname kan om verschillende redenen mislukken. U kunt een gedetailleerd fout bericht vinden door te klikken op de begin tijd van de moment opname en vervolgens op de status van elke gegevensset. 

Als het fout bericht betrekking heeft op machtiging, controleert u of de gegevens share service de vereiste machtiging heeft. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor meer informatie. Als dit de eerste keer is dat u een moment opname maakt, kan het enkele minuten duren voordat de gegevens share bron toegang krijgt tot de Azure-gegevens opslag. Wacht een paar minuten en probeer het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md). 

Ga door naar de zelf studie [gegevens accepteren en ontvangen](subscribe-to-data-share.md) voor meer informatie over het ontvangen van gegevens.

