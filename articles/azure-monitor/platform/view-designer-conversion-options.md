---
title: Conversieopties voor azure-monitorweergave voor werkmappen
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658707"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Conversieopties voor azure-monitorweergave voor werkmappen
[View designer](view-designer.md) is een functie van Azure Monitor waarmee u aangepaste weergaven maken om gegevens in uw Log Analytics-werkruimte te visualiseren, met grafieken, lijsten en tijdlijnen. Ze worden uitgefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel worden fundamentele concepten tussen de twee en de opties voor het converteren van weergaven naar werkmappen vergeleken.

## <a name="basic-workbook-designs"></a>Basiswerkmapontwerpen

De ontwerper van de weergave heeft een vaste statische stijl van representatie, terwijl werkmappen de vrijheid bieden om de weergave van de gegevens op te nemen en aan te passen. De onderstaande afbeeldingen tonen twee voorbeelden van hoe u werkmappen rangschikken bij het converteren van weergaven.

[Verticale werkmap](view-designer-conversion-examples.md#vertical)
![Verticaal](media/view-designer-conversion-options/view-designer-vertical.png)

[Tabmap werkmap](view-designer-conversion-examples.md#tabbed)
![Gegevenstype](media/view-designer-conversion-options/distribution-tab.png)
![distributie tab Gegevenstypen na verloop van tijd tabblad](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Tegelconversie
De ontwerper van de weergave gebruikt de functie overzichtstegel om de algemene status weer te geven en samen te vatten. Deze zijn vertegenwoordigd in zeven tegels, variërend van getallen tot grafieken. In werkmappen kunnen gebruikers vergelijkbare visualisaties maken en vastmaken aan de oorspronkelijke stijl van overzichtstegels. 

![Galerie](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Dashboardconversie weergeven
Ontwerptegels weergeven bestaan meestal uit twee secties, een visualisatie en een lijst die overeenkomt met de gegevens uit de visualisatie, bijvoorbeeld de tegel **Donut & Lijst.**

![Ringdiagram](media/view-designer-conversion-options/donut-example.png)

Met werkmappen kunnen we de gebruiker toestaan om een of beide secties van de weergave op te vragen. Het formuleren van query's in werkmappen is een eenvoudig proces in twee stappen. Eerst worden de gegevens gegenereerd uit de query en ten tweede worden de gegevens weergegeven als een visualisatie.  Een voorbeeld van hoe deze weergave in werkmappen opnieuw wordt gemaakt, is als volgt:

![Converteren](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Volgende stappen
- [Toegang tot werkmappen & machtigingen](view-designer-conversion-access.md)