---
title: Afschaffing van schijfstatistieken in de Azure-portal | Microsoft Documenten
description: Ontdek welke schijfstatistieken zijn afgeschaft en hoe u uw metrische waarschuwingen bijwerken om nieuwe statistieken te gebruiken.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299801"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Afschaffing van schijfstatistieken in de Azure-portal

Afgeschafte schijfgerelateerde statistieken worden binnenkort verwijderd uit de Azure-portal. Een nieuwe versie van elke afgeschafte statistiek is beschikbaar voor u om te gebruiken. In dit artikel ziet u welke statistieken nieuw zijn en hoe u uw metrische waarschuwingen bijwerken om ze te gebruiken.

## <a name="list-of-new-metrics"></a>Lijst met nieuwe statistieken

In deze tabel wordt elke afgeschafte statistiek toegewezen aan de bijbehorende nieuwe statistiek. 

|Afgeschafte statistiek|Nieuwe (vervangende) statistiek|
|----|----|
|Gegevensschijf QD (afgeschaft)|Gegevensschijfwachtrijdiepte (voorbeeld)|
|Bytes/Sec gegevensschijf lezen (afgeschaft)|Bytes/Seconde voor gegevensschijf lezen (voorbeeld)|
|Gegevensschijfleesbewerkingen/Sec (afgeschaft)|Gegevensschijfleesbewerkingen/Sec (voorbeeld)|
|Bytes/Sec voor gegevensschijfschrijven (afgeschaft)|Bytes/sec voor gegevensschijf schrijven (voorbeeld)|
|Gegevensschijfschrijfbewerkingen/Sec (afgeschaft)|Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)|
|OS QD (afgeschaft)|Knoprijdiepte van het besturingssysteem (voorbeeld)|
|GELEZEN Bytes/Sec (afgeschaft)|Gelezen bytes/seconde voor het besturingssysteem (voorbeeld)|
|BE Leesbewerkingen/Sec (afgeschaft)|Be-leesbewerkingen/-seconde (voorbeeld)|
|Bytes/Sec voor schrijven in het besturingssysteem (afgeschaft)|Geschreven bytes/seconde voor het schrijven van besturingssysteem (voorbeeld)|
|OS Schrijfbewerkingen/Sec (afgeschaft)|Be-schrijfbewerkingen/sec (voorbeeld)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Statistieken migreren in uw metrische waarschuwingen

Werk uw metrische waarschuwingen bij om nieuwe statistieken te gebruiken.

1. Zoek in de Azure-portal naar **Waarschuwingen**. Kies vervolgens in de sectie **Services** de optie **Waarschuwingen**.

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Kies op de pagina **Waarschuwingen** de knop **Waarschuwingsregels beheren.** 

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Schakel in de vervolgkeuzelijst **Resourcegroep** het selectievakje **Virtuele machines** in en schakel in de vervolgkeuzelijst **Signaaltype** het selectievakje **Statistieken** in. 

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Identificeer in de lijst met statistieken voorwaarden die betrekking hebben op schijven. Klik op de naam van de regel. 

   De naam wordt weergegeven als hyperlink in de kolom **Naam** van de tabel.

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Klik in het gedeelte **Voorwaarden** van de pagina **Regelsbeheer** op de voorwaarde van de waarschuwing. 

   De voorwaarde wordt weergegeven als een hyperlink.  

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   De pagina **Signaallogica configureren** wordt weergegeven en de instellingen van de voorwaarde worden weergegeven in de sectie **Waarschuwingslogica** van die pagina.

6. Maak een record van deze instellingen als ze verdwijnen wanneer u de afgeschafte metrische verwijderen.

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Overweeg deze instellingen vast te leggen in een schermafbeelding of in een tekstbestand. 

7. Klik op de koppeling **Terug naar signaalselectie.**

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Kies op de pagina **Signaallogica configureren** de juiste vervangende statistiek (nieuwe statistiek). Gebruik de [tabel](#update-metrics) die eerder in dit artikel wordt weergegeven om de naam van de nieuwe statistiek te achterhalen.

   > [!TIP] 
   > Begin met typen in de zoekbalk om de lijst met metrische namen te verkleinen. 

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Kies de knop **Gereed.** 

   > [!div class="mx-imgBorder"]
   > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Bega uw wijzigingen door de knop **Opslaan te** kiezen. 

    > [!div class="mx-imgBorder"]
    > ![Afbeeldingsbeschrijving](./media/portal-disk-metrics-deprecation/save-new-metric.png)






