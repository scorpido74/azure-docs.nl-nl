---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76545199"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>De in de Cloud beruimtelijke-anker sessie configureren

We nemen de configuratie van de ruimtelijk-anker sessie voor de Cloud vervolgens op. Op de eerste regel stellen we de sensor provider in voor de sessie. Vanaf nu wordt elk anker dat tijdens de sessie wordt gemaakt, gekoppeld aan een set sensor-aflezingen. Vervolgens maken we een voor waarde voor het zoeken naar criteria en initialiseert u deze op basis van de toepassings vereisten. Ten slotte geven we de sessie opdracht om sensor gegevens te gebruiken bij het zoeken naar ankers door een Watcher te maken op basis van ons bijna-criterium.