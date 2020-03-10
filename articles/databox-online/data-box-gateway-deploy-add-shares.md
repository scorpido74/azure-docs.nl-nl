---
title: Gegevens overdragen met Azure Data Box Gateway | Microsoft Docs
description: Informatie over het toevoegen van en verbinding maken met shares op een Data Box Gateway-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 623ec5e082a8ed889329936d020bf28434a56fbf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942529"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Zelf studie: gegevens overdragen met Azure Data Box Gateway


## <a name="introduction"></a>Inleiding

In dit artikel wordt beschreven hoe u shares op uw Data Box Gateway toevoegt en er verbinding mee maakt. Nadat u de shares hebt toegevoegd, kan Data Box Gateway apparaat gegevens naar Azure overdragen.

Deze procedure neemt in totaal ongeveer tien minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met een share


## <a name="prerequisites"></a>Vereisten

Voordat u shares aan Data Box Gateway gaat toevoegen, controleert u het volgende:

- U hebt een virtueel apparaat ingericht en er verbinding mee gemaakt zoals beschreven in het [inrichten van een Data Box gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) of het [inrichten van een Data Box gateway in VMware](data-box-gateway-deploy-provision-vmware.md).

- U hebt het virtuele apparaat geactiveerd dat wordt beschreven in [verbinding maken en uw Azure data Box gateway activeren](data-box-gateway-deploy-connect-setup-activate.md).

- Het apparaat is klaar om bestandsshares te maken en gegevens over te dragen.

## <a name="add-a-share"></a>Een share toevoegen

Voer de volgende procedure uit om een share te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw data Box gateway resource en ga vervolgens naar **overzicht**. Uw apparaat moet online zijn. Selecteer **+ share toevoegen** op de opdracht balk van het apparaat.
   
   ![Een share toevoegen](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Voer de volgende procedure uit in **share toevoegen**:

    1. Geef een unieke naam voor de share op. De namen van shares mogen alleen kleine letters, cijfers en afbreek streepjes bevatten. De share naam moet tussen de 3 en 63 tekens lang zijn en met een letter of cijfer beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.
    
    2. Selecteer een **Type** voor de share. Het type kan SMB of NFS zijn, waarbij SMB het standaardtype is. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

    3. Geef een opslag account op waarop de share wordt opgeslagen. Als er nog geen container bestaat, wordt in het opslagaccount een container gemaakt met de naam van de zojuist gemaakte share. Als er al een container bestaat, wordt de bestaande container gebruikt.
    
    4. Kies de **Opslagservice** vanuit blok-blob, pagina-blob of bestanden. Het type service dat u kiest, is afhankelijk van de indeling waarin u de gegevens in Azure wilt opslaan. In dit geval kiezen we ervoor de gegevens als blok-blobs in Azure op te slaan, dus we selecteren Blok-blob. Als u Pagina-blob kiest, dient u ervoor te zorgen dat uw gegevens op 512 bytes zijn uitgelijnd. VHDX is bijvoorbeeld altijd op 512 bytes uitgelijnd.
   
    5. Deze stap hangt af van of u een SMB- of een NFS-share gaat maken.
     
    - **SMB-share** : Selecteer in **alle bevoegdheden lokale gebruiker de**optie **nieuwe maken** of **bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, voert u een **gebruikers naam** en **wacht woord**in en bevestigt u vervolgens het **wacht woord**. Met deze actie worden de machtigingen toegewezen aan de lokale gebruiker. Het wijzigen van machtigingen op share niveau wordt momenteel niet ondersteund.
    
        ![SMB-share toevoegen](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Als u selectie vakje **alleen lees bewerkingen** voor deze share gegevens toestaan inschakelt, kunt u alleen-lezen gebruikers opgeven.
        
    - **NFS-share** : Voer de IP-adressen in van de toegestane clients die toegang hebben tot de share.

        ![NFS-share toevoegen](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selecteer **Maken** om de share te maken.
    
    U ontvangt een melding als wordt begonnen met het maken van de share. Nadat de share is gemaakt met de opgegeven instellingen, wordt de tegel **shares** bijgewerkt om de nieuwe share weer te geven.
    
    ![Tegel bijgewerkte shares](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Verbinding maken met de share

U kunt nu verbinding maken met een of meer shares die u hebt gemaakt in de laatste stap. Afhankelijk van of u een SMB- of een NFS-share hebt, kunnen de stappen variëren.

### <a name="connect-to-an-smb-share"></a>Verbinding maken met een SMB-share

Maak op uw Windows Server-client verbinding met uw Data Box Gateway door de opdrachten in te voeren op een SMB-share:


1. Typ in een opdrachtvenster:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. De voorbeelduitvoer van deze opdracht wordt hier gepresenteerd.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Selecteer op het toetsen bord Windows + R. 
3. Geef in het venster **uitvoeren** de `\\<device IP address>` op en selecteer **OK**. Verkenner wordt geopend. Als het goed is, ziet u nu de shares die u hebt gemaakt, als mappen. Dubbelklik in Verkenner op een share (map) om de inhoud te bekijken.
 
    ![Verbinding maken met een SMB-share](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    De gegevens worden naar deze shares geschreven terwijl ze worden gegenereerd en het apparaat pusht de gegevens naar de cloud.

### <a name="connect-to-an-nfs-share"></a>Verbinding maken met een NFS-share

Ga als volgt te werk in de Linux-client die is verbonden met het Data Box Edge-apparaat:

1. Zorg ervoor dat de NFSv4-client is geïnstalleerd op de client. Als u de NFS-client wilt installeren, gebruikt u de volgende opdracht:

   `sudo apt-get install nfs-common`

    Ga naar [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (NFSv4-client installeren) voor meer informatie.

2. Als de NFS-client is geïnstalleerd, gebruikt u de volgende opdracht om de NFS-share te koppelen die u op uw Data Box Gateway-apparaat hebt gemaakt:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Voordat u de koppelingen gaat instellen, controleert u of de mappen die als koppelpunten op uw lokale computer fungeren, al zijn gemaakt en geen bestanden of submappen bevatten.

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een share op een Gateway-apparaat. Het IP-adres van het virtuele apparaat is `10.10.10.60`, de share `mylinuxshare2` wordt gekoppeld op de Ubuntu-VM, het koppelpunt is `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> De volgende opmerkingen zijn van toepassing op deze release:
> - Wanneer een bestand is gemaakt in de shares, wordt de naam van het bestand niet meer ondersteund.
> - Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.
> - Als u `rsync` gebruikt voor het kopiëren van gegevens, wordt de optie `rsync -a` niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met een share


Ga naar de volgende zelfstudie om te lezen hoe u Data Box Gateway kunt beheren.

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Gateway](https://aka.ms/dbg-docs) (Lokale webgebruikersinterface gebruiken om Data Box Gateway te beheren)


