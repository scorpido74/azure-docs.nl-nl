---
title: Conversie opties Azure Monitor Designer weer geven voor werkmappen
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171551"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Conversie opties Azure Monitor Designer weer geven voor werkmappen
[View Designer](view-designer.md) is een functie van Azure monitor waarmee u aangepaste weer gaven kunt maken waarmee u gegevens in uw werk ruimte log Analytics kunt visualiseren, met grafieken, lijsten en tijd lijnen. Ze worden gefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel worden de basis concepten vergeleken tussen de twee en de opties voor het converteren van weer gaven naar werkmappen.

## <a name="basic-workbook-designs"></a>Eenvoudige werkmap ontwerpen

De weer gave Designer heeft een vaste statische stijl van de weer gave, terwijl werkmappen de vrijheid kunnen bieden om de gegevens weer te geven en te wijzigen. In de onderstaande afbeeldingen ziet u twee voor beelden van de manier waarop u werkmappen kunt ordenen tijdens het converteren van weer gaven.

[Verticale werkmap](view-designer-conversion-examples.md#vertical)
![verticale](media/view-designer-conversion-options/view-designer-vertical.png)

[
![](view-designer-conversion-examples.md#tabbed) gegevens type distributie tabblad](media/view-designer-conversion-options/distribution-tab.png)
![gegevens typen op het tabblad tijd](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Tegel conversie
Weer gave Designer gebruikt de overzichts tegel functie om de algehele status weer te geven en samen te vatten. Deze worden weer gegeven in zeven tegels, variërend van getallen tot grafieken. In werkmappen kunnen gebruikers gelijksoortige visualisaties maken en deze vastzetten zodat ze lijken op de oorspronkelijke stijl van overzichts tegels. 

![Gallery](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Dashboard conversie weer geven
Designer-tegels weer geven bestaan doorgaans uit twee secties: een visualisatie en een lijst die overeenkomt met de gegevens van de visualisatie, bijvoorbeeld de ring **& lijst** tegel.

![ring](media/view-designer-conversion-options/donut-example.png)

Met werkmappen kan de gebruiker ervoor kiezen om een of beide secties van de weer gave op te vragen. Query's formuleren in werkmappen is een eenvoudig proces dat uit twee stappen bestaat. Eerst worden de gegevens gegenereerd op basis van de query en vervolgens worden de gegevens weer gegeven als een visualisatie.  Hier volgt een voor beeld van hoe deze weer gave opnieuw wordt gemaakt in werkmappen:

![Converteren](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Volgende stappen
- [Toegang tot werkmappen & machtigingen](view-designer-conversion-access.md)