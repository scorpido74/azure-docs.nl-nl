---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "76545199"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>De sessie voor het ruimtelijk anker in de cloud configureren

De configuratie van de sessie voor het ruimtelijk anker in de cloud wordt later behandeld. Op de eerste regel wordt de sensorprovider voor de sessie ingesteld. Voortaan wordt elk anker dat tijdens de sessie wordt gemaakt, aan een reeks sensoraflezingen gekoppeld. Vervolgens worden voorwaarden voor het lokaliseren van nabije apparaten geïnstantieerd en vervolgens geïnitialiseerd zodat ze aan de toepassingsvereisten voldoen. Ten slotte krijgt de sessie de opdracht om sensorgegevens te gebruiken bij het lokaliseren van ankers door een watcher te maken op basis van de voorwaarden voor nabije apparaten.