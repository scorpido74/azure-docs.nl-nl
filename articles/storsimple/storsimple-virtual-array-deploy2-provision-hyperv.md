---
title: Voorziening StorSimple Virtual Array in Hyper-V | Microsoft Documenten
description: Deze tweede zelfstudie in StorSimple Virtual Array-implementatie omvat het inrichten van een virtuele array in Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267532"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple Virtual Array implementeren - Voorziening in Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelfstudie wordt beschreven hoe u een StorSimple Virtual Array indient op een hostsysteem met Hyper-V op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2. Dit artikel is van toepassing op de implementatie van StorSimple Virtual Arrays in Azure portal en Microsoft Azure Government Cloud.

U hebt beheerdersbevoegdheden nodig om een virtuele array in te richten en te configureren. De inrichting en de initiële installatie kan ongeveer tien minuten duren.

## <a name="provisioning-prerequisites"></a>Voorzieningen
Hier vindt u de vereisten voor het inrichten van een virtuele array op een hostsysteem met Hyper-V op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen voltooid in [Het portaal voorbereiden voor StorSimple Virtual Array.](storsimple-virtual-array-deploy1-portal-prep.md)
* U hebt de virtuele arrayafbeelding voor Hyper-V gedownload van de Azure-portal. Zie **Stap 3: Download de virtuele arrayafbeelding** van [De portal voor de gids voor Virtuele array voorbereiden](storsimple-virtual-array-deploy1-portal-prep.md)voor meer informatie.

  > [!IMPORTANT]
  > De software die op de StorSimple Virtual Array wordt uitgevoerd, mag alleen worden gebruikt met de StorSimple Device Manager-service.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Voor de StorSimple Virtual Array
Voordat u een virtuele array implementeert, moet u ervoor zorgen dat:

