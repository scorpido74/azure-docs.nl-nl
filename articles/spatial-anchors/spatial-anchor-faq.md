---
title: Veelgestelde vragen
description: Veelgestelde vragen over de Azure Spatial Anchors-service.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844884"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Veelgestelde vragen over Azure Spatial Anchors

Azure Spatial Anchors is een beheerd cloudservice- en ontwikkelplatform dat mixedreality-ervaringen met ruimtelijk inzicht voor meerdere gebruikers mogelijk maakt op HoloLens-, iOS- en Android-apparaten.

Zie het [overzicht van Azure Spatial Anchors](overview.md) voor meer informatie.

## <a name="azure-spatial-anchors-product-faqs"></a>Veelgestelde vragen over het product Azure Spatial Anchors

**V: Welke apparaten ondersteunen Azure Spatial Anchors?**

**A:** Azure Spatial Anchors stelt ontwikkelaars in staat om apps te bouwen op HoloLens, op iOS-apparaten met ARKit-ondersteuning en op Android-apparaten met ARCore-ondersteuning; voor iOS en Android omvat dit zowel telefoons als tablets.

**V: Moet ik verbonden zijn met de cloud om Azure Spatial Anchors te gebruiken?**

**A:** Azure Spatial Anchors vereist momenteel een netwerkverbinding met het internet. We horen graag uw opmerkingen op onze [feedbacksite](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**V: Wat zijn de connectiviteitsvereisten voor Azure Spatial Anchors?**

**A:** Azure Spatial Anchors werkt met Wi-Fi- en mobiele breedbandverbindingen.

**V: Hoe nauwkeurig kunnen Azure Spatial Anchors ankers lokaliseren?**

**A:** Veel factoren beïnvloeden de nauwkeurigheid van het lokaliseren van ankers - lichtomstandigheden, de objecten in de omgeving en zelfs het oppervlak waarop het anker is geplaatst. Probeer de ankers uit in een omgeving die representatief is voor de omgeving waarin u ze wilt gebruiken om te bepalen of de nauwkeurigheid voldoet aan uw behoeften. Als u merkt dat de nauwkeurigheid in bepaalde omgevingen te wensen overlaat, raadpleegt u [Logboekregistratie en diagnostische gegevens in Azure Spatial Anchors](./concepts/logging-diagnostics.md).

**V: Hoe lang duurt het om ankers te maken en te lokaliseren?**

**A:** De tijd die nodig is om ankers te maken en te lokaliseren is afhankelijk van vele factoren- netwerkverbinding, de verwerking en belasting van het apparaat en de specifieke omgeving. We hebben klanten die toepassingen bouwen voor allerlei verschillende bedrijfstakken, zoals productie, detailhandel en gaming. Dit geeft wel aan dat de service een goede gebruikerservaring mogelijk maakt voor hun scenario's.

## <a name="privacy-faq"></a>Veelgestelde vragen over privacy

**V: Wanneer mijn applicatie ergens een Ruimtelijk Anker plaatst, hebben alle apps er toegang toe?**

**A:** Ankers worden geïsoleerd door Azure-account. Alleen apps die u toegang tot uw account verleent, hebben toegang tot ankers binnen het account.

**V: Welke informatie over een omgeving wordt verzonden en opgeslagen op de service bij het gebruik van Azure Spatial Anchors? Worden foto's van de omgeving verzonden en opgeslagen?**

**A:** Bij het maken of lokaliseren van ankers worden foto's van de omgeving op het apparaat verwerkt tot een afgeleide indeling. Deze afgeleide indeling wordt verzonden naar en opgeslagen in de service.

Om transparantie te bieden, wordt hieronder een afbeelding van een omgeving en de afgeleide sparse puntcloud weergegeven. De puntcloud toont de geometrische weergave van de omgeving die wordt verzonden naar en opgeslagen in de service. Voor elk punt in de sparse puntcloud wordt een hash van de visuele kenmerken van dat punt verzonden en opgeslagen. De hash is afgeleid van pixelgegevens, maar bevat deze gegevens zelf niet.

Azure Spatial Anchors voldoet aan de [voorwaarden van de serviceovereenkomst van Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) en de [privacyverklaring van Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Een omgeving en de](./media/sparse-point-cloud.png)
afgeleide schaarse puntenwolk*Figuur 1: Een omgeving en de afgeleide schaarse puntenwolk*


**V: Is er een manier waarop ik diagnostische informatie naar Microsoft kan verzenden?**

**A:** Ja. Azure Spatial Anchors heeft een diagnostische modus die ontwikkelaars kunnen inschakelen via de Azure Spatial Anchors-API. Dit is bijvoorbeeld handig als u een omgeving treft waarin u geen ankers kunt maken en zoeken zoals verwacht. Mogelijk vragen wij u een diagnostisch rapport in te dienen met informatie die ons kan helpen bij het opsporen van fouten. Zie voor meer informatie [Logboekregistratie en diagnostische gegevens in Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Veelgestelde vragen over beschikbaarheid en prijzen

**V: Geeft u een SLA?**

**A:** Zoals standaard voor Azure-services, richten we ons op een beschikbaarheid van meer dan 99,9%. Let op: Azure Spatial Anchors is momenteel alleen beschikbaar als preview-versie. Hiervoor gelden de [aanvullende voorwaarden voor preview-versies](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**V: Kan ik mijn apps publiceren met Azure Spatial Anchors naar app stores? Kan ik Azure Spatial Anchors gebruiken voor bedrijfskritieke productiescenario's?**

**A:** Azure Spatial Anchors bevindt zich momenteel in Preview en gedurende deze periode nodigen we u uit om apps te ontwikkelen, feedback te [geven](https://feedback.azure.com/forums/919252-azure-spatial-anchors) over het product en uw productie-implementaties te plannen.

Algemene beschikbaarheidsdatums worden binnenkort bekendgemaakt.

**V: Heeft u throttling limieten op zijn plaats?**

**A:** Ja, we hebben beperkingslimieten.  We verwachten niet dat u hier tegenaan zult lopen bij het ontwikkelen en testen van toepassingen. We zijn gereed om te voldoen aan de grootschalige behoeften voor productie-implementaties van onze klanten. [Neem contact met ons op](mailto:azuremrs@microsoft.com) om uw wensen te bespreken. Omdat Spatial Anchors nog in de preview-fase is, hebben we nog geen laag- en prijsstructuur gepubliceerd, maar we verwachten dit binnenkort te doen.

**V: In welke regio's is Azure Spatial Anchors beschikbaar?**

**A:** U vandaag nog een Azure Spatial Anchors-account maken in de azure East US 2-regio. Dit betekent dat er zowel rekenkracht als opslagcapaciteit voor deze service zijn in deze regio. Echter, er zijn geen beperkingen aan waar uw klanten zich bevinden. In de toekomst breiden we de beschikbaarheid van de service uit naar alle primaire Azure-regio's.

**V: Brengt u kosten in rekening voor Azure Spatial Anchors? Zal je ooit opladen?**

**A:** Details over prijzen vindt u tijdens Preview op onze [prijspagina.](https://azure.microsoft.com/pricing/details/spatial-anchors/)

## <a name="technical-faqs"></a>Veelgestelde technische vragen

**V: Hoe werken Azure Spatial Anchors?**

**A:** Azure Spatial Anchors is afhankelijk van mixed reality / augmented reality trackers. Deze trackers nemen de omgeving waar met camera's en tracken het apparaat in 6 vrijheidsgraden (6DoF) terwijl het zich verplaatst door de ruimte.

Met behulp van Azure Spatial Anchors en een gegeven 6DoF-tracker als bouwsteen kunt u bepaalde nuttige plaatsen in uw werkelijke omgeving aanduiden als ankerpunten. U kunt bijvoorbeeld een anker gebruiken om inhoud weer te geven op een specifieke locatie in de echte wereld.

Wanneer u een anker maakt, wordt in de client-SDK informatie over de omgeving rond dat punt vastgelegd en naar de service verzonden. Als een ander apparaat zoekt naar het anker in die dezelfde ruimte, worden soortgelijke gegevens naar de service verzonden. Deze gegevens wordt vergeleken met de eerder opgeslagen omgevingsgegevens. De positie van het anker ten opzichte van het apparaat wordt geretourneerd voor gebruik in de toepassing.

**V: Hoe integreert Azure Spatial Anchors met ARKit en ARCore op iOS en Android?**

**A:** Azure Spatial Anchors maakt gebruik van de native trackingmogelijkheden van ARKit en ARCore. Onze SDK's voor iOS en Android bieden bovendien mogelijkheden voor het persistent maken van ankers in een beheerde cloudservice zodat uw apps die ankers eenvoudig opnieuw kunnen vinden door verbinding met de service te maken.

**V: Hoe integreert Azure Spatial Anchors met HoloLens?**

**A:** Azure Spatial Anchors maakt gebruik van de native trackingmogelijkheden van HoloLens. We bieden een Azure Spatial Anchors-SDK voor het bouwen van apps op HoloLens. De SDK is geïntegreerd met de systeemeigen mogelijkheden van HoloLens en biedt extra mogelijkheden. Hiermee kunnen app-ontwikkelaars ankers persistent maken in een beheerde cloudservice zodat uw apps die ankers opnieuw kunnen vinden door verbinding te maken met de service.

**V: Welke platforms en talen ondersteunt Azure Spatial Anchors?**

**A:** Ontwikkelaars kunnen apps bouwen met Azure Spatial Anchors met behulp van bekende hulpprogramma's en frameworks voor hun apparaat:

- Unity op HoloLens, iOS en Android
- Xamarin op iOS en Android
- Swift of Objective-C op iOS
- Java of de Android-NDK op Android
- C++/WinRT op HoloLens

Klik [hier](index.yml) om aan de slag te gaan met ontwikkeling.

**V: Werkt het met Unreal?**

**A:** Ondersteuning voor Unreal zal in de toekomst worden overwogen.

**V: Welke poorten en protocollen gebruiken Azure Spatial Anchors?**

**A:** Azure Spatial Anchors communiceert via TCP-poort 443 via een versleuteld protocol. Voor verificatie wordt gebruik gemaakt van [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), dat via HTTPS via poort 443 communiceert.
