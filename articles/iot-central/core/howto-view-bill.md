---
title: Uw factuur beheren en converteren vanuit het gratis prijsplan in azure IoT Central-toepassing | Microsoft Documenten
description: Als beheerder leert u hoe u uw factuur beheert en overgaat van het gratis prijsplan naar een standaardprijsplan in uw Azure IoT Central-toepassing
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157495"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Uw factuur beheren in een IoT Central-toepassing

In dit artikel wordt beschreven hoe u als beheerder uw factuur beheren in de Azure IoT Central-toepassing in de beheersectie. U leert hoe u uw toepassing verplaatsen van het gratis prijsplan naar een standaard prijsplan, en ook hoe u uw prijsplan upgraden of downgraden.

Als u de sectie **Beheer** wilt openen en gebruiken, moet u zich in de functie *Administrator* bevinden of een *aangepaste gebruikersrol* hebben waarmee u facturering voor een Azure IoT Central-toepassing bekijken. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan de **administratorrol** voor die toepassing.

## <a name="move-from-free-to-standard-pricing-plan"></a>Overstappen van gratis naar standaard prijsplan

- Toepassingen die gebruik maken van het gratis prijsplan zijn zeven dagen gratis voordat ze verlopen. Om te voorkomen dat u gegevens verliest, u deze op elk gewenst moment verplaatsen naar een standaardprijsplan voordat ze verlopen.
- Aanvragen die een standaard prijsplan gebruiken, worden per apparaat in rekening gebracht, waarbij de eerste twee apparaten gratis zijn, per toepassing.

Lees meer over prijzen op de [prijzenpagina van IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

In de prijssectie u uw toepassing verplaatsen van het gratis naar een standaard prijsplan.

Voer de volgende stappen uit om dit selfserviceproces te voltooien:

1. Ga naar de pagina **Prijzen** in de sectie **Administratie.**

    ![Processtatus](media/howto-view-bill/freetrialbilling.png)

1. Selecteer **Converteren naar een betaald abonnement**.

    ![Proefversie converteren](media/howto-view-bill/convert.png)

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die een betaald abonnement gebruikt.

1. Nadat u **Converteren**hebt geselecteerd, gebruikt uw toepassing nu een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een Standard 2-prijsplan. *Standard 2*

## <a name="how-to-change-your-application-pricing-plan"></a>Uw abonnementsprijsplan wijzigen

Aanvragen die een standaard prijsplan gebruiken, worden per apparaat in rekening gebracht, waarbij de eerste twee apparaten gratis zijn, per toepassing.

In de prijssectie u uw Azure IoT-prijsplan op elk gewenst moment upgraden of downgraden.

1. Ga naar de pagina **Prijzen** in de sectie **Administratie.**

    ![Processtatus](media/howto-view-bill/pricing.png)

1. Selecteer het **plan** en klik op **Opslaan** om te upgraden of te downgraden.

## <a name="view-your-bill"></a>Uw factuur weergeven

1. Selecteer de juiste Azure Active Directory en vervolgens het Azure-abonnement dat u wilt gebruiken voor uw toepassing die een betaald abonnement gebruikt.

1. Nadat u **Converteren**hebt geselecteerd, gebruikt uw toepassing nu een betaald abonnement en wordt u gefactureerd.

> [!Note]
> Standaard wordt u geconverteerd naar een Standard 2-prijsplan. *Standard 2*

## <a name="next-steps"></a>Volgende stappen

Nu u meer weet over het beheren van uw factuur in Azure IoT Central-toepassing, is de voorgestelde volgende stap om meer te weten te komen over [de gebruikersinterface van toepassingen aanpassen](howto-customize-ui.md) in Azure IoT Central.