* U hebt toegang tot een hostsysteem met Hyper-V op Windows Server 2008 R2 of hoger dat kan worden gebruikt voor het inrichten van een apparaat.
* Het hostsysteem kan de volgende resources besteden aan het inrichten van uw virtuele array:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele array als bestandsserver te configureren, ondersteunt 8 GB minder dan 2 miljoen bestanden. Je hebt 16 GB RAM nodig om 2 - 4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor gegevens.

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter
Voordat u begint, controleert u de netwerkvereisten om een StorSimple Virtual Array te implementeren en het datacenternetwerk op de juiste manier te configureren. Zie [StorSimple Virtual Array-netwerkvereisten](storsimple-ova-system-requirements.md#networking-requirements)voor meer informatie.

## <a name="step-by-step-provisioning"></a>Stapsgewijze inrichting
Als u een virtuele array wilt inrichten en verbinding wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem over voldoende resources beschikt om te voldoen aan de minimale vereisten voor virtuele arrays.
2. Inrichten van een virtuele array in uw hypervisor.
3. Start de virtuele array en krijg het IP-adres.

Elk van deze stappen wordt uitgelegd in de volgende secties.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Stap 1: Ervoor zorgen dat het hostsysteem voldoet aan minimale vereisten voor virtuele arrays
Als u een virtuele array wilt maken, moet u het andere doen:

* De Hyper-V-rol die is geïnstalleerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2 SP1.
* Microsoft Hyper-V-beheer op een Microsoft Windows-client moet zijn verbonden met de host.

Zorg ervoor dat de onderliggende hardware (hostsysteem) waarop u de virtuele array maakt, de volgende resources aan uw virtuele array kan wijden:

* Minimaal 4 kerngeheugens.
* Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele array als bestandsserver te configureren, ondersteunt 8 GB minder dan 2 miljoen bestanden. Je hebt 16 GB RAM nodig om 2 - 4 miljoen bestanden te ondersteunen.
* Eén netwerkinterface.
* Een virtuele schijf van 500 GB voor systeemgegevens.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Stap 2: Een virtuele array in hypervisor inrichten
Voer de volgende stappen uit om een apparaat in de hypervisor in te richten.

#### <a name="to-provision-a-virtual-array"></a>Een virtuele array inrichten
1. Kopieer de virtuele arrayafbeelding op uw Windows Server-host naar een lokaal station. U hebt deze afbeelding (VHD of VHDX) gedownload via de Azure-portal. Noteer de locatie waar u de installatiekopie naar hebt gekopieerd, want u gaat deze installatiekopie verderop in de procedure gebruiken.
2. Open **Serverbeheer**. Klik in de rechterbovenhoek op **Hulpprogramma's** en selecteer **Hyper-V-beheer**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Als u Windows Server 2008 R2 uitvoert, opent u De Hyper-V Manager. Klik in Serverbeheer op **Rollen > Hyper-V > Hyper-V Manager**.
3. In **Hyper-V-beheer** klikt u in het scopedeelvenster met de rechtermuisknop op uw systeemknooppunt om het contextmenu te openen. Klik vervolgens op **Nieuw** > **Virtuele machine**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Klik op de pagina **Voordat u begint** van de wizard Nieuwe virtuele machine op **Volgende**.
5. Geef **op** de pagina Naam en locatie opgeven een **naam** op voor uw virtuele array. Klik op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Kies **op de** pagina Generatie opgeven het type apparaatafbeelding en klik op **Volgende**. Deze pagina wordt niet weergegeven als u Windows Server 2008 R2 gebruikt.

   * Kies **Generatie 2** als u een .vhdx-afbeelding voor Windows Server 2012 of hoger hebt gedownload.
   * Kies **Generatie 1** als u een .vhd-afbeelding voor Windows Server 2008 R2 of hoger hebt gedownload.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Op de pagina **Geheugen toewijzen** geeft u een **Opstartgeheugen** van ten minste **8192 MB** op. Schakel Dynamisch geheugen niet in en klik op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Op de pagina **Netwerk configureren** geeft de virtuele switch op die is verbonden met internet en klikt u op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Kies **op** de pagina Virtuele harde schijf verbinden de optie Een **bestaande virtuele harde schijf gebruiken,** geef de locatie van de virtuele arrayafbeelding op (.vhdx of .vhd) en klik vervolgens op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Controleer de **Samenvatting** en klik op **Voltooien** om de virtuele machine te maken.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. U hebt 4 kerngeheugens nodig om te voldoen aan de minimale vereisten. Selecteer uw hostsysteem in het venster **Hyper-V-beheer** om 4 virtuele processors toe te voegen. Zoek in het rechterdeelvenster onder de lijst met **Virtuele Machines** de virtuele machine die u zojuist hebt gemaakt. Klik met de rechtermuisknop op de naam van de machine en selecteer **Instellingen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Klik op de pagina **Instellingen** in het linkerdeelvenster op **Processor**. Stel in het rechterdeelvenster **Aantal virtuele processors** in op 4 (of meer). Klik op **Toepassen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Om aan de minimumvereisten te voldoen, moet u ook een virtuele gegevensschijf van 500 GB toevoegen. Op de pagina **Instellingen** doet u het volgende:

    1. Selecteer in het linkerdeelvenster de optie **SCSI-controller**.
    2. In het rechterdeelvenster selecteert u de optie **Harde schijf** en klikt u op **Toevoegen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Op de pagina **Harde schijf** selecteert u de optie **Virtuele harde schijf** en klikt u op **Nieuw**. De **Wizard Nieuwe virtuele harde schijf** wordt gestart.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Op de pagina **Voordat u begint** van de wizard Nieuwe virtuele harde schijf klikt u op **Volgende**.
16. Op de pagina **Schijfindeling kiezen** accepteert u de standaardindeling **VHDX**. Klik op **Volgende**. Dit scherm wordt niet weergegeven als windows server 2008 R2 wordt uitgevoerd.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Op de pagina **Schijftype kiezen** stelt u het type virtuele harde schijf in op **Dynamisch uitbreidbaar** (aanbevolen). Een schijf van het type **Vaste grootte** werkt waarschijnlijk ook, maar dan moet u mogelijk lang wachten. U wordt geadviseerd om niet de optie **Differentiërende** te gebruiken. Klik op **Volgende**. In Windows Server 2012 R2 en Windows Server 2012 is **Dynamisch uitbreiden** de standaardoptie, terwijl in Windows Server 2008 R2 de standaardwaarde **vaste grootte**is.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Op de pagina **Naam en locatie opgeven** geeft u een **Naam** en een **Locatie** voor de gegevensschijf op (u kunt naar een locatie bladeren). Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Selecteer **op** de pagina Schijf configureren de optie **Een nieuwe lege virtuele harde schijf maken** en geef de grootte op als **500 GB** (of meer). Hoewel 500 GB de minimale vereiste is, u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet uitbreiden of verkleinen nadat deze is ingericht. Voor meer informatie over de grootte van schijf tot inlevering, bekijkt u de sectie grootte in het [document met aanbevolen procedures](storsimple-ova-best-practices.md). Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Op de pagina **Samenvatting** controleert u de details van uw virtuele gegevensschijf en als u tevreden bent, klikt u op **Voltooien** om de schijf te maken. De wizard wordt gesloten en er wordt een virtuele harde schijf aan uw machine toegevoegd.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Ga terug naar de pagina **Instellingen**. Klik op **OK** om de pagina **Instellingen** te sluiten en terug te keren naar het venster Hyper-V-beheer.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Stap 3: Start de virtuele array en krijg het IP-adres
Voer de volgende stappen uit om uw virtuele array te starten en er verbinding mee te maken.

#### <a name="to-start-the-virtual-array"></a>De virtuele array starten
1. Start de virtuele array.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Wanneer het apparaat is gestart, selecteert u het apparaat, klikt u met de rechtermuisknop en selecteert u **Verbinding maken**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Het kan zijn dat u 5-10 minuten moet wachten voordat het apparaat klaar is. Er wordt een statusbericht op de console weergegeven om de voortgang aan te geven. Wanneer het apparaat gereed is, gaat u naar **Actie**. Druk `Ctrl + Alt + Delete` om in te loggen op de virtuele array. De standaardgebruiker is *StorSimpleAdmin* en het standaardwachtwoord is *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Voer een wachtwoord in van minimaal acht tekens. Het wachtwoord moet voldoen aan ten minste 3 van de volgende 4 vereisten: hoofdletters, kleine letters, cijfers en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. U ontvangt een melding dat het wachtwoord is gewijzigd.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Nadat het wachtwoord is gewijzigd, kan de virtuele array opnieuw worden opgestart. Wacht tot het apparaat is gestart.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    De Windows PowerShell-console van het apparaat wordt weergegeven, samen met een voortgangsbalk.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, ziet u het volgende scherm.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Configureer vervolgens het netwerk.
7. Gebruik `Get-HcsIpAddress` de opdracht om de netwerkinterfaces weer te geven die zijn ingeschakeld op uw virtuele array. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Zie het volgende voorbeeld:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Nadat de initiële installatie voltooid is en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die wordt weergegeven in de bannertekst om het apparaat te beheren. Gebruik dit IP-adres om verbinding te maken met de webgebruikersinterface van uw virtuele array en de lokale installatie en registratie te voltooien.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Optioneel) Voer deze stap alleen uit als u uw apparaat implementeert in de Overheidscloud. U schakelt nu de FIPS-modus (Federal Information Processing Standard) van de Verenigde Staten in op uw apparaat. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door computersystemen van de Amerikaanse federale overheid voor de bescherming van gevoelige gegevens.

    1. Voer de volgende cmdlet uit om de FIPS-modus in te schakelen:

        `Enable-HcsFIPSMode`
    2. Start uw apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U de FIPS-modus op uw apparaat in- of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratievereisten, ziet u de volgende fout in de bannertekst (zie hieronder). Wijzig de apparaatconfiguratie zo dat de virtuele machine voldoende resources heeft om aan de minimale vereisten te voldoen. U kunt het apparaat vervolgens opnieuw opstarten en er verbinding mee maken. Raadpleeg de minimale configuratievereisten in stap 1: zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor virtuele arrays.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Als u tijdens de eerste configuratie met de lokale web-gebruikersinterface een andere fout ondervindt, raadpleegt u de volgende werkstromen:

* Diagnostische tests uitvoeren om problemen op te [lossen met de installatie van de web-gebruikersinterface](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Logboekpakket genereren en logboekbestanden weergeven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Uw StorSimple Virtual Array instellen als bestandsserver](storsimple-virtual-array-deploy3-fs-setup.md)
* [Uw StorSimple Virtual Array instellen als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
