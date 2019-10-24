---
title: StorSimple Virtual array inrichten in Hyper-V | Microsoft Docs
description: Bij deze tweede zelf studie in de implementatie van een virtuele-StorSimple-matrix moet u een virtuele matrix inrichten in Hyper-V.
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
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516800"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple Virtual array implementeren-inrichten in Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In deze zelf studie wordt beschreven hoe u een virtuele StorSimple-matrix kunt inrichten op een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2. Dit artikel is van toepassing op de implementatie van StorSimple virtuele arrays in Azure Portal en Microsoft Azure Government Cloud.

U hebt beheerders bevoegdheden nodig om een virtuele matrix in te richten en te configureren. De inrichting en de initiële installatie kan circa tien minuten duren.

## <a name="provisioning-prerequisites"></a>Vereisten inrichten
Hier vindt u de vereisten voor het inrichten van een virtuele matrix op een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt alle stappen [voor het voorbereiden van de virtuele StorSimple-matrix in de portal](storsimple-virtual-array-deploy1-portal-prep.md)voltooid.
* U hebt de virtuele-matrix installatie kopie voor Hyper-V gedownload van de Azure Portal. Zie **voor meer informatie stap 3: Down load de installatie kopie** van de virtuele matrix van [de hand leiding voor het maken van de portal voor StorSimple Virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > De software die wordt uitgevoerd op de virtuele StorSimple-matrix mag alleen worden gebruikt met de StorSimple-Apparaatbeheer service.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Voor de virtuele StorSimple-matrix
Voordat u een virtuele matrix implementeert, moet u het volgende doen:

* U hebt toegang tot een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2008 R2 of later dat kan worden gebruikt om een apparaat in te richten.
* Het hostsysteem kan de volgende resources voor het inrichten van uw virtuele matrix toewijzen:

  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor gegevens.

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter
Voordat u begint, controleert u de netwerk vereisten voor het implementeren van een StorSimple-virtuele matrix en configureert u het Data Center-netwerk op de juiste manier. Zie [StorSimple Virtual array Network requirements](storsimple-ova-system-requirements.md#networking-requirements)(Engelstalig) voor meer informatie.

## <a name="step-by-step-provisioning"></a>Stap-voor-stap inrichting
Als u een virtuele matrix wilt inrichten en er verbinding mee wilt maken, moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat het hostsysteem voldoende bronnen heeft om te voldoen aan de minimale vereisten voor virtuele matrix.
2. Een virtuele matrix inrichten in uw Hyper Visor.
3. Start de virtuele matrix en haal het IP-adres op.

Elk van deze stappen wordt uitgelegd in de volgende secties.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Stap 1: Zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor de virtuele matrix
Als u een virtuele matrix wilt maken, hebt u het volgende nodig:

* De Hyper-V-functie die is geïnstalleerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2 SP1.
* Microsoft Hyper-V-beheer op een Microsoft Windows-client moet zijn verbonden met de host.

Zorg ervoor dat de onderliggende hardware (hostsysteem) waarop u de virtuele matrix maakt, de volgende resources aan uw virtuele array kan toewijzen:

* Minimaal 4 kerngeheugens.
* Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix te configureren als bestands server, ondersteunt 8 GB minder dan 2.000.000 bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
* Eén netwerkinterface.
* Een virtuele schijf van 500 GB voor systeem gegevens.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Stap 2: Een virtuele matrix inrichten in Hyper Visor
Voer de volgende stappen uit om een apparaat in de hypervisor in te richten.

#### <a name="to-provision-a-virtual-array"></a>Een virtuele matrix inrichten
1. Kopieer de installatie kopie van de virtuele matrix op de Windows Server-host naar een lokaal station. U hebt deze installatie kopie (VHD of VHDX) gedownload via de Azure Portal. Noteer de locatie waar u de installatiekopie naartoe hebt gekopieerd, want u hebt deze installatiekopie verderop in de procedure nodig.
2. Open **Serverbeheer**. Klik in de rechterbovenhoek op **Hulpprogramma's** en selecteer **Hyper-V-beheer**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Als u Windows Server 2008 R2 uitvoert, opent u Hyper-V-beheer. Klik in Serverbeheer op **rollen > hyper-v > hyper-v-beheer**.
3. In **Hyper-V-beheer** klikt u in het scopedeelvenster met de rechtermuisknop op uw systeemknooppunt om het contextmenu te openen. Klik vervolgens op **Nieuw** > **Virtuele machine**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Klik op de pagina **Voordat u begint** van de wizard Nieuwe virtuele machine op **Volgende**.
5. Geef op de pagina **naam en locatie opgeven** een **naam** op voor de virtuele matrix. Klik op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Kies op de pagina **generatie opgeven** het type installatie kopie van apparaat en klik vervolgens op **volgende**. Deze pagina wordt niet weer gegeven als u Windows Server 2008 R2 gebruikt.

   * Kies **generatie 2** als u een. vhdx-installatie kopie hebt gedownload voor Windows Server 2012 of hoger.
   * Kies **generatie 1** als u een VHD-installatie kopie hebt gedownload voor Windows Server 2008 R2 of hoger.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Op de pagina **Geheugen toewijzen** geeft u een **Opstartgeheugen** van ten minste **8192 MB** op. Schakel Dynamisch geheugen niet in en klik op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Op de pagina **Netwerk configureren** geeft de virtuele switch op die is verbonden met internet en klikt u op **Volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Kies op de pagina **virtuele harde schijf aansluiten** de optie **een bestaande virtuele harde schijf gebruiken**, de locatie van de installatie kopie van de virtuele matrix (. vhdx of. VHD) opgeven en klik vervolgens op **volgende**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Controleer de **Samenvatting** en klik op **Voltooien** om de virtuele machine te maken.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. U hebt 4 kerngeheugens nodig om te voldoen aan de minimale vereisten. Selecteer uw hostsysteem in het venster **Hyper-V-beheer** om 4 virtuele processors toe te voegen. Zoek in het rechterdeelvenster onder de lijst met **Virtuele Machines** de virtuele machine die u zojuist hebt gemaakt. Klik met de rechtermuisknop op de naam van de machine en selecteer **Instellingen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Klik op de pagina **Instellingen** in het linkerdeelvenster op **Processor**. Stel in het rechterdeelvenster **Aantal virtuele processors** in op 4 (of meer). Klik op **Toepassen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Om aan de minimum vereisten te voldoen, moet u ook een virtuele schijf van 500 GB toevoegen. Op de pagina **Instellingen** doet u het volgende:

    1. Selecteer in het linkerdeelvenster de optie **SCSI-controller**.
    2. In het rechterdeelvenster selecteert u de optie **Harde schijf** en klikt u op **Toevoegen**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Op de pagina **Harde schijf** selecteert u de optie **Virtuele harde schijf** en klikt u op **Nieuw**. De **Wizard Nieuwe virtuele harde schijf** wordt gestart.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Op de pagina **Voordat u begint** van de wizard Nieuwe virtuele harde schijf klikt u op **Volgende**.
16. Op de pagina **Schijfindeling kiezen** accepteert u de standaardindeling **VHDX**. Klik op **Volgende**. Dit scherm wordt niet weer gegeven als u Windows Server 2008 R2 uitvoert.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Op de pagina **Schijftype kiezen** stelt u het type virtuele harde schijf in op **Dynamisch uitbreidbaar** (aanbevolen). Een schijf van het type **Vaste grootte** werkt waarschijnlijk ook, maar dan moet u mogelijk lang wachten. U wordt geadviseerd om niet de optie **Differentiërende** te gebruiken. Klik op **Volgende**. In Windows Server 2012 R2 en Windows Server 2012 is **dynamisch uitbreiden** de standaard optie, terwijl in windows server 2008 R2 de standaard waarde is ingesteld op **vaste grootte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Op de pagina **Naam en locatie opgeven** geeft u een **Naam** en een **Locatie** voor de gegevensschijf op (u kunt naar een locatie bladeren). Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Op de pagina **schijf configureren** selecteert u de optie **een nieuwe lege virtuele harde schijf maken** en de grootte van **500 GB** (of meer) opgeven. Hoewel 500 GB de minimale vereiste is, kunt u altijd een grotere schijf inrichten. Houd er rekening mee dat u de schijf niet kunt uitbreiden of verkleinen wanneer deze eenmaal is ingericht. Raadpleeg de sectie grootte in het [document best practices](storsimple-ova-best-practices.md)voor meer informatie over de grootte van de schijf die moet worden ingericht. Klik op **Volgende**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Op de pagina **Samenvatting** controleert u de details van uw virtuele gegevensschijf en als u tevreden bent, klikt u op **Voltooien** om de schijf te maken. De wizard wordt gesloten en er wordt een virtuele harde schijf aan uw machine toegevoegd.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Ga terug naar de pagina **Instellingen**. Klik op **OK** om de pagina **Instellingen** te sluiten en terug te keren naar het venster Hyper-V-beheer.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Stap 3: De virtuele matrix starten en het IP-adres ophalen
Voer de volgende stappen uit om uw virtuele matrix te starten en er verbinding mee te maken.

#### <a name="to-start-the-virtual-array"></a>De virtuele matrix starten
1. Start de virtuele matrix.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Wanneer het apparaat is gestart, selecteert u het apparaat, klikt u met de rechtermuisknop en selecteert u **Verbinding maken**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Mogelijk moet u 5-10 minuten wachten totdat het apparaat klaar is. Er wordt een statusbericht op de console weergegeven om de voortgang aan te geven. Wanneer het apparaat gereed is, gaat u naar **Actie**. Druk `Ctrl + Alt + Delete` op om u aan te melden bij de virtuele matrix. De standaard gebruiker is *StorSimpleAdmin* en het standaard wachtwoord is *Wachtwoord1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Uit veiligheidsoverwegingen verloopt het beheerderswachtwoord voor het apparaat na de eerste aanmelding. U wordt gevraagd om het wachtwoord te wijzigen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Voer een wachtwoord in van minimaal acht tekens. Het wachtwoord moet voldoen aan ten minste 3 van de volgende 4 vereisten: hoofdletters, kleine letters, cijfers en speciale tekens. Voer het wachtwoord opnieuw in ter bevestiging. U ontvangt een melding dat het wachtwoord is gewijzigd.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Nadat het wacht woord is gewijzigd, wordt de virtuele matrix mogelijk opnieuw opgestart. Wacht tot het apparaat is gestart.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    De Windows PowerShell-console van het apparaat wordt weergegeven, samen met een voortgangsbalk.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. De stappen 6-8 gelden alleen als het apparaat wordt opgestart in een niet-DHCP-omgeving. In een DHCP-omgeving kunt u deze stappen overslaan en verder gaan met stap 9. Als u uw apparaat hebt opgestart in een niet-DHCP-omgeving, wordt het volgende scherm weer gegeven.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Configureer vervolgens het netwerk.
7. Gebruik de `Get-HcsIpAddress` opdracht om de netwerk interfaces weer te geven die zijn ingeschakeld voor uw virtuele matrix. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Zie het volgende voorbeeld:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Wanneer de initiële installatie is voltooid en het apparaat is opgestart, ziet u de bannertekst van het apparaat. Noteer het IP-adres en de URL die in de bannertekst worden weergegeven om het apparaat te beheren. Gebruik dit IP-adres om verbinding te maken met de Web-UI van uw virtuele matrix en de lokale installatie en registratie te volt ooien.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Beschrijving Voer deze stap alleen uit als u uw apparaat in de Government Cloud implementeert. U schakelt nu de FIPS-modus (Verenigde Staten Federal Information Processing Standard) op het apparaat in. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse Federal Government-computer systemen voor de beveiliging van gevoelige gegevens.

    1. Als u de FIPS-modus wilt inschakelen, voert u de volgende cmdlet uit:

        `Enable-HcsFIPSMode`
    2. Start het apparaat opnieuw op nadat u de FIPS-modus hebt ingeschakeld, zodat de cryptografische validaties van kracht worden.

       > [!NOTE]
       > U kunt de FIPS-modus op het apparaat in-of uitschakelen. Het wisselen van het apparaat tussen FIPS en niet-FIPS-modus wordt niet ondersteund.
       >
       >

Als uw apparaat niet voldoet aan de minimale configuratie vereisten, ziet u de volgende fout code in de banner tekst (zie hieronder). Wijzig de apparaatconfiguratie zo dat de virtuele machine voldoende resources heeft om aan de minimale vereisten te voldoen. Daarna kunt u het apparaat opnieuw opstarten en verbinding maken met het apparaat. Raadpleeg de minimale configuratie vereisten in stap 1: Zorg ervoor dat het hostsysteem voldoet aan de minimale vereisten voor virtuele matrix.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Als u tijdens de eerste configuratie een andere fout ondervindt met de lokale webgebruikersinterface, raadpleegt u de volgende werk stromen:

* Diagnostische tests uitvoeren voor het [oplossen van problemen met de Web-UI-installatie](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* Een [logboek bestand genereren en logboek bestanden weer geven](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Volgende stappen
* [Stel uw virtuele StorSimple-matrix in als een bestands server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Stel uw virtuele StorSimple-matrix in als een iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md)
