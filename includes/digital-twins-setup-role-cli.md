---
author: baanders
description: bestand insluiten voor vereiste rol in azure Digital Apparaatdubbels-installatie
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832336"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Vereisten: machtigings vereisten

Als u alle stappen in dit artikel wilt uitvoeren, moet u worden geclassificeerd als een eigenaar van uw Azure-abonnement. 

U kunt uw machtigings niveau controleren door deze opdracht uit te voeren in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Als met deze opdracht een fout wordt geretourneerd die aangeeft dat de CLI **geen gebruiker of service-principal kan vinden in Graph data base**:
>
> Gebruik de *object-id* van uw gebruiker in plaats van uw e-mail adres voor de rest van dit artikel. Dit kan gebeuren voor gebruikers op persoonlijke [micro soft-accounts (msa's)](https://account.microsoft.com/account). 
>
> Gebruik de [pagina Azure portal van Azure Active Directory gebruikers](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) om uw gebruikers account te selecteren en de bijbehorende gegevens te openen. De *ObjectID*van uw gebruiker kopiëren:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Weer gave van de gebruikers pagina in Azure Portal markeren van de GUID in het veld object-ID" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Herhaal vervolgens de opdracht lijst met roltoewijzingen met behulp van de *object-id* van de gebruiker in plaats van uw e-mail adres.

Nadat u de opdracht lijst roltoewijzing hebt uitgevoerd, `roleDefinitionName` is de waarde in de uitvoer *eigenaar*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell venster met de uitvoer van de opdracht AZ Role Assignment List":::

Als u merkt dat de waarde *Inzender* of iets anders is dan de *eigenaar*, kunt u door gaan op een van de volgende manieren:
* Neem contact op met de eigenaar van uw abonnement en vraag de eigenaar om de stappen in dit artikel namens u uit te voeren
* Neem contact op met de eigenaar van uw abonnement of iemand met de rol van beheerder voor gebruikers toegang voor het abonnement en vraag om u te helpen bij de eigenaar van het abonnement, zodat u de juiste machtigingen hebt om door te gaan. Of dit geschikt is, is afhankelijk van uw organisatie en uw rol.