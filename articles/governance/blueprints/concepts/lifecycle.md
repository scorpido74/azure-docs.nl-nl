---
title: De levenscyclus van een blauwdruk begrijpen
description: Meer informatie over de levenscyclus die een blauwdrukdefinitie doorloopt en details over elke fase, inclusief het bijwerken en verwijderen van blauwdruktoewijzingen.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd5cb7d085744377cf12998f14c994fb1dcd2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74404579"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>De levenscyclus van een Azure Blueprint begrijpen

Net als veel andere bronnen binnen Azure heeft een blauwdruk in Azure Blueprints een typische en natuurlijke levenscyclus. Ze worden gemaakt, geïmplementeerd en uiteindelijk verwijderd wanneer ze niet meer nodig of relevant zijn.
Blueprints ondersteunt standaard levenscyclusbewerkingen. Het bouwt vervolgens op hen voort om extra statusniveaus te bieden die gemeenschappelijke continue integratie- en continue implementatiepijplijnen ondersteunen voor organisaties die hun infrastructuur als code beheren - een belangrijk element in DevOps.

Om een blauwdruk en de fasen volledig te begrijpen, bestrijken we een standaardlevenscyclus:

> [!div class="checklist"]
> - Een blauwdruk maken en bewerken
> - De blauwdruk publiceren
> - Een nieuwe versie van de blauwdruk maken en bewerken
> - Een nieuwe versie van de blauwdruk publiceren
> - Een specifieke versie van de blauwdruk verwijderen
> - De blauwdruk verwijderen

## <a name="creating-and-editing-a-blueprint"></a>Een blauwdruk maken en bewerken

Voeg bij het maken van een blauwdruk artefacten toe aan een beheergroep of abonnement en biedt een unieke naam en een unieke versie. De blauwdruk bevindt zich nu in een **conceptmodus** en kan nog niet worden toegewezen. In de **conceptmodus** kan deze blijven worden bijgewerkt en gewijzigd.

Een nooit gepubliceerde blauwdruk in **de conceptmodus** geeft een ander pictogram weer op de pagina **Blauwdrukdefinities** dan de pagina die is **gepubliceerd.** De **nieuwste versie** wordt weergegeven als **Concept** voor deze nooit gepubliceerde blauwdrukken.

