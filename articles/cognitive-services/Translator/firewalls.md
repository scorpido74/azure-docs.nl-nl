---
title: Vertalen achter firewalls-Translator Text-API
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text-API kunt vertalen achter firewalls met behulp van de domein naam of IP-filtering.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837390"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Achter IP-firewalls vertalen met de Translator Text-API

Translator Text-API kunt vertalen achter firewalls met behulp van de domein naam of IP-filtering. Het filteren van domein namen is de voorkeurs methode. Het is **niet raadzaam om** micro soft Translator uit te voeren achter een door IP gefilterde firewall. De installatie wordt waarschijnlijk zonder voorafgaande kennisgeving in de toekomst beëindigd.

## <a name="translator-ip-addresses"></a>NAT-IP-adressen
De IP-adressen voor api.cognitive.microsofttranslator.com-micro soft Translator Text-API vanaf 21 augustus 2019:

* **Azië en Stille Oceaan:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Noord-Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertalen achter IP-firewalls in de API-aanroep van Translator](reference/v3-0-translate.md)
