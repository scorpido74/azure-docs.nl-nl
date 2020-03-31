---
title: Azure Data Box Edge-beheer van delen | Microsoft Documenten
description: Beschrijft hoe u de Azure-portal gebruiken om shares te beheren op uw Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: b25409c63806e203bd841b0373543b7cc2b96d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79212941"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>De Azure-portal gebruiken om aandelen te beheren op uw Azure Data Box Edge

In dit artikel wordt beschreven hoe u aandelen beheert op uw Azure Data Box Edge. U de Azure Data Box Edge beheren via de Azure-portal of via de lokale webgebruikersinterface. Gebruik de Azure-portal om shares toe te voegen, te verwijderen, te vernieuwen of te synchroniseren voor een opslagaccount dat is gekoppeld aan de shares.

## <a name="about-shares"></a>Info over shares

Als u gegevens wilt overbrengen naar Azure, moet u shares maken op uw Azure Data Box Edge. De shares die u toevoegt op het Data Box Edge-apparaat kunnen lokale shares of shares zijn die gegevens naar de cloud pushen.

 - **Lokale shares**: Gebruik deze aandelen wanneer u de gegevens lokaal op het apparaat wilt verwerken.
 - **Shares:** Gebruik deze shares wanneer u wilt dat de apparaatgegevens automatisch naar uw opslagaccount in de cloud worden gepusht. Alle cloudfuncties zoals **Opslagsleutels vernieuwen** en **synchroniseren** zijn van toepassing op de shares.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een share toevoegen
> * Een share verwijderen
> * Shares vernieuwen
> * Opslagsleutel synchroniseren


## <a name="add-a-share"></a>Een share toevoegen

Voer in de Azure Portal de volgende stappen uit om een share te maken.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Gateway > Shares**. Selecteer **+ Voeg delen toe** op de opdrachtbalk.

    ![Selecteer aandeel toevoegen](media/data-box-edge-manage-shares/add-share-1.png)

2. Geef in **Share toevoegen** de instellingen voor de share op. Geef een unieke naam voor de share op.
    
    Namen van shares mogen alleen cijfers, kleine letters en afbreekstreepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een cijfer of letter beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.

3. Selecteer een **Type** voor de share. Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

4. Geef het **opslagaccount** op waar de share zich bevindt. In het opslagaccount met de naam van de share wordt een container gemaakt, als deze nog niet bestaat. Als de container al bestaat, wordt de bestaande container gebruikt.

5. Kies in de vervolgkeuzelijst de **opslagservice** uit blokblob, paginablob of bestanden. Het type service dat u kiest, is afhankelijk van de indeling waarin u de gegevens in Azure wilt opslaan. In dit geval willen we bijvoorbeeld dat de gegevens zich bevinden als blokblobs in Azure, vandaar dat we **Blob blokkeren**selecteren. Als u **Paginablob**kiest, moet u ervoor zorgen dat uw gegevens 512 bytes zijn uitgelijnd. Gebruik **Paginablob** voor VHD's of VHDX die altijd 512 bytes uitgelijnd zijn.

   > [!IMPORTANT]
   > Zorg ervoor dat het Azure Storage-account dat u gebruikt geen onveranderlijkbeleid heeft ingesteld als u het gebruikt met een Azure Stack Edge- of Data Box Gateway-apparaat. Zie [Onveranderlijk beleid voor blobopslag instellen en beheren voor](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)meer informatie.

