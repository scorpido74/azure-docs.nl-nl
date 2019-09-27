---
title: Problemen met Azure data share preview oplossen
description: Meer informatie over het oplossen van problemen met Azure data share preview
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 62b386f4648074bbf1f002e162151253e5b43bc9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71320295"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Veelvoorkomende problemen in azure data share preview oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met Azure data share preview kunt oplossen. 

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

Optreedt De bewerking heeft een ongeldige status code onjuiste aanvraag

Optreedt AuthorizationFailed"

"Fout: roltoewijzing aan opslag account"

![Bevoegdheids fout](media/error-write-privilege.png)

Als u een van de bovenstaande foutberichten ontvangt tijdens het maken of ontvangen van een nieuwe gegevensshare, is dit omdat het opslagaccount onvoldoende machtigingen heeft. De vereiste machtiging is *micro soft. autorisatie/roltoewijzingen/schrijven*, die voor komt in de rol van de opslag eigenaar of die kan worden toegewezen aan een aangepaste rol. Zelfs als u het opslagaccount hebt gemaakt, betekent dit NIET automatisch dat u de eigenaar van het opslagaccount bent. Volg deze stappen om uzelf eigenaar te maken van het opslagaccount. U kunt ook een aangepaste rol maken met deze machtiging die u zelf kunt toevoegen aan.  

1. Ga naar Opslagaccount in de Azure-portal
1. **Toegangs beheer (IAM)** selecteren
1. Klik op **toevoegen**
1. Voeg jezelf toe als eigenaar.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.

