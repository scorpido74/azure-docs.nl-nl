---
title: Overzicht en toegang voor de conversievan azure Monitor-weergave voor werkmappen
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658843"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Conversieoverzicht en toegang voor de conversie van ontwerpmappen naar werkmappen weergeven
[View designer](view-designer.md) is een functie van Azure Monitor waarmee u aangepaste weergaven maken om gegevens in uw Log Analytics-werkruimte te visualiseren, met grafieken, lijsten en tijdlijnen. Ze worden uitgefaseerd en vervangen door werkmappen, die extra functionaliteit bieden. In dit artikel wordt beschreven hoe u een overzichtsoverzicht en machtigingen maken die nodig zijn om toegang te krijgen tot werkmappen.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Uw werkruimteoverzicht maken vanuit Azure Dashboard
Gebruikers van ontwerpers van het weergeven hebben mogelijk een overzichtstegel om een reeks weergaven weer te geven. Als u een visueel overzicht wilt behouden, zoals het overzicht van de werkruimte voor de weergavevan de ontwerper, biedt werkmappen vastgemaakte stappen, die kunnen worden vastgemaakt aan uw [Azure-portaldashboard.](../../azure-portal/azure-portal-dashboards.md) Net als de overzichtstegels in het overzichtsoverzicht worden vastgemaakte werkmapitems rechtstreeks gekoppeld aan de werkmapweergave.

U profiteren van het hoge niveau van aanpassingsfuncties die zijn voorzien van Azure-dashboards, waarmee u automatisch vernieuwen, verplaatsen, dimensioneren en extra filteren voor uw vastgemaakte items en visualisaties. 

![Dashboard](media/view-designer-conversion-access/dashboard.png)

Maak een nieuw Azure-dashboard of selecteer een bestaand dashboard om werkmappen items vast te maken.

Als u een afzonderlijk item wilt vastmaken, moet u het pictogram van de pin voor uw specifieke stap inschakelen. Selecteer hiervoor de bijbehorende **knop Bewerken** voor uw stap en selecteer vervolgens het tandwielpictogram om Geavanceerde instellingen te **openen.** Controleer de optie Om **het pictogram van de pin in deze stap altijd weer**te geven en in de rechterbovenhoek van uw stap verschijnt een pinpictogram. Met deze pin u specifieke visualisaties vastmaken aan uw dashboard, zoals de overzichtstegels.

![Stap vastmaken](media/view-designer-conversion-access/pin-step.png)


U ook meerdere visualisaties uit de werkmap of de volledige werkmapinhoud aan een dashboard vastmaken. Als u de hele werkmap wilt vastmaken, selecteert u **Bewerken** op de bovenste werkbalk om **de bewerkingsmodus**in te schakelen . Er verschijnt een pinpictogram, zodat u het volledige werkmapitem of alle afzonderlijke stappen en visualisaties in de werkmap vastmaken.

![Alles vastmaken](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Machtigingen voor delen en bekijken 
Werkmappen hebben het voordeel dat het een privé- of gedeeld document is. Standaard worden opgeslagen werkmappen opgeslagen onder **Mijn rapporten,** wat betekent dat alleen de maker deze werkmap kan bekijken.

U uw werkmappen delen door het pictogram **Delen** te selecteren in de bovenste gereedschapsbalk in **de bewerkingsmodus**. U wordt gevraagd uw werkmap te verplaatsen naar **Gedeelde rapporten,** waardoor een koppeling wordt gegenereerd die directe toegang biedt tot de werkmap.

Als een gebruiker een gedeelde werkmap kan bekijken, moet hij of zij toegang hebben tot zowel de abonnements- als de resourcegroep waarin de werkmap is opgeslagen.

![Toegang op basis van een abonnement](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Volgende stappen

- [Veelvoorkomende taken](view-designer-conversion-tasks.md)