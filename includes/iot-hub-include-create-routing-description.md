---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72808873"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

U gaat berichten naar andere resources doorsturen op basis van eigenschappen die door het gesimuleerde apparaat aan het bericht zijn gekoppeld. Berichten die niet aangepast zijn doorgestuurd, worden verzonden naar het standaardeindpunt (berichten/gebeurtenissen). In de volgende zelfstudie stuurt u berichten naar de IoT-hub en ziet u deze naar de verschillende bestemmingen worden doorgestuurd.

|Waarde |Resultaat|
|------|------|
|niveau = 'opslag' |Schrijf naar Azure Storage.|
|niveau = 'kritiek' |Schrijf naar een Service Bus-wachtrij. Een logische app haalt het bericht op uit de wachtrij en gebruikt Office 365 om het bericht via e-mail te versturen.|
|standaardinstelling |Geef deze gegevens weer met Power BI.|

De eerste stap is het instellen van het eindpunt waarnaar de gegevens worden doorgestuurd. De tweede stap is het instellen van de berichtroute die dat eindpunt gebruikt. Na het instellen van de route u de eindpunten en berichtroutes in de portal bekijken.