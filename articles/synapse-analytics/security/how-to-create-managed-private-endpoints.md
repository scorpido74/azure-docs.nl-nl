---
title: Maak een beheerd privéeindpunt om verbinding te maken met de resultaten van uw gegevensbron.
description: In dit artikel leert u hoe u een beheerd privéeindpunt maakt voor uw gegevensbronnen vanuit een Azure Synapse-werkruimte.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428899"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Een beheerd privéeindpunt maken naar uw gegevensbron (voorbeeld)

In dit artikel leert u hoe u een beheerd privéeindpunt maakt voor uw gegevensbron in Azure. Zie [Beheerde privéeindpunten](./synapse-workspace-managed-private-endpoints.md) voor meer informatie.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Stap 1: Uw Azure Synapse-werkruimte openen in Azure-portal

U een beheerd privéeindpunt maken voor uw gegevensbron vanuit Azure Synapse Studio. Selecteer het tabblad **Overzicht** in azure-portal en selecteer **Synapse Studio starten**.
![Azure Synapse Studio starten](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Stap 2: Navigeer naar het tabblad Beheerde virtuele netwerken in Synapse Studio

Selecteer in Azure Synapse Studio het tabblad **Beheren** op de linkernavigatie. Selecteer **Beheerde virtuele netwerken** en selecteer + **Nieuw**.
![Een nieuw beheerd privéeindpunt maken](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Stap 3: Het gegevensbrontype selecteren

Selecteer het gegevensbrontype. In dit geval is de doelgegevensbron een ADLS gen2-account. Selecteer **Doorgaan**.
![Een doelgegevensbrontype selecteren](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Stap 4: Informatie invoeren over de gegevensbron

Voer in het volgende venster informatie in over de gegevensbron. In dit voorbeeld maken we een beheerd privéeindpunt voor een ADLS gen2-account. Voer een **naam** in voor het privéeindpunt Beheer. Geef een **Azure-abonnement** en een **naam van een Opslagaccount op**. Selecteer **Maken**.
![Gegevensover doelgegevens invoeren](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Stap 5: controleren of uw beheerde privéeindpunt is gemaakt

Nadat u het verzoek hebt ingediend, ziet u de status ervan. Als u wilt controleren of het beheer van uw beheerde privéeindpunt is gemaakt, controleert u de *voorzieningsstatus*. Mogelijk moet u 1 minuut wachten en **Vernieuwen** selecteren om de inrichtingsstatus bij te werken. U zien dat het beheerde privéeindpunt voor het ADLS gen2-account is gemaakt.

U ook zien dat de *goedkeuringsstatus* *in behandeling*is. De eigenaar van de doelbron kan de aanvraag voor privéeindpuntverbinding goedkeuren of weigeren. Als de eigenaar de aanvraag voor privé-eindpuntverbinding goedkeurt, wordt een privékoppeling tot stand gebracht. Als geweigerd, dan is een prive-link niet vastgesteld.
![Status van beheerde status voor het maken van privéeindpunten](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [beheerde privéeindpunten](./synapse-workspace-managed-private-endpoints.md)