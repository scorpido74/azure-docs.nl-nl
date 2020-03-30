---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049061"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

In dit artikel maakt u een back-endservice die een taak plant om een directe methode op een apparaat aan te roepen, een taak plant om de apparaattweeling bij te werken en de voortgang van elke taak bewaakt. Om deze bewerkingen uit te voeren, heeft uw service de **registerlees-** en **registerschrijfmachtigingen** nodig. Standaard wordt elke IoT-hub gemaakt met een beleid voor gedeelde toegang met de naam **registryReadWrite** waarmee deze machtigingen worden toegekend.
