---
title: Snelstartgids-Azure Analysis Services server firewall configureren | Microsoft Docs
description: Deze Quick Start helpt u bij het configureren van een firewall voor een Azure Analysis Services-server door gebruik te maken van de Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68994f9b79af55b32527eed52bbc4e5866c89538
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79205163"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Snelstart: Een serverfirewall configureren - Portal

Deze snelstart helpt u bij het configureren van een firewall voor uw Azure Analysis Services-server. Een firewall inschakelen en het configureren van IP-adresbereiken voor uitsluitend de computers die toegang tot de server hebben, speelt een belangrijke rol bij het beveiligen van uw server en uw gegevens.

## <a name="prerequisites"></a>Vereisten

- Een Azure Analysis Services-server in uw abonnement. Zie [Snelstart: Een server maken - Portal](analysis-services-create-server.md) of [Snelstart: Een server maken - PowerShell](analysis-services-create-powershell.md) voor meer informatie.
- Een of meer IP-adresbereiken voor clientcomputers (indien nodig).
- Sommige scenario's waarbij Power BI Premium verbinding maakt met Azure Analysis Services, met inbegrip van het importeren van gegevens (vernieuwen) en gepagineerde rapporten, worden momenteel niet ondersteund, zelfs niet wanneer toegang vanuit Power BI toestaan is ingeschakeld. Het meest voorkomende scenario voor het gebruik van Live Connect van Power BI Premium wordt ondersteund. Alle Power BI Pro scenario's worden ondersteund.


## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal 

[Aanmelden bij de portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Een firewall configureren

1. Klik op de server om de overzichtspagina te openen. 
2. Klik in **instellingen** > **firewall** > **firewall inschakelen**op **aan**.
3. Klik op **Aan** bij **Toegang toestaan vanuit Power BI** om toegang tot DirectQuery toe te staan vanuit Power BI-service.  
4. (Optioneel) Geef een of meer IP-adresbereiken op. Typ een naam, begin- en eind-IP-adressen voor elk bereik. De naam van de firewall regel mag Maxi maal 128 tekens lang zijn en mag alleen bestaan uit hoofd letters, kleine letters, cijfers, onderstrepings tekens en afbreek streepjes. Spaties en andere speciale tekens zijn niet toegestaan.
5. Klik op **Opslaan**.

     ![Firewallinstellingen](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig zijn, verwijdert u de IP-adresbereiken of schakelt u de firewall uit.

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u geleerd hoe u een firewall configureert voor uw server. Nu dat u een server hebt en deze hebt beveiligd met een firewall, kunt u een eenvoudig voorbeeldgegevensmodel vanuit de portal toevoegen. Een voorbeeldmodel is handig als u meer wilt weten over het configureren van modeldatabaserollen en het testen van clientverbindingen. Als u meer wilt weten, gaat u verder met de zelfstudie waarin u leert een voorbeeldmodel toe te voegen.

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorbeeldmodel toevoegen aan uw server](analysis-services-create-sample-model.md)
