---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050442"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In dit artikel maakt u een back-endservice die gewenste eigenschappen toevoegt aan een apparaattweeling en vervolgens het identiteitsregister opvraagt om alle apparaten met gerapporteerde eigenschappen te vinden die dienovereenkomstig zijn bijgewerkt. Uw service heeft de **service-machtiging** nodig om de gewenste eigenschappen van een apparaattweeling te wijzigen en moet de **registerleestoestemming** hebben om het identiteitsregister op te vragen. Er is geen standaard beleid voor gedeelde toegang dat alleen deze twee machtigingen bevat, dus u moet er een maken.
