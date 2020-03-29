---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034430"
---
## <a name="validate-that-a-container-is-running"></a>Valideren of een container actief is 

Er zijn verschillende manieren om te valideren dat de container wordt uitgevoerd. Zoek het *externe IP-adres* en de blootgestelde poort van de container in kwestie en open uw favoriete webbrowser. Gebruik de onderstaande aanvraag-URL's om te valideren dat de container wordt uitgevoerd. De onderstaande voorbeeldaanvraag-URL's zijn `http://localhost:5000`, maar uw specifieke container kan variëren. Houd er rekening mee dat u moet vertrouwen op het *externe IP-adres* en de blootgestelde poort van uw container.

| Aanvraag-URL | Doel |
|--|--|
| `http://localhost:5000/` | De container biedt een startpagina. |
| `http://localhost:5000/status` | Gevraagd met een HTTP GET, om te valideren dat de container wordt uitgevoerd zonder een eindpuntquery te veroorzaken. Deze aanvraag kan worden gebruikt voor Kubernetes [liveness en readiness probes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) |
| `http://localhost:5000/swagger` | De container biedt een volledige set documentatie voor de eindpunten en een **try it-out** functie. Met deze functie u uw instellingen invoeren in een webgebaseerd HTML-formulier en de query uitvoeren zonder dat u code hoeft te schrijven. Nadat de query is geretourneerd, wordt een voorbeeldopdracht CURL gegeven om de HTTP-kop- en hoofdindeling aan te tonen die vereist is. |

![Startpagina van de container](./media/cognitive-services-containers-api-documentation/container-webpage.png)
