---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093137"
---
## <a name="configure-the-cloud-anchor-session"></a>De Cloud-anker sessie configureren

U kunt de Cloud ankerpunt nu configureren. Op de eerste regel stellen we de sensor provider in voor de sessie. Vanaf nu wordt elk anker dat tijdens de sessie wordt gemaakt, gekoppeld aan een set sensor-aflezingen. Vervolgens maken we een voor waarde voor het zoeken naar criteria en initialiseert u deze op basis van de toepassings vereisten. Ten slotte geven we de sessie opdracht om sensor gegevens te gebruiken bij het zoeken naar ankers door een Watcher te maken op basis van ons bijna-criterium.