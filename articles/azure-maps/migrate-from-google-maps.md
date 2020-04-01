---
title: 'Zelfstudie: Migreren van Google Maps naar Azure Maps | Microsoft Azure Maps'
description: Een zelfstudie over het migreren van Google Maps naar Microsoft Azure Maps. Richtlijnen begeleiden u door hoe u overschakelen naar Azure Maps API's en SDK's.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77913698"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migreren van Google Maps naar Azure Maps

Deze zelfstudie geeft inzicht in het migreren van web-, mobiele en servergebaseerde toepassingen van Google Maps naar het Microsoft Azure Maps-platform. Deze zelfstudie bevat vergelijkende codevoorbeelden, migratiesuggesties en aanbevolen procedures voor het migreren naar Azure Maps.

## <a name="azure-maps-platform-overview"></a>Overzicht van Azure Maps-platforms

Azure Maps biedt ontwikkelaars uit alle bedrijfstakken krachtige georuimtelijke mogelijkheden. De mogelijkheden zitten boordevol regelmatig bijgewerkte kaartgegevens om geografische context te bieden voor web- en mobiele toepassingen. Azure Maps heeft een Azure One API-compatibele set REST API's. De REST API's bieden Maps Rendering, Search, Routing, Traffic, Time Zones, Geolocation, Geofencing, Map Data, Weather, Mobility en Spatial Operations. Bewerkingen worden begeleid door zowel web- als Android-SDK's om de ontwikkeling eenvoudig, flexibel en draagbaar op meerdere platforms te maken.

## <a name="high-level-platform-comparison"></a>Vergelijking van platformen op hoog niveau

De tabel biedt een lijst op hoog niveau met Azure Maps-functies, die overeenkomen met de functies van Google Maps. In deze lijst worden niet alle Azure Maps-functies weergegeven. Aanvullende Azure Maps-functies zijn: toegankelijkheid, geofencing, isochronen, ruimtelijke bewerkingen, directe kaarttegeltoegang, batchservices en vergelijkingen over gegevensdekking (dat wil zeggen beelddekking).

| Google Maps-functie         | Azure Maps-ondersteuning                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Gepland                                |
| REST-service-API's           | ✓                                      |
| Routebeschrijving (routering)        | ✓                                      |
| Afstandsmatrix             | ✓                                      |
| Hoogte                   | Gepland                                |
| Geocodering (doorsturen/achteruit) | ✓                                      |
| Geolocatie                 | N.v.t.                                    |
| Dichtstbijzijnde wegen               | ✓                                      |
| Places Zoeken               | ✓                                      |
| Details plaatsen              | N/A – website & telefoonnummer beschikbaar |
| Plaatst foto's               | N.v.t.                                    |
| Automatisch aanvullen plaatsen          | ✓                                      |
| Snap aan weg                | ✓                                      |
| Snelheidslimieten                | ✓                                      |
| Statische kaarten                 | ✓                                      |
| Statische straatweergave          | N.v.t.                                    |
| Tijdzone                   | ✓                                      |
| Ingesloten API voor kaarten           | N.v.t.                                    |
| URL's van kaarten                    | N.v.t.                                    |

Google Maps biedt basisverificatie op basis van sleutels. Azure Maps biedt zowel basisverificatie op basis van sleutels als Azure Active Directory-verificatie. Azure Active Directory-verificatie biedt meer beveiligingsfuncties in vergelijking met de basisverificatie op basis van sleutels.

## <a name="licensing-considerations"></a>Licentieoverwegingen

Houd bij het migreren naar Azure Maps vanuit Google Maps rekening met de volgende punten over licenties.

- Azure Maps brengt kosten in rekening voor het gebruik van interactieve kaarten, die is gebaseerd op het aantal geladen kaarttegels. Aan de andere kant brengt Google Maps kosten in rekening voor het laden van het kaartbesturingselement. In de interactieve Azure Maps-sdK's worden kaarttegels automatisch in de cache opgeslagen om de ontwikkelingskosten te verlagen. Eén Azure Maps-transactie wordt gegenereerd voor elke 15 kaarttegels die worden geladen. De interactieve Azure Maps SDKs maakt gebruik van tegels van 512 pixels en genereert gemiddeld één of minder transacties per paginaweergave.
- Vaak is het rendabeler om statische kaartafbeeldingen van Google Maps-webservices te vervangen door de Azure Maps Web SDK. De Azure Maps Web SDK maakt gebruik van kaarttegels. Tenzij de gebruiker de kaart pannen en zoomen, genereert de service vaak slechts een fractie van een transactie per kaartbelasting. De Azure Maps web SDK heeft desgewenst opties voor het uitschakelen van pannen en zoomen. Bovendien biedt de Azure Maps web SDK veel meer visualisatieopties dan de statische kaartwebservice.
- Azure Maps maakt het mogelijk om gegevens van het platform op te slaan in Azure. Ook kunnen gegevens elders worden opgeslagen voor maximaal zes maanden volgens de [gebruiksvoorwaarden.](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)

Hier volgen enkele gerelateerde bronnen voor Azure Maps:

- [Prijspagina azure maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Gebruiksduur van Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (opgenomen in de voorwaarden van Microsoft Online Services)
- [De juiste prijscategorie kiezen in Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Voorgesteld migratieplan

Het volgende is een migratieplan op hoog niveau.

1. Inventariseer de Google Maps-SDK's en -services die uw toepassing gebruikt. Controleer of Azure Maps alternatieve SDK's en -services biedt.
2. Als u er nog geen hebt, maakt [https://azure.com](https://azure.com)u een Azure-abonnement op .
3. Maak een Azure Maps-account[(documentatie)](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)en verificatiesleutel of Azure Active Directory[(documentatie).](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
4. Migreer uw toepassingscode.
5. Test uw gemigreerde toepassing.
6. Implementeer uw gemigreerde toepassing naar productie.

## <a name="azure-maps-technical-resources"></a>Technische bronnen van Azure Maps

Hier vindt u een lijst met nuttige technische bronnen voor Azure Maps.

- Overzicht:[https://azure.com/maps](https://azure.com/maps)
- Documentatie:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Voorbeelden van Web SDK-code:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Ontwikkelaarsforums:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Video 's:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Tech Blog:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Feedback van Azure Maps (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter-notitieblok] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Ondersteuning voor migratie

Ontwikkelaars kunnen migratieondersteuning zoeken via de [forums](https://aka.ms/AzureMapsForums) of via een van de vele Azure-ondersteuningsopties:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het migreren van uw Google Maps-toepassing met deze artikelen:

> [!div class="nextstepaction"]
> [Een web-app migreren](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Een Android-app migreren](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Een webservice migreren](migrate-from-google-maps-web-services.md)
