---
title: Failover en disaster recovery voor StorSimple Virtual Array
description: Meer informatie over hoe u uw StorSimple Virtual Array failoveren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467212"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Herstel na noodgevallen en failover van apparaat voor virtuele StorSimple-array via Azure-portal

## <a name="overview"></a>Overzicht
In dit artikel wordt het noodherstel voor uw Microsoft Azure StorSimple Virtual Array beschreven, inclusief de gedetailleerde stappen om niet naar een andere virtuele array te gaan. Met een failover u uw gegevens verplaatsen van een *bronapparaat* in het datacenter naar een *doelapparaat.* Het doelapparaat kan zich op dezelfde of een andere geografische locatie bevinden. De failover van het apparaat is voor het hele apparaat. Tijdens failover worden de cloudgegevens voor het bronapparaat van eigenaar veranderd naar dat van het doelapparaat.

Dit artikel is alleen van toepassing op StorSimple Virtual Arrays. Als u niet wilt falen boven een apparaat uit de 8000-serie, gaat u naar [Apparaatfailover en herstel na noodgevallen van uw StorSimple-apparaat.](storsimple-device-failover-disaster-recovery.md)

## <a name="what-is-disaster-recovery-and-device-failover"></a>Wat is disaster recovery en device failover?

In een DR-scenario (disaster recovery) werkt het primaire apparaat niet meer. In dit scenario u de cloudgegevens die aan het mislukte apparaat zijn gekoppeld, verplaatsen naar een ander apparaat. U het primaire apparaat als *bron* gebruiken en een ander apparaat als *doel*opgeven. Dit proces wordt aangeduid als de *failover*. Tijdens failover veranderen alle volumes of de aandelen van het bronapparaat van eigenaar en worden ze overgebracht naar het doelapparaat. Het filteren van de gegevens is niet toegestaan.

DR is gemodelleerd als een volledig apparaat te herstellen met behulp van de heat map-based tiering en tracking. Een heatmap wordt gedefinieerd door een warmtewaarde toe te kennen aan de gegevens op basis van lees- en schrijfpatronen. Met deze heatmap worden vervolgens eerst de laagste warmtegegevensbrokken naar de cloud gelaagd terwijl de gegevenssegmenten met hoge warmte (meest gebruikte) in de lokale laag blijven. Tijdens een DR gebruikt StorSimple de warmtekaart om de gegevens uit de cloud te herstellen en te hydrateren. Het apparaat haalt alle volumes/aandelen op in de laatste recente back-up (zoals intern bepaald) en voert een herstel uit van die back-up. De virtuele array orkestreert het hele DR-proces.

> [!IMPORTANT]
> Het bronapparaat wordt verwijderd aan het einde van de failover van het apparaat en daarom wordt een failback niet ondersteund.
> 
> 

Disaster recovery wordt georkestreerd via de failoverfunctie van het apparaat en wordt gestart vanaf het **blade Devices.** Dit blad tabulates alle StorSimple apparaten aangesloten op uw StorSimple Device Manager service. Voor elk apparaat ziet u de vriendelijke naam, status, ingerichte en maximale capaciteit, type en model.

## <a name="prerequisites-for-device-failover"></a>Vereisten voor apparaatfailover

### <a name="prerequisites"></a>Vereisten

Voor een failover van een apparaat moet u ervoor zorgen dat aan de volgende voorwaarden is voldaan:

* Het bronapparaat moet in **de uitgeschakelde** status staan.
* Het doelapparaat moet worden weergegeven als klaar om in de Azure-portal **in te stellen.** Een doelvirtuele array van dezelfde of hogere capaciteit inrichten. Gebruik de lokale web-gebruikersinterface om de virtuele array van het doel te configureren en met succes te registreren.
  
  > [!IMPORTANT]
  > Probeer het geregistreerde virtuele apparaat niet via de service te configureren. Er mag geen apparaatconfiguratie worden uitgevoerd via de service.
  > 
  > 
