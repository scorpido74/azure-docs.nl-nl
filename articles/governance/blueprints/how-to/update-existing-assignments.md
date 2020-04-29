---
title: Een bestaande toewijzing bijwerken vanuit de portal
description: Meer informatie over het mechanisme voor het bijwerken van een bestaande blauw druk-toewijzing vanuit de portal in azure-blauw drukken.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381792"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Een bestaande blauw druk-toewijzing bijwerken

Wanneer een blauw druk is toegewezen, kan de toewijzing worden bijgewerkt. Er zijn verschillende redenen voor het bijwerken van een bestaande toewijzing, waaronder:

- [Resource vergrendeling](../concepts/resource-locking.md) toevoegen of verwijderen
- De waarde van [dynamische para meters](../concepts/parameters.md#dynamic-parameters) wijzigen
- De toewijzing upgraden naar een nieuwere, **gepubliceerde** versie van de blauw druk

## <a name="updating-assignments"></a>Toewijzingen bijwerken

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **toegewezen blauw drukken** op de pagina aan de linkerkant.

1. Klik in de lijst met blauw drukken met de rechter muisknop op de blauw druk-toewijzing. Klik vervolgens op de knop **toewijzing bijwerken** of klik met de rechter muisknop op de blauw druk toewijzing en selecteer **Update toewijzing**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Een bestaande blauw druk-toewijzing bijwerken" border="false":::

1. De pagina **blauw** drukken wordt geladen, waarbij alle waarden van de oorspronkelijke toewijzing vooraf zijn ingevuld.
   U kunt de definitie van de **blauw druk**, de **vergrendelings toewijzings** status en de dynamische para meters van de definitie van de blauw druk wijzigen. Klik op **toewijzen** wanneer u klaar bent met het maken van wijzigingen.

1. Bekijk op de pagina bijgewerkte toewijzings Details de nieuwe status. In dit voor beeld hebben we **vergren deling** toegevoegd aan de toewijzing.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Er is een bestaande blauw druk-toewijzing bijgewerkt-vergrendelings modus gewijzigd" border="false":::

1. Bekijk details over andere **toewijzings bewerkingen** met behulp van de vervolg keuzelijst. De tabel met **beheerde resources** die worden bijgewerkt op basis van de geselecteerde toewijzings bewerking.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Toewijzings bewerkingen van een blauw druk toewijzen" border="false":::

## <a name="rules-for-updating-assignments"></a>Regels voor het bijwerken van toewijzingen

De implementatie van de bijgewerkte toewijzingen volgt enkele belang rijke regels. Deze regels bepalen wat er gebeurt met resources die al zijn geïmplementeerd. De aangevraagde wijziging en het type artefact bron dat wordt geïmplementeerd of bijgewerkt, bepalen welke acties worden uitgevoerd.

- Roltoewijzingen
  - Als de rol of de toegewezen rol (gebruiker, groep of app) wordt gewijzigd, wordt een nieuwe roltoewijzing gemaakt. Roltoewijzingen die eerder zijn geïmplementeerd, blijven aanwezig.
- Beleidstoewijzingen
  - Als de para meters van de beleids toewijzing zijn gewijzigd, wordt de bestaande toewijzing bijgewerkt.
  - Als de definitie van de beleids toewijzing is gewijzigd, wordt er een nieuwe beleids toewijzing gemaakt.
    De eerder geïmplementeerde beleids toewijzingen blijven aanwezig.
  - Als het artefact voor beleids toewijzing van de blauw druk wordt verwijderd, blijven geïmplementeerde beleids toewijzingen aanwezig.
- Azure Resource Manager-sjablonen
  - De sjabloon wordt als een **put**verwerkt door Resource Manager. Raadpleeg de documentatie voor elke opgenomen resource als elk resource type deze actie anders verwerkt, om de impact van deze actie te bepalen wanneer deze wordt uitgevoerd door blauw drukken.

## <a name="possible-errors-on-updating-assignments"></a>Mogelijke fouten bij het bijwerken van toewijzingen

Wanneer u toewijzingen bijwerkt, kunt u wijzigingen aanbrengen die onderbreekt wanneer ze worden uitgevoerd. Een voor beeld is het wijzigen van de locatie van een resource groep nadat deze al is geïmplementeerd. Alle wijzigingen die door [Azure Resource Manager](../../../azure-resource-manager/management/overview.md) worden ondersteund, kunnen worden aangebracht, maar elke wijziging die zou leiden tot een fout via Azure Resource Manager, resulteert ook in het mislukken van de toewijzing.

Er is geen limiet voor het aantal keren dat een toewijzing kan worden bijgewerkt. Als er een fout optreedt, controleert u de fout en maakt u een andere update voor de toewijzing.  Voor beelden van fout scenario's:

- Een ongeldige para meter
- Een al bestaand object
- Een wijziging die niet wordt ondersteund door Azure Resource Manager

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).