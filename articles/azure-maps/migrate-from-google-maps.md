---
title: Migreren vanuit Google Maps | Microsoft Docs
description: Een zelf studie over het migreren van Google-kaarten naar Microsoft Azure Maps. U wordt begeleid bij het overschakelen naar Azure Maps-Api's en Sdk's.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770302"
---
# <a name="migrate-from-google-maps"></a>Migreren vanuit Google Maps

In deze zelf studie vindt u informatie over het migreren van web-, mobiele en server toepassingen van Google Maps naar het Microsoft Azure Maps-platform. Deze zelf studie bevat vergelijkende code voorbeelden, migratie suggesties en aanbevolen procedures voor het migreren naar Azure Maps.

## <a name="azure-maps-platform-overview"></a>Overzicht van Azure Maps platform

Azure Maps biedt ontwikkel aars van alle industrie krachtige georuimtelijke mogelijkheden, verpakt met de regel matig bijgewerkte kaart gegevens om geografische context te bieden voor web-en mobiele toepassingen. Azure Maps heeft een Azure One API-compatibele set REST-Api's voor kaarten, zoek functies, route ring, verkeer, tijd zones, geolocatie, geoomheining, kaart gegevens, weer, mobiliteit en ruimtelijke bewerkingen, samen met zowel web-als Android-Sdk's, om ontwikkel gemak te maken, flexibeler en draagbaar op meerdere platforms.

## <a name="high-level-platform-comparison"></a>Vergelijking van platform op hoog niveau

De volgende tabel bevat een lijst op hoog niveau van de functies van Google Maps en de relatieve ondersteuning voor die functies in Azure Maps. Deze lijst bevat geen aanvullende Azure Maps functies, zoals toegankelijkheid, Api's voor geoomheining, van isochronen, ruimtelijke bewerkingen, direct toegang tot de kaart tegel, batch Services en vergelijking van gegevens behoeften (d.w.z. dekking van afbeeldingen).

| Google Maps-functie         | Ondersteuning voor Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Gepland                                |
| REST service-Api's           | ✓                                      |
| Instructies (route ring)        | ✓                                      |
| Distance Matrix             | ✓                                      |
| Uitbrei ding                   | Gepland                                |
| Geocodering (forward/reverse) | ✓                                      |
| Geolocatie                 | N/A                                    |
| Locaties zoeken               | ✓                                      |
| Plaatst Details              | N.v.t.: het telefoon nummer van de website & beschikbaar |
| Plaatst Foto's               | N/A                                    |
| Automatisch aanvullen plaatsen          | ✓                                      |
| Statische kaarten                 | ✓                                      |
| Statische straat weergave          | N/A                                    |
| Tijdzone                   | ✓                                      |
| Maps-Inge sloten API           | N/A                                    |
| Kaart-Url's                    | N/A                                    |

Google Maps biedt basis verificatie op basis van een sleutel. Azure Maps biedt zowel basis verificatie op basis van sleutels als zeer veilige Azure Active Directory authenticatie.

## <a name="licensing-considerations"></a>Licentie overwegingen

Bij de migratie naar Azure Maps van Google Maps, moeten de volgende punten worden overwogen met betrekking tot licenties.

- Azure Maps kosten voor het gebruik van interactieve kaarten op basis van het aantal geladen kaart tegels, terwijl Google kaarten toerekent voor het laden van het kaart besturings element. In de interactieve Azure Maps Sdk's worden kaart tegels automatisch in de cache geplaatst om de kosten voor de ontwikkelaar te verminderen. Er wordt één Azure Maps trans actie gegenereerd voor elke 15 kaart tegels die worden geladen. De interactieve Azure Maps Sdk's gebruiken tegels van 512 pixels en gemiddeld genereert een of minder trans acties per pagina weergave.
- Het is vaak veel rendabeler om statische kaart installatie kopieën van Google Maps Web Services te vervangen door de Azure Maps Web-SDK. Dit maakt gebruik van kaart tegels en tenzij de gebruiker de kaart pannt en inzoomt, worden er vaak slechts een fractie van een trans actie per kaart belasting gegenereerd. De Azure Maps Web-SDK bevat opties voor het uitschakelen van pannen en zoomen. Daarnaast biedt de Azure Maps Web-SDK veel meer visualisatie opties dan een statische map-webservice.
- Met Azure Maps kunnen gegevens van het platform worden opgeslagen in Azure. Het kan ook ergens anders in de cache worden opgeslagen gedurende een periode [van](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)Maxi maal zes maanden.

Hier volgen enkele verwante resources voor Azure Maps:

- [Pagina met Azure Maps prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) gebruiks voorwaarden (opgenomen in de voor waarden voor micro soft Online Services)
- [Kies de juiste prijs categorie in Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Aanbevolen migratie plan

Hier volgt een migratie plan op hoog niveau.

1. Maak inventarisatie van de Sdk's en services van Google Maps die uw toepassing gebruikt en controleer of Azure Maps alternatieve Sdk's en services biedt die u kunt migreren naar.
2. Een Azure-abonnement maken (als u er nog geen hebt) op [https://azure.com](https://azure.com).
3. Maak een Azure Maps-account ([documentatie](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) en een verificatie sleutel of Azure Active Directory ([documentatie](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migreer uw toepassings code.
5. Test uw gemigreerde toepassing.
6. Implementeer uw gemigreerde toepassing naar productie.

## <a name="azure-maps-technical-resources"></a>Azure Maps technische bronnen

Hier volgt een lijst met nuttige technische bronnen voor Azure Maps.

- Overzicht: [https://azure.com/maps](https://azure.com/maps)
- Documentatie: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Voor beelden van Web SDK-code: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Ontwikkelaars forums: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Video's: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps feedback (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Ondersteuning voor migratie

Ontwikkel aars kunnen migratie ondersteuning zoeken via de [forums](https://aka.ms/AzureMapsForums) of via een van de vele ondersteunings opties voor Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het migreren van uw Google Maps-toepassing met de volgende artikelen:

> [!div class="nextstepaction"]
> [Een web-app migreren](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Een Android-app migreren](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Een webservice migreren](migrate-from-google-maps-web-services.md)
