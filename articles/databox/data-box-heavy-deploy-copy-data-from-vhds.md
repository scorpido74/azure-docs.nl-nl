---
title: "Zelf studie: kopiëren van Vhd's naar Managed disks"
titleSuffix: Azure Data Box Heavy
description: Meer informatie over het kopiëren van gegevens van Vhd's van on-premises VM-workloads naar uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471326"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Zelf studie: Data Box Heavy gebruiken om gegevens te importeren als beheerde schijven in azure

In deze zelf studie wordt beschreven hoe u de Azure Data Box Heavy gebruikt voor het migreren van uw on-premises Vhd's naar Managed disks in Azure. De Vhd's van on-premises Vm's worden gekopieerd naar Data Box Heavy als pagina-blobs en als beheerde schijven in Azure worden geüpload. Deze beheerde schijven kunnen vervolgens worden gekoppeld aan virtuele Azure-machines.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de [zelf studie voltooid: stel Azure data Box Heavy](data-box-heavy-deploy-set-up.md)in.
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U bent verbonden met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt. 
4. U hebt het volgende gecontroleerd:

    - Ondersteunde [beheerde schijf grootten in azure-object grootte limieten](data-box-heavy-limits.md#azure-object-size-limits).
    - [Inleiding tot Azure Managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Verbinding maken met Data Box Heavy

Op basis van de opgegeven resource groepen, maakt Data Box Heavy één share voor elke gekoppelde resource groep per knoop punt. Als bijvoorbeeld `mydbmdrg1` en `mydbmdrg2` zijn gemaakt bij het plaatsen van de order, worden de volgende shares gemaakt:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Binnen elke share worden de volgende drie mappen gemaakt die overeenkomen met containers in uw opslag account.

- Premium - SSD
- Standard - HDD
- Standard - SSD

In de volgende tabel ziet u de UNC-paden naar de shares op uw Data Box Heavy.
 
|        Verbindings Protocol           |             UNC-pad naar de share                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

De stappen om verbinding te maken, zijn afhankelijk van het feit of u SMB of NFS gebruikt om verbinding te maken met Data Box Heavy-shares.

> [!NOTE]
> - Verbinding maken via REST wordt niet ondersteund voor deze functie.
> - Herhaal de verbindings instructies om verbinding te maken met het tweede knoop punt van Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Verbinding maken met Data Box Heavy via SMB

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box Heavy te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Klik op **referenties ophalen** om de toegangs referenties op te halen voor de shares die zijn gekoppeld aan uw resource groep. U kunt ook de toegangs referenties ophalen van de **apparaatgegevens** in de Azure Portal.

    > [!NOTE]
    > De referenties voor alle shares voor beheerde schijven zijn identiek.

    ![Sharereferenties 1 ophalen](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Kopieer de **gebruikers naam** en het **wacht woord** voor de share in het dialoog venster toegang delen en gegevens kopiëren. Klik op **OK**.
    
    ![Sharereferenties 1 ophalen](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Als u toegang wilt krijgen tot de shares die zijn gekoppeld aan uw resource (*mydbmdrg1* in het volgende voor beeld), opent u een opdracht venster. Typ bij de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Uw UNC-share paden in dit voor beeld zijn als volgt:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>\<ShareName>` op. Klik op **OK** om Verkenner te openen.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    U ziet nu de volgende vooraf gemaakte mappen in elke share.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Verbinding maken met Data Box Heavy via NFS

Als u een Linux-hostcomputer gebruikt, voert u de volgende stappen uit om uw apparaat te configureren om toegang tot NFS-clients toe te staan.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**.

    ![NFS-clienttoegang configureren 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Geef het IP-adres op van de NFS-client en klik op **Toevoegen**. U kunt deze stap herhalen om toegang voor meerdere NFS-clients te configureren. Klik op **OK**.

    ![NFS-clienttoegang configureren 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie.

3. Zodra de NFS-client is geïnstalleerd, gebruikt u de volgende opdracht om de NFS-share op uw apparaat te koppelen:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    In het volgende voor beeld ziet u hoe u verbinding maakt via NFS met een Data Box of Data Box Heavy share. De Data Box Data Box Heavy of het IP-adres van het apparaat is `169.254.250.200`, de share `mydbmdrg1_MDisk` is gekoppeld aan het ubuntuVM, het koppel punt dat wordt `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Nadat u verbinding hebt gemaakt met de gegevens server, is de volgende stap het kopiëren van gegevens. Het VHD-bestand wordt gekopieerd naar het staging Storage-account als pagina-blob. De pagina-BLOB wordt vervolgens geconverteerd naar een beheerde schijf en verplaatst naar een resource groep.

Bekijk de volgende overwegingen voordat u begint met het kopiëren van gegevens:

- Kopieer de VHD's altijd naar een van de vooraf gemaakte mappen. Als u de Vhd's buiten deze mappen of in een map die u hebt gemaakt kopieert, worden de Vhd's geüpload naar Azure Storage-account als pagina-blobs en niet-beheerde schijven.
- Alleen de vaste VHD's kunnen worden geüpload om beheerde schijven te maken. VHDX-bestanden of dynamische en differentiërende Vhd's worden niet ondersteund.
- U kunt slechts één beheerde schijf met een opgegeven naam in een resource groep in alle voorgemaakte mappen hebben. Dit betekent dat de VHD's die naar de vooraf gemaakte mappen zijn geüpload, unieke namen moeten hebben. Zorg ervoor dat de opgegeven naam niet overeenkomt met een bestaande beheerde schijf in een resourcegroep.
- Bekijk de limieten voor beheerde schijven in [Azure-object grootte limieten](data-box-heavy-limits.md#azure-object-size-limits).

Afhankelijk van of u verbinding maakt via SMB of NFS, kunt u het volgende gebruiken:

- [Gegevens kopiëren via SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Gegevens kopiëren via NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Wacht tot de kopieertaken zijn voltooid. Zorg ervoor dat de Kopieer taken zonder fouten zijn voltooid voordat u naar de volgende stap gaat.

![Geen fouten op de pagina **Verbinding maken en kopiëren**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Als er fouten optreden tijdens het kopieer proces, downloadt u de logboeken van de pagina **verbinding maken en kopiëren** .

- Als u een bestand hebt gekopieerd dat niet 512 bytes is uitgelijnd, wordt het bestand niet geüpload als pagina-BLOB naar uw staging Storage-account. Er wordt een fout in de logboeken weer geven. Verwijder het bestand en kopieer een bestand dat 512 bytes is uitgelijnd.

- Als u een VHDX hebt gekopieerd (deze bestanden worden niet ondersteund) met een lange naam, wordt een fout in de logboeken weer geven.

    ![Fout in de logboeken van * * verbinding maken en kopiëren * * pagina](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Los de fouten op voordat u verdergaat met de volgende stap.

Om de gegevensintegriteit te garanderen wordt de controlesom inline berekend terwijl de gegevens worden gekopieerd. Verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.
    
![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Wanneer de kopieertaak is voltooid, kunt u naar **Voorbereiding voor verzending** gaan.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box Heavy, zoals:

> [!div class="checklist"]
> * Vereisten controleren
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box Heavy naar Microsoft verstuurt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy verzenden naar Microsoft](./data-box-heavy-deploy-picked-up.md)

