---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevens shares met Azure data share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490570"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Veelvoorkomende problemen in azure data share oplossen 

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met Azure data share oplost. 

## <a name="azure-data-share-invitations"></a>Uitnodigingen voor Azure-gegevens delen 

In sommige gevallen wordt er een lege lijst met uitnodigingen weer gegeven wanneer een nieuwe gebruiker op **uitnodiging accepteren** klikt vanuit de uitnodiging voor het verzenden van e-mail. 

![Geen uitnodigingen](media/no-invites.png)

De bovenstaande fout is een bekend probleem met de service en wordt op dit moment opgelost. Volg de onderstaande stappen als tijdelijke oplossing. 

1. Ga in het Azure Portal naar **abonnementen**
1. Selecteer het abonnement dat u gebruikt voor Azure-gegevens share
1. Klik op **resource providers**
1. Zoeken naar micro soft. DataShare
1. Klik op **registreren**

U moet de [rol van Azure INZENDER RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) hebben om deze stappen te kunnen volt ooien. 

Als u nog steeds geen uitnodiging voor gegevens delen kunt zien, neemt u contact op met uw gegevens provider en zorgt u ervoor dat ze de uitnodiging hebben verzonden naar uw e-mail adres voor Azure-aanmelding en *niet* uw e-mail alias. 

> [!IMPORTANT]
> Als u al een uitnodiging voor een Azure-gegevens share hebt geaccepteerd en de service hebt afgesloten voordat u de opslag ruimte hebt geconfigureerd, volgt u de instructies in de hand leiding [Configure a dataset mapping](how-to-configure-mapping.md) How-to om te leren hoe u de configuratie van uw ontvangen gegevens delen kunt volt ooien en beginnen met het ontvangen van gegevens. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Fout bij het maken of ontvangen van een nieuwe gegevens share

' Fout: de bewerking heeft een ongeldige status code ' onjuiste aanvraag ' geretourneerd

"Fout: AuthorizationFailed"

"Fout: roltoewijzing aan opslag account"

![Bevoegdheids fout](media/error-write-privilege.png)

Als u een van de bovenstaande foutberichten ontvangt tijdens het maken of ontvangen van een nieuwe gegevensshare, is dit omdat het opslagaccount onvoldoende machtigingen heeft. De vereiste machtiging is *micro soft. autorisatie/roltoewijzingen/schrijven*, die voor komt in de rol van de opslag eigenaar of die kan worden toegewezen aan een aangepaste rol. Zelfs als u het opslagaccount hebt gemaakt, betekent dit NIET automatisch dat u de eigenaar van het opslagaccount bent. Volg deze stappen om uzelf eigenaar te maken van het opslagaccount. U kunt ook een aangepaste rol maken met deze machtiging die u zelf kunt toevoegen aan.  

1. Ga naar Opslagaccount in de Azure-portal
1. **Toegangs beheer (IAM)** selecteren
1. Klik op **toevoegen**
1. Voeg jezelf toe als eigenaar.

## <a name="troubleshooting-sql-based-sharing"></a>Problemen met delen op basis van SQL oplossen

"Fout: x gegevens sets zijn niet toegevoegd omdat u niet over de vereiste machtigingen beschikt om te delen."

Als deze fout optreedt bij het toevoegen van een gegevensset van een bron op basis van SQL, is het mogelijk dat u geen gebruiker hebt gemaakt voor de Azure data share MSI op uw SQL Server.  Voer het volgende script uit om dit probleem op te lossen:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Houd er rekening mee dat de *< share_acc_name >* de naam is van uw gegevens share-account. Als u nog geen gegevens share-account hebt gemaakt, kunt u later terugkeren naar deze vereiste.         

Zorg ervoor dat u alle vereisten hebt gevolgd die worden vermeld in uw zelf studie [uw gegevens delen](share-your-data.md) .

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.

