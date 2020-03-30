---
title: Dasboards maken en delen in Azure Portal
description: In dit artikel wordt beschreven hoe u dashboards in de Azure-portal maakt, aanpast, publiceert en deelt.
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
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4c01321662b302103cdedfb5b78dadf89860fb8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132078"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Dasboards maken en delen in Azure Portal

Dashboards zijn een gerichte en georganiseerde weergave van uw cloudresources in de Azure-portal. Gebruik dashboards als werkruimte waar u snel taken kunt starten voor dagelijkse bewerkingen en resources kunt bewaken. Bouw bijvoorbeeld aangepaste dashboards op basis van projecten, taken of gebruikersrollen.

De Azure-portal biedt een standaarddashboard als uitgangspunt. U het standaarddashboard bewerken. Maak en pas extra dashboards aan en publiceer en deel dashboards om ze beschikbaar te maken voor andere gebruikers. In dit artikel wordt beschreven hoe u een nieuw dashboard maakt, de interface aanpast en dashboards publiceert en deelt.

## <a name="create-a-new-dashboard"></a>Een nieuw dashboard maken

In dit voorbeeld maken we een nieuw, privédashboard en wijzen we een naam toe. Volg deze stappen om aan de slag te gaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Dashboard**in het menu Azure-portal . Uw standaardweergave is mogelijk al ingesteld op het dashboard.

    ![Het dashboard openen](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecteer **Nieuw dashboard**.

    ![Schermafbeelding van nieuw dashboard](./media/azure-portal-dashboards/create-new-dashboard.png)

    Met deze actie wordt de **tegelgalerie**geopend, waaruit u tegels selecteert en een leeg raster waar u de tegels instelt.

    ![Schermafbeelding van tegelgalerie en leeg raster](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecteer de tekst **Mijn dashboard** in het dashboardlabel en voer een naam in waarmee u eenvoudig het aangepaste dashboard identificeren.

1. Selecteer **Gereed aanpassen** in de paginakoptekst om de bewerkingsmodus af te sluiten.

De dashboardweergave toont nu uw nieuwe dashboard. Selecteer de pijl naast de dashboardnaam om dashboards te zien die voor u beschikbaar zijn. De lijst bevat mogelijk dashboards die andere gebruikers hebben gemaakt en gedeeld.

## <a name="edit-a-dashboard"></a>Een dashboard bewerken

Laten we nu het dashboard bewerken om tegels toe te voegen, het formaat te wijzigen en te rangschikken die uw Azure-bronnen vertegenwoordigen.

### <a name="add-tiles-from-the-dashboard"></a>Tegels toevoegen vanuit het dashboard

Voer de volgende stappen uit om tegels aan een dashboard toe te voegen:

1. Selecteer ![pictogram](./media/azure-portal-dashboards/dashboard-edit-icon.png) bewerken **Bewerken** vanuit de paginakoptekst.

    ![Schermafbeelding van bewerken van dashboardmarkering](./media/azure-portal-dashboards/dashboard-edit.png)

1. Blader door de **tegelgalerie** of gebruik het zoekveld om de gewenste tegel te vinden.

1. Selecteer **Toevoegen** om de tegel toe te voegen aan het dashboard met een standaardgrootte en locatie. Of sleep de tegel naar het raster en plaats deze op de gewenste plaats.

> [!TIP]
> Als u met meer dan één organisatie werkt, voegt u de **identiteitstegel Organisatie** toe aan uw dashboard om duidelijk te laten zien tot welke organisatie de resources behoren.

### <a name="add-tiles-from-a-resource-page"></a>Tegels toevoegen vanaf een resourcepagina

Er is een alternatieve manier om tegels toe te voegen aan uw dashboard. Veel resourcepagina's bevatten een punaisepictogram in de opdrachtbalk. Als u het pictogram selecteert, wordt een tegel die de bronpagina vertegenwoordigt, vastgemaakt aan het dashboard dat momenteel actief is. 

![Schermafbeelding van de opdrachtbalk van de pagina met het pictogram Pin](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Het formaat van tegels wijzigen of opnieuw rangschikken

Voer de volgende stappen uit om de grootte van een tegel te wijzigen of de tegels op een dashboard te herschikken:

1. Selecteer ![pictogram](./media/azure-portal-dashboards/dashboard-edit-icon.png) bewerken **Bewerken** vanuit de paginakoptekst.

1. Selecteer het contextmenu in de rechterbovenhoek van een tegel. Kies vervolgens een tegelgrootte. Tegels die elke grootte ondersteunen, bevatten ook een 'handgreep' in de rechterbenedenhoek waarmee u de tegel naar de gewenste grootte slepen.

    ![Schermafbeelding van dashboard met menu tegelgrootte geopend](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecteer een tegel en sleep deze naar een nieuwe locatie op het raster om uw dashboard te rangschikken.

### <a name="additional-tile-configuration"></a>Aanvullende tegelconfiguratie

Voor sommige tegels is mogelijk meer configuratie vereist om de gewenste informatie weer te geven. De tegel **Grafiek met statistieken** moet bijvoorbeeld worden ingesteld om een statistiek van Azure Monitor weer te **geven.** U ook tegelgegevens aanpassen om de standaardtijdinstellingen van het dashboard te overschrijven.

Elke tegel die moet worden ingesteld, geeft een **tegelbanner configureren** weer totdat u de tegel aanpast. Ga als u de tegel aanpassen:

1. Selecteer **Gereed aanpassen** in de paginakoptekst om de bewerkingsmodus af te sluiten.

1. Selecteer de banner en voer de vereiste installatie uit.

    ![Schermafbeelding van een tegel waarvoor configuratie vereist is](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Met een markeringstegel u aangepaste, statische inhoud weergeven op uw dashboard. Dit kunnen basisinstructies, een afbeelding, een reeks hyperlinks of zelfs contactgegevens zijn. Zie [Een afwaarderingstegel gebruiken op Azure-dashboards gebruiken om aangepaste inhoud weer te geven voor](azure-portal-markdown-tile.md)meer informatie over het gebruik van een afwaarderingstegel.

### <a name="customize-tile-data"></a>Tegelgegevens aanpassen

Gegevens op het dashboard tonen automatisch activiteit van de afgelopen 24 uur. Voer de volgende stappen uit om een andere tijdspanne voor alleen deze tegel weer te geven:

1. Selecteer **Tegelgegevens aanpassen** in het ![contextmenu of het filterpictogramfilter](./media/azure-portal-dashboards/dashboard-filter.png) in de linkerbovenhoek van de tegel.

    ![Schermafbeelding van het contextmenu van tegels](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Schakel het selectievakje in om **de instellingen voor de dashboardtijd op tegelniveau**te overschrijven .

    ![Schermafbeelding van het dialoogvenster om tegeltijd-instellingen te configureren](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Kies de tijdspanne die u voor deze tegel wilt weergeven. U kiezen uit de afgelopen 30 minuten tot de afgelopen 30 dagen of een aangepast bereik definiëren.

1. Kies de tijdsgranulariteit die u wilt weergeven. U stappen van één minuut tot één maand weergeven.

1. Selecteer **Toepassen**.

## <a name="delete-a-tile"></a>Een tegel verwijderen

Voer de volgende stappen uit om een tegel uit een dashboard te verwijderen:

* Selecteer het contextmenu in de rechterbovenhoek van de tegel en selecteer **Verwijderen uit dashboard**. Of

* Selecteer ![pictogram](./media/azure-portal-dashboards/dashboard-edit-icon.png) bewerken **Bewerken** om de aanpassingsmodus in te voeren. Plaats de plaats in de rechterbovenhoek van ![de](./media/azure-portal-dashboards/dashboard-delete-icon.png) tegel en selecteer vervolgens het pictogram verwijderen verwijderen om de tegel uit het dashboard te verwijderen.

   ![Schermafbeelding van het verwijderen van tegels uit het dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Een dashboard klonen

Voer de volgende stappen uit om een bestaand dashboard als sjabloon voor een nieuw dashboard te gebruiken:

1. Zorg ervoor dat in de dashboardweergave het dashboard wordt weergegeven dat u wilt kopiëren.

1. Selecteer ![kloonpictogram](./media/azure-portal-dashboards/dashboard-clone.png) **Kloon**in de paginakoptekst .

1. Een kopie van het dashboard, met de naam **Kloon van** *uw dashboard,* wordt geopend in de bewerkingsmodus. Gebruik de voorgaande stappen in dit artikel om de naam van het dashboard te wijzigen en aan te passen.

## <a name="publish-and-share-a-dashboard"></a>Een dashboard publiceren en delen

Wanneer u een dashboard maakt, is het standaard privé, wat betekent dat u de enige bent die het kan zien. Als u dashboards beschikbaar wilt maken voor anderen, u ze publiceren en delen. Zie [Azure-dashboards delen met op rollen gebaseerd toegangsbeheer](azure-portal-dashboard-share-access.md)voor meer informatie.

### <a name="open-a-shared-dashboard"></a>Een gedeeld dashboard openen

Voer de volgende stappen uit om een gedeeld dashboard te zoeken en te openen:

1. Selecteer de pijl naast de naam van het dashboard.

1. Kies in de weergegeven lijst met dashboards. Als het dashboard dat u wilt openen niet wordt weergegeven:

    1. selecteer **Door alle dashboards bladeren**.

        ![Schermafbeelding van het menu dashboardselectie](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Selecteer in het veld **Tekst** de optie **Gedeelde dashboards**.

        ![Schermafbeelding van het selectiemenu van alle dashboards](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selecteer een of meer abonnementen. U ook tekst invoeren om dashboards op naam te filteren.

    1. Selecteer een dashboard in de lijst met gedeelde dashboards.

## <a name="delete-a-dashboard"></a>Een dashboard verwijderen

Voer de volgende stappen uit om een privé- of gedeeld dashboard permanent te verwijderen:

1. Selecteer het dashboard dat u wilt verwijderen uit de lijst naast de dashboardnaam.

1. Selecteer ![pictogram](./media/azure-portal-dashboards/dashboard-delete-icon.png) **verwijderen uit** de paginakoptekst.

1. Selecteer **OK** in het bevestigingsdialoogvenster om het dashboard te verwijderen voor een privédashboard. Schakel voor een gedeeld dashboard in het bevestigingsdialoogvenster het selectievakje in om te controleren of het gepubliceerde dashboard niet meer zichtbaar is voor anderen. Selecteer vervolgens **OK**.

    ![Schermafbeelding van verwijderingsbevestiging](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer](azure-portal-dashboard-share-access.md)
* [Programmatisch Azure-dashboards maken](azure-portal-dashboards-create-programmatically.md)
