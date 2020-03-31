---
title: Adaptive Network Hardening in Azure Security Center | Microsoft Documenten
description: Leer hoe u actuele verkeerspatronen gebruiken om de regels van uw netwerkbeveiligingsgroepen (NSG) te verharden en uw beveiligingshouding verder te verbeteren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385075"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptive Network Hardening in Azure Security Center
Meer informatie over het configureren van Adaptive Network Hardening in Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Wat is Adaptive Network Hardening?
Het toepassen van [netwerkbeveiligingsgroepen (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) om verkeer van en naar resources te filteren, verbetert uw netwerkbeveiligingshouding. Er kunnen echter nog enkele gevallen waarin het werkelijke verkeer stroomt via de NSG is een subset van de NSG regels gedefinieerd. In deze gevallen kan een verdere verbetering van de beveiligingshouding worden bereikt door de NSG-regels te verharden op basis van de werkelijke verkeerspatronen.

Adaptive Network Hardening doet aanbevelingen om de NSG-regels verder te verharden. Het maakt gebruik van een machine learning-algoritme dat factoren in het werkelijke verkeer, bekende vertrouwde configuratie, bedreigingsinformatie en andere indicatoren van compromissen, en biedt vervolgens aanbevelingen om verkeer alleen van specifieke IP / poorttuples mogelijk te maken.

Stel dat de bestaande NSG-regel is om verkeer vanaf 140.20.30.10.10/24 toe te staan op poort 22. De aanbeveling van adaptive network hardening, gebaseerd op de analyse, zou zijn om het bereik te verkleinen en het verkeer toe te staan vanaf 140.23.30.10/29 - dat is een smaller IP-bereik, en ontkennen al het andere verkeer naar die poort.

>[!TIP]
> Adaptive Network Hardening-aanbevelingen worden alleen ondersteund op de volgende specifieke poorten (voor zowel UDP als TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 23019 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Netwerkverhardingsweergave](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Waarschuwingen en regels voor adaptieve netwerkverharding weergeven

1. Selecteer in Security Center **netwerkadaptieve** -> **netwerkverharding**. De netwerk VM's worden vermeld onder drie afzonderlijke tabbladen:
   * **Ongezonde resources:** VM's die momenteel aanbevelingen en waarschuwingen hebben die zijn geactiveerd door het algoritme Adaptive Network Hardening uit te voeren. 
   * **Gezonde bronnen:** VM's zonder waarschuwingen en aanbevelingen.
   * **Niet-gescande resources**: VM's waarop het algoritme Adaptive Network Hardening niet kan worden uitgevoerd vanwege een van de volgende redenen:
      * **VM's zijn klassieke VM's:** alleen Azure Resource Manager VM's worden ondersteund.
      * **Er zijn onvoldoende gegevens beschikbaar**: Om nauwkeurige aanbevelingen voor verkeersverharding te genereren, heeft Security Center ten minste 30 dagen aan verkeersgegevens nodig.
      * **VM wordt niet beschermd door ASC-standaard:** Alleen VM's die zijn ingesteld op de standaardprijslaag van het Beveiligingscentrum komen in aanmerking voor deze functie.

     ![ongezonde bronnen](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Selecteer op het tabblad **Ongezonde resources** een VM om de waarschuwingen en de aanbevolen verhardingsregels weer te geven.

    ![verhardingvan waarschuwingen](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Aanbevolen regels voor Adaptive Network Hardening controleren en toepassen

1. Selecteer op het tabblad **Ongezonde resources** een VM. De waarschuwingen en aanbevolen verhardingsregels worden weergegeven.

     ![verhardingsregels](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Op het tabblad **Regels** worden de regels weergegeven die Adaptive Network Hardening u aanbeveelt toe te voegen. Op het tabblad **Waarschuwingen** worden de waarschuwingen weergegeven die zijn gegenereerd als gevolg van verkeer dat naar de bron stroomt en dat niet binnen het IP-bereik is toegestaan in de aanbevolen regels.

2. Als u een aantal parameters van een regel wilt wijzigen, u deze wijzigen, zoals uitgelegd in [Een regel wijzigen.](#modify-rule)
   > [!NOTE]
   > U ook een regel [verwijderen](#delete-rule) of [toevoegen.](#add-rule)

3. Selecteer de regels die u op de NSG wilt toepassen en klik op **Afdwingen**.

      > [!NOTE]
      > De afgedwongen regels worden toegevoegd aan de NSG(s) bescherming van de VM. (Een VM kan worden beschermd door een NSG die is gekoppeld aan de NIC, of het subnet waarin de VM zich bevindt, of beide)

    ![regels afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Een regel <a name ="modify-rule"> </a> wijzigen

U de parameters van een aanbevolen regel wijzigen. U bijvoorbeeld de aanbevolen IP-bereiken wijzigen.

Enkele belangrijke richtlijnen voor het wijzigen van een adaptieve netwerkverhardingsregel:

* U alleen de parameters van 'toestaan'-regels wijzigen. 
* U niet veranderen toestaan regels te worden ontkennen regels. 

  > [!NOTE]
  > Het maken en wijzigen van "deny" regels gebeurt rechtstreeks op de NSG. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)voor meer informatie.

* Een **regel Alle verkeer weigeren** is het enige type 'weigeren'-regel dat hier wordt weergegeven en deze kan niet worden gewijzigd. U deze echter verwijderen (zie [Een regel verwijderen).](#delete-rule)
  > [!NOTE]
  > Een **regel Alle verkeer weigeren** wordt aanbevolen wanneer beveiligingscentrum als gevolg van het uitvoeren van het algoritme geen verkeer identificeert dat moet worden toegestaan, op basis van de bestaande NSG-configuratie. Daarom is de aanbevolen regel om al het verkeer naar de opgegeven poort te weigeren. De naam van dit type regel wordt weergegeven als "*Systeem gegenereerd*". Na het handhaven van deze regel, zal de werkelijke naam in de NSG een tekenreeks zijn die bestaat uit het protocol, de verkeersrichting, "DENY" en een willekeurig getal.

*Ga als lid van het ief netwerkverharding:*

1. Als u een aantal parameters van een regel wilt wijzigen, klikt u op het tabblad **Regels** op de drie puntjes (...) aan het einde van de rij van de regel en klikt u op **Bewerken**.

   ![regel bewerken](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Werk in het **venster Regel bewerken** de details bij die u wilt wijzigen en klik op **Opslaan**.

   > [!NOTE]
   > Nadat u op **Opslaan**hebt geklikt, hebt u de regel gewijzigd. *U hebt het echter niet toegepast op de NSG.* Als u deze wilt toepassen, moet u de regel in de lijst selecteren en op **Afdwingen** klikken (zoals uitgelegd in de volgende stap).

   ![regel bewerken](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Als u de bijgewerkte regel wilt toepassen, selecteert u in de lijst de bijgewerkte regel en klikt u op **Afdwingen**.

    ![afdwingregel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Een nieuwe <a name ="add-rule"> </a> regel toevoegen

U een regel 'toestaan' toevoegen die niet is aanbevolen door Security Center.

> [!NOTE]
> Alleen "toestaan" regels kunnen hier worden toegevoegd. Als u deny-regels wilt toevoegen, u dit rechtstreeks op de NSG doen. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)voor meer informatie.

*Ga als lid van het ief netwerkverharding:*

1. Klik **op Regel toevoegen** (in de linkerbovenhoek).

   ![regel toevoegen](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Voer in het **venster Nieuwe regel** de details in en klik op **Toevoegen**.

   > [!NOTE]
   > Nadat u op **Toevoegen**hebt geklikt, hebt u de regel toegevoegd en deze wordt weergegeven met de andere aanbevolen regels. U hebt het echter niet toegepast op de NSG. Als u deze wilt activeren, moet u de regel in de lijst selecteren en op **Afdwingen** klikken (zoals uitgelegd in de volgende stap).

3. Als u de nieuwe regel wilt toepassen, selecteert u in de lijst de nieuwe regel en klikt u op **Afdwingen**.

    ![afdwingregel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Een regel <a name ="delete-rule"> </a> verwijderen

Indien nodig u een aanbevolen regel voor de huidige sessie verwijderen. U bijvoorbeeld bepalen dat het toepassen van een voorgestelde regel legitiem verkeer kan blokkeren.

*Ga als lid van het werk om een adaptieve netwerkverhardingsregel voor uw huidige sessie te verwijderen:*

1. Klik op het tabblad **Regels** op de drie puntjes (...) aan het einde van de rij van de regel en klik op **Verwijderen**.  

    ![verhardingsregels](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)