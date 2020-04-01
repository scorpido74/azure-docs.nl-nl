---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619301"
---
1. Als u een nieuwe app-configuratiewinkel wilt maken, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com) Selecteer in de linkerbovenhoek van de startpagina de optie **Een resource maken**. Voer in het vak **Marketplace zoeken de** **app-configuratie** in en selecteer Enter.

    ![Zoeken naar App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecteer **App-configuratie** in de zoekresultaten en selecteer **Vervolgens Maken**.

    ![Selecteer Maken](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Voer in het deelvenster **App-configuratie** > **maken** de volgende instellingen in:

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Resourcenaam** | Wereldwijd unieke naam | Voer een unieke bronnaam in die u wilt gebruiken voor de bron van het App Configuration Store-archief. De naam moet een tekenreeks tussen 5 en 50 tekens `-` zijn en alleen cijfers, letters en het teken bevatten. De naam kan niet beginnen `-` of eindigen met het teken.  |
    | **Abonnement** | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken om App Configuration te testen. Als uw account slechts één abonnement heeft, wordt het automatisch geselecteerd en wordt de **lijst met abonnementen** niet weergegeven. |
    | **Resourcegroep** | *AppConfigTestResources* | Selecteer of maak een resourcegroep voor uw app-configuratiearchiefbron. Deze groep is handig voor het organiseren van meerdere resources die u tegelijkertijd wilt verwijderen door de brongroep te verwijderen. Zie [Brongroepen gebruiken om uw Azure-bronnen te beheren voor](/azure/azure-resource-manager/resource-group-overview)meer informatie. |
    | **Locatie** | *VS - centraal* | Gebruik **Locatie** om de geografische locatie op te geven waar het app-configuratiearchief wordt gehost. Maak de resource voor de beste prestaties in dezelfde regio als andere onderdelen van uw toepassing. |
    | **Prijslaag** | *Gratis* | Selecteer de gewenste prijscategorie. Zie de [prijspagina app-configuratie](https://azure.microsoft.com/pricing/details/app-configuration/)voor meer informatie.

    ![Een app-configuratieopslagbron maken](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecteer **Maken**. De implementatie kan enkele minuten duren.

1. Nadat de implementatie is voltooid, selecteert u **Toegangssleutels** > **instellingen**. Noteer de primaire alleen-lezen-toetstekenreeks. U gebruikt deze verbindingstekenreeks later om uw toepassing te configureren om te communiceren met de app-configuratieopslag die u hebt gemaakt.
