---
title: StorSimple Virtual Array inVMware voorzien
description: Deze tweede zelfstudie in de implementatiereeks StorSimple Virtual Array omvat het inrichten van een virtueel apparaat in VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272095"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple Virtual Array implementeren - Voorziening in VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelfstudie wordt beschreven hoe u een StorSimple Virtual Array inrichten en aansluiten op een hostsysteem met VMware ESXi 5.0, 5.5, 6.0 of 6.5. Dit artikel is van toepassing op de implementatie van StorSimple Virtual Arrays in Azure-portal en de Microsoft Azure Government Cloud.

U hebt beheerdersmachtigingen nodig voor het inrichten van een virtueel apparaat en om er verbinding mee te maken. De inrichting en de initiële installatie kan ongeveer tien minuten duren.

## <a name="provisioning-prerequisites"></a>Voorzieningen
De voorwaarden voor het inrichten van een virtueel apparaat op een hostsysteem met VMware ESXi 5.0, 5.5, 6.0 of 6.5, zijn als volgt.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen voltooid in [Het portaal voorbereiden voor StorSimple Virtual Array.](storsimple-virtual-array-deploy1-portal-prep.md)
* U hebt de virtuele apparaatafbeelding voor VMware gedownload van de Azure-portal. Zie **Stap 3: Download de virtuele apparaatafbeelding** van De portal voor de gids Voor bereiden op de virtuele array van [Fors.](storsimple-virtual-array-deploy1-portal-prep.md)

### <a name="for-the-storsimple-virtual-device"></a>Voor het virtuele StorSimple-apparaat
Voordat u een virtueel apparaat implementeert, controleert u of:

* U hebt toegang tot een hostsysteem met Hyper-V (2008 R2 of hoger) dat kan worden gebruikt voor een voorziening van een apparaat.
* Het hostsysteem kan de volgende resources volledig toewijzen aan het inrichten van uw virtuele apparaat:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele array als bestandsserver te configureren, ondersteunt 8 GB minder dan 2 miljoen bestanden. Je hebt 16 GB RAM nodig om 2 - 4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor systeemgegevens.

### <a name="for-the-network-in-datacenter"></a>Voor het netwerk in het datacenter
Zorg voordat u begint voor het volgende:

* U hebt de netwerkvereisten voor het implementeren van een Virtueel StorSimple-apparaat beoordeeld en het datacenternetwerk geconfigureerd volgens de vereisten. 

## <a name="step-by-step-provisioning"></a>Stapsgewijze inrichting
Als u een virtueel apparaat wilt inrichten en verbinding wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem over voldoende resources beschikt om te voldoen aan de minimale vereisten voor virtuele apparaten.
2. Inrichten van een virtueel apparaat in uw hypervisor.
3. Start het virtuele apparaat en haal het IP-adres op.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Stap 1: Zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor virtuele apparaten
Als u een virtueel apparaat wilt maken, hebt u het:

* Toegang tot een hostsysteem met VMware ESXi Server 5.0, 5.5, 6.0 of 6.5.
* Een VMware vSphere-client op uw systeem voor het beheren van de ESXi-host.

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele array als bestandsserver te configureren, ondersteunt 8 GB minder dan 2 miljoen bestanden. Je hebt 16 GB RAM nodig om 2 - 4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface die is verbonden met het netwerk en verkeer naar internet kan routeren. De minimale internetbandbreedte moet 5 Mbps bedragen om een optimale werking van het apparaat mogelijk te maken.
  * Een virtuele schijf van 500 GB voor gegevens.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Stap 2: Een virtueel apparaat inhypervisor inrichten
Voer de volgende stappen uit voor het inrichten van een virtueel apparaat in de hypervisor.

1. Kopieer de installatiekopie van het virtuele apparaat naar uw systeem. U hebt deze virtuele afbeelding gedownload via de Azure-portal.

   1. Zorg ervoor dat u het nieuwste afbeeldingsbestand hebt gedownload. Als u de afbeelding eerder hebt gedownload, downloadt u deze opnieuw om ervoor te zorgen dat u over de nieuwste afbeelding beschikt. De nieuwste afbeelding heeft twee bestanden (in plaats van een).
   2. Noteer de locatie waar u de installatiekopie naar hebt gekopieerd, want u gaat deze installatiekopie verderop in de procedure gebruiken.