* Het doelapparaat kan niet dezelfde naam hebben als het bronapparaat.
* De bron en het doelapparaat moeten hetzelfde type zijn. U alleen mislukken via een virtuele array die is geconfigureerd als bestandsserver naar een andere bestandsserver. Hetzelfde geldt voor een iSCSI-server.
* Voor een DR van bestandsserver raden we u aan het doelapparaat samen te voegen met hetzelfde domein als de bron. Deze configuratie zorgt ervoor dat de machtigingen voor delen automatisch worden opgelost. Alleen de failover naar een doelapparaat in hetzelfde domein wordt ondersteund.
* De beschikbare doelapparaten voor DR zijn apparaten met dezelfde of grotere capaciteit in vergelijking met het bronapparaat. De apparaten die zijn aangesloten op uw service, maar niet voldoen aan de criteria van voldoende ruimte zijn niet beschikbaar als doelapparaten.

### <a name="other-considerations"></a>Andere overwegingen

* Voor een geplande failover:
  
  * We raden u aan alle volumes of aandelen op het bronapparaat offline te halen.
  * We raden u aan een back-up van het apparaat te maken en vervolgens verder te gaan met de failover om gegevensverlies te minimaliseren.
* Voor een ongeplande failover gebruikt het apparaat de meest recente back-up om de gegevens te herstellen.

### <a name="device-failover-prechecks"></a>Apparaatfailover prechecks

Voordat de DR begint, voert het apparaat prechecks uit. Deze controles helpen ervoor te zorgen dat er geen fouten optreden wanneer DR begint. De prechecks omvatten:

* Het valideren van de opslagrekening.
* De cloudconnectiviteit met Azure controleren.
* Controleer de beschikbare ruimte op het doelapparaat.
* Controleren of het volume van een iSCSI-serverbronapparaat
  
  * geldige ACR-namen.
  * geldige IQN (niet meer dan 220 tekens).
  * geldige CHAP-wachtwoorden (12-16 tekens lang).

Als een van de voorgaande voorcontroles mislukt, u niet verder gaan met de DR. Los deze problemen op en probeer DR opnieuw.

Nadat de DR is voltooid, wordt het eigendom van de cloudgegevens op het bronapparaat overgebracht naar het doelapparaat. Het bronapparaat is dan niet meer beschikbaar in de portal. Toegang tot alle volumes/shares op het bronapparaat wordt geblokkeerd en het doelapparaat wordt actief.

> [!IMPORTANT]
> Hoewel het apparaat niet meer beschikbaar is, verbruikt de virtuele machine die u op het hostsysteem hebt ingericht, nog steeds resources. Zodra de DR is voltooid, u deze virtuele machine uit uw hostsysteem verwijderen.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Overgaan naar een virtuele array

We raden u aan een andere StorSimple Virtual Array in te richten, te configureren en te registreren bij uw StorSimple Device Manager-service voordat u deze procedure uitvoert.

> [!IMPORTANT]
> 
> * U niet mislukken van een StorSimple 8000-serie apparaat naar een 1200 virtueel apparaat.
> * U mislukken van een virtual device (Federal Information Processing Standard) naar een ander FIPS-apparaat of naar een niet-FIPS-apparaat dat is geïmplementeerd in de portal van de overheid.


Voer de volgende stappen uit om het apparaat te herstellen naar een virtueel apparaat van doelstorSimple.

