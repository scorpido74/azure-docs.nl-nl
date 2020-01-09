---
title: bestand opnemen
description: bestand opnemen
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: ccd2a0ded1cd915dc52dea1449d75136bbef18fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413409"
---
1. Als u een nieuwe app-configuratie wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de start pagina de optie **een resource maken**. In het vak **Marketplace doorzoeken** voert u de **app-configuratie** in en selecteert u ENTER.

    ![Zoeken naar App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Selecteer **app-configuratie** in de zoek resultaten en selecteer vervolgens **maken**.

1. Voer in het deel venster **app-configuratie** > **maken** de volgende instellingen in:

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Resourcenaam** | Wereldwijd unieke naam | Voer een unieke resource naam in om te gebruiken voor de bron van het configuratie archief van de app. De naam moet een tekenreeks zijn van 1 tot 63 tekens die alleen cijfers, letters en het teken `-` mag bevatten. De naam mag niet beginnen of eindigen met het `-` teken en opeenvolgende `-` tekens zijn niet geldig.  |
    | **Abonnement** | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken om App Configuration te testen. Als uw account slechts één abonnement heeft, wordt dit automatisch geselecteerd en wordt de lijst met **abonnementen** niet weer gegeven. |
    | **Resourcegroep** | *AppConfigTestResources* | Selecteer of maak een resource groep voor uw app-configuratie opslag resource. Deze groep is handig als u meerdere resources wilt ordenen die u op hetzelfde moment wilt verwijderen door de resource groep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](/azure/azure-resource-manager/resource-group-overview) voor meer informatie. |
    | **Locatie** | *US - centraal* | Gebruik **Locatie** om de geografische locatie op te geven waar het app-configuratiearchief wordt gehost. Voor de beste prestaties maakt u de resource in dezelfde regio als andere onderdelen van uw toepassing. |

    ![Een resource voor het configuratie archief van de app maken](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Selecteer **Maken**. De implementatie kan enkele minuten duren.

1. Nadat de implementatie is voltooid, selecteert u **instellingen** > **toegangs sleutels**. Noteer de primaire alleen-lezen sleutel connection string. U gebruikt deze connection string later om uw toepassing zo te configureren dat deze communiceert met de app-configuratie die u hebt gemaakt.
