---
title: Een bestaande toewijzing vanuit de portal bijwerken
description: Meer informatie over het mechanisme voor het bijwerken van een bestaande blauwdruktoewijzing vanuit de portal in Azure Blueprints.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381792"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Een bestaande blauwdruktoewijzing bijwerken

Wanneer een blauwdruk is toegewezen, kan de toewijzing worden bijgewerkt. Er zijn verschillende redenen om een bestaande opdracht bij te werken, waaronder:

- Bronvergrendeling [resource locking](../concepts/resource-locking.md) toevoegen of verwijderen
- De waarde van [dynamische parameters wijzigen](../concepts/parameters.md#dynamic-parameters)
- De toewijzing upgraden naar een nieuwere **gepubliceerde** versie van de blauwdruk

## <a name="updating-assignments"></a>Toewijzingen bijwerken

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de linkerzijde op de blauwdruktoewijzing. Klik vervolgens op de knop **Toewijzing bijwerken** OF klik met de rechtermuisknop op de blauwdruktoewijzing en selecteer **Toewijzing bijwerken**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Een bestaande blauwdruktoewijzing bijwerken" border="false":::

1. Op **de blauwdrukpagina toewijzen** wordt vooraf gevuld met alle waarden uit de oorspronkelijke toewijzing geladen.
   U de versie van de **blauwdrukdefinitie,** de status **Toewijzing vergrendelen** en een van de dynamische parameters die op de blauwdrukdefinitie bestaan, wijzigen. Klik **op Toewijzen** wanneer u klaar bent met het aanbrengen van wijzigingen.

1. Zie op de pagina bijgewerkte toewijzingsdetails de nieuwe status. In dit voorbeeld hebben we **Vergrendeling** aan de toewijzing toegevoegd.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Een bestaande blauwdruktoewijzing bijgewerkt - de vergrendelingsmodus is gewijzigd" border="false":::

1. Meer informatie over andere **toewijzingsbewerkingen** vindt u in de vervolgkeuzelijst. De tabel **met beheerde resources** wordt bijgewerkt op geselecteerde toewijzingsbewerking.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Toewijzingsbewerkingen van een blauwdruktoewijzing" border="false":::

## <a name="rules-for-updating-assignments"></a>Regels voor het bijwerken van toewijzingen

De implementatie van de bijgewerkte toewijzingen volgt een aantal belangrijke regels. Deze regels bepalen wat er gebeurt met reeds geïmplementeerde resources. De gevraagde wijziging en het type artefact-bron dat wordt geïmplementeerd of bijgewerkt, bepalen welke acties worden uitgevoerd.

- Roltoewijzingen
  - Als de rol of de functie-toegewezene (gebruiker, groep of app) verandert, wordt een nieuwe roltoewijzing gemaakt. Roltoewijzingen die eerder zijn geïmplementeerd, blijven op hun plaats.
- Beleidstoewijzingen
  - Als de parameters van de beleidstoewijzing worden gewijzigd, wordt de bestaande toewijzing bijgewerkt.
  - Als de definitie van de beleidstoewijzing wordt gewijzigd, wordt een nieuwe beleidstoewijzing gemaakt.
    Beleidstoewijzingen die eerder zijn geïmplementeerd, blijven op hun plaats.
  - Als het artefact voor beleidstoewijzing uit de blauwdruk wordt verwijderd, blijven geïmplementeerde beleidstoewijzingen op hun plaats.
- Azure Resource Manager-sjablonen
  - De sjabloon wordt verwerkt via Resource Manager als **put**. Terwijl elk resourcetype deze actie anders verwerkt, controleert u de documentatie voor elke opgenomen resource om de impact van deze actie te bepalen wanneer deze wordt uitgevoerd door Blauwdrukken.

## <a name="possible-errors-on-updating-assignments"></a>Mogelijke fouten bij het bijwerken van toewijzingen

Bij het bijwerken van toewijzingen is het mogelijk om wijzigingen aan te brengen die breken wanneer deze worden uitgevoerd. Een voorbeeld is het wijzigen van de locatie van een resourcegroep nadat deze al is geïmplementeerd. Elke wijziging die wordt ondersteund door [Azure Resource Manager](../../../azure-resource-manager/management/overview.md) kan worden aangebracht, maar elke wijziging die zou resulteren in een fout via Azure Resource Manager zal ook resulteren in het mislukken van de toewijzing.

Er is geen limiet aan het aantal keren dat een toewijzing kan worden bijgewerkt. Als er een fout optreedt, bepaalt u de fout en maakt u een nieuwe update voor de toewijzing.  Voorbeeld van foutscenario's:

- Een slechte parameter
- Een reeds bestaand object
- Een wijziging die niet wordt ondersteund door Azure Resource Manager

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../concepts/resource-locking.md)
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)