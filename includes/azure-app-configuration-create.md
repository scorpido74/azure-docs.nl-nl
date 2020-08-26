---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "77619301"
---
1. Als u een nieuw App Configuration-archief wilt maken, moet u zich eerst aanmelden bij de [Azure-portal](https://portal.azure.com). Selecteer in de linkerbovenhoek van de startpagina de optie **Een resource maken**. Voer in het vak **Marketplace doorzoeken** **App Configuration** in en selecteer Invoeren.

    ![Zoeken naar App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecteer **App Configuration** in de zoekresultaten en selecteer vervolgens **Maken**.

    ![Selecteer Maken](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Voer op het deelvenster **App Configuration** > **Maken** de volgende instellingen in:

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Resourcenaam** | Wereldwijd unieke naam | Voer een unieke resourcenaam in voor de resource van het App Configuration-archief. De naam moet een tekenreeks zijn van 5 tot 50 tekens en mag alleen cijfers, letters en `-` bevatten. De naam mag niet beginnen of eindigen met `-`.  |
    | **Abonnement** | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken om App Configuration te testen. Als uw account maar één abonnement heeft, wordt dit automatisch geselecteerd en wordt de lijst **Abonnement** niet weergegeven. |
    | **Resourcegroep** | *AppConfigTestResources* | Selecteer of maak een resourcegroep voor de resource van het App Configuration-archief. Deze groep is handig voor het ordenen van meerdere resources die u mogelijk op een bepaald moment wilt verwijderen door resourcegroep te verwijderen. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](/azure/azure-resource-manager/resource-group-overview) voor meer informatie. |
    | **Locatie** | *US - centraal* | Gebruik **Locatie** om de geografische locatie op te geven waar het app-configuratiearchief wordt gehost. Voor de beste prestaties maakt u de resource in dezelfde regio als de andere onderdelen van uw toepassing. |
    | **Prijscategorie** | *Gratis* | Selecteer de gewenste prijscategorie. Ga voor meer informatie naar de [pagina met prijzen voor App Configuration](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Een resource voor een App Configuration-archief maken](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecteer **Maken**. De implementatie kan enkele minuten duren.

1. Nadat de implementatie is voltooid, selecteert u **Instellingen** > **Toegangssleutels**. Noteer de verbindingsreeks van de primaire alleen-lezensleutel. U hebt de verbindingsreeks later nodig voor de configuratie van uw toepassing, zodat deze kan communiceren met het App Configuration-archief dat u hebt gemaakt.
