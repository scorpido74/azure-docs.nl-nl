---
title: Problemen oplossen met de SDK voor spraakapparaten - Spraakservice
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie om problemen op te lossen die u tegenkomen wanneer u de Spraakapparaten SDK gebruikt.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815564"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Probleem oplossen met de Speech Devices-SDK

In dit artikel vindt u informatie om problemen op te lossen die u tegenkomen wanneer u de Spraakapparaten SDK gebruikt.

## <a name="certificate-failures"></a>Certificaatfouten

Als u certificaatfouten krijgt wanneer u de spraakservice gebruikt, controleert u of uw apparaat de juiste datum en tijd heeft:

1. Ga naar **Settings**. Selecteer **Onder Systeem**de optie Datum & **tijd**.

    ![Selecteer onder Instellingen datum & tijd](media/speech-devices-sdk/qsg-12.png)

1. Houd de optie **Automatische datum & tijd** geselecteerd. Selecteer **onder Tijdzone selecteren**uw huidige tijdzone.

    ![Opties voor datum- en tijdzoneselecteren](media/speech-devices-sdk/qsg-13.png)

    Wanneer u ziet dat de tijd van de dev kit overeenkomt met de tijd op uw pc, is de dev kit verbonden met het internet.

## <a name="next-steps"></a>Volgende stappen

* [Bekijk de releasenotes](devices-sdk-release-notes.md)