2. Meld u aan bij de ESXi-server met behulp van de vSphere-client. U moet beheerdersbevoegdheden hebben om een virtuele machine te kunnen maken.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. Selecteer in de vSphere-client in de voorraadsectie in het linkerdeelvenster de ESXi-server.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Upload de VMDK naar de ESXi-server. Navigeer naar het tabblad **Configuratie** in het rechterdeelvenster. Selecteer **Onder Hardware**de optie **Opslag**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. In het rechterdeelvenster bij **Gegevensopslag** selecteert u de gegevensopslag waarin u de VMDK wilt uploaden. De datastore moet voldoende vrije ruimte hebben voor het besturingssysteem en de gegevensschijven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klik met de rechtermuisknop op **Browsen in de gegevensopslag** en selecteer deze.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Een venster met de **gegevensopslagbrowser** wordt weergegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Klik op de ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) gereedschapsbalk op pictogram om een nieuwe map te maken. Geef de mapnaam op en noteer deze. U gebruikt deze mapnaam later bij het maken van een virtuele machine (aanbevolen best practices). Klik op **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. De nieuwe map wordt weergegeven in het linkerdeelvenster van de **gegevensopslagbrowser**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klik op ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) het pictogram Uploaden en selecteer **Bestand uploaden**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Blader en wijs naar de VMDK-bestanden die u hebt gedownload. Er zijn twee bestanden. Selecteer een bestand dat u wilt uploaden.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klik op **Openen**. Het uploaden van het VMDK-bestand met de opgegeven gegevensopslag wordt gestart. Het duurt enkele minuten om het bestand te uploaden.
13. Nadat het uploaden is voltooid, ziet u het bestand in de gegevensopslag in de map die u hebt gemaakt.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Upload nu het tweede VMDK-bestand naar dezelfde gegevensopslag.
14. Ga terug naar het venster van de vSphere-client. Als ESXi-server is geselecteerd, klikt u met de rechtermuisknop en selecteert u **Nieuwe virtuele machine**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Er verschijnt een venster **Nieuwe virtuele machine maken.** Selecteer **op** de pagina Configuratie de optie **Aangepast.** Klik op **Volgende**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Geef op de pagina **Naam en locatie** de naam van uw virtuele machine op. Deze naam moet overeenkomen met de mapnaam (aanbevolen aanbevolen aanbevolen procedures) die u eerder in stap 8 hebt opgegeven.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Selecteer **op** de pagina Opslag een gegevensarchief dat u wilt gebruiken om uw vm in te richten.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Selecteer virtuele **machineversie** op de pagina Virtuele **machineversie: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Selecteer op de pagina **Gastbesturingssysteem** het **gastbesturingssysteem** als **Windows.** Selecteer **Microsoft Windows Server 2012 (64-bits)** voor **versie**in de vervolgkeuzelijst.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Pas op de pagina **CPU's** het **aantal virtuele sockets** en **het aantal cores per virtuele socket** aan, zodat het totale aantal **cores** 4 (of meer) is. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Geef **op** de pagina Geheugen 8 GB (of meer) RAM op. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Geef op de pagina **Netwerk** het nummer van de netwerkinterfaces op. De minimale vereiste is één netwerkinterface.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Accepteer op de pagina **SCSI-controller** de standaard **LSI Logic SAS-controller**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Kies **op** de pagina Een schijf selecteren de optie **Een bestaande virtuele schijf gebruiken**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Klik op de pagina **Bestaande schijf selecteren** onder **Schijfbestandspad**op **Bladeren**. Hiermee wordt een dialoogvenster **Browse Datastores** geopend. Navigeer naar de locatie waar u de VMDK hebt geüpload. U ziet nu slechts één bestand in het gegevensarchief omdat de twee bestanden die u in eerste instantie hebt geüpload, zijn samengevoegd. Selecteer het bestand en klik op **OK**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Accepteer op de pagina **Geavanceerde opties** de standaardinstelling en klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Op de pagina **Gereed om te voltooien** controleert u alle instellingen voor de nieuwe virtuele machine. Schakel **De instellingen van de virtuele machine bewerken voordat u klaar bent**. Klik **op Doorgaan**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Zoek op de pagina **Eigenschappen van virtuele machines** op het tabblad **Hardware** de apparaathardware. Selecteer **Nieuwe harde schijf**. Klik op**toevoegen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. U ziet een venster **Hardware toevoegen.** Selecteer harde schijf en klik op de pagina **Apparaattype** onder **Het type apparaat kiezen dat u wilt toevoegen,** harde **schijf**en klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Kies **op** de pagina Een schijf selecteren de optie **Een nieuwe virtuele schijf maken**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Wijzig op de pagina **Een schijf maken** de **schijfgrootte** in 500 GB (of meer). Hoewel 500 GB de minimale vereiste is, u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet uitbreiden of verkleinen nadat deze is ingericht. Voor meer informatie over de grootte van schijf tot inlevering, bekijkt u de sectie grootte in het [document met aanbevolen procedures](storsimple-ova-best-practices.md). Selecteer **Thin Provisioning**onder **Schijfinrichting**. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Accepteer op de pagina **Geavanceerde opties** de standaardinstelling.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Bekijk op de pagina **Klaar om te voltooien** de schijfopties. Klik op **Voltooien**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Ga terug naar de pagina Eigenschappen van virtuele machines. Er wordt een nieuwe harde schijf aan uw virtuele machine toegevoegd. Klik op **Voltooien**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Als uw virtuele machine in het rechterdeelvenster is geselecteerd, navigeert u naar het tabblad **Overzicht.** Controleer de instellingen voor uw virtuele machine.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Uw virtuele machine is nu ingericht. De volgende stap is om deze machine te laten werken en om het IP-adres op te halen.

