---
title: bestand opnemen
description: bestand opnemen
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: c53486bf3368039f172c7a13420e2291dd9c9892
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122643"
---
1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** de optie **reken**  >  **functie-app**.

1. Gebruik de instellingen voor de functie-app, zoals opgegeven in de volgende tabel, op de pagina **basis informatie** :

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resource groep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **functie-app naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (niet hoofdletter gevoelig), `0-9` en `-` .  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**Deel**| Voorkeurs regio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    ![De pagina Basisinformatie](./media/functions-premium-create/function-app-create-basics.png)

1. Selecteer **volgende: hosten**. Voer op de **Hosting** pagina de volgende instellingen in:

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Storage-account](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Besturingssysteem**| Voor keur besturings systeem | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Plannen](../articles/azure-functions/functions-scale.md)** | Premium | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. Selecteer **Premium**en selecteer vervolgens de standaard waarden voor **Windows-plan** en- **SKU en-grootte**. |

    ![Hosting pagina](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Selecteer **volgende: bewaken**. Voer op de pagina **controle** de volgende instellingen in:

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen om uw gegevens op te slaan. |

    ![Bewakings pagina](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.

1. Controleer uw instellingen op de pagina **controleren en maken** en selecteer vervolgens **maken** om de functie-app in te richten en te implementeren.

1. Selecteer het pictogram **meldingen** in de rechter bovenhoek van de portal en Bekijk het bericht **implementatie voltooid** .

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.

    ![Implementatiemelding](./media/functions-premium-create/function-app-create-notification2.png)