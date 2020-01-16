---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 7da9f795f1865d635a26cd3acf3866dff3565716
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021241"
---
1. Selecteer in het menu Azure Portal de optie **een resource maken**.

    ![Resource toevoegen met behulp van het menu Azure Portal](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. Selecteer op de pagina **Nieuw** de optie **reken** > **functie-app**.

1. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    ![Basisbeginselen](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (niet hoofdletter gevoelig), `0-9`en `-`.  |
    |**Publiceren**| Coderen | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**Regio**| Voorkeurs regio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **volgende: hosten >** .

1. Voer de volgende instellingen in voor hosting.

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Besturingssysteem**| Voor keur besturings systeem | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Fonds](../articles/azure-functions/functions-scale.md)** | Verbruiksabonnement | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. In het standaard **Verbruiksabonnement** worden resources dynamisch toegevoegd zoals door uw functies wordt vereist. Met hosten [zonder server](https://azure.microsoft.com/overview/serverless-computing/) betaalt u alleen voor de tijd dat uw functies actief zijn. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.  |

    Selecteer de knop **volgende: bewaking >** .

1. Voer de volgende instellingen in voor bewaking.

    ![Controleren](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan. |

    Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.

1. Selecteer **Maken** om de functie-app in te richten en te implementeren.

1. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

    ![Implementatiemelding](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.
