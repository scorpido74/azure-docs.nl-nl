---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67176684"
---
Gebruik de volgende procedure om de runtime-versie weer te geven en bij te werken die momenteel wordt gebruikt door een functie-app.

1. Blader in de [Azure-portal](https://portal.azure.com) naar uw functie-app.

1. Kies onder **Geconfigureerde functies** de optie **Instellingen voor functie-apps**.

    ![Instellingen voor functie-apps selecteren](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Zoek op het tabblad **Instellingen voor functie-apps** de **Runtime-versie**. Noteer de specifieke runtime-versie en de gevraagde primaire versie. In onderstaand voorbeeld is de versie ingesteld op `~2`.

   ![Instellingen voor functie-apps selecteren](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Als u uw functie-app wilt vastmaken aan de runtime met versie 1.x, kiest u **~ 1** onder **Runtime-versie**. Deze schakeloptie is uitgeschakeld als uw app functies bevat.

1. Wanneer u de runtime-versie wijzigt, gaat u terug naar het tabblad **Overzicht** en kiest u **Opnieuw opstarten** om de app opnieuw op te starten.  De functie-app wordt opnieuw opgestart met versie 1.x van de runtime en de sjablonen van versie 1.x worden gebruikt wanneer u functies maakt.