> [!NOTE]
> We raden u aan geen VMware-tools op uw virtuele array te installeren (zoals hierboven is ingericht). Als u VMware-hulpprogramma's installeert, leidt dat tot een niet-ondersteunde configuratie.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Stap 3: Start het virtuele apparaat en krijg het IP-adres
Voer de volgende stappen uit om uw virtuele apparaat te starten en verbinding te maken met het apparaat.

#### <a name="to-start-the-virtual-device"></a>Het virtuele apparaat starten
1. Start het virtuele apparaat. Selecteer in vSphere Configuration Manager in het linkerdeelvenster uw apparaat en klik met de rechtermuisknop om het contextmenu weer te geven. Selecteer **Aanzetten** en selecteer vervolgens **Inschakelen**. Nu zou uw virtuele machine moeten zijn ingeschakeld. U de status in het onderste deelvenster **Recente taken** van de vSphere-client weergeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Het voltooien van de installatietaken duurt enkele minuten. Zodra het apparaat actief is, navigeert u naar het tabblad **Console.** Ctrl+Alt+Delete verzenden om u aan te melden bij het apparaat. U de cursor ook op het consolevenster plaatsen en op Ctrl+Alt+Invoegen drukken. De standaardgebruiker is *StorSimpleAdmin* en het standaardwachtwoord is *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Voer een wachtwoord in van minimaal acht tekens. Het wachtwoord moet 3 van de 4 van deze vereisten bevatten: hoofdletters, kleine letters, numerieke en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. U krijgt een melding dat het wachtwoord is gewijzigd.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Nadat het wachtwoord is gewijzigd, kan het virtuele apparaat opnieuw worden opgestart. Wacht tot de reboot is voltooid. De Windows PowerShell-console van het apparaat kan samen met een voortgangsbalk worden weergegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, ziet u het volgende scherm.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Configureer vervolgens het netwerk.
7. Gebruik `Get-HcsIpAddress` de opdracht om de netwerkinterfaces weer te geven die zijn ingeschakeld op uw virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Hieronder kunt u een voorbeeld bekijken:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Nadat de initiële installatie voltooid is en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die wordt weergegeven in de bannertekst om het apparaat te beheren. U gebruikt dit IP-adres om verbinding te maken met de webgebruikersinterface van uw virtuele apparaat en de lokale installatie en registratie te voltooien.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Optioneel) Voer deze stap alleen uit als u uw apparaat implementeert in de Overheidscloud. U schakelt nu de FIPS-modus (Federal Information Processing Standard) van de Verenigde Staten in op uw apparaat. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door computersystemen van de Amerikaanse federale overheid voor de bescherming van gevoelige gegevens.

    1. Voer de volgende cmdlet uit om de FIPS-modus in te schakelen:

        `Enable-HcsFIPSMode`
    2. Start uw apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U de FIPS-modus op uw apparaat in- of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratievereisten, wordt er een fout weergegeven in de bannertekst (zie hieronder). U moet de apparaatconfiguratie wijzigen zodat er voldoende resources zijn om aan de minimale vereisten te voldoen. U kunt het apparaat vervolgens opnieuw opstarten en er verbinding mee maken. Raadpleeg de minimale configuratievereisten in [Stap 1: Controleren of het hostsysteem voldoet aan minimale vereisten voor virtuele apparaten](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Als u tijdens de eerste configuratie met de lokale web-gebruikersinterface een andere fout ondervindt, raadpleegt u de volgende werkstromen:

* Diagnostische tests uitvoeren om problemen op te [lossen met de installatie van de web-gebruikersinterface](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Logboekpakket genereren en logboekbestanden weergeven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Uw StorSimple Virtual Array instellen als bestandsserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Uw StorSimple Virtual Array instellen als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