Maak en bewerk een blauwdruk met de [Azure-portal](../create-blueprint-portal.md#create-a-blueprint) of [REST API.](../create-blueprint-rest-api.md#create-a-blueprint)

## <a name="publishing-a-blueprint"></a>Een blauwdruk publiceren

Zodra alle geplande wijzigingen zijn aangebracht in een blauwdruk in de **conceptmodus,** kan deze worden **gepubliceerd** en beschikbaar worden gesteld voor toewijzing. De **gepubliceerde** versie van de blauwdruk kan niet worden gewijzigd. Eenmaal **gepubliceerd**wordt de blauwdruk weergegeven met een ander pictogram dan **ontwerpblauwdrukken** en wordt het opgegeven versienummer weergegeven in de kolom **Laatste versie.**

Publiceer een blauwdruk met de [Azure-portal](../create-blueprint-portal.md#publish-a-blueprint) of [REST API.](../create-blueprint-rest-api.md#publish-a-blueprint)

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Een nieuwe versie van de blauwdruk maken en bewerken

Een **gepubliceerde** versie van een blauwdruk kan niet worden gewijzigd. Echter, een nieuwe versie van de blauwdruk kan worden toegevoegd aan de bestaande blauwdruk en gewijzigd als dat nodig is. Breng wijzigingen aan in een bestaande blauwdruk door deze te bewerken. Wanneer de nieuwe wijzigingen worden opgeslagen, heeft de blauwdruk nu **niet-gepubliceerde wijzigingen**. Deze wijzigingen zijn een nieuwe **conceptversie** van de blauwdruk.

Bewerk een blauwdruk met de [Azure-portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Een nieuwe versie van de blauwdruk publiceren

Elke bewerkte versie van een blauwdruk moet worden **gepubliceerd** voordat deze kan worden toegewezen. Wanneer **er geen wijzigingen** zijn aangebracht in een blauwdruk, maar niet zijn **gepubliceerd,** is de knop **Blauwdruk publiceren** beschikbaar op de blauwdrukpagina bewerken. Als de knop niet zichtbaar is, is de blauwdruk al **gepubliceerd** en zijn er geen **niet-gepubliceerde wijzigingen.**

> [!NOTE]
> Eén blauwdruk kan meerdere **gepubliceerde** versies bevatten die elk aan abonnementen kunnen worden toegewezen.

Als u een blauwdruk met **niet-gepubliceerde wijzigingen wilt**publiceren, gebruikt u dezelfde stappen voor het publiceren van een nieuwe blauwdruk.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Een specifieke versie van de blauwdruk verwijderen

Elke versie van een blauwdruk is een uniek object en kan individueel worden **gepubliceerd.** Als zodanig kan elke versie van een blauwdruk ook worden verwijderd. Het verwijderen van een versie van een blauwdruk heeft geen invloed op andere versies van die blauwdruk.

> [!NOTE]
> Het is niet mogelijk om een blauwdruk met actieve toewijzingen te verwijderen. Verwijder eerst de toewijzingen en verwijder vervolgens de versie die u wilt verwijderen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant en gebruik de filteropties om de blauwdruk te zoeken waarvan u een versie wilt verwijderen. Selecteer deze optie om de bewerkingspagina te openen.

1. Selecteer het tabblad **Gepubliceerde versies** en zoek de versie die u wilt verwijderen.

1. Klik met de rechtermuisknop op de versie om **deze versie**te verwijderen en te selecteren .

## <a name="deleting-the-blueprint"></a>De blauwdruk verwijderen

De kernblauwdruk kan ook worden verwijderd. Als u de kernblauwdruk verwijdert, worden ook de blauwdrukversies van die blauwdruk verwijderd, inclusief zowel **ontwerp-** als **gepubliceerde** blauwdrukken. Net als bij het verwijderen van een versie van een blauwdruk, verwijdert het verwijderen van de kernblauwdruk de bestaande toewijzingen van een van de blauwdrukversies niet.

> [!NOTE]
> Het is niet mogelijk om een blauwdruk met actieve toewijzingen te verwijderen. Verwijder eerst de toewijzingen en verwijder vervolgens de versie die u wilt verwijderen.

Een blauwdruk verwijderen met de [Azure-portal](../create-blueprint-portal.md#delete-a-blueprint) of [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Toewijzingen

Er zijn verschillende punten tijdens de levenscyclus die een blauwdruk aan een abonnement kan worden toegewezen. Wanneer de modus van een versie van de blauwdruk wordt **gepubliceerd,** kan die versie worden toegewezen aan een abonnement. Met deze levenscyclus kunnen versies van een blauwdruk worden gebruikt en actief worden toegewezen terwijl een nieuwere versie wordt ontwikkeld.

Aangezien versies van blauwdrukken zijn toegewezen, is het belangrijk om te begrijpen waar ze zijn toegewezen en met welke parameters ze zijn toegewezen. De parameters kunnen statisch of dynamisch zijn. Zie [statische en dynamische parameters](parameters.md)voor meer informatie.

### <a name="updating-assignments"></a>Toewijzingen bijwerken

Wanneer een blauwdruk is toegewezen, kan de toewijzing worden bijgewerkt. Er zijn verschillende redenen om een bestaande opdracht bij te werken, waaronder:

- Bronvergrendeling [resource locking](resource-locking.md) toevoegen of verwijderen
- De waarde van [dynamische parameters wijzigen](parameters.md#dynamic-parameters)
- De toewijzing upgraden naar een nieuwere **gepubliceerde** versie van de blauwdruk

Zie [bestaande toewijzingen bijwerken](../how-to/update-existing-assignments.md)voor meer informatie.

### <a name="unassigning-assignments"></a>Toewijzingen ontkoppelen

Als de blauwdruk niet meer nodig is, kan deze worden losgekoppeld van de beheergroep of het abonnement. Tijdens het uittoewijzing van een blauwdruk vindt het volgende plaats:

- Verwijdering van het vergrendelen van [blauwdrukbronnen](resource-locking.md)
- Verwijderen van het object blueprintassignment
- (Voorwaardelijk) Als een **door het systeem toegewezen beheerde identiteit** is gebruikt, wordt deze ook verwijderd

> [!NOTE]
> Alle resources die door de blauwdruktoewijzing worden geïmplementeerd, blijven op hun plaats, maar worden niet langer beschermd door Azure Blueprints.

## <a name="next-steps"></a>Volgende stappen

- Begrijpen hoe [statische en dynamische parameters](parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)