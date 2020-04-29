---
title: Maak een beheerd privé-eind punt om verbinding te maken met uw gegevens bron resultaten.
description: In dit artikel leert u hoe u een beheerd persoonlijk eind punt maakt voor uw gegevens bronnen vanuit een Azure Synapse-werk ruimte.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428899"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Een beheerd persoonlijk eind punt maken voor uw gegevens bron (preview-versie)

In dit artikel leert u hoe u een beheerd persoonlijk eind punt maakt voor uw gegevens bron in Azure. Zie [beheerde persoonlijke eind punten](./synapse-workspace-managed-private-endpoints.md) voor meer informatie.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Stap 1: Open de Azure Synapse-werk ruimte in Azure Portal

U kunt een beheerd privé-eind punt maken voor uw gegevens bron vanuit Azure Synapse Studio. Selecteer het tabblad **overzicht** in azure Portal en selecteer **Synapse Studio starten**.
![Azure Synapse Studio starten](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Stap 2: Navigeer naar het tabblad beheerde virtuele netwerken in Synapse Studio

Selecteer in azure Synapse Studio het tabblad **beheren** vanuit het navigatie venster aan de linkerkant. Selecteer **beheerde virtuele netwerken** en selecteer vervolgens **+ Nieuw**.
![Een nieuw beheerd persoonlijk eind punt maken](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Stap 3: het type gegevens bron selecteren

Selecteer het type gegevens bron. In dit geval is de doel gegevens bron een ADLS Gen2-account. Selecteer **Doorgaan**.
![Selecteer een doel gegevens bron type](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Stap 4: Voer informatie over de gegevens bron in

Voer in het volgende venster informatie over de gegevens bron in. In dit voor beeld maken we een beheerd privé-eind punt voor een ADLS Gen2-account. Voer een **naam** in voor het beheerde persoonlijke eind punt. Geef een **Azure-abonnement** en een naam voor het **opslag account**op. Selecteer **Maken**.
![Details van doel gegevens bron opgeven](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Stap 5: controleren of uw beheerde persoonlijke eind punt is gemaakt

Nadat de aanvraag is verzonden, ziet u de status. Controleer de *inrichtings status*om te controleren of uw beheerde persoonlijke eind punt is gemaakt. Mogelijk moet u 1 minuut wachten en vervolgens **vernieuwen** selecteren om de inrichtings status bij te werken. U kunt zien dat het beheerde persoonlijke eind punt voor het ADLS Gen2-account is gemaakt.

U kunt ook zien dat de *goedkeurings status* *in behandeling*is. De eigenaar van de doel resource kan de verbindings aanvraag van het particuliere eind punt goed keuren of weigeren. Als de eigenaar de verbindings aanvraag van het particuliere eind punt goedkeurt, wordt een privé-koppeling tot stand gebracht. Als u deze weigert, wordt er geen persoonlijke koppeling tot stand gebracht.
![Status van de aanvraag voor het maken van beheerde privé-eind punten](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [beheerde privé-eind punten](./synapse-workspace-managed-private-endpoints.md)