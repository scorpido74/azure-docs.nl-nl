---
title: Releases van micro soft-beveiligings code analyse
description: In dit artikel worden aanstaande releases beschreven van de uitbrei ding voor de analyse van beveiligings codes van micro soft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146129"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Release en route ring van micro soft security code Analysis

Het micro soft security code Analysis-Team in samen werking met Developer Support is trots om recente en aanstaande verbeteringen aan te kondigen aan onze MSCA-extensie. Zie de onderstaande route kaart.

![Releases](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Referentie scanner v 2.0: uitgebracht in april 2020

### <a name="innovations--improvements"></a>Vernieuwt & verbeteringen

- **Kern engine**

   - Gemiddelde prestatie-upgrade van 25% met nabije lineaire uitvoerings tijden
   - Op context/bewijs gebaseerd zoeken en classificatie voor een grotere nauw keurigheid
   - Verbeteringen in algemene wachtwoord detecties en overeenkomende logica voor duidelijke tijdelijke aanduidingen (bijvoorbeeld fakePassword)

- **Dekking** : ondersteuning voor 25 + geheime typen, waaronder de volgende aangevraagde eerste.

   - Wachtwoordzin van certificaat van infrastructuur account
   - Client geheim/API-sleutel
   - HTTP-autorisatie-header
   - Amazon S3-client geheim toegangs sleutel
   - Azure Active Directory token voor client toegang
   - Azure function Master/API-sleutel
   - Toegangs sleutel Power BI
   - Wachtwoord patroon van Azure Resource Manager sjabloon

- **Uitvoer**

   - Ondersteuning voor bestands indelingen voor SARIF 2,1 en CSV-uitvoer

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: uitgebracht in april 2020

### <a name="improvements"></a>Verbeteringen

- FUNCTIE: werk bij naar laatste SARIF v2 (versie 2.1.16). Hierdoor kunnen resultaten in de cache worden opgeslagen tijdens het door geven van hashes op de opdracht regel, een aanzienlijke prestatie verbetering bij het recursief analyseren van directory's met meerdere exemplaren van scan doelen.
- Probleem oplossing: type fout oplossing in BA2021. DoNotMarkWritableSectionsAsExecutable uitvoer.
- PRESTATIES: Elimineer het laden van PDB voor alle niet-gemengde modus voor beheerde assembly's, inclusief IL-bibliotheek (voor tijd gecompileerd) binaire bestanden.
- ONJUISTE negatieve correctie: Controleer of een PDB die is geplaatst naast een binair element, echt overeenkomt met het binaire bestand onder analyse
- FUNCTIE: Geef het argument--Local-Symbol-Directors op om extra (lokale, niet-symbool server) PDB-opzoek locaties op te geven
- Fout-positieve correctie: overs laan van PDB gestuurde analyses voor de gegenereerde systeem eigen Boots trap exe van .NET core (dit is geen door de gebruiker te bepalen code).

## <a name="whats-next-in-fy20"></a>Wat is er nieuw in FY20?

- Hulp programma voor Java-beveiligings analyse
- Python-beveiligings analyse programma
- S pluis om TS-linten te vervangen voor type script en Java script

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [hand leiding](security-code-analysis-onboard.md)voor de installatie van micro soft voor meer informatie over het uitvoeren van een onboarding en het installeren van de analyse van beveiligings codes.

Bekijk onze [pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding en de hulpprogram ma's die worden aangeboden.
