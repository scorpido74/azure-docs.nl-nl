---
title: Gegevens importeren of exporteren met Azure-app configuratie | Microsoft Docs
description: Meer informatie over het importeren of exporteren van gegevens naar of van Azure-app configuratie
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9780dc34aa6b146fe62b11586cbab46825e60535
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185156"
---
# <a name="import-or-export-configuration-data"></a>Configuratiegegevens importeren of exporteren

De configuratie van Azure-app ondersteunt het importeren en exporteren van gegevens. Gebruik deze bewerkingen om met configuratie gegevens in bulk te werken en gegevens uit te wisselen tussen uw app-configuratie archief en het code project. U kunt bijvoorbeeld één app-configuratie archief instellen voor test doeleinden en een andere voor productie. Vervolgens kunt u de toepassings instellingen ertussen kopiëren via een bestand, zodat u gegevens niet twee keer hoeft in te voeren.

Dit artikel bevat een hand leiding voor het importeren en exporteren van gegevens met app-configuratie.

## <a name="import-data"></a>Gegevens importeren

Import brengt configuratie gegevens over naar een app-configuratie archief van een bestaande bron, in plaats van deze hand matig in te voeren. Gebruik de functie Import om gegevens te migreren naar een app-configuratie archief of om gegevens uit meerdere bronnen samen te voegen. App-configuratie ondersteunt het importeren vanuit een JSON-, YAML-of properties-bestand.

Gegevens importeren met behulp van de [Azure Portal](https://portal.azure.com) of de [Azure cli](./scripts/cli-import.md). Voer de volgende stappen uit op de Azure Portal:

1. Blader naar het configuratie archief van uw app en selecteer **importeren/exporteren**.

2. Selecteer het **configuratie bestand** **bron service** > op het tabblad **importeren** .

3. Selecteer **voor taal** > **bestands type**.

4. Selecteer het mappictogram en blader **naar het bestand** dat u wilt importeren.

    ![Bestand importeren](./media/import-file.png)

5. Selecteer een **scheidings teken**en geef eventueel een **voor voegsel** op dat moet worden gebruikt voor geïmporteerde sleutel namen.

6. Selecteer desgewenst een **Label**.

7. Selecteer **Toep assen** om de import te volt ooien.

    ![Het import bestand is voltooid](./media/import-file-complete.png)

## <a name="export-data"></a>Gegevens exporteren

Export schrijft configuratie gegevens die zijn opgeslagen in de app-configuratie naar een andere bestemming. Gebruik de functie exporteren om bijvoorbeeld gegevens in een app-configuratie archief op te slaan in een bestand dat is inge sloten met uw toepassings code tijdens de implementatie.

Gegevens exporteren met behulp van de [Azure Portal](https://portal.azure.com) of de [Azure cli](./scripts/cli-export.md). Voer de volgende stappen uit op de Azure Portal:

1. Blader naar het configuratie archief van uw app en selecteer **importeren/exporteren**.

2. Selecteer op het tabblad **exporteren** de optie **doel service** > **configuratie bestand**.

3. Geef eventueel een **voor voegsel** op en selecteer een **Label** en een tijdstip voor de sleutels die moeten worden geëxporteerd.

4. Selecteer een **bestands type** > **scheidings teken**.

5. Selecteer **Toep assen** om de export te volt ooien.

    ![Het export bestand is voltooid](./media/export-file-complete.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
