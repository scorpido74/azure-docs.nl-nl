---
title: Azure CDN-inhoud beperken per land/regio | Microsoft Documenten
description: Meer informatie over hoe u de toegang per land/regio tot uw Azure CDN-inhoud beperken met behulp van de functie geofiltering.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 75c422d456f2509ce478e2609a6509f78a6eb31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593429"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Azure CDN-inhoud per land/regio beperken

## <a name="overview"></a>Overzicht
Wanneer een gebruiker uw inhoud opvraagt, wordt de inhoud standaard weergegeven, ongeacht de locatie van de gebruiker die het verzoek indient. In sommige gevallen u de toegang tot uw inhoud echter per land/regio beperken. Met de *functie geofiltering* u regels maken voor specifieke paden op uw CDN-eindpunt om inhoud toe te staan of te blokkeren in geselecteerde landen/regio's.

> [!IMPORTANT]
> **Azure CDN Standard van Microsoft-profielen** biedt geen ondersteuning voor geofiltering op basis van paden.
> 

## <a name="standard-profiles"></a>Standaardprofielen
De procedures in deze sectie zijn alleen voor **Azure CDN Standard van Akamai** en **Azure CDN Standard van** Verizon-profielen. 

Voor **Azure CDN Premium van Verizon-profielen** moet u de **portal Beheren** gebruiken om geofiltering te activeren. Zie [Azure CDN Premium van Verizon-profielen voor](#azure-cdn-premium-from-verizon-profiles)meer informatie.

### <a name="define-the-directory-path"></a>Het mappad definiëren
Als u toegang wilt krijgen tot de functie geofiltering, selecteert u het CDN-eindpunt in de portal en selecteert u **Geofiltering** onder INSTELLINGEN in het linkermenu. 

![Geofilterstandaard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Geef **in** het vak PATH het relatieve pad op naar de locatie waartoe gebruikers toegang of toegang worden geweigerd. 

U geofiltering toepassen voor al uw bestanden met een voorwaartse slash (/) of specifieke mappen selecteren door mappaden op te geven (bijvoorbeeld */pictures/*). U geofiltering ook toepassen op één bestand (bijvoorbeeld */pictures/city.png).* Meerdere regels zijn toegestaan; nadat u een regel hebt ingevoerd, wordt een lege rij weergegeven om de volgende regel in te voeren.

Alle volgende directorypadfilters zijn bijvoorbeeld geldig:   
*/*                                 
*/Foto's/*     
*/Foto's/Straatsburg/*     
*/Foto's/Straatsburg/city.png*

### <a name="define-the-type-of-action"></a>Het type actie definiëren

Selecteer **toestaan** of **Blokkeren**in **de** actielijst: 

- **Toestaan:** Alleen gebruikers uit de opgegeven landen/regio's hebben toegang tot activa die zijn aangevraagd vanaf het recursieve pad.

- **Blokkeren:** gebruikers uit de opgegeven landen/regio's krijgen geen toegang tot de assets die zijn aangevraagd op het recursieve pad. Als er geen andere filteropties voor land/regio zijn geconfigureerd voor die locatie, krijgen alle andere gebruikers toegang.

Een geofilterregel voor het blokkeren van het pad */Foto's/Straatsburg/* filtert bijvoorbeeld de volgende bestanden:     
*\//http:\<eindpunt>.azureedge.net/Photos/Strasbourg/1000.jpg*
*\//\<http: eindpunt>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>De landen/regio's definiëren
Selecteer in de lijst **LANDCODES** de landen/regio's die u wilt blokkeren of toestaan voor het pad. 

Nadat u klaar bent met het selecteren van de landen/regio's, selecteert u **Opslaan** om de nieuwe regel voor geofiltering te activeren. 

![Regels voor geofiltering](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Resources opschonen
Als u een regel wilt verwijderen, selecteert u deze in de lijst op de **geofilterpagina** en kiest u **Verwijderen**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium van Verizon-profielen
Voor **Azure CDN Premium van Verizon-profielen** is de gebruikersinterface voor het maken van een geofilterregel anders:

1. Selecteer **Beheren**in het bovenste menu in uw Azure CDN-profiel .

2. Selecteer http Groot **in**de Verizon-portal en selecteer **Vervolgens Landfilteren**.

    ![Geofilterstandaard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selecteer **Landfilter toevoegen**.

    De **pagina Stap één:** wordt weergegeven.

4. Voer het mappad in, selecteer **Blokkeren** of **Toevoegen**en selecteer **Volgende**.

    De **pagina Stap twee:** wordt weergegeven. 

5. Selecteer een of meer landen/regio's in de lijst en selecteer **Voltooien** om de regel te activeren. 
    
    De nieuwe regel wordt weergegeven in de tabel op de pagina **Landfiltering.**

    ![Regels voor geofiltering](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Resources opschonen
Selecteer in de tabel regels voor filteren op land/regio het pictogram verwijderen naast een regel om deze te verwijderen of het pictogram bewerken om het te wijzigen.

## <a name="considerations"></a>Overwegingen
* Wijzigingen in uw geofilterconfiguratie worden niet onmiddellijk van kracht:
   * Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
   * Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
   * Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 
 
* Deze functie ondersteunt geen jokertekens (bijvoorbeeld *).

* De geofilterconfiguratie die is gekoppeld aan het relatieve pad wordt recursief toegepast op dat pad.

* Er kan slechts één regel worden toegepast op hetzelfde relatieve pad. Dat wil zeggen dat u geen meerdere land-/regiofilters maken die naar hetzelfde relatieve pad wijzen. Omdat land-/regiofilters echter recursief zijn, kan een map meerdere land-/regiofilters bevatten. Met andere woorden, aan een submap van een eerder geconfigureerde map kan een ander land/regiofilter worden toegewezen.

* De functie geofiltering gebruikt landcodes om de landen/regio's te definiëren waaruit een aanvraag is toegestaan of geblokkeerd voor een beveiligde map. Hoewel Akamai en Verizon profielen ondersteunen de meeste van hetzelfde land codes, zijn er een paar verschillen. Zie [Azure CDN-landcodes](/previous-versions/azure/mt761717(v=azure.100))voor meer informatie . 