6. Deze stap hangt af van of u een SMB- of een NFS-share gaat maken.
    - **Als u een SMB-share maakt**: maak in het veld **All privilege local user** (Lokale gebruiker met alle bevoegdheden) een keuze uit **Nieuwe maken** of **Bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, geeft u de **gebruikersnaam** en het **wachtwoord** op en bevestigt u het wachtwoord. Hiermee worden de bevoegdheden aan de lokale gebruiker toegewezen. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.

        ![SMB-share toevoegen](media/data-box-edge-manage-shares/add-smb-share.png)

        Als u ervoor kiest om alleen alleen-lezen bewerkingen toe te staan voor deze share-gegevens, kunt u gebruikers opgeven met alleen-lezen machtigingen.
    - **Als u een NFS-share maakt**: geef de **IP-adressen op van de clients** die toegang hebben tot de share.

        ![NFS-share toevoegen](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Als u eenvoudig toegang wilt krijgen tot de shares van Edge compute modules, gebruikt u het lokale bevestigingspunt. Selecteer **Het aandeel gebruiken met Edge compute,** zodat het aandeel automatisch wordt gemonteerd nadat het is gemaakt. Wanneer deze optie is geselecteerd, kan de Edge-module ook de compute gebruiken met het lokale bevestigingspunt.

8. Klik op **Maken** om de share te maken. U ontvangt een melding als wordt begonnen met het maken van de share. Als de share met de opgegeven instellingen is gemaakt, wordt de blade **Shares** bijgewerkt in overeenstemming met de nieuwe share.

## <a name="add-a-local-share"></a>Een lokaal aandeel toevoegen

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Gateway > Shares**. Selecteer **+ Voeg delen toe** op de opdrachtbalk.

    ![Selecteer aandeel toevoegen](media/data-box-edge-manage-shares/add-local-share-1.png)

2. Geef in **Share toevoegen** de instellingen voor de share op. Geef een unieke naam voor de share op.
    
    Namen van shares mogen alleen cijfers, kleine letters en afbreekstreepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een cijfer of letter beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.

3. Selecteer een **Type** voor de share. Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

4. Als u eenvoudig toegang wilt krijgen tot de shares van Edge compute modules, gebruikt u het lokale bevestigingspunt. Selecteer **Het aandeel gebruiken met Edge compute,** zodat de Edge-module de compute kan gebruiken met het lokale bevestigingspunt.

5. Selecteer **Configureren als lokale edgeaandelen**. De gegevens in lokale aandelen blijven lokaal op het apparaat staan. U deze gegevens lokaal verwerken.

6. Kies in het **veld Lokale gebruikersvan alle bevoegdheden** uit Nieuw **maken** of **Bestaande gebruiken**.

7. Selecteer **Maken**. 

    ![Lokaal delen maken](media/data-box-edge-manage-shares/add-local-share-2.png)

    U ziet een melding dat de creatie van het aandeel aan de gang is. Als de share met de opgegeven instellingen is gemaakt, wordt de blade **Shares** bijgewerkt in overeenstemming met de nieuwe share.

    ![Updates bekijken Aandelenblad](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Selecteer het aandeel om het lokale mountpoint voor de Edge-rekenmodules voor dit aandeel weer te geven.

    ![Lokale share details bekijken](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Een aandeel monteren

Als u een aandeel hebt gemaakt voordat u compute configureerde op uw Data Box Edge-apparaat, moet u het aandeel monteren. Neem de volgende stappen om een aandeel te monteren.


1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Gateway > Shares**. Selecteer in de lijst van de aandelen het aandeel dat u wilt monteren. In de kolom **Gebruikt voor rekenkracht** wordt de status als **uitgeschakeld** voor het geselecteerde aandeel weergegeven.

    ![Share selecteren](media/data-box-edge-manage-shares/select-share-mount.png)

2. Selecteer **Monteren**.

    ![Mount selecteren](media/data-box-edge-manage-shares/select-mount.png)

3. Wanneer u om bevestiging wordt gevraagd, selecteert u **Ja**. Dit zal het aandeel monteren.

    ![Mount bevestigen](media/data-box-edge-manage-shares/confirm-mount.png)

4. Nadat het aandeel is gemonteerd, gaat u naar de lijst van aandelen. U ziet dat in de kolom **Gebruikt voor rekenkracht** de sharestatus als **Ingeschakeld wordt weergegeven.**

    ![Gedeeld gemonteerd](media/data-box-edge-manage-shares/share-mounted.png)

5. Selecteer het aandeel opnieuw om het lokale mountpoint voor het aandeel weer te geven. Edge compute module maakt gebruik van deze lokale mountpoint voor het aandeel.

    ![Lokaal mountpoint voor het aandeel](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Een aandeel demonteren

Volg de volgende stappen in de Azure-portal om een share los te maken.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Gateway > Shares**.

    ![Share selecteren](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Selecteer in de lijst van de aandelen het aandeel dat u wilt loskoppelen. U wilt ervoor zorgen dat het aandeel dat u loskoppelt, niet door modules wordt gebruikt. Als het aandeel wordt gebruikt door een module, dan ziet u problemen met de bijbehorende module. Selecteer **Het opheffen van de knop selecteren**.

    ![De montage opheffen selecteren](media/data-box-edge-manage-shares/select-unmount.png)

3. Wanneer u om bevestiging wordt gevraagd, selecteert u **Ja**. Dit zal het aandeel ontkoppelen.

    ![De montage bevestigen](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Nadat het aandeel is ontkoppeld, gaat u naar de lijst met aandelen. U ziet dat de kolom **Gebruikt voor rekenkolom** de sharestatus als **Uitgeschakeld**weergeeft.

    ![Niet gemonteerd delen](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Een share verwijderen

Voer in de Azure Portal de volgende stappen uit om een share te verwijderen.

1. Klik in de lijst met shares op de share die u wilt verwijderen.

    ![Share selecteren](media/data-box-edge-manage-shares/delete-share-1.png)

2. Klik **op Verwijderen**.

    ![Op Verwijderen klikken](media/data-box-edge-manage-shares/delete-share-2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![De verwijdering bevestigen](media/data-box-edge-manage-shares/delete-share-3.png)

De lijst met aandelen wordt bijgewerkt om de verwijdering weer te geven.


## <a name="refresh-shares"></a>Shares vernieuwen

Met de vernieuwingsfunctie u de inhoud van een aandeel vernieuwen. Wanneer u een share vernieuwt, wordt er een zoekopdracht gestart om de Azure-objecten te zoeken, inclusief blobs en bestanden die aan de cloud zijn toegevoegd na de laatste vernieuwing. Deze extra bestanden worden vervolgens gedownload om de inhoud van het aandeel op het apparaat te vernieuwen.

> [!IMPORTANT]
> - U lokale aandelen niet vernieuwen.
> - Machtigingen en toegangscontrolelijsten (ACL's) blijven niet behouden voor een vernieuwingsbewerking. 

Voer in de Azure Portal de volgende stappen uit om een share te vernieuwen.

1.   Ga in de Azure Portal naar **Shares**. Klik op de share die u wilt vernieuwen.

    ![Share selecteren](media/data-box-edge-manage-shares/refresh-share-1.png)

2.   Klik op **Vernieuwen**. 

    ![Op Vernieuwen klikken](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.   Klik op **Ja** als u om bevestiging wordt gevraagd. Er wordt een taak gestart om de inhoud van de on-premises share te vernieuwen.

    ![Vernieuwen bevestigen](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.   Terwijl het vernieuwen wordt uitgevoerd, is de optie Vernieuwen niet beschikbaar in het contextmenu. Klik op de taakmelding om de status van de vernieuwingstaak weer te geven.

5.   Hoe lang de vernieuwing duurt is afhankelijk van het aantal bestanden in de Azure-container en van het aantal bestanden op het apparaat. Zodra het vernieuwen is voltooid, wordt het tijdstempel van de share bijgewerkt. Zelfs als de vernieuwing niet geheel zonder fouten is uitgevoerd, wordt de bewerking als voltooid beschouwd en wordt het tijdstempel bijgewerkt. De vernieuwingsfoutlogboeken worden ook bijgewerkt.

    ![Bijgewerkt tijdstempel](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Als er een fout optreedt, wordt er een waarschuwing gegenereerd. De waarschuwing bevat meer informatie over de oorzaak en een aanbeveling om het probleem te verhelpen. De waarschuwing bevat ook een koppeling naar een bestand dat een volledig overzicht bevat van de fouten en van de bestanden die niet zijn bijgewerkt of verwijderd.


## <a name="sync-storage-keys"></a>Opslagsleutels synchroniseren

Als de sleutels van uw opslagaccount zijn geroteerd, moet u de toegangssleutels voor opslag synchroniseren. Door een synchronisatie kan het apparaat de meest recente sleutels voor uw opslagaccount verkrijgen.

Voer in de Azure Portal de volgende stappen uit om uw toegangssleutel voor opslag te synchroniseren.

1. Ga in uw resource naar **Overzicht**. Klik in de lijst met shares op een share die is gekoppeld aan het opslagaccount dat u wilt synchroniseren.

    ![Delen selecteren met een relevant opslagaccount](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Klik op **Opslagsleutel synchroniseren**. Klik op **Ja** als u om bevestiging wordt gevraagd.

     ![Opslagtoets synchroniseren selecteren](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Sluit het dialoogvenster wanneer de synchronisatie is voltooid.

>[!NOTE]
> U hoeft dit maar eenmaal te doen voor een gegeven opslagaccount. U hoeft deze actie niet te herhalen voor alle shares die zijn gekoppeld aan hetzelfde opslagaccount.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](data-box-edge-manage-users.md).
