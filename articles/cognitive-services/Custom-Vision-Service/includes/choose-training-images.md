---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130076"
---
We raden u ten zeerste aan om ten minste 30 afbeeldingen per tag in de eerste Trainingsset te gebruiken. U wilt ook een paar extra installatie kopieën verzamelen om uw model te testen zodra het is getraind.

Gebruik installatie kopieën met visualisatie om uw model effectief te trainen. Selecteer installatie kopieën die variëren per:
* camera hoek
* verlichting
* achtergrondbitmap
* visuele stijl
* afzonderlijke/gegroepeerde items
* size
* Type

Zorg er bovendien voor dat al uw trainings beeldafbeeldingen voldoen aan de volgende criteria:
* jpg-, PNG-, BMP-of GIF-indeling
* niet groter dan 6 MB grootte (4 MB voor Voorspellings installatie kopieën)
* Maxi maal 256 pixels op de kortste rand; afbeeldingen die korter zijn dan deze worden automatisch omhoog geschaald door de Custom Vision Service
