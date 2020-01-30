---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.openlocfilehash: 21811041a25c63bb7542b101812222a9430c20fe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887762"
---
1. Als u een nieuwe app-configuratie wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de start pagina de optie **een resource maken**. In het vak **Marketplace doorzoeken** voert u de **app-configuratie** in en selecteert u ENTER.

    ![Zoeken naar App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecteer **app-configuratie** in de zoek resultaten en selecteer vervolgens **maken**.

    ![Selecteer Maken](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Voer in het deel venster **app-configuratie** > **maken** de volgende instellingen in:

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Resourcenaam** | Wereldwijd unieke naam | Voer een unieke resource naam in om te gebruiken voor de bron van het configuratie archief van de app. De naam moet een teken reeks zijn tussen 5 en 50 tekens en mag alleen cijfers, letters en het `-` teken bevatten. De naam mag niet beginnen of eindigen met het `-` teken.  |
    | **Abonnement** | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken om App Configuration te testen. Als uw account slechts één abonnement heeft, wordt dit automatisch geselecteerd en wordt de lijst met **abonnementen** niet weer gegeven. |
    | **Resourcegroep** | *AppConfigTestResources* | Selecteer of maak een resource groep voor uw app-configuratie opslag resource. Deze groep is handig als u meerdere resources wilt ordenen die u op hetzelfde moment wilt verwijderen door de resource groep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](/azure/azure-resource-manager/resource-group-overview) voor meer informatie. |
    | **Locatie** | *US - centraal* | Gebruik **Locatie** om de geografische locatie op te geven waar het app-configuratiearchief wordt gehost. Voor de beste prestaties maakt u de resource in dezelfde regio als andere onderdelen van uw toepassing. |

    ![Een resource voor het configuratie archief van de app maken](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecteer **Maken**. De implementatie kan enkele minuten duren.

1. Nadat de implementatie is voltooid, selecteert u **instellingen** > **toegangs sleutels**. Noteer de primaire alleen-lezen sleutel connection string. U gebruikt deze connection string later om uw toepassing zo te configureren dat deze communiceert met de app-configuratie die u hebt gemaakt.
