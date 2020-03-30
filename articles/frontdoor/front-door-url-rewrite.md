---
title: Azure Front Door - URL Herschrijven | Microsoft Documenten
description: In dit artikel u begrijpen hoe Azure Front Door URL herschrijven voor uw routes, indien geconfigureerd.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471469"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL herschrijven (aangepast doorstuurpad)
Azure Front Door ondersteunt URL-herschrijven door dat u een optioneel **aangepast doorstuurpad** configureren dat u gebruiken bij het maken van het verzoek om door te sturen naar de backend. Als er geen aangepast doorstuurpad wordt opgegeven, kopieert Front Door het pad van de binnenkomende URL naar de URL die in de doorgestuurde aanvraag wordt gebruikt. De hostkoptekst in de doorgestuurde aanvraag is de hostkoptekst zoals deze is geconfigureerd voor de geselecteerde back-end. Lees [Backend Host Header](front-door-backend-pool.md#hostheader) om te leren wat het doet en hoe u deze configureren.

Het krachtige deel van url-herschrijven met behulp van aangepast doorstuurpad is dat het een deel van het binnenkomende pad kopieert dat overeenkomt met een wildcardpad naar het doorgestuurde pad (deze padsegmenten zijn de **groene** segmenten in het onderstaande voorbeeld):
</br>
![Azure-URL voor de voordeur herschrijven][1]

## <a name="url-rewrite-example"></a>Voorbeeld van URL herschrijven
Overweeg een routeringsregel met de volgende frontendhosts en -paden geconfigureerd:

| Hosts      | Paden       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

In de eerste kolom van de onderstaande tabel ziet u voorbeelden van binnenkomende aanvragen en in de tweede kolom wordt weergegeven wat de 'meest specifieke' overeenkomende route 'Pad' zou zijn.  De derde en volgende kolommen van de eerste rij van de tabel zijn voorbeelden van geconfigureerde **aangepaste doorstuurpaden,** waarbij de rest van de rijen in die kolommen voorbeelden weergeven van wat het doorgestuurde aanvraagpad zou zijn als het overeenkomt met de aanvraag in die rij.

Als we bijvoorbeeld over de tweede rij lezen, staat er `www.contoso.com/sub`dat voor binnenkomende `/`aanvraag , als het `/sub`aangepaste doorstuurpad was, het doorgestuurde pad . Als het aangepaste `/fwd/`doorstuurpad was, dan `/fwd/sub`zou het doorgestuurde pad . Enzovoort, voor de resterende kolommen. De benadrukte delen van de **onderstaande** paden geven de gedeelten weer die deel uitmaken van de wildcard-overeenkomst.


| Binnenkomend verzoek       | Meest specifieke matchpad | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**Bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Optionele instellingen
Er zijn aanvullende optionele instellingen die u ook opgeven voor een bepaalde routeringsregelinstellingen:

* **Cacheconfiguratie** - Als deze is uitgeschakeld of niet is opgegeven, worden aanvragen die overeenkomen met deze routeringsregel niet geprobeerd inhoud in de cache te gebruiken en wordt deze in plaats daarvan altijd opgehaald vanaf de backend. Lees meer over [Caching met Voordeur](front-door-caching.md).



## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg