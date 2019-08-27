---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034430"
---
## <a name="validate-that-a-container-is-running"></a>Controleren of een container wordt uitgevoerd 

Er zijn verschillende manieren om te controleren of de container wordt uitgevoerd. Zoek het *externe IP-* adres en de weer gegeven poort van de betreffende container en open uw favoriete webbrowser. Gebruik de onderstaande aanvraag-Url's om te controleren of de container wordt uitgevoerd. De onderstaande voorbeeld aanvraag-url's zijn `http://localhost:5000`, maar uw specifieke container kan variëren. Houd er rekening mee dat u afhankelijk bent van het *externe IP-* adres van uw container en de weer gegeven poort.

| Aanvraag-URL | Doel |
|--|--|
| `http://localhost:5000/` | De container bevat een start pagina. |
| `http://localhost:5000/status` | Er wordt met een HTTP GET-aanvraag gecontroleerd of de container wordt uitgevoerd zonder dat dit een eindpunt query veroorzaakt. Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | De container bevat een volledige documentatie voor de eind punten en een try- **out** -functie. Met deze functie kunt u uw instellingen invoeren in een HTML-formulier op het web en de query maken zonder dat u code hoeft te schrijven. Nadat de query is geretourneerd, wordt een voor beeld van een krul opdracht weer gegeven om te demonstreren welke HTTP-headers en hoofdtekst indeling vereist zijn. |

![Start pagina van container](./media/cognitive-services-containers-api-documentation/container-webpage.png)
