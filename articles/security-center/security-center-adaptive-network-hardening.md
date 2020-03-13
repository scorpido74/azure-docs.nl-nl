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
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139586"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptieve netwerk beveiliging in Azure Security Center
Meer informatie over het configureren van adaptieve netwerk beveiliging in Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Wat is adaptieve netwerk beveiliging?
Het Toep assen van [netwerk beveiligings groepen (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) voor het filteren van verkeer naar en van resources verbetert uw netwerk beveiligings postuur. Er kunnen echter wel enkele gevallen zijn waarin het daad werkelijke verkeer dat via de NSG stroomt, een subset is van de gedefinieerde NSG-regels. In dergelijke gevallen kunt u de beveiligings postuur verder verbeteren door de NSG-regels te verfijnen op basis van de werkelijke verkeers patronen.

Adaptieve netwerk beveiliging biedt aanbevelingen voor verdere beveiliging van de NSG-regels. Er wordt gebruikgemaakt van een machine learning algoritme dat betrekking heeft op het werkelijke verkeer, bekende vertrouwde configuratie, bedreigings informatie en andere indica toren van inbreuk, en geeft aanbevelingen om alleen verkeer van specifieke IP-poort-Tuples toe te staan.

Stel bijvoorbeeld dat de bestaande NSG-regel verkeer van 140.20.30.10/24 op poort 22 toestaat. De aanbeveling voor adaptieve netwerk beveiliging, op basis van de analyse, zou het bereik moeten beperken en verkeer van 140.23.30.10/29 kunnen toestaan. Dit is een smallere IP-bereik en weigert al het andere verkeer naar die poort.

>[!TIP]
> Aanbevelingen voor adaptieve netwerk beveiliging worden alleen ondersteund op specifieke poorten. Zie voor de volledige lijst [welke poorten worden ondersteund?](#which-ports-are-supported) hieronder. 


![Weer gave netwerk beveiliging](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Waarschuwingen en regels voor adaptieve netwerk beveiliging weer geven

1. Selecteer in Security Center **netwerk** -> **adaptieve netwerk beveiliging**. De netwerk-Vm's worden weer gegeven onder drie afzonderlijke tabbladen:
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


### Een regel <a name ="modify-rule"> </a> wijzigen

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

### Een nieuwe regel <a name ="add-rule"> </a> toevoegen

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


### Een regel <a name ="delete-rule"> </a> verwijderen

Als dat nodig is, kunt u een aanbevolen regel voor de huidige sessie verwijderen. U kunt bijvoorbeeld bepalen dat het Toep assen van een voorgestelde regel legitiem verkeer kan blok keren.

*Een regel voor het beveiligen van een adaptieve netwerk beveiliging voor uw huidige sessie verwijderen:*

1. Klik op het tabblad **regels** op de drie puntjes (...) aan het einde van de rij van de regel en klik op **verwijderen**.  

    ![beveiligings regels](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>Welke poorten worden ondersteund?

Aanbevelingen voor adaptieve netwerk beveiliging worden alleen ondersteund op specifieke poorten. Deze tabel bevat de volledige lijst:

|Poort|Protocol|Gekoppelde service|
|:---:|:----:|:----|
|13|UDP|Daytime-service|
|17|UDP|QOTD-Protocol|
|19|UDP|Protocol voor kosten|
|22|TCP|SSH|
|23|TCP|Via|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|Potentieel schadelijk (TOR-eind knooppunt)|
|111|TCP/UDP|RPC|
|119|TCP|NNTP|
|123|UDP|NTP|
|135|TCP/UDP|Endpoint Mapper; RPC'S DCE|
|137|TCP/UDP|NetBIOS-naam service|
|138|TCP/UDP|NetBIOS-data gram service|
|139|TCP|NetBIOS-sessie service|
|161|TCP/UDP|SNMP|
|162|TCP/UDP|SNMP|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|Externe shell|
|593|TCP/UDP|HTTP RPC|
|636|TCP|LDAP|
|873|TCP|Rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|SSDP|
|1900|UDP|SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Compaq-beheer service|
|2323|TCP|3D-nfsd|
|2381|TCP|Compaq-beheer service|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|mDNS|
|5432|TCP|PostgreSQL|
|5555|TCP|Persoonlijke agent; HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|Externe framebuffer; VNC|
|5900|TCP|VNC|
|5985|TCP|Windows Powershell|
|5986|TCP|Windows Powershell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDB Sun-proxy beheerder|
|8089|TCP|Splunk|
|8545|TCP|Mogelijk schadelijk (Cryptominer)|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|Mogelijk schadelijk|
||||