---
title: Metingen van echte gebruikers met webpagina's - Azure Traffic Manager
description: In dit artikel leest u hoe u uw webpagina's instelt om echte gebruikersmetingen naar Azure Traffic Manager te verzenden.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938687"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Echte gebruikersmetingen verzenden naar Azure Traffic Manager met behulp van webpagina's

U uw webpagina's configureren om echte gebruikersmetingen naar Traffic Manager te verzenden door een RUM-sleutel (Real User Measurements) te verkrijgen en de gegenereerde code in te sluiten op webpagina's.

## <a name="obtain-a-real-user-measurements-key"></a>Een sleutel voor het bekijken van een echte gebruiker verkrijgen

De metingen die u uitvoert en naar Traffic Manager stuurt vanuit uw clienttoepassing worden door de service geïdentificeerd met behulp van een unieke tekenreeks, de **zogenaamde Rum-toets (Real User Measurements).** U een RUM-sleutel krijgen via de Azure-portal, een REST-API of met de PowerShell- of Azure CLI-code.

Ga als u de RUM-sleutel wilt verkrijgen via azure-portal:
1. Meld u vanuit een browser aan bij Azure Portal. Als u nog geen account hebt, kunt u zich registreren voor een gratis proefversie van één maand.
2. Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik in het profielblad Verkeerbeheer op **Echte gebruikersmetingen** onder **Instellingen**.
4. Klik **op Sleutel genereren** om een nieuwe RUM-sleutel te maken.
 
   ![De sleutel Voor echte gebruikersmetingen genereren](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figuur 1: Real User Measurements Key Generation**

5. Het blad geeft nu de gegenereerde RUM-sleutel weer en een JavaScript-codefragment dat moet worden ingesloten in uw HTML-pagina.
 
    ![Javascript-code voor de sleutel voor metingen van echte gebruikers](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figuur 2: Real User Measurements Key en Measurement JavaScript**
 
6. Klik **op** de knop Kopiëren om de JavaScript-code te kopiëren. 

>[!IMPORTANT]
> Gebruik de functie gegenereerde JavaScript for Real-gebruikersmetingen om goed te functioneren. Wijzigingen in dit script of de scripts die worden gebruikt door Real User Measurements kunnen leiden tot onvoorspelbaar gedrag.

## <a name="embed-the-code-to-an-html-web-page"></a>De code insluiten op een HTML-webpagina

Nadat u de RUM-sleutel hebt verkregen, is de volgende stap om deze gekopieerde JavaScript in te sluiten in een HTML-pagina die uw eindgebruikers bezoeken. Het bewerken van HTML kan op vele manieren worden gedaan en met behulp van verschillende tools en workflows. In dit voorbeeld ziet u hoe u een HTML-pagina bijwerkt om dit script toe te voegen. U deze richtlijnen gebruiken om deze aan te passen aan uw HTML-bronbeheerworkflow.

1.  De HTML-pagina openen in een teksteditor
2.  Plak de JavaScript-code die u in de eerdere stap naar de sectie BODY van de HTML hebt gekopieerd (de gekopieerde code staat op regel 8 & 9, zie figuur 3).
 
    ![Javascript-code insluiten in webpagina voor metingen van echte gebruikers](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figuur 3: Eenvoudige HTML met ingesloten echte gebruikersmetingen JavaScript**

3.  Sla het HTML-bestand op en host het op een webserver die is verbonden met internet. 
4. De volgende keer dat deze pagina wordt weergegeven in een webbrowser, wordt de JavaScript waarnaar wordt verwezen gedownload en voert het script de meet- en rapportagebewerkingen uit.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [echte gebruikersmetingen](traffic-manager-rum-overview.md)
- Meer informatie over [hoe Traffic Manager werkt](traffic-manager-overview.md)
- Meer informatie over de [verkeersrouteringsmethoden](traffic-manager-routing-methods.md) die worden ondersteund door Traffic Manager
- Meer informatie over het [maken van een Traffic Manager-profiel](traffic-manager-create-profile.md)

