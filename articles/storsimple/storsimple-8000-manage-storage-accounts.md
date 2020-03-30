---
title: Opslagaccountreferenties beheren, apparaat uit de StorSimple 8000-serie
description: Hier wordt uitgelegd hoe u de pagina StorSimple Device Manager Configure gebruiken om de beveiligingssleutels voor een opslagaccount toe te voegen, te bewerken, te verwijderen of te roteren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297750"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>De StorSimple Device Manager-service gebruiken om uw opslagaccountreferenties te beheren

## <a name="overview"></a>Overzicht

De sectie **Configuratie** in het storSimple Device Manager-serviceblad bevat alle algemene serviceparameters die kunnen worden gemaakt in de StorSimple Device Manager-service. Deze parameters kunnen worden toegepast op alle apparaten die met de service zijn verbonden en omvatten:

* Opslagaccountreferenties
* Bandbreedtesjablonen 
* Toegangsbeheerrecords 

In deze zelfstudie wordt uitgelegd hoe u opslagaccountreferenties toevoegt, bewerkt of verwijdert of de beveiligingssleutels voor een opslagaccount roteren.

 ![Lijst met opslagaccountreferenties](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Opslagaccounts bevatten de referenties die het StorSimple-apparaat gebruikt om toegang te krijgen tot uw opslagaccount bij uw cloudserviceprovider. Voor Microsoft Azure-opslagaccounts zijn dit referenties zoals de accountnaam en de primaire toegangssleutel. 

In het blade **met referenties voor opslagaccounts** worden alle opslagaccounts die voor het factureringsabonnement zijn gemaakt, weergegeven in een tabelindeling met de volgende gegevens:

* **Naam** : de unieke naam die aan het account is toegewezen toen het werd gemaakt.
* **SSL ingeschakeld** – Of de TLS is ingeschakeld en device-to-cloud communicatie is via het beveiligde kanaal.
* **Gebruikt door** – Het aantal volumes dat het opslagaccount gebruikt.

De meest voorkomende taken met betrekking tot opslagaccounts die kunnen worden uitgevoerd, zijn:

* Een opslagaccount toevoegen 
* Een opslagaccount bewerken 
* Een opslagaccount verwijderen 
* Sleutelrotatie van opslagrekeningen 

## <a name="types-of-storage-accounts"></a>Typen opslagaccounts

Er zijn drie soorten opslagaccounts die kunnen worden gebruikt met uw StorSimple-apparaat.

* **Automatisch gegenereerde opslagaccounts** – Zoals de naam al doet vermoeden, wordt dit type opslagaccount automatisch gegenereerd wanneer de service voor het eerst wordt gemaakt. Zie [Stap 1: Een nieuwe service maken](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) in Uw [on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie over hoe dit opslagaccount wordt gemaakt. 
* **Opslagaccounts in het serviceabonnement:** dit zijn de Azure-opslagaccounts die zijn gekoppeld aan hetzelfde abonnement als dat van de service. Zie [Over Azure Storage Accounts](../storage/common/storage-create-storage-account.md)voor meer informatie over hoe deze opslagaccounts worden gemaakt. 
* **Opslagaccounts buiten het serviceabonnement:** dit zijn de Azure-opslagaccounts die niet aan uw service zijn gekoppeld en waarschijnlijk bestonden voordat de service werd gemaakt.

## <a name="add-a-storage-account"></a>Een opslagaccount toevoegen

U een opslagaccount toevoegen door een unieke vriendelijke naam en toegangsreferenties op te geven die zijn gekoppeld aan het opslagaccount (met de opgegeven cloudserviceprovider). U hebt ook de mogelijkheid om de TLS-modus (Transport Layer Security), voorheen bekend als Secure Sockets Layer (SSL)-modus, in te schakelen om een beveiligd kanaal voor netwerkcommunicatie tussen uw apparaat en de cloud te maken.

U meerdere accounts maken voor een bepaalde cloudserviceprovider. Houd er echter rekening mee dat u na het aanmaken van een opslagaccount de cloudserviceprovider niet wijzigen.

Terwijl het opslagaccount wordt opgeslagen, probeert de service te communiceren met uw cloudserviceprovider. De referenties en het door u opgegeven toegangsmateriaal worden op dit moment geverifieerd. Er wordt alleen een opslagaccount gemaakt als de verificatie slaagt. Als de verificatie mislukt, wordt een geschikt foutbericht weergegeven.

Gebruik de volgende procedures om Azure-opslagaccountreferenties toe te voegen:

* Een opslagaccountreferentie toevoegen met hetzelfde Azure-abonnement als de Service Apparaatbeheer
* Een Azure-opslagaccountreferentie toevoegen die buiten het serviceabonnement voor Apparaatbeheer valt

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Een Azure-opslagaccountreferentie toevoegen buiten het StorSimple Device Manager-serviceabonnement

1. Navigeer naar uw StorSimple Device Manager-service en selecteer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.** Hier worden alle bestaande opslagaccountreferenties weergegeven die zijn gekoppeld aan de StorSimple Device Manager-service.
3. Klik op**toevoegen**.
4. Ga als volgt te werk in het referentieblad voor **een opslagaccount** toevoegen:
   
    1. Selecteer Voor **Abonnement**de optie **Andere**.
   
    2. Geef de naam op van uw Azure-opslagaccountreferentie.
   
    3. Geef in het tekstvak **Voor toegang tot opslagaccounttoegang** de primaire toegangssleutel voor uw Azure-opslagaccountreferentie. Als u deze sleutel wilt ophalen, gaat u naar de Azure Storage-service, selecteert u uw opslagaccountreferentie en klikt u op **Accountsleutels beheren**. U nu de primaire toegangssleutel kopiëren.
   
    4. Als u TLS wilt inschakelen, klikt u op de knop **Inschakelen** om een beveiligd kanaal te maken voor netwerkcommunicatie tussen uw StorSimple Device Manager-service en de cloud. Klik alleen **op de** knop Uitschakelen als u actief bent in een privécloud.
   
    5. Klik op**toevoegen**. U wordt hiervan op de hoogte gesteld nadat het opslagaccountreferentie is gemaakt.

5. De nieuw gemaakte opslagaccountreferentie wordt weergegeven op het StorSimple Configure Device Manager-serviceblad onder **Accountreferenties opslag.**
   


## <a name="edit-a-storage-account"></a>Een opslagaccount bewerken

U een opslagaccount bewerken dat wordt gebruikt door een volumecontainer. Als u een opslagaccount bewerkt dat momenteel in gebruik is, is het enige veld dat u wijzigen de toegangssleutel voor het opslagaccount. U de nieuwe opslagtoegangssleutel leveren en de bijgewerkte instellingen opslaan.

#### <a name="to-edit-a-storage-account"></a>Een opslagaccount bewerken

1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.

    ![Opslagaccountreferenties](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Selecteer en klik in het blad voor referenties van **het opslagaccount** in de lijst met referenties van opslagaccounten op het blad dat u wilt bewerken. 

3. U de **SSL-selectie inschakelen** wijzigen. U ook op **Meer klikken...** en vervolgens toegangssleutel synchroniseren selecteren om de toegangssleutels van uw opslagaccount **te roteren.** Ga naar [Key rotation of storage accounts](#key-rotation-of-storage-accounts) voor meer informatie over het uitvoeren van sleutelrotatie. Nadat u de instellingen hebt gewijzigd, klikt u op **Opslaan**. 

    ![Referenties van bewerkte opslagaccount opslaan](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Klik op **Ja** als u om bevestiging wordt gevraagd. 

    ![Wijzigingen bevestigen](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

De instellingen worden bijgewerkt en opgeslagen voor uw opslagaccount. 

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

> [!IMPORTANT]
> U een opslagaccount alleen verwijderen als het niet wordt gebruikt door een volumecontainer. Als een opslagaccount wordt gebruikt door een volumecontainer, verwijdert u eerst de volumecontainer en verwijdert u vervolgens het bijbehorende opslagaccount.

#### <a name="to-delete-a-storage-account"></a>Een opslagaccount verwijderen

1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.

2. Plaats in de tabellijst met opslagaccounts de plaats in het account dat u wilt verwijderen. Klik met de rechtermuisknop om het contextmenu aan te roepen en klik op **Verwijderen**.

    ![Opslagaccountreferenties verwijderen](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Klik op **Ja** om door te gaan met de verwijdering wanneer u om bevestiging wordt gevraagd. De tabeltabelaanbieding wordt bijgewerkt om de wijzigingen weer te geven.

    ![De verwijdering bevestigen](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Sleutelrotatie van opslagrekeningen

Om veiligheidsredenen is sleutelrotatie vaak een vereiste in datacenters. Elk Microsoft Azure-abonnement kan een of meer gekoppelde opslagaccounts hebben. De toegang tot deze accounts wordt beheerd door het abonnement en toegangssleutels voor elk opslagaccount. 

Wanneer u een opslagaccount maakt, genereert Microsoft Azure twee 512-bits opslagtoegangssleutels die worden gebruikt voor verificatie wanneer het opslagaccount wordt geopend. Met twee opslagtoegangssleutels u de sleutels opnieuw genereren zonder onderbreking van uw opslagservice of toegang tot die service. De sleutel die momenteel in gebruik is, is de *primaire* sleutel en de back-upsleutel wordt de *secundaire* sleutel genoemd. Een van deze twee sleutels moet worden geleverd wanneer uw Microsoft Azure StorSimple-apparaat toegang heeft tot uw cloudopslagserviceprovider.

## <a name="what-is-key-rotation"></a>Wat is sleutelrotatie?

Toepassingen gebruiken doorgaans slechts één van de sleutels om toegang te krijgen tot uw gegevens. Na een bepaalde periode u uw toepassingen laten overschakelen naar het gebruik van de tweede sleutel. Nadat u uw toepassingen hebt overgeschakeld naar de secundaire sleutel, u de eerste sleutel met pensioen laten gaan en vervolgens een nieuwe sleutel genereren. Met behulp van de twee sleutels op deze manier u uw toepassingen toegang tot de gegevens zonder enige downtime.

De opslagaccountsleutels worden altijd in een versleutelde vorm in de service opgeslagen. Deze kunnen echter worden gereset via de StorSimple Device Manager-service. De service kan de primaire sleutel en secundaire sleutel krijgen voor alle opslagaccounts in hetzelfde abonnement, inclusief accounts die zijn gemaakt in de opslagservice en de standaardopslagaccounts die zijn gegenereerd toen de StorSimple Device Manager-service voor het eerst werd gegenereerd Gemaakt. De StorSimple Device Manager-service haalt deze sleutels altijd uit de klassieke Azure-portal en slaat ze vervolgens op een versleutelde manier op.

## <a name="rotation-workflow"></a>Rotatiewerkstroom

Een Microsoft Azure-beheerder kan de primaire of secundaire sleutel regenereren of wijzigen door rechtstreeks toegang te krijgen tot het opslagaccount (via de Microsoft Azure Storage-service). De StorSimple Device Manager-service ziet deze wijziging niet automatisch.

Als u de StorSimple Device Manager-service wilt informeren over de wijziging, moet u toegang krijgen tot de StorSimple Device Manager-service, toegang krijgen tot het opslagaccount en vervolgens de primaire of secundaire sleutel synchroniseren (afhankelijk van welke sleutel is gewijzigd). De service krijgt dan de nieuwste sleutel, versleutelt de sleutels en stuurt de versleutelde sleutel naar het apparaat.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Sleutels voor opslagaccounts synchroniseren in hetzelfde abonnement als de service 
1. Ga naar uw StorSimple-apparaatbeheerservice. Klik in de sectie **Configuratie** op **Opslagaccountreferenties**.
2. Klik in de tabeltabellijst met opslagaccounts op de vermelding die u wilt wijzigen. 

    ![toetsen synchroniseren](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klik **op ... Meer** en selecteer vervolgens **Toegangssleutel synchroniseren om te roteren.**   

    ![toetsen synchroniseren](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. In de StorSimple Device Manager-service moet u de sleutel bijwerken die eerder is gewijzigd in de Microsoft Azure Storage-service. Als de primaire toegangssleutel is gewijzigd (geregenereerd), selecteert u **primaire** sleutel. Als de secundaire sleutel is gewijzigd, selecteert u **de secundaire** toets. Klik **op Synchronisatietoets**.
      
      ![toetsen synchroniseren](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

U wordt hiervan op de hoogte gesteld nadat de sleutel is gesynchroniseerd.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Sleutels voor opslagaccounts buiten het serviceabonnement synchroniseren
1. Klik **op** de pagina Services op het tabblad **Configureren.**
2. Klik **op Opslagaccounts toevoegen/bewerken**.
3. Ga als volgt te werk in het dialoogvenster:
   
   1. Selecteer het opslagaccount met de toegangssleutel die u wilt bijwerken.
   2. U moet de opslagtoegangssleutel bijwerken in de StorSimple Device Manager-service. In dit geval ziet u de opslagtoegangssleutel. Voer de nieuwe sleutel in het vak **Toegangssleutel voor opslagaccount in.** 
   3. Sla uw wijzigingen op. Uw toegangssleutel voor uw opslagaccount moet nu worden bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Beveiliging van StorSimple](storsimple-8000-security.md).
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

