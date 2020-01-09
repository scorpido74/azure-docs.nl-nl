---
title: Een aangepaste prijs opstaande tegel in azure-Dash boards gebruiken
description: Meer informatie over het toevoegen van een tegel met korting op een Azure-dash board voor het weer geven van statische inhoud
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640139"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Een tegel met korting op Azure-Dash boards gebruiken om aangepaste inhoud weer te geven

U kunt een tegel met korting toevoegen aan uw Azure-Dash boards om aangepaste statische inhoud weer te geven. U kunt bijvoorbeeld basis instructies, een afbeelding of een set met Hyper links weer geven met een tegel prijs verlaging.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Een tegel met korting toevoegen aan uw dash board

1. Selecteer **dash board** in de Azure Portal zijbalk. Als u aangepaste Dash boards hebt gemaakt, gebruikt u in de dashboard weergave de vervolg keuzelijst om het dash board te selecteren waar de tegel aangepaste prijs opgave moet worden weer gegeven. Selecteer het bewerkings pictogram om de **tegel galerie**te openen.

   ![Scherm opname van weer gave dash board bewerken](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Zoek in de **tegel galerie**de tegel met de naam **prijs** opgave en klik op **toevoegen**. De tegel wordt toegevoegd aan het dash board en het deel venster **verlaging bewerken** wordt geopend.

1. Bewerk de velden **titel**, **subtitel**en **inhoud** om de tegel aan te passen. In het voor beeld dat hier wordt weer gegeven, is de tegel prijs opgave bewerkt om aangepaste Help Desk-informatie weer te geven.

   ![Scherm opname van de tegel bewerkings weergave korting weer geven](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Selecteer **gereed** om het deel venster **prijs verlaging bewerken** te sluiten. Uw inhoud wordt weer gegeven op de tegel prijs opgave, die vervolgens kan worden aangepast door de greep in de rechter benedenhoek te slepen.

   ![Scherm opname van tegel met aangepaste prijs verlaging](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Mogelijkheden en beperkingen van de inhoud prijs verlaging

U kunt elke combi natie van de syntaxis voor tekst zonder opmaak en de inhoud van een HTML-bestand gebruiken op de tegel prijs verlaging. De Azure Portal gebruikt een open-source bibliotheek met de naam _gemarkeerd_ om uw inhoud te TRANSFORMEREN in HTML die wordt weer gegeven op de tegel. De HTML-code die wordt geproduceerd door _gemarkeerd_ , wordt vooraf verwerkt door de portal voordat deze wordt weer gegeven. Met deze stap zorgt u ervoor dat uw aanpassing geen invloed heeft op de beveiliging of indeling van de portal. Tijdens deze vooraf-verwerking wordt elk deel van de HTML dat een mogelijke bedreiging vormt, verwijderd. De volgende typen inhoud zijn niet toegestaan door de portal:

* Java script: `<script>` Tags en inline java script-evaluaties worden verwijderd.
* iframes-`<iframe>` Tags worden verwijderd.
* Style-`<style>` Tags worden verwijderd. Inline stijl kenmerken voor HTML-elementen worden niet officieel ondersteund. Het kan voor komen dat sommige inline-stijl elementen werken voor u, maar als deze de indeling van de portal beïnvloeden, kunnen ze op elk gewenst moment niet meer werken. De tegel prijs verlaging is bedoeld voor basis statische inhoud die gebruikmaakt van de standaard stijlen van de portal.

## <a name="next-steps"></a>Volgende stappen

* Zie [Dash boards maken en delen in de Azure portal voor het maken van](../azure-portal/azure-portal-dashboards.md) een aangepast dash board.
