---
title: StorSimple Virtual array inrichten in VMware
description: In deze tweede zelf studie in StorSimple Virtual array implementation Series wordt een virtueel apparaat ingericht in VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76272095"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple Virtual array implementeren-inrichten in VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelf studie wordt beschreven hoe u een virtuele StorSimple-matrix inricht en verbindt op een hostsysteem met VMware ESXi 5,0, 5,5, 6,0 of 6,5. Dit artikel is van toepassing op de implementatie van StorSimple virtuele arrays in Azure Portal en de Microsoft Azure Government Cloud.

U hebt beheerdersmachtigingen nodig voor het inrichten van een virtueel apparaat en om er verbinding mee te maken. De inrichting en de initiële installatie kan ongeveer tien minuten duren.

## <a name="provisioning-prerequisites"></a>Vereisten inrichten
De vereisten voor het inrichten van een virtueel apparaat op een hostsysteem met VMware ESXi 5,0, 5,5, 6,0 of 6,5, zijn als volgt.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen [voor het voorbereiden van de virtuele StorSimple-matrix in de portal](storsimple-virtual-array-deploy1-portal-prep.md)voltooid.
* U hebt de installatie kopie van het virtuele apparaat voor VMware gedownload van de Azure Portal. Zie voor meer informatie **stap 3: de installatie kopie van het virtuele apparaat downloaden** van [de hand leiding voor het maken van de portal voor StorSimple Virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Voor het virtuele StorSimple-apparaat
Voordat u een virtueel apparaat implementeert, controleert u of:

* U hebt toegang tot een hostsysteem waarop Hyper-V (2008 R2 of hoger) wordt uitgevoerd en dat kan worden gebruikt om een apparaat in te richten.
* Het hostsysteem kan de volgende resources reserveren voor het inrichten van uw virtuele apparaat:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor systeem gegevens.

### <a name="for-the-network-in-datacenter"></a>Voor het netwerk in het datacenter
Zorg voordat u begint voor het volgende:

* U hebt de netwerk vereisten voor het implementeren van een virtueel StorSimple-apparaat gecontroleerd en het Data Center-netwerk volgens de vereisten geconfigureerd. 

## <a name="step-by-step-provisioning"></a>Stap-voor-stap inrichting
Als u een virtueel apparaat wilt inrichten en er verbinding mee wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem voldoende bronnen heeft om te voldoen aan de minimale vereisten voor virtuele apparaten.
2. Een virtueel apparaat inrichten in uw Hyper Visor.
3. Start het virtuele apparaat en haal het IP-adres op.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Stap 1: controleren of het hostsysteem voldoet aan de minimale vereisten voor virtuele apparaten
Als u een virtueel apparaat wilt maken, hebt u het volgende nodig:

* Toegang tot een hostsysteem met VMware ESXi Server 5,0, 5,5, 6,0 of 6,5.
* Een VMware vSphere-client op uw systeem voor het beheren van de ESXi-host.

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface die is verbonden met het netwerk en verkeer naar internet kan routeren. De minimale Internet bandbreedte moet 5 Mbps zijn om optimaal te kunnen werken met het apparaat.
  * Een virtuele schijf van 500 GB voor gegevens.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Stap 2: een virtueel apparaat inrichten in Hyper Visor
Voer de volgende stappen uit voor het inrichten van een virtueel apparaat in de hypervisor.

1. Kopieer de installatiekopie van het virtuele apparaat naar uw systeem. U hebt deze virtuele installatie kopie gedownload via de Azure Portal.

   1. Zorg ervoor dat u het meest recente afbeeldings bestand hebt gedownload. Als u de afbeelding eerder hebt gedownload, downloadt u deze opnieuw om te controleren of u over de meest recente installatie kopie beschikt. De nieuwste afbeelding heeft twee bestanden (in plaats van één).
   2. Noteer de locatie waar u de installatiekopie naar hebt gekopieerd, want u gaat deze installatiekopie verderop in de procedure gebruiken.

2. Meld u aan bij de ESXi-server met behulp van de vSphere-client. U moet beheerdersbevoegdheden hebben om een virtuele machine te kunnen maken.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Selecteer in de vSphere-client in de sectie inventaris in het linkerdeel venster de ESXi-server.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Upload de VMDK naar de ESXi-server. Ga naar het tabblad **configuratie** in het rechterdeel venster. Onder **Hardware**selecteert u **opslag**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. In het rechterdeelvenster bij **Gegevensopslag** selecteert u de gegevensopslag waarin u de VMDK wilt uploaden. Het gegevens archief moet voldoende beschik bare ruimte hebben voor het besturings systeem en de gegevens schijven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klik met de rechtermuisknop op **Browsen in de gegevensopslag** en selecteer deze.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Een venster met de **gegevensopslagbrowser** wordt weergegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Klik in de werk balk op ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) pictogram om een nieuwe map te maken. Geef de mapnaam op en noteer deze. U gebruikt deze mapnaam later bij het maken van een virtuele machine (aanbevolen best practices). Klik op **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. De nieuwe map wordt weergegeven in het linkerdeelvenster van de **gegevensopslagbrowser**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klik op het pictogram voor uploaden ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) en selecteer **bestand uploaden**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Blader en wijs naar de VMDK-bestanden die u hebt gedownload. Er zijn twee bestanden. Selecteer een bestand dat u wilt uploaden.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klik op **Openen**. Het uploaden van het VMDK-bestand met de opgegeven gegevensopslag wordt gestart. Het duurt enkele minuten om het bestand te uploaden.
13. Nadat het uploaden is voltooid, ziet u het bestand in de gegevensopslag in de map die u hebt gemaakt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Upload nu het tweede VMDK-bestand naar dezelfde gegevensopslag.
14. Ga terug naar het venster van de vSphere-client. Als ESXi-server is geselecteerd, klikt u met de rechter muisknop en selecteert u **nieuwe virtuele machine**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Er wordt een nieuw venster voor de **virtuele machine maken** weer gegeven. Selecteer op de pagina **configuratie** de optie **aangepast** . Klik op **Volgende**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Geef op de pagina **naam en locatie** de naam van uw virtuele machine op. Deze naam moet overeenkomen met de mapnaam (aanbevolen best practice) die u eerder hebt opgegeven in stap 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Selecteer op de pagina **opslag** een gegevens opslag die u wilt gebruiken om uw vm in te richten.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Selecteer op de pagina **versie van virtuele machine** de versie van de **virtuele machine: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Op de pagina **gast besturingssysteem** selecteert u het **gast besturingssysteem** als **Windows**. Selecteer voor **versie**in de vervolg keuzelijst de optie **micro soft Windows Server 2012 (64-bits)** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Pas op de pagina **cpu's** het **aantal virtuele sockets** en het **aantal kernen per virtuele socket** aan zodat het **totale aantal kernen** 4 (of meer) is. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Geef op de pagina **geheugen** 8 GB (of meer) RAM-geheugen op. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Geef op de pagina **netwerk** het aantal netwerk interfaces op. De minimale vereiste is één netwerk interface.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Accepteer op de pagina **SCSI-controller** de standaard **LSI Logic SAS-controller**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Kies op de pagina **een schijf selecteren** de optie **een bestaande virtuele schijf gebruiken**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Klik op de pagina **bestaande schijf selecteren** onder **schijf bestands pad**op **Bladeren**. Hiermee opent u het dialoog venster **Bladeren in data stores** . Navigeer naar de locatie waar u de VMDK hebt geüpload. U ziet nu slechts één bestand in de gegevens opslag als de twee bestanden die u in eerste instantie hebt geüpload, zijn samengevoegd. Selecteer het bestand en klik op **OK**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Accepteer de standaard instellingen op de pagina **Geavanceerde opties** en klik op **volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Op de pagina **Gereed om te voltooien** controleert u alle instellingen voor de nieuwe virtuele machine. Controleer **de instellingen voor de virtuele machine voordat u de bewerking hebt voltooid**. Klik op **Doorgaan**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Zoek op de pagina **virtual machines eigenschappen** op het tabblad **Hardware** de hardware van het apparaat. Selecteer **nieuwe harde schijf**. Klik op **Add**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. U ziet het venster **Hardware toevoegen** . Selecteer op de pagina **type apparaat** onder **Kies het type apparaat dat u wilt toevoegen de**optie **harde schijf**en klik op **volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Kies op de pagina **een schijf selecteren** de optie **een nieuwe virtuele schijf maken**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Op de pagina **een schijf maken** wijzigt u de **schijf grootte** in 500 GB (of meer). Hoewel 500 GB de minimale vereiste is, kunt u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet kunt uitbreiden of verkleinen wanneer deze eenmaal is ingericht. Raadpleeg de sectie grootte in het [document best practices](storsimple-ova-best-practices.md)voor meer informatie over de grootte van de schijf die moet worden ingericht. Onder **schijf inrichting**selecteert u **Thin Provisioning**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Accepteer de standaard instellingen op de pagina **Geavanceerde opties** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Controleer op de pagina **gereed om te volt ooien** de schijf opties. Klik op **Voltooien**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Ga terug naar de pagina eigenschappen van de virtuele machine. Er wordt een nieuwe harde schijf toegevoegd aan uw virtuele machine. Klik op **Voltooien**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Als uw virtuele machine in het rechterdeel venster is geselecteerd, gaat u naar het tabblad **samen vatting** . Controleer de instellingen voor de virtuele machine.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Uw virtuele machine is nu ingericht. De volgende stap is om deze machine te laten werken en om het IP-adres op te halen.

