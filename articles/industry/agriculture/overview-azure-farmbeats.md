---
title: Wat is Azure FarmBeats
description: Biedt een overzicht van Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6f0a782309edc33a8a5ce661652922494ead2ec0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80667307"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Overzicht van Azure FarmBeats (preview-versie)

Azure FarmBeats is een Business-to-Business-aanbieding die beschikbaar is in azure Marketplace. Hiermee worden aggregatie van agrarische gegevens sets in verschillende providers mogelijk. Met Azure FarmBeats kunt u modellen voor kunst matige intelligentie (AI) of machine learning (ML) maken op basis van gegevens sets met zekering. Door gebruik te maken van Azure FarmBeats kunnen de landbouw bedrijven zich richten op de belangrijkste waarde-toevoegen in plaats van de niet-gedifferentieerde zware opheffen van data engineering.

> [!NOTE]
> Azure FarmBeats is momenteel beschikbaar als open bare preview. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats wordt zonder service level agreement gegeven. Gebruik het [Azure FarmBeats-forum](https://aka.ms/FarmBeatsMSDN ) voor ondersteuning.

![Maten van project-Farm](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Met de preview-versie van Azure FarmBeats kunt u het volgende doen:

- Evalueer de farm status met behulp van de vegetatie-index en de water index op basis van satelliet afbeelding.
- Krijg aanbevelingen over het aantal vocht sensors dat moet worden gebruikt en waar ze moeten worden geplaatst.
- Houd Farm voorwaarden bij door te visualiseren van de massa gegevens die door Sens oren van verschillende leveranciers zijn verzameld.
- Kaart voor bodem vocht ophalen op basis van het fusie van satelliet-en sensor gegevens.
- Krijg inzicht in de bewerkings acties door AI/ML-modellen boven op geaggregeerde gegevens sets te bouwen.
- Bouw of verbeter uw oplossing voor digitale land bouwers door de farm status adviezen te bieden.

## <a name="datahub"></a>Datahub

De Azure FarmBeats Datahub is een API-laag, waarmee aggregatie, normalisatie en contextualization van verschillende agrarische gegevens sets tussen providers worden ingeschakeld. U kunt Azure FarmBeats gebruiken om het volgende te krijgen:
- **Sensor gegevens** van twee sensor-providers [Davis instrumenten](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [pessl-instrumenten](https://metos.at/)
- **Satelliet afbeelding** van de missie van de [Sentinel-2-](https://sentinel.esa.int/web/sentinel/home) satelliet van het Euro pees ruimte-agentschap
- **Drone image** uit drie drone-installatie kopie providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub is ontworpen als een uitbreidbaar API-platform. We werken met veel meer providers om te integreren met Azure FarmBeats, zodat u meer keuze hebt tijdens het bouwen van uw oplossing.

## <a name="accelerator"></a>Snelle

De Azure FarmBeats Accelerator is een voor beeld van een webtoepassing, die boven op Datahub is gebouwd. De Accelerator-Jump-Start uw gebruikers interface en model ontwikkeling. De Azure FarmBeats Accelerator maakt gebruik van Azure FarmBeats-Api's. Er worden opgenomen sensor gegevens gevisualiseerd als grafieken en model uitvoer als kaarten. U kunt de Accelerator bijvoorbeeld gebruiken om snel een farm te maken en een vegetatie-index toewijzing of een sensor plaatsings kaart voor die Farm te verkrijgen.

## <a name="role-based-access-control-rbac"></a>Toegangsbeheer op basis van rollen (RBAC)

Een beheerder kan toegangs regels voor Azure FarmBeats definiëren met behulp van een van de vooraf gedefinieerde rollen. Rollen bepalen op welke gebieden van de toepassing een gebruiker toegang heeft en welke acties ze kunnen uitvoeren. Er zijn twee soorten rollen in azure FarmBeats: voor gebruikers en voor partners.

### <a name="user-roles"></a>Gebruikersrollen

Een [beheerder kan gebruikers toevoegen en beheren](manage-users-in-azure-farmbeats.md) en hun toegangs niveaus definiëren op basis van twee gebruikers rollen: beheerder en alleen-lezen.

### <a name="partner-roles"></a>Partner rollen

Een beheerder kan meerdere partners als gegevens providers toevoegen aan Azure FarmBeats. Hieronder vindt u een overzicht van de beschik bare partner rollen in FarmBeats en de bijbehorende machtigingen:

| Partner type    |   Acties  | Bereik |
| ---- | -------- | -------- |
| Sensor partner  |   Maken, lezen, bijwerken <br/> <br/> Lezen, bijwerken | DeviceModel, apparaat, SensorModel, sensor <br/> <br/> Extended type |
| Imagey-partner  |   Maken, lezen, bijwerken <br/> <br/> Lezen, bijwerken <br/> <br/> Lezen | Scène, SceneFile <br/> <br/> Extended type <br/> <br/> Voorzien |
| Weer partner * <br/> <br/>  (* Binnenkort beschikbaar) |   Maken, lezen, bijwerken <br/> <br/> Lezen, bijwerken <br/> <br/> Lezen | WeatherDataModel, WeatherDataLocation, taak type <br/> <br/> Extended type <br/> <br/> Voorzien |

## <a name="resources"></a>Resources

Azure FarmBeats wordt zonder extra kosten aangeboden en u betaalt alleen voor de Azure-resources die u gebruikt. U kunt de onderstaande bronnen gebruiken voor meer informatie over de aanbieding:

- Blijf op de hoogte van het nieuwste Azure FarmBeats News door de [Azure FarmBeats-blog](https://aka.ms/farmbeatsblog)te bezoeken.
- Zoek hulp door een vraag te plaatsen op ons [ondersteunings forum voor Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Geef feedback door post of stem toe voor een functie idee op ons [Feedback forum van Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure FarmBeats installeren](install-azure-farmbeats.md)
