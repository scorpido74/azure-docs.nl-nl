---
title: Fouten in gereserveerde resourcenamen
description: Beschrijft hoe u fouten oplossen bij het opgeven van een resourcenaam die een gereserveerd woord bevat.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477627"
---
# <a name="resolve-reserved-resource-name-errors"></a>Fouten in de naam van gereserveerde resources oplossen

In dit artikel wordt de fout beschreven die u ondervindt bij het implementeren van een resource die een gereserveerd woord in de naam bevat.

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert die beschikbaar is via een openbaar eindpunt, ontvangt u mogelijk de volgende fout:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Oorzaak

Resources met een openbaar eindpunt kunnen geen gereserveerde woorden of handelsmerken in de naam gebruiken.

De volgende woorden zijn voorbehouden:

* TOEGANG
* Azure
* Bing
* Bizspark
* Biztalk
* CORTANA CORTANA
* Directx
* Dotnet
* Dynamics
* EXCEL
* Exchange
* Voorhoede
* Groove
* HOLOLENS (HOLOLENS)
* HYPERV (HYPERV)
* KINECT
* LYNC
* MSDN
* O365
* Office
* OFFICE365
* ONEDRIVE (ONEDRIVE)
* Onenote
* Outlook
* Powerpoint
* Sharepoint
* Skype
* Visio
* VISUALSTUDIO VISUALSTUDIO

De volgende woorden kunnen niet worden gebruikt als een geheel woord of als substring in de naam:

* Login
* Microsoft
* Windows
* Xbox

## <a name="solution"></a>Oplossing

Geef een naam op die een van de gereserveerde woorden niet gebruikt.