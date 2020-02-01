---
title: Dash boards maken en delen in de Azure Portal
description: In dit artikel wordt beschreven hoe u Dash boards kunt maken, aanpassen, publiceren en delen in de Azure Portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901054"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Dash boards maken en delen in de Azure Portal

Dash boards zijn een gerichte en georganiseerde weer gave van uw cloud resources in de Azure Portal. Gebruik Dash boards als werk ruimte waar u snel taken kunt starten voor dagelijkse bewerkingen en om resources te bewaken. Bouw bijvoorbeeld aangepaste Dash boards op basis van projecten, taken of gebruikers rollen.

De Azure Portal biedt een standaard dashboard als uitgangs punt. U kunt het standaard dashboard bewerken. Maak en pas extra dash boards aan en publiceer en deel Dash boards om ze beschikbaar te maken voor andere gebruikers. In dit artikel wordt beschreven hoe u een nieuw dash board kunt maken, de interface kunt aanpassen en dash boards kunt publiceren en delen.

## <a name="create-a-new-dashboard"></a>Een nieuw dashboard maken

In dit voor beeld maken we een nieuw persoonlijk dash board en wijzen we een naam toe. Volg deze stappen om aan de slag te gaan:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu Azure Portal de optie **dash board**. De standaard weergave is mogelijk al ingesteld op dash board.

    ![Het dash board openen](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecteer **Nieuw dash board**.

    ![Scherm opname van standaard dashboard](./media/azure-portal-dashboards/create-new-dashboard.png)

    Met deze actie wordt de **tegel galerie**geopend, van waaruit u tegels selecteert en een leeg raster waarin u de tegels rangschikt.

    ![Scherm opname van Tegel galerie en leeg raster](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecteer de tekst van **mijn dash board** in het label van het dash board en voer een naam in waarmee u het aangepaste dash board eenvoudig kunt identificeren.

1. Selecteer **gereed aanpassen** in de paginakop tekst om de bewerkings modus af te sluiten.

In de dashboard weergave wordt nu uw lege dash board weer gegeven. Selecteer de pijl naast de naam van het dash board om de Dash boards weer te geven die voor u beschikbaar zijn. De lijst kan Dash boards bevatten die andere gebruikers hebben gemaakt en gedeeld.

## <a name="edit-a-dashboard"></a>Een dash board bewerken

Nu gaan we het dash board bewerken om tegels die uw Azure-resources vertegenwoordigen, toe te voegen, te verg Roten of te verkleinen en te rangschikken.

### <a name="add-tiles"></a>Tegels toevoegen

Voer de volgende stappen uit om tegels toe te voegen aan een dash board:

1. Selecteer ![pictogram bewerken](./media/azure-portal-dashboards/dashboard-edit-icon.png) **bewerken** in de koptekst van de pagina.

    ![Scherm opname van het markeren van Dash boards bewerken](./media/azure-portal-dashboards/dashboard-edit.png)

1. Blader door de **tegel galerie** of gebruik het zoek veld om de gewenste tegel te vinden.

1. Selecteer **toevoegen** om de tegel toe te voegen aan het dash board met een standaard grootte en-locatie. U kunt ook de tegel naar het raster slepen en daar neerzetten.

Veel resource pagina's bevatten een pictogram van een punaise in de opdracht balk. Als u het pictogram selecteert, wordt een tegel die de bron pagina vertegenwoordigt, vastgemaakt aan het dash board dat momenteel actief is. Deze methode is een alternatieve manier om tegels aan uw dash board toe te voegen.

![Scherm afbeelding van de opdracht balk pagina met een speld pictogram](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Als u met meer dan één organisatie werkt, voegt u de tegel **organisatie-identiteit** toe aan uw dash board om duidelijk te zien in welke organisatie de resources horen.
>
>

### <a name="resize-or-rearrange-tiles"></a>Tegels verg Roten of verkleinen of rangschikken

Als u de grootte van een tegel wilt wijzigen of de tegels op een dash board opnieuw wilt rangschikken, volgt u deze stappen:

1. Selecteer ![pictogram bewerken](./media/azure-portal-dashboards/dashboard-edit-icon.png) **bewerken** in de koptekst van de pagina.

1. Selecteer het context menu in de rechter bovenhoek van een tegel. Kies vervolgens een tegel grootte. Tegels die elke grootte ondersteunen, bevatten ook een ' ingang ' in de rechter benedenhoek waarmee u de tegel naar de gewenste grootte kunt slepen.

    ![Scherm opname van het dash board met het menu grootte van Tegel open](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecteer een tegel en sleep deze naar een nieuwe locatie op het raster om uw dash board te rangschikken.

### <a name="additional-tile-configuration"></a>Aanvullende tegel configuratie

Voor sommige tegels is mogelijk meer configuratie vereist om de gewenste informatie weer te geven. De tegel grafiek met **metrische gegevens** moet bijvoorbeeld worden ingesteld om een metrische waarde van **Azure monitor**weer te geven. U kunt ook tegel gegevens aanpassen om de standaard tijd instellingen van het dash board te overschrijven.

Elke tegel die moet worden ingesteld, geeft een **tegel banner configureren** weer totdat u de tegel aanpast. Selecteer de banner en voer de vereiste installatie uit.

![Scherm opname van de tegel waarvoor configuratie is vereist](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Met de tegel prijs verlaging kunt u aangepaste statische inhoud op uw dash board weer geven. Dit kunnen basis instructies, een afbeelding, een aantal hyper links of zelfs contact gegevens zijn. Zie voor meer informatie over het gebruik van een geprijsde tegel [een tegel korting gebruiken op Azure-Dash boards om aangepaste inhoud weer te geven](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Tegel gegevens aanpassen

Gegevens in het dash board geven automatisch activiteiten weer voor de afgelopen 24 uur. Voer de volgende stappen uit om een andere tijds Panne voor alleen deze tegel weer te geven:

1. Selecteer **tegel gegevens aanpassen** in het context menu of het filter pictogram ![](./media/azure-portal-dashboards/dashboard-filter.png) vanuit de linkerbovenhoek van de tegel.

    ![Scherm opname van context menu voor tegel](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Schakel het selectie vakje in om **de instellingen van het dash board te overschrijven op het niveau van de tegel**.

    ![Scherm opname van dialoog venster voor het configureren van de instellingen voor de tegel tijd](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Kies de tijds duur die voor deze tegel moet worden weer gegeven. U kunt kiezen uit de afgelopen 30 minuten tot de afgelopen 30 dagen of een aangepast bereik definiëren.

1. Kies de tijd granulariteit om weer te geven. U kunt elk van de stappen van één minuut tot één maand weer geven.

1. Selecteer **Toepassen**.

## <a name="delete-a-tile"></a>Een tegel verwijderen

Voer de volgende stappen uit om een tegel uit een dash board te verwijderen:

* Selecteer het context menu in de rechter bovenhoek van de tegel en selecteer vervolgens **verwijderen in het dash board**. Of

* Selecteer ![pictogram bewerken](./media/azure-portal-dashboards/dashboard-edit-icon.png) **bewerken** om de aanpassings modus in te voeren. Plaats de muis aanwijzer in de rechter bovenhoek van de tegel en selecteer vervolgens het pictogram ![verwijderen](./media/azure-portal-dashboards/dashboard-delete-icon.png) pictogram verwijderen om de tegel te verwijderen uit het dash board.

   ![Scherm afbeelding die laat zien hoe u de tegel van het dash board kunt verwijderen](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Een dash board klonen

Ga als volgt te werk om een bestaand dash board als sjabloon te gebruiken voor een nieuw dash board:

1. Zorg ervoor dat in de dashboard weergave het dash board wordt weer gegeven dat u wilt kopiëren.

1. Selecteer in de paginakop tekst ![kloon pictogram](./media/azure-portal-dashboards/dashboard-clone.png) **kloon**.

1. Een kopie van het dash board, **met de naam kloon van** *uw dashboard naam* , wordt geopend in de bewerkings modus. Gebruik de voor gaande stappen in dit artikel om de naam van het dash board te wijzigen en aan te passen.

## <a name="publish-and-share-a-dashboard"></a>Een dash board publiceren en delen

Wanneer u een dash board maakt, is dit standaard privé, wat betekent dat u het enige kunt zien. Als u Dash boards beschikbaar wilt maken voor anderen, kunt u deze delen met andere gebruikers. Eerst moet u het dash board publiceren als een Azure-resource. Als u een aangepast dash board wilt publiceren en delen, volgt u deze stappen:

1. Selecteer ![pictogram delen](./media/azure-portal-dashboards/dashboard-share-icon.png) **share** uit de paginakop tekst. **Delen en toegangs beheer** wordt geopend.

1. Controleer of de juiste dashboard naam wordt weer gegeven.

1. Selecteer een **naam**voor het abonnement. Gebruikers met toegang tot het abonnement kunnen het gedeelde dash board gebruiken. Toegang tot de resources vertegenwoordigd door de afzonderlijke tegels wordt bepaald door toegangs beheer op basis van rollen in Azure.

1. Schakel het selectie vakje in om dit dash board te publiceren naar de resource groep Dash boards voor het geselecteerde abonnement. Of schakel het selectie vakje uit en kies in plaats daarvan naar een bestaande resource groep te publiceren.

1. Kies een locatie voor de resource in het dash board. We raden u aan het dash board met andere resources te vinden. Als u kiest voor bestaande resource groepen, wordt het dash board automatisch gevonden met die resource groep.

1. Selecteer **Publiceren**.

    ![Scherm afbeelding van het dialoog venster dash board publiceren](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Toegangs beheer instellen op een gedeeld dash board

Nadat het dash board is gepubliceerd, beheert u wie toegang heeft tot het dash board door de volgende stappen uit te voeren:

1. Selecteer in het deel venster **delen en toegangs beheer** de optie **gebruikers beheren**.

    ![Scherm afbeelding van het delen van het dash board en het dialoog venster toegangs beheer](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. De pagina **Access Control** wordt geopend. U kunt het toegangs niveau voor iemand controleren of een nieuwe roltoewijzing toevoegen. Wanneer u hier een roltoewijzing toevoegt, verleent u machtigingen aan het dash board.

> [!NOTE]
> Tegels zijn representatieve weer gaven van resources in uw organisatie. Toegang tot resources wordt beheerd via de toewijzing van toegangs beheer op basis van rollen en machtigingen worden overgenomen van het abonnement tot de resource. Door toegang te verlenen aan een dash board worden niet automatisch machtigingen toegewezen aan de resources die worden weer gegeven op het dash board. Zie [Azure-Dash boards delen met behulp van op rollen gebaseerde Access Control](azure-portal-dashboard-share-access.md)voor meer informatie over machtigingen voor gedeelde Dash boards en op rollen gebaseerd toegangs beheer voor resources.

### <a name="open-a-shared-dashboard"></a>Een gedeeld dash board openen

Voer de volgende stappen uit om een gedeeld dash board te zoeken en te openen:

1. Selecteer de pijl naast de naam van het dash board.

1. Selecteer in de weer gegeven lijst met Dash boards of **Blader door alle Dash boards** als het dash board dat u wilt openen, niet wordt weer gegeven.

    ![Scherm opname van het menu dash board selecteren](./media/azure-portal-dashboards/dashboard-browse.png)

1. In het veld **type** selecteert u **gedeelde Dash boards**.

1. Selecteer een of meer abonnementen. U kunt ook tekst invoeren om Dash boards op naam te filteren.

1. Selecteer een dash board in de lijst met gedeelde Dash boards.

## <a name="delete-a-dashboard"></a>Een dash board verwijderen

Voer de volgende stappen uit om een persoonlijk of gedeeld dash board permanent te verwijderen:

1. Selecteer het dash board dat u wilt verwijderen in de lijst naast de naam van het dash board.

1. Selecteer ![pictogram verwijderen](./media/azure-portal-dashboards/dashboard-delete-icon.png) **verwijderen** uit de paginakop tekst.

1. Voor een persoonlijk dash board selecteert u **OK** in het bevestigings venster om het dash board te verwijderen. Voor een gedeeld dash board selecteert u in het bevestigings dialoogvenster het selectie vakje om te bevestigen dat het gepubliceerde dash board niet meer door anderen kan worden weer gegeven. Selecteer vervolgens **OK**.

    ![Scherm opname van verwijderen bevestigen](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure-Dash boards delen met behulp van op rollen gebaseerde Access Control](azure-portal-dashboard-share-access.md)
* [Programmatisch Azure-Dash boards maken](azure-portal-dashboards-create-programmatically.md)
