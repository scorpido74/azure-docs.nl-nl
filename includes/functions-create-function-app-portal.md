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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057205"
---
1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** de optie **reken** > **functie-app**.

1. Gebruik de instellingen voor de functie-app, zoals opgegeven in de volgende tabel op de pagina **basis beginselen** .

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resource groep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **functie-app naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (niet hoofdletter gevoelig), `0-9`en `-`.  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.net core** voor C#-en F #-functies. |
    |**Versie**| Versienummer | Kies de versie van de geïnstalleerde runtime.  |
    |**Regio**| Voorkeurs regio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    ![Basisbeginselen](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Selecteer **volgende: hosten**. Voer op de **Hosting** pagina de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Storage-account](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Besturingssysteem**| Voor keur besturings systeem | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Plannen](../articles/azure-functions/functions-scale.md)** | **Verbruik (Serverloos)** | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. In het standaard **verbruiks** plan worden resources dynamisch toegevoegd als vereist door uw functies. In deze [serverloze](https://azure.microsoft.com/overview/serverless-computing/) hosting betaalt u alleen voor de tijd dat uw functies worden uitgevoerd. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../articles/azure-functions/functions-scale.md) beheren.  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Selecteer **volgende: bewaken**. Voer op de pagina **controle** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan. |

    ![Bewaking](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.

1. Controleer uw instellingen op de pagina **controleren en maken** en selecteer vervolgens **maken** om de functie-app in te richten en te implementeren.

1. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.

    ![Implementatiemelding](./media/functions-create-function-app-portal/function-app-create-notification2.png)