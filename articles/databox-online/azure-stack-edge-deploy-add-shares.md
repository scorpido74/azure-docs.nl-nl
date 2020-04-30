---
title: Zelf studie voor het overdragen van gegevens naar shares met Azure Stack Edge | Microsoft Docs
description: Meer informatie over het toevoegen en verbinden met shares op Azure Stack edge-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e62d746be92583abead1c3edb93900068b123838
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82571075"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge"></a>Zelf studie: gegevens overdragen met Azure Stack Edge

In deze zelf studie wordt beschreven hoe u shares toevoegt aan en verbindt met uw Azure Stack edge-apparaat. Nadat u de shares hebt toegevoegd, kan Azure Stack Edge gegevens overdragen naar Azure.

Deze procedure neemt in totaal ongeveer tien minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met de share

 
## <a name="prerequisites"></a>Vereisten

Voordat u shares toevoegt aan Azure Stack Edge, moet u ervoor zorgen dat:

- U hebt uw fysieke apparaat geïnstalleerd zoals beschreven in [Azure stack Edge installeren](azure-stack-edge-deploy-install.md).

- U hebt het fysieke apparaat geactiveerd zoals beschreven in [verbinding maken, instellen en Azure stack Edge activeren](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Een share toevoegen

Ga als volgt te werk om een share te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw Azure stack Edge-resource en ga vervolgens naar het **overzicht**. Uw apparaat moet online zijn.

   ![Apparaat online](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Selecteer **+ share toevoegen** op de opdracht balk van het apparaat.

   ![Een share toevoegen](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. Ga als volgt te werk in het deelvenster **Share delen**:

    a. Geef in het vak **Naam** een unieke naam op voor de share.  
    De naam van de share mag alleen bestaan uit kleine letters, cijfers en afbreekstreepjes. De waarde moet tussen de 3 en 63 tekens lang zijn en met een letter of cijfer beginnen. Afbreekstreepjes moeten worden voorafgegaan door een letter of cijfer.
    
    b. Selecteer een **Type** voor de share.  
    Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients.  
    De andere opties verschillen enigszins, afhankelijk van of u kiest voor SMB- of NFS-shares. 

    c. Geef een opslag account op waarop de share wordt opgeslagen. 

    > [!IMPORTANT]
    > Zorg ervoor dat voor het Azure Storage account dat u gebruikt geen Onveranderbaarheid-beleid is ingesteld als u dit gebruikt met een Azure Stack Edge-of Data Box Gateway-apparaat. Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)voor meer informatie.
    
    d. Selecteer in de vervolgkeuzelijst **Storage-service** de optie **Blok-blob**, **Pagina-blob** of **Bestanden**.  
    Het type service dat u selecteert, is afhankelijk van de gewenste indeling voor de gegevens in Azure. In dit voor beeld selecteren we de gegevens als blok-blobs in azure, maar **blok-BLOB**. Als u **pagina-BLOB**selecteert, moet u ervoor zorgen dat uw gegevens 512 bytes zijn uitgelijnd. VHDX is bijvoorbeeld altijd op 512 bytes uitgelijnd.

    e. Een nieuwe BLOB-container maken of een bestaande uit de vervolg keuzelijst gebruiken. Als u een BLOB-container maakt, geeft u de naam van een container op. Als er nog geen container bestaat, wordt in het opslagaccount een container gemaakt met de naam van de zojuist gemaakte share.
   
    f. Voer een van de volgende stappen uit, afhankelijk van of u een SMB-share of een NFS-share hebt gemaakt: 
     
    - **SMB-share**: Selecteer onder **alle bevoegdheden lokale gebruiker de**optie **nieuwe maken** of **bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, voert u een gebruikersnaam en wachtwoord in. Vervolgens bevestigt u het wachtwoord. Met deze actie worden machtigingen toegewezen aan de lokale gebruiker. Nadat u de machtigingen hier hebt toegewezen, kunt u Verkenner gebruiken om ze aan te passen.

        Als u het selectie vakje **alleen lees bewerkingen toestaan** voor deze share gegevens inschakelt, kunt u alleen-lezen gebruikers opgeven.

        ![SMB-share toevoegen](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-share**: Voer de IP-adressen in van de toegestane clients die toegang hebben tot de share.

        ![NFS-share toevoegen](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecteer **Maken** om de share te maken.
    
    U ontvangt een melding als wordt begonnen met het maken van de share. Nadat de share is gemaakt met de opgegeven instellingen, wordt de tegel **shares** bijgewerkt om de nieuwe share weer te geven.
    

## <a name="connect-to-the-share"></a>Verbinding maken met de share

U kunt nu verbinding maken met een of meer shares die u hebt gemaakt in de laatste stap. Afhankelijk van of u een SMB- of een NFS-share hebt, kunnen de stappen variëren.

### <a name="connect-to-an-smb-share"></a>Verbinding maken met een SMB-share

Maak verbinding met een SMB-share op uw Windows Server-client die is verbonden met uw Azure Stack edge-apparaat en voer de volgende opdrachten in:


1. Typ in een opdrachtvenster:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Voer het wachtwoord voor de share in als u hierom wordt gevraagd.  
   De voorbeelduitvoer van deze opdracht wordt hier gepresenteerd.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Selecteer Windows + R op het toetsenbord.

4. Geef in het venster **Uitvoeren** de `\\<device IP address>` op en selecteer vervolgens **OK**.  
   Verkenner wordt geopend. Als het goed is, ziet u nu de shares die u hebt gemaakt, als mappen. Dubbelklik in Verkenner op een share (map) om de inhoud te bekijken.
 
    ![Verbinding maken met een SMB-share](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    De gegevens worden naar deze shares geschreven terwijl ze worden gegenereerd en het apparaat pusht de gegevens naar de cloud.

### <a name="connect-to-an-nfs-share"></a>Verbinding maken met een NFS-share

Voer de volgende procedure uit op de Linux-client die is verbonden met uw Azure Stack edge-apparaat:

1. Zorg ervoor dat de NFSv4-client is geïnstalleerd op de client. Als u de NFS-client wilt installeren, gebruikt u de volgende opdracht:

   `sudo apt-get install nfs-common`

    Ga naar [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (NFSv4-client installeren) voor meer informatie.

2. Nadat de NFS-client is geïnstalleerd, koppelt u de NFS-share die u hebt gemaakt op uw Azure Stack edge-apparaat met behulp van de volgende opdracht:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Gebruik van `sync` de optie bij het koppelen van shares verbetert de overdrachts snelheid van grote bestanden.
    > Voordat u de share koppelt, controleert u of de mappen die zullen fungeren als koppelpunten op de lokale computer, al zijn gemaakt. Deze mappen mogen geen bestanden of submappen bevatten.

    In het volgende voor beeld ziet u hoe u verbinding maakt via NFS met een share op uw Azure Stack edge-apparaat. Het IP-adres van het apparaat is `10.10.10.60`. De share `mylinuxshare2` is gekoppeld aan de ubuntuVM. Het koppelpunt van de share is `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> De volgende opmerkingen zijn van toepassing op deze release:
> - Wanneer een bestand is gemaakt in de share, wordt de naam van het bestand niet meer ondersteund. 
> - Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de volgende Azure Stack Edge-onderwerpen geleerd:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met een share

Ga verder met de volgende zelf studie als u wilt weten hoe u uw gegevens kunt transformeren met behulp van Azure Stack Edge:

> [!div class="nextstepaction"]
> [Gegevens transformeren met Azure Stack rand](./azure-stack-edge-deploy-configure-compute.md)


