---
title: Veelgestelde vragen
description: Veelgestelde vragen over de Azure spatiale ankers-service.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 046e438304991c4af7ea4a208e51b7a695a5bf23
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83004474"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Veelgestelde vragen over Azure Spatial Anchors

Azure Spatial Anchors is een beheerd cloudservice- en ontwikkelplatform dat mixedreality-ervaringen met ruimtelijk inzicht voor meerdere gebruikers mogelijk maakt op HoloLens-, iOS- en Android-apparaten.

Zie het [overzicht van Azure Spatial Anchors](overview.md) voor meer informatie.

## <a name="azure-spatial-anchors-product-faqs"></a>Veelgestelde vragen over het product Azure Spatial Anchors

**V: welke apparaten worden door Azure ruimtelijk-ankers ondersteund?**

**A:** Met Azure spatiale ankers kunnen ontwikkel aars apps bouwen op HoloLens, op iOS-apparaten met ARKit-ondersteuning en op Android-apparaten met ARCore-ondersteuning; voor iOS en Android is dit inclusief telefoons en tablets.

**V: moet ik verbinding maken met de cloud om gebruik te maken van Azure spatiale ankers?**

**A:** Voor Azure spatiale ankers is momenteel een netwerk verbinding met Internet vereist. We horen graag uw opmerkingen op onze [feedbacksite](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**V: wat zijn de connectiviteits vereisten voor Azure spatiale ankers?**

**A:** Ruimtelijke Azure-ankers werken met Wi-Fi-en Mobile Broadband-verbindingen.

**V: hoe nauw keurig kunnen Azure spatiale ankers ankers vinden?**

**A:** Veel factoren zijn van invloed op de nauw keurigheid van het vinden van ankers, de belichtings omstandigheden, de objecten in de omgeving en zelfs het Opper vlak waarop het anker is geplaatst. Probeer de ankers uit in een omgeving die representatief is voor de omgeving waarin u ze wilt gebruiken om te bepalen of de nauwkeurigheid voldoet aan uw behoeften. Als u merkt dat de nauwkeurigheid in bepaalde omgevingen te wensen overlaat, raadpleegt u [Logboekregistratie en diagnostische gegevens in Azure Spatial Anchors](./concepts/logging-diagnostics.md).

**V: hoe lang duurt het om ankers te maken en te vinden?**

**A:** De tijd die nodig is voor het maken en vinden van ankers is afhankelijk van veel factoren: netwerk verbinding, het verwerken en laden van het apparaat en de specifieke omgeving. We hebben klanten die toepassingen bouwen voor allerlei verschillende bedrijfstakken, zoals productie, detailhandel en gaming. Dit geeft wel aan dat de service een goede gebruikerservaring mogelijk maakt voor hun scenario's.

## <a name="privacy-faq"></a>Veelgestelde vragen over privacy

**V: wanneer mijn toepassing ergens anders een ruimtelijke anker plaatst, hebben alle apps toegang tot de app?**

**A:** Ankers worden geïsoleerd door Azure-account. Alleen apps die u toegang tot uw account verleent, hebben toegang tot ankers binnen het account.

**V: hoe worden gegevens in azure spatiale ankers opgeslagen?**

**A:** Alle gegevens worden opgeslagen en versleuteld met een door micro soft beheerde gegevens versleutelings sleutel.

**V: welke informatie over een omgeving wordt verzonden en opgeslagen op de service bij gebruik van Azure spatiale ankers? Worden afbeeldingen van de omgeving verzonden en opgeslagen?**

**A**: wanneer u ankers maakt of zoekt, worden afbeeldingen van de omgeving op het apparaat verwerkt in een afgeleide indeling. Deze afgeleide indeling wordt verzonden naar en opgeslagen in de service.

Om transparantie te bieden, wordt hieronder een afbeelding van een omgeving en de afgeleide sparse puntcloud weergegeven. De puntcloud toont de geometrische weergave van de omgeving die wordt verzonden naar en opgeslagen in de service. Voor elk punt in de sparse puntcloud wordt een hash van de visuele kenmerken van dat punt verzonden en opgeslagen. De hash is afgeleid van pixelgegevens, maar bevat deze gegevens zelf niet.

Azure Spatial Anchors voldoet aan de [voorwaarden van de serviceovereenkomst van Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) en de [privacyverklaring van Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Een omgeving en het afgeleide sparse-punt Cloud](./media/sparse-point-cloud.png)
*afbeelding 1: een omgeving en de afgeleide sparse-punt Cloud*

**V: is er een manier om diagnostische gegevens naar micro soft te verzenden?**

**A**: Ja. Azure Spatial Anchors heeft een diagnostische modus die ontwikkelaars kunnen inschakelen via de Azure Spatial Anchors-API. Dit is bijvoorbeeld handig als u een omgeving treft waarin u geen ankers kunt maken en zoeken zoals verwacht. Mogelijk vragen wij u een diagnostisch rapport in te dienen met informatie die ons kan helpen bij het opsporen van fouten. Zie voor meer informatie [Logboekregistratie en diagnostische gegevens in Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Veelgestelde vragen over beschikbaarheid en prijzen

**V: geeft u een SLA op?**

**A:** Net als Standard voor Azure-Services, hebben we een Beschik baarheid bereikt die groter is dan 99,9%. Let op: Azure Spatial Anchors is momenteel alleen beschikbaar als preview-versie. Hiervoor gelden de [aanvullende voorwaarden voor preview-versies](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**V: kan ik mijn apps met behulp van Azure spatiale ankers publiceren naar App Stores? Kan ik ruimtelijke ankers van Azure gebruiken voor kritieke productie scenario's?**

**A:** De Azure spatiale ankers zijn momenteel beschikbaar als preview-versie en we nodigen u uit om apps te ontwikkelen, [feedback te geven](https://feedback.azure.com/forums/919252-azure-spatial-anchors) over het product en uw productie-implementaties te plannen.

Algemene beschikbaarheidsdatums worden binnenkort bekendgemaakt.

**V: zijn er beperkingen voor de beperking van toepassing?**

**A**: Ja, er zijn beperkings limieten.  We verwachten niet dat u hier tegenaan zult lopen bij het ontwikkelen en testen van toepassingen. We zijn gereed om te voldoen aan de grootschalige behoeften voor productie-implementaties van onze klanten. [Neem contact met ons op](mailto:azuremrs@microsoft.com) om uw wensen te bespreken. Omdat Spatial Anchors nog in de preview-fase is, hebben we nog geen laag- en prijsstructuur gepubliceerd, maar we verwachten dit binnenkort te doen.

**V: in welke regio's zijn ruimtelijke Azure-ankers beschikbaar?**

**A:** U kunt vandaag nog een Azure spatiale-anker account maken in de regio Azure-Oost 2. Dit betekent dat er zowel rekenkracht als opslagcapaciteit voor deze service zijn in deze regio. Echter, er zijn geen beperkingen aan waar uw klanten zich bevinden. In de toekomst breiden we de beschikbaarheid van de service uit naar alle primaire Azure-regio's.

**V: worden er kosten in rekening gebracht voor Azure spatiale ankers? Wordt u ooit in rekening gebracht?**

**A:** Meer informatie over prijzen vindt u in de preview-versie op onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Veelgestelde technische vragen

**V: Hoe werkt Azure spatiale ankers?**

**A:** Ruimtelijke Azure-ankers zijn afhankelijk van de gemengde realiteit/uitbrei ding van de realiteit. Deze trackers nemen de omgeving waar met camera's en tracken het apparaat in 6 vrijheidsgraden (6DoF) terwijl het zich verplaatst door de ruimte.

Met behulp van Azure Spatial Anchors en een gegeven 6DoF-tracker als bouwsteen kunt u bepaalde nuttige plaatsen in uw werkelijke omgeving aanduiden als ankerpunten. U kunt bijvoorbeeld een anker gebruiken om inhoud weer te geven op een specifieke locatie in de echte wereld.

Wanneer u een anker maakt, wordt in de client-SDK informatie over de omgeving rond dat punt vastgelegd en naar de service verzonden. Als een ander apparaat zoekt naar het anker in die dezelfde ruimte, worden soortgelijke gegevens naar de service verzonden. Deze gegevens wordt vergeleken met de eerder opgeslagen omgevingsgegevens. De positie van het anker ten opzichte van het apparaat wordt geretourneerd voor gebruik in de toepassing.

**V: hoe worden ruimtelijke Azure-ankers geïntegreerd met ARKit en ARCore op iOS en Android?**

**A:** Ruimtelijke ankers van Azure maken gebruik van de systeem eigen tracerings mogelijkheden van ARKit en ARCore. Onze SDK's voor iOS en Android bieden bovendien mogelijkheden voor het persistent maken van ankers in een beheerde cloudservice zodat uw apps die ankers eenvoudig opnieuw kunnen vinden door verbinding met de service te maken.

**V: hoe worden ruimtelijke Azure-ankers geïntegreerd met HoloLens?**

**A:** Ruimtelijke ankers van Azure maken gebruik van de systeem eigen tracerings mogelijkheden van HoloLens. We bieden een Azure Spatial Anchors-SDK voor het bouwen van apps op HoloLens. De SDK is geïntegreerd met de systeemeigen mogelijkheden van HoloLens en biedt extra mogelijkheden. Hiermee kunnen app-ontwikkelaars ankers persistent maken in een beheerde cloudservice zodat uw apps die ankers opnieuw kunnen vinden door verbinding te maken met de service.

**V: welke platforms en talen worden door Azure ruimtelijk-ankers ondersteund?**

**A:** Ontwikkel aars kunnen apps bouwen met behulp van vertrouwde hulp middelen en frameworks voor hun apparaat:

- Unity op HoloLens, iOS en Android
- Xamarin op iOS en Android
- Swift of Objective-C op iOS
- Java of de Android-NDK op Android
- C++/WinRT op HoloLens

Klik [hier](index.yml) om aan de slag te gaan met ontwikkeling.

**V: werkt het met Unreal?**

**A:** Ondersteuning voor Unreal wordt in de toekomst in overweging genomen.

**V: welke poorten en protocollen worden door Azure ruimtelijk-ankers gebruikt?**

**A:** Ruimtelijke Azure-ankers communiceren via TCP-poort 443 met behulp van een versleuteld protocol. Voor verificatie gebruikt het [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), dat communiceert met behulp van HTTPS via poort 443.
