---
title: Problemen met Azure Data Share oplossen
description: Meer informatie over het oplossen van problemen met uitnodigingen en fouten bij het maken of ontvangen van gegevens shares met Azure data share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/02/2020
ms.openlocfilehash: 620fe1e693a177123e166220ab94bbd74c4826ff
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761528"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Veelvoorkomende problemen in Azure Data Share oplossen 

In dit artikel wordt beschreven hoe u veelvoorkomende problemen met Azure data share oplost. 

## <a name="azure-data-share-invitations"></a>Uitnodigingen voor Azure-gegevens delen 

In sommige gevallen wordt er een lege lijst met uitnodigingen weer gegeven wanneer een nieuwe gebruiker op **uitnodiging accepteren** klikt vanuit de uitnodiging voor het verzenden van e-mail. 

![Geen uitnodigingen](media/no-invites.png)

Dit kan een van de volgende oorzaken hebben:

* **De Azure Data Share-service is niet geregistreerd als een resourceprovider van een Azure-abonnement in de Azure-tenant.** Dit probleem treedt op als er geen Data Share-resource aanwezig is in uw Azure-tenant. Wanneer u een Azure Data Share-resource maakt, wordt de resourceprovider automatisch geregistreerd in uw Azure-abonnement. U kunt de Data Share-service ook handmatig registreren door de volgende stappen uit te voeren. U hebt de rol Azure-inzender nodig om deze stappen te voltooien.

    1. Navigeer in Azure Portal naar **Abonnementen**
    1. Selecteer het abonnement dat u wilt gebruiken voor het maken van een Azure Data Share-resource
    1. Klik op **Resourceproviders**
    1. Zoek naar **Microsoft.DataShare**
    1. Klik op **Registreren** 

    U moet de [rol Azure Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) hebben voor het Azure-abonnement om deze stappen uit te voeren. 

* **Er wordt een uitnodiging verzonden naar uw e-mailalias in plaats van uw e-mailadres voor aanmelding bij Azure.** Als u de Azure Data Share-service hebt geregistreerd of al een Data Share-resource hebt gemaakt in de Azure-tenant, maar de uitnodiging nog steeds niet ziet, kan het zijn dat de provider uw e-mailalias heeft ingevoerd als ontvanger in plaats van uw e-mailadres voor aanmelding bij Azure. Neem contact op met uw gegevensprovider en vraag na of de uitnodiging is verzonden naar uw e-mailadres voor aanmelding bij Azure en niet uw e-mailalias.

* **De uitnodiging is al geaccepteerd.** Via de koppeling in het e-mailbericht gaat u naar de Data Share-uitnodigingspagina in Azure Portal, waar alleen openstaande uitnodigingen worden weergegeven. Als u de uitnodiging al hebt geaccepteerd, wordt deze niet meer weergegeven op de Data Share-uitnodigingspagina. Ga naar uw Data Share-resource die u hebt gebruikt om de uitnodiging te accepteren om ontvangen shares weer te geven en de instelling van uw Azure Data Explorer-doelcluster te configureren.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Fout bij het maken of ontvangen van een nieuwe share

Kan geen gegevens sets toevoegen

Fout bij het toewijzen van gegevens sets

' Kan geen gegevens share bron x toegang verlenen tot y '

U hebt niet de juiste machtigingen voor x

"Er kunnen geen schrijf machtigingen voor het Azure data share-account worden toegevoegd aan een of meer van de geselecteerde resources"

Als u een van de bovenstaande fouten ontvangt bij het maken van een nieuwe share of het toewijzen van gegevens sets, kan dit worden veroorzaakt door onvoldoende machtigingen voor de Azure-gegevens opslag. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor de vereiste machtigingen. 

U hebt schrijf machtigingen nodig om gegevens te delen of te ontvangen van een Azure-gegevens archief, dat meestal voor komt in de rol Inzender. 

Als dit de eerste keer is dat u gegevens uit het Azure-gegevens archief deelt of ontvangt, hebt u ook *micro soft. Authorization/Role Assignments/write* Permission, die meestal voor komt in de rol van eigenaar. Zelfs als u de Azure-gegevens opslag resource hebt gemaakt, wordt de eigenaar van de resource niet automatisch door de bron. Met de juiste machtigingen verleent de Azure data share-service automatisch de beheerde identiteits toegang van de gegevens share bron aan het gegevens archief. Het kan enkele minuten duren voordat dit proces is doorgevoerd. Probeer het over enkele minuten opnieuw als u fouten ondervindt als gevolg van deze vertraging.

Voor delen op basis van SQL zijn aanvullende machtigingen vereist. Zie [share van SQL-bronnen](how-to-share-from-sql.md) voor een gedetailleerde lijst met vereisten.

## <a name="snapshot-failed"></a>De moment opname is mislukt
De moment opname kan om verschillende redenen mislukken. U kunt een gedetailleerd fout bericht vinden door te klikken op de begin tijd van de moment opname en vervolgens op de status van elke gegevensset. Hier volgen enkele redenen waarom de moment opname is mislukt:

* Gegevens share heeft geen machtiging voor het lezen van de brongegevens opslag of het schrijven naar het doel gegevens archief. Zie de [rollen en vereisten](concepts-roles-permissions.md) voor gedetailleerde machtigings vereisten. Als dit de eerste keer is dat u een moment opname maakt, kan het enkele minuten duren voordat de gegevens share bron toegang krijgt tot de Azure-gegevens opslag. Wacht een paar minuten en probeer het opnieuw.
* De verbinding van de gegevens share met het bron-of doel gegevens archief wordt geblokkeerd door de firewall.
* De gedeelde gegevensset of het bron-of doel gegevens archief wordt verwijderd.
* Voor het delen van SQL worden gegevens typen niet ondersteund door het momentopname proces of het doel gegevens archief. Raadpleeg de [share van SQL-bronnen](how-to-share-from-sql.md#supported-data-types) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md). 

Ga door naar de zelf studie [gegevens accepteren en ontvangen](subscribe-to-data-share.md) voor meer informatie over het ontvangen van gegevens.