> [!NOTE]
> U wordt aangeraden VMware-hulpprogram ma's niet te installeren op uw virtuele array (zoals hierboven ingericht). Als u VMware-hulpprogramma's installeert, leidt dat tot een niet-ondersteunde configuratie.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Stap 3: het virtuele apparaat starten en het IP-adres ophalen
Voer de volgende stappen uit om uw virtuele apparaat te starten en verbinding te maken met het apparaat.

#### <a name="to-start-the-virtual-device"></a>Het virtuele apparaat starten
1. Start het virtuele apparaat. Selecteer uw apparaat in de vSphere Configuration Manager in het linkerdeel venster en klik met de rechter muisknop om het context menu weer te geven. Selecteer **Aanzetten** en selecteer vervolgens **Inschakelen**. Nu zou uw virtuele machine moeten zijn ingeschakeld. U kunt de status bekijken in het deel venster met de onderste **recente taken** van de vSphere-client.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Het duurt enkele minuten voordat de installatie taken zijn voltooid. Wanneer het apparaat wordt uitgevoerd, gaat u naar het tabblad **console** . Ctrl + Alt + Delete verzenden om u aan te melden bij het apparaat. U kunt ook de cursor naar het console venster wijzen en op CTRL + ALT + INSERT drukken. De standaard gebruiker is *StorSimpleAdmin* en het standaard wachtwoord is *Wachtwoord1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Voer een wachtwoord in van minimaal acht tekens. Het wacht woord moet drie van de vier van de volgende vereisten bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. Er wordt een melding weer gegeven dat het wacht woord is gewijzigd.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Nadat het wacht woord is gewijzigd, wordt het virtuele apparaat mogelijk opnieuw opgestart. Wacht tot de computer opnieuw is opgestart. De Windows Power shell-console van het apparaat kan samen met een voortgangs balk worden weer gegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, wordt het volgende scherm weer gegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Configureer vervolgens het netwerk.
7. Gebruik de opdracht `Get-HcsIpAddress` om de netwerk interfaces weer te geven die zijn ingeschakeld op het virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Hieronder kunt u een voorbeeld bekijken:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Wanneer de initiële installatie is voltooid en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die in de bannertekst worden weergegeven om het apparaat te beheren. U gebruikt dit IP-adres om verbinding te maken met de Web-UI van uw virtuele apparaat en de lokale installatie en registratie te volt ooien.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Beschrijving Voer deze stap alleen uit als u uw apparaat in de Government Cloud implementeert. U schakelt nu de FIPS-modus (Verenigde Staten Federal Information Processing Standard) op het apparaat in. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse Federal Government-computer systemen voor de beveiliging van gevoelige gegevens.

    1. Als u de FIPS-modus wilt inschakelen, voert u de volgende cmdlet uit:

        `Enable-HcsFIPSMode`
    2. Start het apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U kunt de FIPS-modus op het apparaat in-of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratievereisten, wordt er een fout weergegeven in de bannertekst (zie hieronder). U moet de apparaatconfiguratie wijzigen zodat er voldoende resources zijn om aan de minimale vereisten te voldoen. U kunt het apparaat vervolgens opnieuw opstarten en er verbinding mee maken. Raadpleeg de minimale configuratievereisten in [Stap 1: Controleren of het hostsysteem voldoet aan minimale vereisten voor virtuele apparaten](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Als u tijdens de eerste configuratie een andere fout ondervindt met de lokale webgebruikersinterface, raadpleegt u de volgende werk stromen:

* Diagnostische tests uitvoeren voor het [oplossen van problemen met de Web-UI-installatie](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* Een [logboek bestand genereren en logboek bestanden weer geven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Stel uw virtuele StorSimple-matrix in als een bestands server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Stel uw virtuele StorSimple-matrix in als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
