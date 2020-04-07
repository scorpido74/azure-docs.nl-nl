---
title: Een Azure Remote Rendering-account maken
description: Beschrijft de stappen om een account voor Azure Remote Rendering te maken
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681296"
---
# <a name="create-an-azure-remote-rendering-account"></a>Een Azure Remote Rendering-account maken

In dit hoofdstuk u de stappen doorlopen om een account voor de **Azure Remote Rendering-service te** maken. Een geldig account is verplicht voor het invullen van een van de quickstarts of tutorials.

## <a name="create-an-account"></a>Een account maken

De volgende stappen zijn nodig om een account voor de Azure Remote Rendering-service te maken:

1. Ga naar de [pagina Mixed Reality Preview](https://aka.ms/MixedRealityPrivatePreview)
1. Klik op de knop 'Een resource maken'
1. Typ in het zoekveld ('Zoek de marktplaats') in 'Externe rendering' en druk op 'enter'.
1. Klik in de resultatenlijst op de tegel 'Extern renderen'
1. Klik in het volgende scherm op de knop Maken. Er wordt een formulier geopend om een nieuw extern renderingaccount te maken:
    1. 'Resourcenaam' instellen op de naam van het account
    1. Update 'Abonnement' indien nodig
    1. 'Resourcegroep' instellen op een resourcegroep naar keuze
1. Zodra het account is gemaakt, navigeert u ernaar en:
    1. Noteer op het tabblad *Overzicht* de 'Account-ID'
    1. Noteer in het tabblad *Instellingen > Toegangssleutels* de 'primaire sleutel' - dit is de geheime accountsleutel van het account

### <a name="retrieve-the-account-information"></a>De accountgegevens ophalen

De voorbeelden en tutorials vereisen dat u de account-ID en een sleutel. Bijvoorbeeld in het **arrconfig.json-bestand** dat wordt gebruikt voor de PowerShell-voorbeeldscripts:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Bekijk de [lijst met beschikbare regio's](../reference/regions.md) voor het invullen van de *regiooptie.*

De waarden **`arrAccountId`** **`arrAccountKey`** voor en zijn te vinden in de portal zoals beschreven in de volgende stappen:

* Ga naar [Azure Portal](https://www.portal.azure.com)
* Zoek uw **"Remote Rendering Account"** - het moet in de lijst **'Recente bronnen'** staan. U er ook naar zoeken in de zoekbalk bovenaan. Controleer in dat geval of het abonnement dat u wilt gebruiken is geselecteerd in het filter standaardabonnement (filterpictogram naast de zoekbalk):

![Abonnementsfilter](./media/azure-subscription-filter.png)

Als u op uw account klikt, gaat u naar dit scherm, waarop meteen de **account-id** wordt weergegeven:

![Azure-account-id](./media/azure-account-id.png)

Selecteer **Access-toetsen** in het deelvenster aan de linkerkant voor de sleutel. Op de volgende pagina ziet u een primaire en een secundaire sleutel:

![Azure-toegangssleutels](./media/azure-account-primary-key.png)

De waarde **`arrAccountKey`** voor kan primaire of secundaire sleutel zijn.

## <a name="link-storage-accounts"></a>Opslagaccounts koppelen

In deze paragraaf wordt uitgelegd hoe u opslagaccounts koppelt aan uw externe renderingaccount. Wanneer een opslagaccount is gekoppeld, is het niet nodig om een SAS URI te genereren wanneer u wilt communiceren met de gegevens in uw account, bijvoorbeeld bij het laden van een model. In plaats daarvan u de namen van het opslagaccount rechtstreeks gebruiken zoals beschreven in de [sectie het laden van een model.](../concepts/models.md#loading-models)

De stappen in deze alinea moeten worden uitgevoerd voor elk opslagaccount dat deze alternatieve toegangsmethode moet gebruiken. Als u nog geen opslagaccounts hebt gemaakt, u de respectievelijke stap in het [model converteren voor het renderen van snelstart](../quickstarts/convert-model.md#storage-account-creation)doorlopen.

Nu wordt aangenomen dat u een opslagaccount hebt. Navigeer naar het opslagaccount in de portal en ga naar het tabblad **Toegangsbeheer (IAM)** voor dat opslagaccount:

![Opslagaccount IAM](./media/azure-storage-account.png)

 Zorg ervoor dat u eigenaarmachtigingen hebt voor dit opslagaccount om ervoor te zorgen dat u roltoewijzingen toevoegen. Als u geen toegang hebt, wordt de optie **Een roltoewijzing toevoegen** uitgeschakeld.

 U moet drie verschillende rollen toevoegen zoals beschreven in de volgende stappen. Als u niet alle drie de toegangsniveaus opgeeft, zijn er toestemmingsproblemen terwijl u toegang probeert te krijgen tot het opslagaccount.

 Klik op de knop **Toevoegen** in de tegel 'Een roltoewijzing toevoegen' om de eerste rol toe te voegen:

![Opslagaccount IAM](./media/azure-add-role-assignment.png)

* De eerste rol die u wilt toewijzen is **Eigenaar** zoals weergegeven in de bovenstaande schermafbeelding. 
* Selecteer **Extern renderaccount** in de ***Toegang toewijzen tot** vervolgkeuzelijst.
* Selecteer uw abonnement en Extern renderaccount in de laatste vervolgkeuzetijden.

Herhaal het toevoegen van nieuwe rollen nog twee keer voor de respectievelijke selecties in de **vervolgkeuzelijst Rol:**
* **Inzender voor opslagaccounts**
* **Opslagblob-gegevensbijdrager**

De andere vervolgkeuzeklassen worden geselecteerd zoals in de eerste stap.

Als u alle drie de rollen hebt toegevoegd, heeft uw Azure Remote Rendering-account toegang tot uw opslagaccount met behulp van systeemtoegewezen Managed Service Identities.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie](authentication.md)
* [De Azure Frontend API's gebruiken voor verificatie](frontend-apis.md)
* [Voorbeeld PowerShell-scripts](../samples/powershell-example-scripts.md)
