---
title: Gereserveerde resource naam fouten
description: Hierin wordt beschreven hoe u fouten oplost bij het opgeven van een resource naam die een gereserveerd woord bevat.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477627"
---
# <a name="resolve-reserved-resource-name-errors"></a>Problemen met gereserveerde resource namen oplossen

In dit artikel wordt de fout beschreven die u tegen komt bij het implementeren van een resource die een gereserveerd woord bevat in de naam.

## <a name="symptom"></a>Symptoom

Bij het implementeren van een resource die beschikbaar is via een openbaar eind punt, wordt mogelijk de volgende fout weer gegeven:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Oorzaak

Resources met een openbaar eind punt kunnen geen gereserveerde woorden of handels merken gebruiken in de naam.

De volgende woorden zijn gereserveerd:

* TOEGANG
* AZURE
* Zoek
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* STIFT
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* KOPIËREN
* ER
* POWERPOINT
* Point
* SKYPE
* VISIO
* Visual Studio

De volgende woorden mogen niet worden gebruikt als een geheel woord of een subtekenreeks in de naam:

* NAMEN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Oplossing

Geef een naam op die geen gebruik maakt van een van de gereserveerde woorden.