1. Een doelapparaat inrichten en configureren dat voldoet aan de [vereisten voor het mislukken van het apparaat.](#prerequisites) Voltooi de apparaatconfiguratie via de lokale webgebruikersinterface en registreer deze bij uw StorSimple Device Manager-service. Als u een bestandsserver maakt, gaat u naar stap 1 van [het instellen als bestandsserver.](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device) Als u een iSCSI-server maakt, gaat u naar stap 1 van [het instellen als iSCSI-server.](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)

2. Neem volumes/aandelen offline op de host. Als u de volumes/aandelen offline wilt halen, raadpleegt u de specifieke instructies van het besturingssysteem voor de host. Als u nog niet offline bent, moet u alle volumes/shares offline halen op het apparaat door het volgende te doen.
   
    1. Ga naar Het blad **apparaten** en selecteer uw apparaat.
   
    2. Ga naar **Instellingen > > Shares beheren** (of Instellingen > > volumes **beheren).** 
   
    3. Selecteer een aandeel/volume, klik met de rechtermuisknop en selecteer **Offline nemen**. 
   
    4. Wanneer gevraagd om bevestiging, controleer **ik begrijp de impact van het nemen van dit aandeel offline.** 
   
    5. Klik **op Offline halen**.

3. Ga in uw StorSimple Device Manager-service naar **Beheer > Apparaten**. Selecteer en klik in het blade **Apparaten** op uw bronapparaat.

4. Klik in het **dashboardblad van het apparaat** op **Deactiveren**.

5. In het **mes deactiveren** wordt u om bevestiging gevraagd. Apparaatdeactivering is een *permanent* proces dat niet ongedaan kan worden gemaakt. U wordt er ook aan herinnerd om uw aandelen/volumes offline te halen op de host. Typ de apparaatnaam om te bevestigen en klik op **Deactiveren**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. De deactivering begint. U ontvangt een melding nadat de deactivering is voltooid.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Op de pagina Apparaten wordt de apparaatstatus nu gewijzigd in **Gedeactiveerd**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Selecteer en klik in het blade **Apparaten** op het gedeactiveerde bronapparaat voor failover. 
9. Klik in het **dashboardblad apparaat** op **Fail over**. 
10. Ga als volgt te werk in het mes **Van** het apparaat falen:
    
    1. Het veld bronapparaat wordt automatisch ingevuld. Let op de totale gegevensgrootte voor het bronapparaat. De gegevensgrootte moet kleiner zijn dan de beschikbare capaciteit op het doelapparaat. Controleer de details die zijn gekoppeld aan het bronapparaat, zoals de naam van het apparaat, de totale capaciteit en de namen van de shares die zijn mislukt.

    2. Kies in de vervolgkeuzelijst met beschikbare apparaten een **doelapparaat**. Alleen de apparaten met voldoende capaciteit worden weergegeven in de vervolgkeuzelijst.

    3. Controleer of **ik begrijp dat deze bewerking zal mislukken over gegevens naar het doelapparaat**. 

    4. Klik **op Mislukken over**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Een failovertaak initieert en u ontvangt een melding. Ga naar **Apparaten >-jobs** om de failover te controleren.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. In het blade **Jobs** ziet u een failovertaak die is gemaakt voor het bronapparaat. Deze taak voert de DR-voorcontroles uit.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Nadat de DR-voorcontroles zijn geslaagd, spawnt de failovertaak hersteltaken voor elk aandeel/volume dat op uw bronapparaat bestaat.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Nadat de failover is voltooid, gaat u naar het **apparaatblad.**
    
    1. Selecteer en klik op het StorSimple-apparaat dat is gebruikt als doelapparaat voor het failoverproces.
    2. Ga naar **Instellingen > Beheer > Aandelen** (of **Volumes** als iSCSI-server). In het **blad Aandelen** u alle aandelen (volumes) bekijken vanaf het oude apparaat.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. U moet [een DNS-alias maken,](https://support.microsoft.com/kb/168322) zodat alle toepassingen die verbinding proberen te maken, naar het nieuwe apparaat kunnen worden doorgestuurd.

## <a name="errors-during-dr"></a>Fouten tijdens DR

**Storing in cloudconnectiviteit tijdens DR**

Als de cloudverbinding wordt verstoord nadat DR is gestart en voordat het apparaat is hersteld, mislukt de DR. U ontvangt een melding van een storing. Het doelapparaat voor DR is gemarkeerd als *onbruikbaar.* U niet hetzelfde doelapparaat gebruiken voor toekomstige Dr.R.T.Z.

**Geen compatibele doelapparaten**

Als de beschikbare doelapparaten niet voldoende ruimte hebben, ziet u een fout dat er geen compatibele doelapparaten zijn.

**Precheck-fouten**

Als een van de voorcontroles niet is voldaan, ziet u precheck-fouten.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business continuity disaster recovery (BCDR)

Een BCDR-scenario (Business Continuity disaster recovery) treedt op wanneer het hele Azure-datacenter niet meer functioneert. Dit kan gevolgen hebben voor uw StorSimple Device Manager-service en de bijbehorende StorSimple-apparaten.

Als er StorSimple-apparaten zijn die vlak voor een ramp zijn geregistreerd, moeten deze StorSimple-apparaten mogelijk worden verwijderd. Na de ramp u deze apparaten opnieuw maken en configureren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van uw StorSimple Virtual Array met behulp van de lokale web-gebruikersinterface.](storsimple-ova-web-ui-admin.md)

