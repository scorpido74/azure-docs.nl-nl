---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545199"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>De ruimteankersessie in de cloud configureren

We zorgen daarna voor het configureren van de cloud spatial anchor-sessie. Op de eerste regel zetten we de sensorprovider op de sessie. Vanaf nu wordt elk anker dat we tijdens de sessie maken gekoppeld aan een reeks sensormetingen. Vervolgens animeeren we een near-device lokaliseren criteria en initialiseren we deze om aan de toepassingsvereisten te voldoen. Tot slot instrueren we de sessie om sensorgegevens te gebruiken bij het vinden van ankers door een watcher te maken op basis van onze criteria voor bijna-apparaten.