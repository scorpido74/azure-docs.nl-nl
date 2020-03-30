---
title: Gegevens van StorSimple Virtual Array-opslagaccountgegevens beheren | Microsoft Documenten
description: Hier wordt uitgelegd hoe u de pagina StorSimple Device Manager Configure gebruiken om de beveiligingssleutels voor opslagaccountreferenties die zijn gekoppeld aan de Virtuele Array StorSimple toe te voegen, te bewerken, te verwijderen of te roteren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297575"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple-apparaatbeheer gebruiken om opslagaccountreferenties voor StorSimple Virtual Array te beheren

## <a name="overview"></a>Overzicht
De **sectie Configuratie** van het StorSimple Device Manager-serviceblad van uw StorSimple Virtual Array bevat de algemene serviceparameters die kunnen worden gemaakt in de StorSimple Manager-service. Deze parameters kunnen worden toegepast op alle apparaten die met de service zijn verbonden en omvatten:

* Opslagaccountreferenties
* Toegangsbeheerrecords
  
  ![Dashboard Apparaatbeheerservice](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

In deze zelfstudie wordt uitgelegd hoe u opslagaccountreferenties voor uw StorSimple Virtual Array toevoegen, bewerken of verwijderen. De informatie in deze zelfstudie is alleen van toepassing op de StorSimple Virtual Array. Zie [De StorSimple Manager-service gebruiken om uw opslagaccount te beheren voor](storsimple-manage-storage-accounts.md)informatie over het beheren van opslagaccounts in de serie 8000.

Opslagaccountreferenties bevatten de referenties die het apparaat gebruikt om toegang te krijgen tot uw opslagaccount bij uw cloudserviceprovider. Voor Microsoft Azure-opslagaccounts zijn dit referenties zoals de accountnaam en de primaire toegangssleutel.

In het blade **met referenties voor opslagaccounts** worden alle opslagaccountreferenties die voor het factureringsabonnement zijn gemaakt, weergegeven in een tabelindeling met de volgende gegevens:

* **Naam** : de unieke naam die aan het account is toegewezen toen het werd gemaakt.
* **SSL ingeschakeld** – Of de TLS is ingeschakeld en device-to-cloud communicatie is via het beveiligde kanaal.
  
  ![Configuratiesectie](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

De meest voorkomende taken met betrekking tot opslagaccountreferenties die kunnen worden uitgevoerd op het blade met referenties van het **opslagaccount,** zijn:

* Een opslagaccountreferentie toevoegen
* Een opslagaccountreferentie bewerken
* Een opslagaccountreferentie verwijderen

## <a name="types-of-storage-account-credentials"></a>Typen opslagaccountreferenties
Er zijn drie soorten opslagaccountreferenties die kunnen worden gebruikt met uw StorSimple-apparaat.

* **Automatisch gegenereerde opslagaccountreferenties** – Zoals de naam al doet vermoeden, wordt dit type opslagaccountreferentie automatisch gegenereerd wanneer de service voor het eerst wordt gemaakt. Zie [Een nieuwe service maken](storsimple-virtual-array-manage-service.md#create-a-service)voor meer informatie over hoe deze opslagaccountreferentie wordt gemaakt.
* **opslagaccountreferenties in het serviceabonnement:** dit zijn de Azure-opslagaccountreferenties die zijn gekoppeld aan hetzelfde abonnement als dat van de service. Zie [Over Azure Storage Accounts](../storage/common/storage-create-storage-account.md)voor meer informatie over hoe deze opslagaccountreferenties worden gemaakt.
* **opslagaccountreferenties buiten het serviceabonnement:** dit zijn de Azure-opslagaccountreferenties die niet aan uw service zijn gekoppeld en waarschijnlijk bestonden voordat de service werd gemaakt.

## <a name="add-a-storage-account-credential"></a>Een opslagaccountreferentie toevoegen
U een opslagaccountreferentie toevoegen aan uw StorSimple Device Manager-serviceconfiguratie door een unieke vriendelijke naam en toegangsreferenties op te geven die zijn gekoppeld aan het opslagaccount. U hebt ook de mogelijkheid om de TLS-modus (Transport Layer Security), voorheen bekend als Secure Sockets Layer (SSL)-modus, in te schakelen om een beveiligd kanaal voor netwerkcommunicatie tussen uw apparaat en de cloud te maken.

U meerdere accounts maken voor een bepaalde cloudserviceprovider. Terwijl de opslagaccountgegevens worden opgeslagen, probeert de service te communiceren met uw cloudserviceprovider. De referenties en het door u opgegeven toegangsmateriaal worden op dit moment geverifieerd. Een opslagaccountreferentie wordt alleen gemaakt als de verificatie slaagt. Als de verificatie mislukt, wordt een geschikt foutbericht weergegeven.

Gebruik de volgende procedures om Azure-opslagaccountreferenties toe te voegen:

* Een opslagaccountreferentie toevoegen met hetzelfde Azure-abonnement als de Service Apparaatbeheer
* Een Azure-opslagaccountreferentie toevoegen die buiten het serviceabonnement voor Apparaatbeheer valt

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Een opslagaccountreferentie toevoegen met hetzelfde Azure-abonnement als de Service Apparaatbeheer

1. Navigeer naar de service Apparaatbeheer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.**
3. Klik op**toevoegen**.
4. Ga als volgt te werk in het blad **Een opslagaccount toevoegen:**
   
    1. Selecteer **Huidig** **voor Abonnement**.
    2. Geef de naam op van uw Azure-opslagaccount.
    3. Selecteer **Inschakelen** om een beveiligd kanaal te maken voor netwerkcommunicatie tussen uw StorSimple-apparaat en de cloud. Selecteer **Alleen uitschakelen** als u binnen een privécloud werkt.
    4. Klik op**toevoegen**. U wordt hiervan op de hoogte gesteld nadat het opslagaccount is gemaakt.<br></br>
   
        ![Een bestaand opslagaccountreferentie toevoegen](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Een Azure-opslagaccountreferentie toevoegen die buiten het serviceabonnement voor Apparaatbeheer valt

1. Navigeer naar de service Apparaatbeheer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.** Hier worden alle bestaande opslagaccountreferenties weergegeven die zijn gekoppeld aan de StorSimple Device Manager-service.
3. Klik op**toevoegen**.
4. Ga als volgt te werk in het blad **Een opslagaccount toevoegen:**
   
    1. Selecteer Voor **Abonnement**de optie **Andere**.
   
    2. Geef de naam op van uw Azure-opslagaccountreferentie.
   
    3. Geef in het tekstvak **Voor toegang tot opslagaccounttoegang** de primaire toegangssleutel voor uw Azure-opslagaccountreferentie. Als u deze sleutel wilt ophalen, gaat u naar de Azure Storage-service, selecteert u uw opslagaccountreferentie en klikt u op **Accountsleutels beheren**. U nu de primaire toegangssleutel kopiëren.
   
    4. Als u TLS wilt inschakelen, klikt u op de knop **Inschakelen** om een beveiligd kanaal te maken voor netwerkcommunicatie tussen uw StorSimple Device Manager-service en de cloud. Klik alleen **op de** knop Uitschakelen als u actief bent in een privécloud.
   
    5. Klik op**toevoegen**. U wordt hiervan op de hoogte gesteld nadat het opslagaccountreferentie is gemaakt.

5. De nieuw gemaakte opslagaccountreferentie wordt weergegeven op het StorSimple Configure Device Manager-serviceblad onder **Accountreferenties opslag.**
   
    ![Een opslagaccountreferentie toevoegen buiten het abonnement op de Service Apparaatbeheer](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Een opslagaccountreferentie bewerken
U een opslagaccountgegevens bewerken die door uw apparaat worden gebruikt. Als u een opslagaccountreferentie bewerkt die momenteel in gebruik is, zijn de velden die beschikbaar zijn om te wijzigen de toegangssleutel en de TLS-modus voor het opslagaccountreferentie. U de nieuwe opslagtoegangssleutel leveren of de selectie van de **SSL-modus inschakelen** wijzigen en de bijgewerkte instellingen opslaan.

#### <a name="to-edit-a-storage-account-credential"></a>Een opslagaccountreferentie bewerken
1. Navigeer naar de service Apparaatbeheer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.** Hier worden alle bestaande opslagaccountreferenties weergegeven die zijn gekoppeld aan de StorSimple Device Manager-service.
3. Selecteer en dubbelklik in de tabellijst met opslagaccountreferenties op het account dat u wilt wijzigen.
4. Ga als volgt te werk in het **eigenschappenblad met** voorraadgegevens voor opslagaccount:
   
   1. Indien nodig u de selectie van de **SSL-modus inschakelen** wijzigen.
   2. U ervoor kiezen om toegangssleutels voor uw opslagaccountreferentie opnieuw te genereren. Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie . Lever de nieuwe referentiesleutel voor opslagaccount. Voor een Azure-opslagaccount is dit de primaire toegangssleutel.
   3. Klik boven aan **het** eigenschappenblad op **Opslaan** om de instellingen op te slaan. De instellingen worden bijgewerkt op het blade van de referenties van het **opslagaccount.**
      
      ![Een opslagaccountreferentie bewerken](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Een opslagaccountreferentie verwijderen
> [!IMPORTANT]
> U een opslagaccountalleen verwijderen als deze niet in gebruik is. Als er een opslagaccountreferentie in gebruik is, wordt u hiervan op de hoogte gesteld.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Een opslagaccountreferentie verwijderen
1. Navigeer naar de service Apparaatbeheer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.** Hier worden alle bestaande opslagaccountreferenties weergegeven die zijn gekoppeld aan de StorSimple Device Manager-service.
3. Selecteer en dubbelklik in de tabellijst met opslagaccountreferenties op het account dat u wilt verwijderen.
4. Ga als volgt te werk in het **eigenschappenblad met** voorraadgegevens voor opslagaccount:
   
   1. Klik **op Verwijderen** om de referenties te verwijderen.
   2. Klik op **Ja** om door te gaan met de verwijdering wanneer u om bevestiging wordt gevraagd. De tabelvermelding wordt bijgewerkt om de wijzigingen weer te geven.
      
      ![Een opslagaccountreferentie verwijderen](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Referentiessleutels voor opslagaccount synchroniseren
Om veiligheidsredenen is sleutelrotatie vaak een vereiste in datacenters. Een Microsoft Azure-beheerder kan de primaire of secundaire sleutel regenereren of wijzigen door rechtstreeks toegang te krijgen tot de opslagaccountreferenties (via de Microsoft Azure Storage-service). De StorSimple Device Manager-service ziet deze wijziging niet automatisch.

Als u de StorSimple Device Manager-service wilt informeren over de wijziging, moet u toegang krijgen tot de StorSimple Device Manager-service, toegang krijgen tot het opslagaccountgegevens en vervolgens de primaire of secundaire sleutel synchroniseren (afhankelijk van welke sleutel is gewijzigd). De service krijgt dan de nieuwste sleutel, versleutelt de sleutels en stuurt de versleutelde sleutel naar het apparaat.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Sleutels voor opslagaccountreferenties synchroniseren in hetzelfde abonnement als de service (alleen Azure)
1. Selecteer in het landingsblad van de service uw service, dubbelklik op de servicenaam en klik vervolgens in de sectie **Configuratie** op **Accountreferenties opslaan**.
2. Selecteer in het blad Voor referenties van **het opslagaccount** in de lijst met referenties van opslagaccount de hoogtevan het opslagaccount waarvan u de sleutels wilt synchroniseren.
3. Ga als volgt te werk in het blad **Eigenschappen** voor het geselecteerde opslagaccount:
   
    1. Klik op **Meer**en klik vervolgens op **Toegangstoets synchroniseren**.
   
    2. Klik op **Synchronisatiesleutel** om de synchronisatie te voltooien wanneer u om bevestiging wordt gevraagd.
    
4. In de StorSimple Device Manager-service moet u de sleutel bijwerken die eerder is gewijzigd in de Microsoft Azure Storage-service. Als de primaire toegangssleutel is gewijzigd (geregenereerd) in het **hoofdblad van** het opslagaccount account synchroniseren, klikt u op Primair en klikt u vervolgens op **Synchronisatiesleutel**. Als de secundaire sleutel is gewijzigd, klikt u op **Secundair**en klikt u vervolgens op **Synchronisatiesleutel**.
   
    ![Toegangssleutel synchroniseren](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van uw StorSimple Virtual Array.](storsimple-ova-web-ui-admin.md)

