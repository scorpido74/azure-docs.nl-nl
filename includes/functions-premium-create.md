---
title: bestand opnemen
description: bestand opnemen
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020826"
---
1. Open de Azure Portal vanuit [https://portal.azure.com](https://portal.azure.com)

1. Selecteer de knop **een resource maken**

    ![Bron maken](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Selecteer **reken** > **functie-app**.

    ![Een functie-app maken in Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    ![Basisbeginselen](./media/functions-premium-create/function-app-create-basics.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (niet hoofdletter gevoelig), `0-9`en `-`.  |
    |**Publiceren**| Coderen | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**Regio**| Voorkeurs regio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **volgende: hosten >** .

1. Voer de volgende hosting-instellingen in.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat moet voldoen aan de [vereisten voor het opslag account](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Besturingssysteem**| Voor keur besturings systeem | Er wordt vooraf een besturings systeem geselecteerd voor u op basis van de selectie van de runtime stack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Fonds](../articles/azure-functions/functions-scale.md)** | Premium | Selecteer **Premium (preview)** bij plan type en selecteer standaard instellingen voor het *Windows-plan* en de opties voor de *SKU en de grootte* . |

    Selecteer de knop **volgende: bewaking >** .

1. Voer de volgende bewakings instellingen in.

    ![Controleren](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan. |

    Selecteer **controleren + maken** om de selecties van de app-configuratie te controleren.

1. Selecteer **Maken** om de functie-app in te richten en te implementeren.