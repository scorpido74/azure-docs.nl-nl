---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c44effe0bde3c7e880e53706fcb59d91a8605e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176086"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>De minimale StorSimple-apparaatinstelling voltooien

   > [!NOTE]
   > U kunt de naam van het apparaat niet wijzigen nadat de minimale apparaatinstelling is voltooid.
   
1. Selecteer en klik op uw apparaat in de lijst in tabelvorm met apparaten op de blade **Apparaten**. Het apparaat heeft de status **Gereed voor configuratie**. De blade **Apparaat configureren** wordt geopend.

     ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Doe het volgende op de blade **Apparaat** configureren:
   
   1. Geef een **beschrijvende naam** voor het apparaat op. De standaardapparaatnaam bevat informatie zoals het model en serienummer van het apparaat. U kunt een beschrijvende naam van maximaal 64 tekens toewijzen om uw apparaat te beheren.
   2. Stel de **tijdzone** in op basis van de geografische regio waarin het apparaat wordt geïmplementeerd. Het apparaat gebruikt deze tijdzone gebruiken voor alle geplande bewerkingen.
   3. Onder de **DATA 0-INSTELLINGEN**:

       1. Uw DATA 0-netwerkinterface wordt weergegeven als ingeschakeld met de netwerkinstellingen (IP, subnet, gateway) geconfigureerd via de installatiewizard. DATA 0 wordt ook automatisch ingeschakeld voor de cloud, evenals voor iSCSI.

       2. Geef de vaste IP-adressen voor Controller 0 en Controller 1 op. **De vaste IP-adressen voor de controllers moeten beschikbare IP-adressen binnen het subnet zijn die toegankelijk zijn voor het IP-adres van het apparaat.** Als de DATA 0-interface is geconfigureerd voor IPv4, moeten de vaste IP-adressen worden opgegeven in de IPv4-indeling. Als u een voorvoegsel voor IPv6-configuratie hebt opgegeven, worden de vaste IP-adressen automatisch in deze velden ingevuld.

            ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            De vaste IP-adressen voor de controller worden gebruikt voor het uitvoeren van de updates op het apparaat en voor garbagecollection. Daarom moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet. U kunt controleren of uw vaste IP-adressen voor de controller routeerbaar zijn met de cmdlet [Test-HcsmConnection][Test]. In het volgende voorbeeld is te zien dat de vaste IP-adressen voor de controller worden doorgestuurd naar internet en dat deze toegang hebben tot de Microsoft Update-servers.

            ![Test-HcsmConnection met routeerbare IP-adressen](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klik op **OK**. De apparaatconfiguratie wordt gestart. Wanneer de configuratie is voltooid, krijgt u een melding. De status van het apparaat verandert in **Online** op de blade **Apparaten**.

    ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx
