---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057205"
---
1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer **op** de pagina Nieuw de optie > **Rekenfunctie-app**. **Compute**

1. Gebruik op de pagina **Basisbeginselen** de instellingen van de functie-app zoals opgegeven in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Functie-app-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens `a-z` zijn (geval `0-9`ongevoelig), en `-`.  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET Core** voor c# en F#-functies. |
    |**Versie**| Versienummer | Kies de versie van de geïnstalleerde runtime.  |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    ![Basisbeginselen](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Selecteer **Volgende: Hosting**. Voer **op** de pagina Hosting de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U ook een bestaand account gebruiken, dat moet voldoen aan de [vereisten voor opslagaccount.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Besturingssysteem**| Voorkeursbesturingssysteem | Een besturingssysteem is vooraf voor u geselecteerd op basis van uw runtime stackselectie, maar u de instelling indien nodig wijzigen. |
    | **[Plan](../articles/azure-functions/functions-scale.md)** | **Verbruik (serverloos)** | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. In het **standaardverbruiksplan** worden resources dynamisch toegevoegd, zoals vereist door uw functies. In deze [serverless](https://azure.microsoft.com/overview/serverless-computing/) hosting betaalt u alleen voor de tijd dat uw functies worden uitgevoerd. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Selecteer **Volgende: Controle**. Voer **op** de pagina Controle de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een application insights-bron met dezelfde *app-naam* in het dichtstbijzijnde ondersteunde gebied. Door deze instelling uit te breiden, u de **naam van** de nieuwe bron wijzigen of een andere **locatie** kiezen in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

    ![Bewaking](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecteer **Controleren + maken** om de selecties voor app-configuraties te bekijken.

1. Controleer op de pagina **Controleren + maken** uw instellingen en selecteer Vervolgens **Maken** om de functie-app in te richten en te implementeren.

1. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U ook **Vastmaken aan het dashboard**selecteren. Vastmaken maakt het eenvoudiger om vanuit je dashboard terug te keren naar deze functie-appbron.

    ![Implementatiemelding](./media/functions-create-function-app-portal/function-app-create-notification2.png)