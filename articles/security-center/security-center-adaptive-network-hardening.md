---
title: Adaptieve netwerk beveiliging in Azure Security Center | Microsoft Docs
description: Meer informatie over het gebruik van werkelijke verkeers patronen om uw NSG-regels (netwerk beveiligings groepen) te beveiligen en uw beveiligings postuur verder te verbeteren.
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
ms.openlocfilehash: 1f69fe027772dc2d008a567723a5b3c04f3ee51b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378199"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptieve netwerk beveiliging in Azure Security Center
Meer informatie over het configureren van adaptieve netwerk beveiliging in Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Wat is adaptieve netwerk beveiliging?
Het Toep assen van [netwerk beveiligings groepen (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) voor het filteren van verkeer naar en van resources verbetert uw netwerk beveiligings postuur. Er kunnen echter wel enkele gevallen zijn waarin het daad werkelijke verkeer dat via de NSG stroomt, een subset is van de gedefinieerde NSG-regels. In dergelijke gevallen kunt u de beveiligings postuur verder verbeteren door de NSG-regels te verfijnen op basis van de werkelijke verkeers patronen.

Adaptieve netwerk beveiliging biedt aanbevelingen voor verdere beveiliging van de NSG-regels. Er wordt gebruikgemaakt van een machine learning algoritme dat betrekking heeft op het werkelijke verkeer, bekende vertrouwde configuratie, bedreigings informatie en andere indica toren van inbreuk, en geeft aanbevelingen om alleen verkeer van specifieke IP-poort-Tuples toe te staan.

Stel bijvoorbeeld dat de bestaande NSG-regel verkeer van 140.20.30.10/24 op poort 22 toestaat. De aanbeveling voor adaptieve netwerk beveiliging, op basis van de analyse, zou het bereik moeten beperken en verkeer van 140.23.30.10/29 kunnen toestaan. Dit is een smallere IP-bereik en weigert al het andere verkeer naar die poort.

>[!TIP]
> Aanbevelingen voor adaptieve netwerk beveiliging worden alleen ondersteund op de volgende specifieke poorten (voor UDP en TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Weer gave netwerk beveiliging](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemene beschikbaarheid|
|Koers|Standaardlaag|
|Vereiste rollen en machtigingen:|Schrijf machtigingen op de Nsg's van de computer|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Waarschuwingen en regels voor adaptieve netwerk beveiliging weer geven

1. Selecteer in Security Center **netwerk**  ->  **adaptief netwerk beveiliging**. De netwerk-Vm's worden weer gegeven onder drie afzonderlijke tabbladen:
   * **Slechte resources**: vm's die momenteel aanbevelingen en waarschuwingen hebben geactiveerd door het uitvoeren van het adaptieve netwerk beveiligings algoritme. 
   * **Gezonde resources**: vm's zonder waarschuwingen en aanbevelingen.
   * Niet- **gescande resources**: vm's waarvoor het adaptieve netwerk beveiligings algoritme niet kan worden uitgevoerd om een van de volgende redenen:
      * **Vm's zijn klassieke vm's**: alleen Azure Resource Manager vm's worden ondersteund.
      * **Er zijn onvoldoende gegevens beschikbaar**: voor het genereren van nauw keurige aanbevelingen voor het beveiligen van de beveiliging is Security Center ten minste 30 dagen aan verkeers gegevens vereist.
      * **VM wordt niet beveiligd door ASC Standard**: alleen vm's die zijn ingesteld op de prijs categorie standard van Security Center, komen in aanmerking voor deze functie.

     ![beschadigde resources](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Op het tabblad **beschadigde bronnen** selecteert u een virtuele machine om de waarschuwingen en de aanbevolen beveiligings regels weer te geven die moeten worden toegepast.

    ![beveiligings waarschuwingen](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Aanbevolen regels voor adaptieve netwerk beveiliging controleren en Toep assen

1. Selecteer een virtuele machine op het tabblad **beschadigde resources** . De waarschuwingen en aanbevolen regels voor de beveiliging worden weer gegeven.

     ![beveiligings regels](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Het tabblad **regels** bevat een lijst met de regels die adaptieve netwerk beveiliging aanbeveelt. Op het tabblad **waarschuwingen** worden de waarschuwingen weer gegeven die zijn gegenereerd als gevolg van verkeer, stromen naar de resource, die niet binnen het IP-bereik vallen dat is toegestaan in de aanbevolen regels.

2. Als u een aantal para meters van een regel wilt wijzigen, kunt u deze wijzigen, zoals wordt uitgelegd in [een regel wijzigen](#modify-rule).
   > [!NOTE]
   > U kunt ook een regel [verwijderen](#delete-rule) of [toevoegen](#add-rule) .

3. Selecteer de regels die u wilt Toep assen op de NSG en klik op **afdwingen**.

      > [!NOTE]
      > De afgedwongen regels worden toegevoegd aan de NSG (s) die de virtuele machine beveiligen. (Een virtuele machine kan worden beveiligd door een NSG die is gekoppeld aan de NIC of het subnet waarin de virtuele machine zich bevindt, of beide)

    ![regels afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Een regel <a name ="modify-rule"> </a> wijzigen

Mogelijk wilt u de para meters van een regel die is aanbevolen wijzigen. Het is bijvoorbeeld mogelijk dat u de aanbevolen IP-bereiken wilt wijzigen.

Enkele belang rijke richt lijnen voor het wijzigen van een adaptieve netwerk beperkings regel:

* U kunt de para meters van alleen regels wijzigen. 
* U kunt regels voor ' toestaan ' niet wijzigen zodat regels worden ' deny '. 

  > [!NOTE]
  > Het maken en wijzigen van regels voor weigeren wordt rechtstreeks uitgevoerd op het NSG. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)voor meer informatie.

* Een regel voor het **weigeren van alle verkeer** is het enige type ' deny ' dat hier wordt vermeld en kan niet worden gewijzigd. U kunt het echter wel verwijderen (Zie [een regel verwijderen](#delete-rule)).
  > [!NOTE]
  > Een regel voor het weigeren van een **netwerk** wordt aanbevolen wanneer als gevolg van het uitvoeren van het Security Center algoritme geen verkeer wordt geïdentificeerd dat moet worden toegestaan op basis van de bestaande NSG-configuratie. Daarom is de aanbevolen regel het weigeren van al het verkeer naar de opgegeven poort. De naam van dit type regel wordt weer gegeven als '*systeem gegenereerd*'. Na het afdwingen van deze regel is de daad werkelijke naam in de NSG een teken reeks die bestaat uit het Protocol, de richting van het verkeer, de "weigeren" en een wille keurig getal.

*Een regel voor adaptieve netwerk beveiliging wijzigen:*

1. Als u een aantal para meters van een regel wilt wijzigen, klikt u op het tabblad **regels** op de drie puntjes (...) aan het einde van de rij van de regel en klikt u op **bewerken**.

   ![regel bewerken](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. In het venster **regel bewerken** werkt u de details bij die u wilt wijzigen en klikt u op **Opslaan**.

   > [!NOTE]
   > Nadat u op **Opslaan**hebt geklikt, is de regel gewijzigd. *U hebt deze echter niet toegepast op de NSG.* Als u deze wilt Toep assen, moet u de regel in de lijst selecteren en op **afdwingen** klikken (zoals in de volgende stap wordt uitgelegd).

   ![regel bewerken](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Als u de bijgewerkte regel wilt Toep assen, selecteert u in de lijst de bijgewerkte regel en klikt u op **afdwingen**.

    ![regel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Een nieuwe regel <a name ="add-rule"> </a> toevoegen

U kunt een regel ' toestaan ' toevoegen die niet wordt aanbevolen door Security Center.

> [!NOTE]
> U kunt hier alleen regels voor ' toestaan ' toevoegen. Als u regels voor weigeren wilt toevoegen, kunt u dit rechtstreeks doen op het NSG. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)voor meer informatie.

*Een regel voor adaptieve netwerk beveiliging toevoegen:*

1. Klik op **regel toevoegen** (bevindt zich in de linkerbovenhoek).

   ![regel toevoegen](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Voer in het venster **nieuwe regel** de details in en klik op **toevoegen**.

   > [!NOTE]
   > Nadat u op **toevoegen**hebt geklikt, is de regel toegevoegd en wordt deze weer gegeven met de andere aanbevolen regels. U hebt deze echter niet toegepast op de NSG. Als u deze wilt activeren, moet u de regel in de lijst selecteren en op **afdwingen** klikken (zoals in de volgende stap wordt uitgelegd).

3. Als u de nieuwe regel wilt Toep assen, selecteert u de nieuwe regel in de lijst en klikt u op **afdwingen**.

    ![regel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Een regel <a name ="delete-rule"> </a> verwijderen

Als dat nodig is, kunt u een aanbevolen regel voor de huidige sessie verwijderen. U kunt bijvoorbeeld bepalen dat het Toep assen van een voorgestelde regel legitiem verkeer kan blok keren.

*Een regel voor het beveiligen van een adaptieve netwerk beveiliging voor uw huidige sessie verwijderen:*

1. Klik op het tabblad **regels** op de drie puntjes (...) aan het einde van de rij van de regel en klik op **verwijderen**.  

    ![beveiligings regels](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)