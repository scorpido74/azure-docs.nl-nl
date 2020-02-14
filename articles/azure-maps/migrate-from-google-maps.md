---
title: 'Zelf studie: migreren van Google Maps naar Azure Maps | Microsoft Azure kaarten'
description: Een zelf studie over het migreren van Google-kaarten naar Microsoft Azure Maps. U wordt begeleid bij het overschakelen naar Azure Maps-Api's en Sdk's.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 175625ab9fca9103bde027c3c0ea0986806ad846
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208299"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migreren van Google-kaarten naar Azure Maps

In deze zelf studie vindt u informatie over het migreren van web-, mobiele en server toepassingen van Google Maps naar het Microsoft Azure Maps-platform. Deze zelf studie bevat vergelijkende code voorbeelden, migratie suggesties en aanbevolen procedures voor het migreren naar Azure Maps.

## <a name="azure-maps-platform-overview"></a>Overzicht van Azure Maps platform

Azure Maps biedt ontwikkel aars uit alle branches krachtige georuimtelijke mogelijkheden. De mogelijkheden zijn verpakt met regel matige bijgewerkte kaart gegevens om geografische context te bieden voor web-en mobiele toepassingen. Azure Maps heeft een Azure One API-compatibele set REST-Api's. De REST Api's bieden rendering van kaarten, zoek functies, route ring, verkeer, tijd zones, geolocatie, geoomheining, kaart gegevens, weer, mobiliteit en ruimtelijke bewerkingen. Bewerkingen worden gecombineerd met zowel web-als Android-Sdk's om ontwikkel gemak, flexibel en draagbaar te maken op meerdere platforms.

## <a name="high-level-platform-comparison"></a>Vergelijking van platform op hoog niveau

De tabel bevat een lijst op hoog niveau van Azure Maps functies die overeenkomen met de functies van Google Maps. In deze lijst worden niet alle Azure Maps functies weer gegeven. Aanvullende Azure Maps functies zijn onder andere: toegankelijkheid, geoomheining, van isochronen, ruimtelijke bewerkingen, direct kaarten tegel toegang, batch Services en vergelijking van gegevens behoeften (dat wil zeggen, afbeeldings dekking).

| Google Maps-functie         | Ondersteuning voor Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Gepland                                |
| REST service-Api's           | ✓                                      |
| Instructies (route ring)        | ✓                                      |
| Afstands matrix             | ✓                                      |
| Uitbrei ding                   | Gepland                                |
| Geocodering (forward/reverse) | ✓                                      |
| Geolocatie                 | N.v.t.                                    |
| Locaties zoeken               | ✓                                      |
| Plaatst Details              | N.v.t.: het telefoon nummer van de website & beschikbaar |
| Plaatst Foto's               | N.v.t.                                    |
| Automatisch aanvullen plaatsen          | ✓                                      |
| Statische kaarten                 | ✓                                      |
| Statische straat weergave          | N.v.t.                                    |
| Tijdzone                   | ✓                                      |
| Maps-Inge sloten API           | N.v.t.                                    |
| Kaart-Url's                    | N.v.t.                                    |

Google Maps biedt basis verificatie op basis van een sleutel. Azure Maps biedt zowel basis verificatie op basis van sleutels als Azure Active Directory-verificatie. Azure Active Directory-verificatie biedt meer beveiligings functies, vergeleken met de basis verificatie op basis van sleutels.

## <a name="licensing-considerations"></a>Licentie overwegingen

Houd rekening met de volgende punten over licenties bij het migreren naar Azure Maps van Google Maps.

- Azure Maps kosten voor het gebruik van interactieve kaarten, dat is gebaseerd op het aantal geladen kaart tegels. Aan de andere kant worden er kosten in rekening gebracht voor het laden van het kaart besturings element. In de interactieve Azure Maps Sdk's worden kaart tegels automatisch in de cache geplaatst om de ontwikkelings kosten te verlagen. Er wordt één Azure Maps trans actie gegenereerd voor elke 15 kaart tegels die worden geladen. De interactieve Azure Maps Sdk's maakt gebruik van tegels van 512 pixels en er worden gemiddeld een of minder trans acties gegenereerd per pagina weergave.
- Vaak zijn de kosten effectief om statische kaart installatie kopieën van Google Maps Web Services te vervangen door de Azure Maps Web-SDK. De Azure Maps Web-SDK gebruikt kaart tegels. Tenzij de gebruiker de kaart pannt en inzoomt, genereert de service vaak slechts een fractie van een trans actie per kaart belasting. De Azure Maps Web-SDK bevat opties voor het uitschakelen van pannen en zoomen, indien gewenst. Daarnaast biedt de Azure Maps Web-SDK veel meer visualisatie opties dan de statische map-webservice.
- Met Azure Maps kunnen gegevens van het platform worden opgeslagen in Azure. Ook kunnen gegevens in de cache worden opgeslagen in een periode van Maxi maal zes maanden, afhankelijk [van de gebruiks voorwaarden](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Hier volgen enkele verwante resources voor Azure Maps:

- [Pagina met Azure Maps prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) gebruiks voorwaarden (opgenomen in de voor waarden voor micro soft Online Services)
- [Kies de juiste prijs categorie in Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Aanbevolen migratie plan

Hier volgt een migratie plan op hoog niveau.

1. Maak inventarisatie van de Google Maps-Sdk's en-services die door uw toepassing worden gebruikt. Controleer of Azure Maps alternatieve Sdk's en services biedt.
2. Als u er nog geen hebt, maakt u een Azure-abonnement op [https://azure.com](https://azure.com).
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

## <a name="migration-support"></a>Migratieondersteuning

Ontwikkel aars kunnen migratie ondersteuning zoeken via de [forums](https://aka.ms/AzureMapsForums) of via een van de vele ondersteunings opties voor Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het migreren van uw Google Maps-toepassing met de volgende artikelen:

> [!div class="nextstepaction"]
> [Een web-app migreren](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Een Android-app migreren](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Een webservice migreren](migrate-from-google-maps-web-services.md)
