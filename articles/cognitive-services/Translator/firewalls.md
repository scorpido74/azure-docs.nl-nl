---
title: Vertalen achter firewalls - Translator Text API
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text API kan achter firewalls vertalen met behulp van domeinnaam of IP-filtering.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837390"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Vertalen achter IP-firewalls met de Translator Text API

Translator Text API kan achter firewalls vertalen met behulp van domeinnaam of IP-filtering. Domeinnaamfiltering heeft de voorkeursmethode. We **raden niet aan om** Microsoft Translator van achter een door het IP gefilterde firewall uit te voeren. De setup zal waarschijnlijk breken in de toekomst zonder kennisgeving.

## <a name="translator-ip-addresses"></a>Ip-adressen vertaler
De IP-adressen voor api.cognitive.microsofttranslator.com - Microsoft Translator Text API vanaf 21 augustus 2019:

* **Azië-Pacific:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138,4 van 40.90.141,99, 51.105.170,64, 52.155.218.251
* **Noord-Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Vertalen achter IP-firewalls in uw Translator API-aanroep](reference/v3-0-translate.md)
