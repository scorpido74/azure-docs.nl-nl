---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130076"
---
We raden je ten minste 30 afbeeldingen per tag aan in de initiële trainingsset. U wilt ook een paar extra afbeeldingen verzamelen om uw model te testen zodra het is getraind.

Gebruik afbeeldingen met visuele variatie om uw model effectief te trainen. Selecteer afbeeldingen die variëren op:
* camerahoek
* Verlichting
* background
* visuele stijl
* individueel/gegroepeerd onderwerp(en)
* grootte
* type

Zorg er bovendien voor dat al uw trainingsafbeeldingen aan de volgende criteria voldoen:
* .jpg, .png, .bmp of .gif-indeling
* niet groter dan 6 MB groot (4 MB voor voorspellingsafbeeldingen)
* maar liefst 256 pixels op de kortste rand; alle afbeeldingen die korter zijn dan dit, worden automatisch opgeschaald door de Custom Vision Service
