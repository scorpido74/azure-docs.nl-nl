---
title: 'Zelfstudie: Uitgaven voorspellen met Cloudyn in Azure'
description: In deze zelfstudie leest u hoe u uitgaven kunt voorspellen met behulp van historische gegevens van gebruik en uitgaven.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 455f611b57cf11e29d35e617df4fd9c09aff2906
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79463778"
---
# <a name="tutorial-forecast-future-spending"></a>Zelfstudie: Toekomstige uitgaven voorspellen

Cloudyn helpt u bij het voorspellen van toekomstige uitgaven aan de hand van historische gegevens van gebruik en uitgaven. U gebruikt rapporten van Cloudyn om gegevens van alle geschatte kosten weer te geven. De voorbeelden in deze zelfstudie laten zien hoe u kostenschattingen kunt controleren met behulp van de rapporten. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toekomstige uitgaven voorspellen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben.
- U moet een proefregistratie of een betaald abonnement voor Cloudyn hebben.

## <a name="forecast-future-spending"></a>Toekomstige uitgaven voorspellen

Cloudyn bevat rapporten voor kostenschatting om u helpen bij het voorspellen van uitgaven op basis van uw gebruik in de loop der tijd. Het hoofddoel van de rapporten is ervoor te zorgen dat uw kostentrends niet de verwachtingen van uw organisatie overschrijden. U gebruikt hiervoor de rapporten Current Month Projected Cost en Annual Projected Cost. Beide rapporten tonen de verwachte toekomstige uitgaven als uw gebruik relatief gelijk blijft met het gebruik over de 30 laatste dagen.

Het rapport Current Month Projected Cost toont de kosten van uw services. De geschatte kosten worden bepaald aan de hand van de kosten aan het begin van de maand en van de vorige maand. Klik in het menu Reports boven aan de portal op **Costs** > **Projection and Budget** > **Current Month Projected Cost**. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeldinformatie die wordt weergegeven in het rapport Verwachte kosten voor de huidige maand](./media/tutorial-forecast-spending/project-month01.png)

In het voorbeeld kunt u zien voor welke services de uitgaven het hoogst zijn. De kosten voor Azure waren lager dan voor AWS. Als u kostenschattingen wilt zien voor virtuele machines van Azure, selecteert u **Azure/VM** in de lijst **Filter**.

![Voorbeeld waarin de verwachte kosten voor Azure-VM's voor de huidige maand worden weergegeven](./media/tutorial-forecast-spending/project-month02.png)

Volg dezelfde voorgaande basisstappen om te kijken naar de maandelijkse verwachte kosten voor andere services waarin u geïnteresseerd bent.

Het rapport Annual Projected Cost bevat de geëxtrapoleerde kosten van uw services voor de volgende twaalf maanden.

Klik in het menu Reports boven aan de portal op **Costs** > **Projection and Budget** > **Annual Projected Cost**. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeld waarin het rapport Jaarlijkse verwachte kosten wordt weergegeven](./media/tutorial-forecast-spending/project-annual01.png)

In het voorbeeld kunt u zien voor welke services de uitgaven het hoogst zijn. Net als in het voorbeeld voor een maand zijn de kosten voor Azure lager dan de kosten voor AWS. Als u kostenschattingen wilt zien voor virtuele machines van Azure, selecteert u **Azure/VM** in de lijst **Filter**.

![Voorbeeld waarin de jaarlijkse verwachte kosten voor VM's worden weergegeven](./media/tutorial-forecast-spending/project-annual02.png)

In de afbeelding hierboven worden de jaarlijkse kosten van virtuele machines van Azure geschat op $ 28.374.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Toekomstige uitgaven voorspellen


Ga naar de volgende zelfstudie als u wilt leren hoe u kosten kunt beheren met kostentoewijzing en showback-rapporten.

> [!div class="nextstepaction"]
> [Kosten beheren met kostentoewijzing en showback-rapporten](../../cost-management/tutorial-manage-costs.md)
