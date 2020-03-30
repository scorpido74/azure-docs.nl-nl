---
title: Microsoft Azure StorSimple Virtual Array iSCSI-serversetup | Microsoft Documenten
description: Beschrijft hoe u de initiële installatie uitvoert, uw StorSimple iSCSI-server registreert en de installatie van het apparaat voltooit.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254493"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>StorSimple Virtual Array implementeren – Instellen als een iSCSI-server via Azure-portal

![iscsi-installatieprocesstroom](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Deze zelfstudie voor implementatie is van toepassing op de Virtuele Array van Microsoft Azure StorSimple. In deze zelfstudie wordt beschreven hoe u de eerste installatie uitvoert, uw StorSimple iSCSI-server registreert, de apparaatinstelling voltooit en vervolgens volumes maakt, monteert, initialiseert en opmaakt op uw StorSimple Virtual Array geconfigureerd als iSCSI-server. 

De hier beschreven procedures duren ongeveer 30 minuten tot 1 uur. De informatie gepubliceerd in dit artikel is alleen van toepassing op StorSimple Virtual Arrays.

## <a name="setup-prerequisites"></a>Vereisten voor installatie

Voordat u uw StorSimple Virtual Array configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtuele array ingericht en erop verbonden zoals beschreven in [Deploy StorSimple Virtual Array - Een virtuele array inrichten in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) of [Deploy StorSimple Virtual Array - Een virtuele array inVMware inrichten](storsimple-virtual-array-deploy2-provision-vmware.md).
* U beschikt over de serviceregistratiesleutel van de StorSimple Device Manager-service die u hebt gemaakt om uw StorSimple Virtual Arrays te beheren. Zie **Stap 2: Download de serviceregistratiesleutel** in [StorSimple Virtual Array implementeren - De portal voorbereiden voor](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)meer informatie.
* Als dit de tweede of volgende virtuele array is die u registreert bij een bestaande StorSimple Device Manager-service, moet u de versleutelingssleutel voor servicegegevens hebben. Deze sleutel is gegenereerd toen het eerste apparaat met succes bij deze service werd geregistreerd. Als u deze sleutel bent kwijtgeraakt, **raadpleegt u De versleutelingssleutel voor servicegegevens opvragen** in [De webgebruikersinterface gebruiken om uw StorSimple Virtual Array te beheren.](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)

## <a name="step-by-step-setup"></a>Stapsgewijze installatie

Gebruik de volgende stapsgewijze instructies om uw StorSimple Virtual Array in te stellen en te configureren:

* [Stap 1: De lokale web-gebruikersinterface-installatie voltooien en uw apparaat registreren](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Stap 2: De vereiste apparaatconfiguratie voltooien
* [Stap 3: Een volume toevoegen](#step-3-add-a-volume)
* [Stap 4: Een volume monteren, initialiseren en opmaken](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Stap 1: De lokale web-gebruikersinterface-installatie voltooien en uw apparaat registreren

#### <a name="to-complete-the-setup-and-register-the-device"></a>De installatie voltooien en het apparaat registreren

1. Open een browservenster. Ga als u verbinding maken met het web-gebruikersinterfacetype:
   
    `https://<ip-address of network interface>`
   
    Gebruik de verbindings-URL die in de vorige stap is vermeld. U ziet een fout waarin u wordt gemeld dat er een probleem is met het beveiligingscertificaat van de website. Klik **op Doorgaan naar deze webpagina**.
   
    ![fout in beveiligingscertificaat](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Meld u aan bij de webgebruikersinterface van uw virtuele apparaat als **StorSimpleAdmin**. Voer het wachtwoord van de apparaatbeheerder in dat u hebt gewijzigd in stap 3: Start het virtuele apparaat in [StorSimple Virtual Array implementeren - Een virtueel apparaat inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of [StorSimple Virtual Array implementeren - Een virtueel apparaat invMware inrichten](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Aanmeldingspagina](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. U wordt naar de **startpagina** gebracht. Op deze pagina worden de verschillende instellingen beschreven die nodig zijn om het virtuele apparaat te configureren en te registreren met de StorSimple Device Manager-service. Houd er rekening mee dat de **instellingen voor netwerkinstellingen,** **webproxy-instellingen**en **Tijdoptioneel** zijn. De enige vereiste instellingen zijn **apparaatinstellingen** en **cloudinstellingen.**
   
    ![Startpagina](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Op de pagina **Netwerkinstellingen** onder **Netwerkinterfaces**wordt DATA 0 automatisch voor u geconfigureerd. Elke netwerkinterface is standaard ingesteld om automatisch een IP-adres (DHCP) te krijgen. Daarom worden automatisch een IP-adres, subnet en gateway toegewezen (voor zowel IPv4 als IPv6).
   
    Als u van plan bent uw apparaat te implementeren als een iSCSI-server (om blokopslag in te richten), raden we u aan de optie **IP-adres automatisch ontvangen** uit te schakelen en statische IP-adressen te configureren.
   
    ![Pagina Netwerkinstellingen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Als u tijdens de inrichting van het apparaat meer dan één netwerkinterface hebt toegevoegd, u deze hier configureren. Houd er rekening mee dat u uw netwerkinterface alleen als IPv4 of als IPv4 en IPv6 configureren. Alleen IPv6-configuraties worden niet ondersteund.
5. DNS-servers zijn vereist omdat ze worden gebruikt wanneer uw apparaat probeert te communiceren met uw cloudopslagserviceproviders of om uw apparaat op naam op te lossen als het is geconfigureerd als bestandsserver. Ga naar de pagina **Netwerkinstellingen** onder de **DNS-servers:**
   
   1. Een primaire en secundaire DNS-server wordt automatisch geconfigureerd. Als u ervoor kiest om statische IP-adressen te configureren, u DNS-servers opgeven. Voor hoge beschikbaarheid raden we u aan een primaire en een secundaire DNS-server te configureren.
   2. Klik op **Toepassen**. Dit is van toepassing en valideert de netwerkinstellingen.
6. Ga als een op de pagina **Apparaatinstellingen:**
   
   1. Wijs een unieke **naam** toe aan uw apparaat. Deze naam kan 1-15 tekens zijn en kan letter, getallen en koppeltekens bevatten.
   2. Klik op het **iSCSI-serverpictogram** ![voor iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) voor het **type** apparaat dat u maakt. Met een iSCSI-server u blokopslag inrichten.
   3. Geef op of u wilt dat dit apparaat wordt verbonden met een domein. Als uw apparaat een iSCSI-server is, is het optioneel om lid te worden van het domein. Als u besluit uw iSCSI-server niet aan een domein toe te voegen, klikt u op **Toepassen,** wacht u tot de instellingen zijn toegepast en gaat u vervolgens door naar de volgende stap.
      
       Als u het apparaat wilt aansluiten bij een domein. Voer een **domeinnaam in**en klik op **Toepassen**.
      
      > [!NOTE]
      > Als u uw iSCSI-server aansluit bij een domein, moet u ervoor zorgen dat uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Microsoft Azure Active Directory bevindt en dat er geen groepsbeleidsobjecten (GPO) op worden toegepast.
      > 
      > 
   4. Er verschijnt een dialoogvenster. Voer uw domeinreferenties in de opgegeven indeling in. Klik op het vinkje ![vinkje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). De domeinreferenties worden geverifieerd. U ziet een foutmelding als de referenties onjuist zijn.
      
       ![referenties](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klik op **Toepassen**. Dit is van toepassing en valideert de apparaatinstellingen.
7. (Optioneel) configureer uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd.
   
    ![webproxy configureren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Op de **webpagina:**
   
   1. De **URL van** de webproxy in deze indeling opgeven: *http:\//host-IP-adres* of *FQDN:Poortnummer*. Https-URL's worden niet ondersteund.
   2. **Verificatie** opgeven als **basis** of **geen**.
   3. Als u verificatie gebruikt, moet u ook een **gebruikersnaam** en **wachtwoord**opgeven.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde webproxy-instellingen gevalideerd en toegepast.
8. (Optioneel) configureer de tijdinstellingen voor uw apparaat, zoals tijdzone en de primaire en secundaire NTP-servers. NTP-servers zijn vereist omdat uw apparaat de tijd moet synchroniseren, zodat het kan verifiëren met uw cloudserviceproviders.
   
    ![Tijdinstellingen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Op de pagina **Tijdinstellingen:**
   
   1. Selecteer in de vervolgkeuzelijst de **tijdzone** op basis van de geografische locatie waarin het apparaat wordt geïmplementeerd. De standaardtijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
   2. Geef een **primaire NTP-server** op voor uw apparaat of accepteer de standaardwaarde van time.windows.com. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.
   3. Geef optioneel een **secundaire NTP-server** op voor uw apparaat.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde tijdinstellingen gevalideerd en toegepast.
9. Configureer de cloudinstellingen voor uw apparaat. In deze stap voltooit u de lokale apparaatconfiguratie en registreert u het apparaat bij uw StorSimple Device Manager-service.
   
   1. Voer de **serviceregistratiesleutel** in die u hebt in **stap 2: Download de serviceregistratiesleutel** in [Implementeren van StorSimple Virtual Array - Bereid de portal voor.](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)
   2. Als dit niet het eerste apparaat is dat u bij deze service registreert, moet u de **versleutelingssleutel servicegegevens**verstrekken. Deze sleutel is vereist met de serviceregistratiesleutel om extra apparaten te registreren bij de StorSimple Device Manager-service. Raadpleeg voor meer informatie [de versleutelingssleutel voor servicegegevens](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) op uw lokale webgebruikersinterface.
   3. Klik **op Registreren**. Hiermee wordt het apparaat opnieuw opgestart. Het kan nodig zijn om 2-3 minuten te wachten voordat het apparaat met succes is geregistreerd. Nadat het apparaat opnieuw is opgestart, wordt u naar de aanmeldingspagina geleid.
      
      ![Apparaat registreren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Ga terug naar de Azure-portal.
11. Navigeer naar **het** apparaatblad van uw service. Als u veel bronnen hebt, klikt u op **Alle bronnen,** klikt u op uw servicenaam (zoek er indien nodig naar) en klikt u vervolgens op **Apparaten**.
12. Controleer op het blade **apparaten** of het apparaat met succes is verbonden met de service door de status op te zoeken. Het apparaat moet de status **Gereed voor configuratie** hebben.
    
    ![Apparaat registreren](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Stap 2: Het apparaat configureren als iSCSI-server

Voer de volgende stappen uit in de Azure-portal om de vereiste apparaatconfiguratie te voltooien.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Het apparaat configureren als iSCSI-server

1. Ga naar uw StorSimple Device Manager-service en ga vervolgens naar **Beheer >-apparaten**. Selecteer in het blade **Apparaten** het apparaat dat u zojuist hebt gemaakt. Dit apparaat zou worden weergegeven als **klaar om in te stellen.**
   
    ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klik op het apparaat en u ziet een bannerbericht dat aangeeft dat het apparaat klaar is om in te stellen.
   
    ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klik **op Configureren** op de opdrachtbalk van het apparaat. Hiermee wordt het **configureerblad** geopend. Ga als volgt te werk in het blad **configureren:**
   
   * De naam van de iSCSI-server wordt automatisch ingevuld.
   * Controleer of de cloudopslagversleuteling is ingesteld op **Ingeschakeld**. Dit zorgt ervoor dat de gegevens die van het apparaat naar de cloud worden verzonden, worden versleuteld.
   * Geef een versleutelingssleutel van 32 tekens op en neem deze op in een sleutelbeheer-app voor toekomstig gebruik.
   * Selecteer een opslagaccount dat met uw apparaat moet worden gebruikt. In dit abonnement u een bestaand opslagaccount selecteren of op **Toevoegen** klikken om een account uit een ander abonnement te kiezen.
     
     ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klik **op Configureren** om de iSCSI-server in te stellen.
   
    ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. U krijgt een melding dat de iSCSI-server wordt gemaakt. Nadat de iSCSI-server is gemaakt, wordt het blade **Apparaten** bijgewerkt en is de bijbehorende apparaatstatus **Online.**
   
    ![Apparaat configureren als iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Stap 3: Een volume toevoegen

1. Selecteer in het blade **Apparaten** het apparaat dat u zojuist hebt geconfigureerd als iSCSI-server. Klik **op ...** (klik ook met de rechtermuisknop in deze rij) en selecteer in het contextmenu Volume **toevoegen.** U ook op **+ Volume toevoegen** vanaf de opdrachtbalk klikken. Hiermee opent u het **volumeblad toevoegen.**
   
    ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Ga als volgt te werk in het **volumeblad Toevoegen:**
   
   * Voer in het veld **Volumenaam** een unieke naam voor uw volume in. De naam moet een tekenreeks zijn die 3 tot 127 tekens bevat.
   * Geef in de vervolgkeuzelijst **Type** op of u een **trapsgeweneerd** of **lokaal vastgemaakt** volume wilt maken. Selecteer **Lokaal vastgemaakt** **volume**voor workloads waarvoor lokale garanties, lage latencies en hogere prestaties nodig zijn. Selecteer **Laagvolume** **volume**voor alle andere gegevens .
   * Geef **in** het veld Capaciteit de grootte van het volume op. Een gelaagd volume moet tussen 500 GB en 5 TB liggen en een lokaal vastgemaakt volume moet tussen 50 GB en 500 GB liggen.
     
     Een lokaal vastgemaakt volume is dik ingericht en zorgt ervoor dat de primaire gegevens in het volume op het apparaat blijven en niet naar de cloud worden gelekt.
     
     Een gelaagd volume daarentegen is dun ingericht. Wanneer u een gelaagd volume maakt, wordt ongeveer 10% van de ruimte op de lokale laag ingericht en wordt 90% van de ruimte in de cloud ingericht. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de cloud gebruikt wanneer de gegevenslagen worden gebruikt. Dit impliceert op zijn beurt is dat als u geen ruimte meer hebt op het apparaat, u geen gedifferentieerd aandeel inrichten (omdat de 10% niet beschikbaar is).
     
     ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klik **op Verbonden hosts,** selecteer een access control record (ACR) die overeenkomt met de iSCSI-initiator die u met dit volume wilt verbinden en klik vervolgens op **Selecteren**. <br><br> 
3. Als u een nieuwe verbonden host wilt toevoegen, klikt u op **Nieuw toevoegen,** voert u een naam in voor de host en de iSCSI-gekwalificeerde naam (IQN) en klikt u vervolgens op **Toevoegen**. Als u de IQN niet hebt, gaat u naar [bijlage A: Download het IQN van een Windows Server-host.](#appendix-a-get-the-iqn-of-a-windows-server-host)
   
      ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Klik op **OK**wanneer u klaar bent met het configureren van uw volume. Er wordt een volume gemaakt met de opgegeven instellingen en u ziet een melding. Standaard worden bewaking en back-up ingeschakeld voor het volume.
   
     ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Ga naar het blad **Volumes** om te controleren of het volume is gemaakt. U ziet het vermelde volume.
   
   ![Een volume toevoegen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Stap 4: Een volume monteren, initialiseren en opmaken

Voer de volgende stappen uit om uw StorSimple-volumes op een Windows Server-host te monteren, te initialiseren en op te maken.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Een volume koppelen, initialiseren en formatteren

1. Open de **iSCSI-initiator-app** op de juiste server.
2. Klik in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Detectie** op **Portal detecteren**.
   
    ![ontdek portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Geef in het dialoogvenster **Doelportaal detecteren** het IP-adres op van de netwerkinterface met iSCSI-functionaliteit en klik vervolgens op **OK**.
   
    ![IP-adres](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Ga in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Doelen** naar **Gedetecteerde doelen**. (Elk volume zal een ontdekt doel zijn.) De status van het apparaat moet worden weergegeven als **Inactief**.
   
    ![ontdekte doelen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecteer een doelapparaat en klik op **Verbinding maken**. Nadat het apparaat is verbonden, moet de status zijn gewijzigd in **Verbonden**. (Zie [Microsoft iSCSI Initiator installeren en configureren][1]voor meer informatie over het gebruik van de Microsoft iSCSI-initiator.
   
    ![doelapparaat selecteren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Druk op uw Windows-hots op de Windows-toets + X en klik vervolgens op **Uitvoeren**.
7. Typ in het dialoogvenster **Uitvoeren****Diskmgmt.msc**. Klik op **OK**. Het dialoogvenster **Schijfbeheer** wordt weergegeven. In het rechterdeelvenster worden de volumes op uw host weergeven.
8. In het venster **Schijfbeheer** worden de gekoppelde volumes weergegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.
   
    ![schijfbeheer](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klik met de rechtermuisknop en selecteer **Schijf initialiseren**.
   
    ![schijf 1 initialiseren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Selecteer in het dialoogvenster de schijf(en) om te initialiseren en klik op **OK**.
    
    ![schijf 2 initialiseren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. De wizard Nieuw eenvoudig volume wordt gestart. Selecteer een schijfgrootte en klik op **Volgende**.
    
    ![nieuwe volumewizard 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Wijs een stationsletter toe aan het volume en klik op **Volgende**.
    
    ![nieuwe volumewizard 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Voer de parameters in om het volume op te maken. **Op Windows Server wordt alleen NTFS ondersteund.** Stel de grootte van de toewijzingseenheid in op 64K. Geef een label op voor uw volume. It is a recommended best practice for this name to be identical to the volume name you provided on your StorSimple Virtual Array. Klik op **Volgende**.
    
    ![nieuwe volumewizard 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Controleer de waarden voor uw volume en klik op **Voltooien**.
    
    ![nieuwe volumewizard 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    De volumes worden weergegeven als **Online** op de pagina **Schijfbeheer.**
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de lokale web-gebruikersinterface om [uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)te beheren.

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Bijlage A: Download de IQN van een Windows Server-host

Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server 2012 wordt uitgevoerd.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Het IQN ophalen van een Windows-host

1. Start de Microsoft iSCSI-initiator op uw Windows-host.
2. Selecteer en kopieer in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Configuratie** de tekenreeks in het veld **Naam van initiator**.
   
    ![Eigenschappen iSCSI-initiator](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Bewaar deze tekenreeks.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



