---
title: StorSimple Virtual Array instellen als bestandsserver | Microsoft Documenten
description: Deze derde zelfstudie in StorSimple Virtual Array-implementatie geeft u de opdracht om een virtueel apparaat in te stellen als bestandsserver.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297647"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple Virtual Array implementeren - Instellen als bestandsserver via Azure-portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Inleiding

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In dit artikel wordt beschreven hoe u de initiële installatie uitvoert, uw StorSimple-bestandsserver registreert, de apparaatinstelling voltooit en verbinding maakt met SMB-shares. Dit is het laatste artikel in de reeks implementatiezelfstudies die nodig zijn om uw virtuele array volledig te implementeren als bestandsserver of iSCSI-server.

Het installatie- en configuratieproces kan ongeveer 10 minuten in beslag nemen. De informatie in dit artikel is alleen van toepassing op de implementatie van de StorSimple Virtual Array. Ga voor de implementatie van StorSimple 8000-serie-apparaten naar: [Implementeer uw StorSimple 8000-serie apparaat met Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Vereisten voor installatie
Voordat u uw StorSimple Virtual Array configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtuele array ingericht en ermee verbonden zoals beschreven in de [Provision a StorSimple Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of [Provision a StorSimple Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* U beschikt over de serviceregistratiesleutel van de StorSimple Device Manager-service die u hebt gemaakt om StorSimple Virtual Arrays te beheren. Zie [Stap 2: Download de serviceregistratiesleutel](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) voor StorSimple Virtual Array voor meer informatie.
* Als dit de tweede of volgende virtuele array is die u registreert bij een bestaande StorSimple Device Manager-service, moet u de versleutelingssleutel voor servicegegevens hebben. Deze sleutel is gegenereerd toen het eerste apparaat met succes bij deze service werd geregistreerd. Zie [De versleutelingssleutel voor servicegegevens](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) voor uw StorSimple Virtual Array ophalen als u deze sleutel kwijt bent.

## <a name="step-by-step-setup"></a>Stapsgewijze installatie
Gebruik de volgende stapsgewijze instructies om uw StorSimple Virtual Array in te stellen en te configureren.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Stap 1: De lokale web-gebruikersinterface-installatie voltooien en uw apparaat registreren
#### <a name="to-complete-the-setup-and-register-the-device"></a>De installatie voltooien en het apparaat registreren
1. Open een browservenster en maak verbinding met de lokale web-gebruikersinterface. Type:
   
   `https://<ip-address of network interface>`
   
   Gebruik de verbindings-URL die in de vorige stap is vermeld. Er ziet een fout die aangeeft dat er een probleem is met het beveiligingscertificaat van de website. Klik **op Doorgaan naar deze webpagina**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Meld u aan bij de webgebruikersinterface van uw virtuele array als **StorSimpleAdmin**. Voer het wachtwoord van de apparaatbeheerder in dat u hebt gewijzigd in stap 3: Start de virtuele array in [Provision a StorSimple Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of in [Provision a StorSimple Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. U wordt naar de **startpagina** gebracht. Op deze pagina worden de verschillende instellingen beschreven die nodig zijn om de virtuele array te configureren en te registreren met de StorSimple Device Manager-service. De **netwerkinstellingen,** **webproxy-instellingen**en **tijdinstellingen** zijn optioneel. De enige vereiste instellingen zijn **apparaatinstellingen** en **cloudinstellingen.**
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Op de pagina **Netwerkinstellingen** onder **Netwerkinterfaces**wordt DATA 0 automatisch voor u geconfigureerd. Elke netwerkinterface is standaard ingesteld om ip-adres automatisch (DHCP) op te halen. Daarom worden automatisch een IP-adres, subnet en gateway toegewezen (voor zowel IPv4 als IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Als u tijdens de inrichting van het apparaat meer dan één netwerkinterface hebt toegevoegd, u deze hier configureren. Houd er rekening mee dat u uw netwerkinterface alleen als IPv4 of als IPv4 en IPv6 configureren. Alleen IPv6-configuraties worden niet ondersteund.
5. DNS-servers zijn vereist omdat ze worden gebruikt wanneer uw apparaat probeert te communiceren met uw cloudopslagserviceproviders of om uw apparaat op naam op te lossen wanneer het is geconfigureerd als bestandsserver. Klik op de pagina **Netwerkinstellingen** onder de **DNS-servers:**
   
   1. Een primaire en secundaire DNS-server worden automatisch geconfigureerd. Als u ervoor kiest om statische IP-adressen te configureren, u DNS-servers opgeven. Voor hoge beschikbaarheid raden we u aan een primaire en een secundaire DNS-server te configureren.
   2. Klik **op Toepassen** om de netwerkinstellingen toe te passen en te valideren.
6. Ga als een op de pagina **Apparaatinstellingen:**
   
   1. Wijs een unieke **naam** toe aan uw apparaat. Deze naam kan 1-15 tekens zijn en kan letter, getallen en koppeltekens bevatten.
   2. Klik **op** het ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) pictogram Bestandsserver voor het **type** apparaat dat u maakt. Met een bestandsserver u gedeelde mappen maken.
   3. Omdat uw apparaat een bestandsserver is, moet u het apparaat aansluiten bij een domein. Voer een **domeinnaam in**.
   4. Klik op **Toepassen**.
7. Er verschijnt een dialoogvenster. Voer uw domeinreferenties in de opgegeven indeling in. Klik op het controlepictogram. De domeinreferenties worden geverifieerd. U ziet een foutbericht als de referenties onjuist zijn.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klik op **Toepassen**. Dit is van toepassing en valideert de apparaatinstellingen.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Controleer of uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Active Directory bevindt en dat er geen groepsbeleidsobjecten (GPO) op worden toegepast of overgenomen. Groepsbeleid kan toepassingen zoals antivirussoftware installeren op de StorSimple Virtual Array. Het installeren van extra software wordt niet ondersteund en kan leiden tot gegevensbeschadiging. 
   > 
   > 
9. (Optioneel) configureer uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Ga **als** een webpagina naar de andere:
   
   1. De URL van de **webproxy** in deze indeling opgeven: *http://&lt;host-IP-adres of FQDN-:Port-nummer&gt;*. Https-URL's worden niet ondersteund.
   2. **Verificatie** opgeven als **basis** of **geen**.
   3. Als u verificatie gebruikt, moet u ook een **gebruikersnaam** en **wachtwoord**opgeven.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde webproxy-instellingen gevalideerd en toegepast.
10. (Optioneel) configureer de tijdinstellingen voor uw apparaat, zoals tijdzone en de primaire en secundaire NTP-servers. NTP-servers zijn vereist omdat uw apparaat de tijd moet synchroniseren, zodat het kan verifiëren met uw cloudserviceproviders.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Ga als een op de pagina **Tijdinstellingen:**
    
    1. Selecteer in de vervolgkeuzelijst de **tijdzone** op basis van de geografische locatie waarin het apparaat wordt geïmplementeerd. De standaardtijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
    2. Geef een **primaire NTP-server** op voor uw apparaat of accepteer de standaardwaarde van time.windows.com. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.
    3. Geef optioneel een **secundaire NTP-server** op voor uw apparaat.
    4. Klik op **Toepassen**. Hiermee worden de geconfigureerde tijdinstellingen gevalideerd en toegepast.
11. Configureer de cloudinstellingen voor uw apparaat. In deze stap voltooit u de lokale apparaatconfiguratie en registreert u het apparaat bij uw StorSimple Device Manager-service.
    
    1. Voer de **serviceregistratiesleutel** in die u hebt in [stap 2: Download de serviceregistratiesleutel](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) voor StorSimple Virtual Array.
    2. Als dit uw eerste apparaat is dat zich registreert bij deze service, krijgt u de **versleutelingssleutel servicegegevens**te zien. Kopieer deze sleutel en bewaar deze op een veilige plaats. Deze sleutel is vereist met de serviceregistratiesleutel om extra apparaten te registreren bij de StorSimple Device Manager-service. 
       
       Als dit niet het eerste apparaat is dat u bij deze service registreert, moet u de versleutelingssleutel voor servicegegevens verstrekken. Raadpleeg voor meer informatie de [versleutelingssleutel voor servicegegevens](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) op uw lokale webgebruikersinterface.
    3. Klik **op Registreren**. Hiermee wordt het apparaat opnieuw opgestart. Het kan nodig zijn om 2-3 minuten te wachten voordat het apparaat met succes is geregistreerd. Nadat het apparaat opnieuw is opgestart, wordt u naar de aanmeldingspagina geleid.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Ga terug naar de Azure-portal. Ga naar **Alle bronnen,** zoek naar uw StorSimple Device Manager-service.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Selecteer in de gefilterde lijst de StorSimple Device Manager-service en navigeer vervolgens naar **Beheer > apparaten.** Controleer in het blade **Apparaten** of het apparaat met succes is verbonden met de service en de status Klaar is **om in te stellen.**
    
    ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Stap 2: Het apparaat configureren als bestandsserver
Voer de volgende stappen uit in de [Azure-portal](https://portal.azure.com/) om de vereiste apparaatconfiguratie te voltooien.

#### <a name="to-configure-the-device-as-file-server"></a>Het apparaat configureren als bestandsserver
1. Ga naar uw StorSimple Device Manager-service en ga vervolgens naar **Beheer >-apparaten**. Selecteer in het blade **Apparaten** het apparaat dat u zojuist hebt gemaakt. Dit apparaat zou worden weergegeven als **klaar om in te stellen.**
   
   ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klik op het apparaat en u ziet een bannerbericht dat aangeeft dat het apparaat klaar is om in te stellen.
   
    ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klik **op Configureren** op de opdrachtbalk. Hiermee wordt het **configureerblad** geopend. Ga als volgt te werk in het blad **configureren:**
   
   1. De naam van de bestandsserver wordt automatisch ingevuld.
    
   2. Controleer of de cloudopslagversleuteling is ingesteld op **Ingeschakeld**. Hiermee worden alle gegevens die naar de cloud worden verzonden, versleuteld. 
    
   3. Een 256-bits AES-toets wordt gebruikt met de door de gebruiker gedefinieerde sleutel voor versleuteling. Geef een 32-tekensleutel op en voer de sleutel opnieuw in om deze te bevestigen. Neem de sleutel op in een sleutelbeheer-app voor toekomstige referentie.
    
   4. Klik **op Vereiste instellingen configureren** om opslagaccountreferenties op te geven die met uw apparaat moeten worden gebruikt. Klik **op Nieuw toevoegen** als er geen opslagaccountreferenties zijn geconfigureerd. **Zorg ervoor dat het opslagaccount dat u gebruikt blokblobs ondersteunt. Paginablobs worden niet ondersteund.** Meer informatie over [blokken blobs en pagina blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Ga als volgt te werk in het blad Referenties **van een opslagaccount** toevoegen: 

    1. Kies het huidige abonnement als het opslagaccount in hetzelfde abonnement zit als de service. Geef een andere is de opslag account is buiten de service abonnement. 
    
    2. Kies in de vervolgkeuzelijst een bestaand opslagaccount. 
    
    3. De locatie wordt automatisch ingevuld op basis van het opgegeven opslagaccount. 
    
    4. Stel TLS in om een veilig netwerkcommunicatiekanaal tussen het apparaat en de cloud te garanderen.
    
    5. Klik **op Toevoegen** om deze opslagaccountreferenties toe te voegen. 
   
        ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Zodra het opslagaccountreferentie is gemaakt, wordt het blad **configureren** bijgewerkt om de opgegeven opslagaccountreferenties weer te geven. Klik **op Configureren**.
   
   ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   U ziet een dat een bestandsserver wordt gemaakt. Zodra de bestandsserver is gemaakt, wordt u hiervan op de hoogte gesteld.
   
   ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   De status van het apparaat wordt ook gewijzigd in **Online.**
   
   ![Een bestandsserver configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   U doorgaan met het toevoegen van een aandeel.

## <a name="step-3-add-a-share"></a>Stap 3: Een aandeel toevoegen
Voer in de [Azure-portal](https://portal.azure.com/) de volgende stappen uit om een share te maken.

#### <a name="to-create-a-share"></a>Een aandeel maken
1. Selecteer het bestandsserverapparaat dat u in de vorige stap hebt geconfigureerd en klik op **...** (of klik met de rechtermuisknop). Selecteer **Delen toevoegen**in het contextmenu . U ook op **+ Delen toevoegen op** de opdrachtbalk van het apparaat klikken.
   
   ![Een share toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Geef de volgende deelinstellingen op:

   1. Een unieke naam voor uw aandeel. De naam moet een tekenreeks zijn die 3 tot 127 tekens bevat.
    
   2. Een optionele **beschrijving** voor het aandeel. De beschrijving helpt bij het identificeren van de eigenaren van aandelen.
    
   3. Een **type** voor het aandeel. Het type kan **worden gerijd** of **lokaal vastgemaakt,** waarbij gelaagd is de standaardinstelling. Selecteer een **lokaal vastgemaakt** aandeel voor workloads waarvoor lokale garanties, lage latencies en hogere prestaties nodig zijn. Selecteer een **trapsgewijs** aandeel voor alle andere gegevens.
      Een lokaal vastgemaakt aandeel is dik ingericht en zorgt ervoor dat de primaire gegevens op het aandeel lokaal op het apparaat blijven en niet naar de cloud worden gelekt. Een gelaagd aandeel daarentegen is dun ingericht. Wanneer u een gelaagd aandeel maakt, wordt 10% van de ruimte op de lokale laag ingericht en wordt 90% van de ruimte in de cloud ingericht. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de cloud gebruikt wanneer de gegevenslagen worden gebruikt. Dit impliceert op zijn beurt dat als u geen ruimte meer hebt op het apparaat, u geen gedifferentieerd aandeel inrichten.
   
   4. Wijs in het veld **Standaardmachtigingen voor volledige machtigingen instellen** toe aan de gebruiker of de groep die toegang heeft tot dit aandeel. Geef de naam van de gebruiker of de gebruikersgroep op in *de\@contoso.com-indeling.* We raden u aan een gebruikersgroep (in plaats van één gebruiker) te gebruiken om beheerdersrechten toe te staan toegang te krijgen tot deze shares. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.
   
   5. Klik **op Toevoegen** om de share te maken. 
    
       ![Een share toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       U ontvangt een melding als wordt begonnen met het maken van de share.
   
       ![Een share toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Nadat het aandeel is gemaakt met de opgegeven instellingen, wordt het blade **van Aandelen** bijgewerkt om het nieuwe aandeel weer te geven. Standaard zijn monitoring en back-up ingeschakeld voor het aandeel.
   
      ![Een share toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Stap 4: Verbinding maken met het aandeel
U moet nu verbinding maken met een of meer aandelen die u in de vorige stap hebt gemaakt. Voer deze stappen uit op uw Windows Server-host die is verbonden met uw StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Verbinding maken met het aandeel
1. Druk ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) op + R. Geef in het venster Uitvoeren de * &lt;&#92;&#92;bestandsservernaam&gt; * als pad op en vervang de naam van de *bestandsserver* door de apparaatnaam die u aan de bestandsserver hebt toegewezen. Klik op **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Hiermee wordt Verkenner geopend. U moet nu de shares kunnen zien die u als mappen hebt gemaakt. Selecteer een share (map) en dubbelklik erop om de inhoud te bekijken.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. U nu bestanden toevoegen aan deze shares en een back-up maken.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van de lokale web-gebruikersinterface om [uw StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)te beheren.

