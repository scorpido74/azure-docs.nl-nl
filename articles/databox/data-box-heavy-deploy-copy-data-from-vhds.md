---
title: "Zelfstudie: Kopiëren van VHD's naar beheerde schijven"
titleSuffix: Azure Data Box Heavy
description: Meer informatie over het kopiëren van gegevens van VHD's van on-premises VM-workloads naar uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77471326"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Zelfstudie: Gegevensvak Zwaar gebruiken om gegevens te importeren als beheerde schijven in Azure

In deze zelfstudie wordt beschreven hoe u de Azure Data Box Heavy gebruiken om u on-premises VHD's te migreren naar beheerde schijven in Azure. De VHD's van on-premises VM's worden gekopieerd naar Data Box Heavy als paginablobs en worden als beheerde schijven naar Azure geüpload. Deze beheerde schijven kunnen vervolgens worden gekoppeld aan Azure VM's.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de [zelfstudie: Azure Data Box Heavy instellen](data-box-heavy-deploy-set-up.md)voltooid.
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U bent verbonden met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt. 
4. Je hebt het:

    - Ondersteunde [beheerde schijfgroottes in azure-objectgroottelimieten](data-box-heavy-limits.md#azure-object-size-limits).
    - [Inleiding tot door Azure beheerde schijven](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Verbinding maken met Data Box Heavy

Op basis van de opgegeven resourcegroepen maakt Data Box Heavy één aandeel voor elke bijbehorende resourcegroep per knooppunt. Als en `mydbmdrg1` `mydbmdrg2` zijn gemaakt bij het plaatsen van de order, worden bijvoorbeeld de volgende shares gemaakt:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Binnen elk aandeel worden de volgende drie mappen gemaakt die overeenkomen met containers in uw opslagaccount.

- Premium - SSD
- Standard HDD
- Standard - SSD

In de volgende tabel ziet u de UNC-paden naar de shares op uw Gegevensvak Zwaar.
 
|        Verbindingsprotocol           |             UNC-pad naar het aandeel                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Op basis van of u SMB of NFS gebruikt om verbinding te maken met Gegevensbox Zwaar-shares, zijn de stappen om verbinding te maken verschillend.

> [!NOTE]
> - Verbinding maken via REST wordt niet ondersteund voor deze functie.
> - Herhaal de verbindingsinstructies om verbinding te maken met het tweede knooppunt van Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Verbinding maken met Data Box Heavy via SMB

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box Heavy te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Klik **op Referenties opvragen halen** om de toegangsreferenties voor de aandelen die zijn gekoppeld aan uw resourcegroep op te vragen. U de toegangsreferenties ook ophalen via de **apparaatgegevens** in de Azure-portal.

    > [!NOTE]
    > De referenties voor alle aandelen voor beheerde schijven zijn identiek.

    ![Sharereferenties 1 ophalen](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Kopieer in het dialoogvenster Gegevens delen en kopiëren van Access de **gebruikersnaam** en het **wachtwoord** voor het aandeel. Klik op **OK**.
    
    ![Sharereferenties 1 ophalen](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Als u vanaf uw hostcomputer toegang wilt krijgen tot de aandelen die aan uw resource zijn gekoppeld *(mydbmdrg1* in het volgende voorbeeld), opent u een opdrachtvenster. Typ het volgende na de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Uw UNC-padden met delen in dit voorbeeld zijn als volgt:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Voer het wachtwoord voor de share in wanneer hierom wordt gevraagd. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>\<ShareName>` op. Klik op **OK** om Verkenner te openen.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    U ziet nu de volgende vooraf gemaakte mappen binnen elk aandeel.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Verbinding maken met Data Box Heavy via NFS

Als u een Linux-hostcomputer gebruikt, voert u de volgende stappen uit om uw apparaat te configureren om toegang te verlenen tot NFS-clients.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**.

    ![NFS-clienttoegang configureren 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Geef het IP-adres op van de NFS-client en klik op **Toevoegen**. U kunt deze stap herhalen om toegang voor meerdere NFS-clients te configureren. Klik op **OK**.

    ![NFS-clienttoegang configureren 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie.

3. Zodra de NFS-client is geïnstalleerd, gebruikt u de volgende opdracht om het NFS-aandeel op uw apparaat te monteren:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    In het volgende voorbeeld ziet u hoe u via NFS verbinding maken met een gegevensvak of een deel van Data Box Heavy. De Data Box of Data `169.254.250.200`Box Heavy `mydbmdrg1_MDisk` apparaat IP is , het aandeel `/home/databoxubuntuhost/databox`is gemonteerd op de ubuntuVM, mount punt wordt .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Nadat u bent verbonden met de gegevensserver, is de volgende stap het kopiëren van gegevens. Het VHD-bestand wordt gekopieerd naar het opslagaccount voor tijdelijke opslag als paginablob. De paginablob wordt vervolgens geconverteerd naar een beheerde schijf en verplaatst naar een resourcegroep.

Bekijk de volgende overwegingen voordat u met het kopiëren van gegevens begint:

- Kopieer de VHD's altijd naar een van de vooraf gemaakte mappen. Als u de VHD's buiten deze mappen of in een map kopieert die u hebt gemaakt, worden de VHD's geüpload naar Azure Storage-account als paginablobs en niet-beheerde schijven.
- Alleen de vaste VHD's kunnen worden geüpload om beheerde schijven te maken. VHDX-bestanden of dynamische en differentiërende VHD's worden niet ondersteund.
- U slechts één beheerde schijf met een bepaalde naam in een resourcegroep hebben in alle vooraf gemaakte mappen. Dit betekent dat de VHD's die naar de vooraf gemaakte mappen zijn geüpload, unieke namen moeten hebben. Zorg ervoor dat de opgegeven naam niet overeenkomt met een bestaande beheerde schijf in een resourcegroep.
- Beheerde schijflimieten controleren in [azure-objectgroottelimieten](data-box-heavy-limits.md#azure-object-size-limits).

Afhankelijk van of u verbinding maakt via SMB of NFS, u het:

- [Gegevens kopiëren via SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Gegevens kopiëren via NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Wacht tot de kopieertaken zijn voltooid. Zorg ervoor dat de kopie taken zijn voltooid met geen fouten voordat u naar de volgende stap.

![Geen fouten op de pagina **Verbinding maken en kopiëren**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Als er fouten zijn tijdens het kopieerproces, downloadt u de logboeken van de pagina **Verbinding maken en kopiëren.**

- Als u een bestand hebt gekopieerd dat niet 512 bytes is uitgelijnd, wordt het bestand niet geüpload als paginablob naar uw opslagaccount voor tijdelijke bestanden. U ziet een fout in de logboeken. Verwijder het bestand en kopieer een bestand dat 512 bytes is uitgelijnd.

- Als u een VHDX hebt gekopieerd (deze bestanden worden niet ondersteund) met een lange naam, ziet u een fout in de logboeken.

    ![Fout in de logboeken van de pagina **Verbinden en kopiëren**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Los de fouten op voordat u doorgaat naar de volgende stap.

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

