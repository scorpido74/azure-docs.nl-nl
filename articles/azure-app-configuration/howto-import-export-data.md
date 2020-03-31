---
title: Gegevens importeren of exporteren met Azure App-configuratie
description: Meer informatie over het importeren of exporteren van gegevens naar of vanuit Azure App-configuratie
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056866"
---
# <a name="import-or-export-configuration-data"></a>Configuratiegegevens importeren of exporteren

Azure App Configuration ondersteunt import- en exportbewerkingen voor gegevens. Gebruik deze bewerkingen om te werken met configuratiegegevens in bulk en gegevens uit te wisselen tussen uw App Configuration Store en codeproject. U bijvoorbeeld één app-configuratiearchief instellen voor tests en een andere voor productie. U toepassingsinstellingen tussen deze instellingen kopiëren, zodat u geen twee keer gegevens hoeft in te voeren.

Dit artikel bevat een handleiding voor het importeren en exporteren van gegevens met app-configuratie. Als je een doorlopende synchronisatie met je GitHub repo wilt instellen, bekijk dan onze [GitHub Action.](https://aka.ms/azconfig-gha1)

## <a name="import-data"></a>Gegevens importeren

Import brengt configuratiegegevens naar een App Configuration Store van een bestaande bron. Gebruik de importfunctie om gegevens te migreren naar een app-configuratiearchief of gegevens uit meerdere bronnen te verzamelen. App-configuratie ondersteunt het importeren uit een JSON-, YAML- of eigenschappenbestand.

Importeer gegevens met behulp van de [Azure-portal](https://portal.azure.com) of de [Azure CLI](./scripts/cli-import.md). Voer de volgende stappen uit vanuit de Azure-portal:

1. Blader naar uw app-configuratiearchief en selecteer **Importeren/exporteren** in het menu **Bewerkingen.**

1. Selecteer op het tabblad **Importeren** de optie**Configuratiebestand** **bronservice** > .

1. Selecteer **Voor taal** en selecteer het gewenste invoertype.

1. Selecteer het **mappictogram** en blader naar het bestand dat u wilt importeren.

    ![Bestand importeren](./media/import-file.png)

1. Selecteer een **scheidingsteken**en voer optioneel een **voorvoegsel** in dat u wilt gebruiken voor geïmporteerde sleutelnamen.

1. Selecteer desgewenst een **label**.

1. Selecteer **Toepassen** om de import te voltooien.

    ![Bestand importeren voltooid](./media/import-file-complete.png)

## <a name="export-data"></a>Gegevens exporteren

Export voor schrijven configuratiegegevens die zijn opgeslagen in app-configuratie naar een andere bestemming. Gebruik de exportfunctie bijvoorbeeld om gegevens in een app-configuratiearchief op te slaan in een bestand dat tijdens de implementatie is ingesloten met uw toepassingscode.

Gegevens exporteren met behulp van de [Azure-portal](https://portal.azure.com) of de [Azure CLI](./scripts/cli-export.md). Voer de volgende stappen uit vanuit de Azure-portal:

1. Blader naar uw app-configuratiearchief en selecteer **Importeren/exporteren**.

1. Selecteer op het tabblad **Exporteren** de optie**Configuratiebestand** **van doelservice** > .

1. Voer optioneel een **voorvoegsel in** en selecteer een **label** en een in-time-tijd voor het exporteren van sleutels.

1. Selecteer een **scheidingsteken voor** > **bestandstype**.

1. Selecteer **Toepassen** om de export te voltooien.

    ![Exportbestand voltooid](./media/export-file-complete.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)  
