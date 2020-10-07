---
title: 'Zelfstudie: Back-ups maken van virtuele Linux-machines in de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Portal gebruikt om uw virtuele Linux-machines te beschermen met Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 08e6491e1d8d94e8e6e9112e5a19682018103325
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400346"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Zelfstudie: Back-ups maken en bestanden herstellen voor virtuele Linux-machines in Azure

U kunt uw gegevens beschermen door regelmatig back-ups te maken. Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of specifieke bestanden herstellen. In dit artikel wordt uitgelegd hoe u een enkel bestand herstelt naar een Linux-VM waarop nginx wordt uitgevoerd. Als u nog geen VM hebt om te gebruiken, kunt u er een maken met behulp van de [Linux-quickstart](quick-create-cli.md). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een back-up maken van een VM
> * Een dagelijkse back-up plannen
> * Een bestand herstellen vanaf een back-up

## <a name="backup-overview"></a>Overzicht van Backup

Wanneer met de Azure Backup-service een back-up wordt gestart, activeert dit de back-upextensie om een momentopname van een bepaald tijdstip te maken. De Azure Backup-service maakt gebruik van de extensie _VMSnapshotLinux_ in Linux. De extensie is geïnstalleerd tijdens de eerste VM-back-up als de VM actief is. Als de VM niet actief is, wordt met de Backup-service een momentopname gemaakt van de onderliggende opslag (aangezien er geen schrijfbewerkingen van toepassingen plaatsvinden als de VM is gestopt).

Met Azure Backup wordt standaard een bestandssysteemconsistente back-up gemaakt voor Linux-VM, maar deze kan worden geconfigureerd om [toepassingsconsistente back-ups te maken met behulp van frameworks voor scripts die voorafgaan aan en scripts die volgen op back-ups](../../backup/backup-azure-linux-app-consistent.md). Nadat de momentopname is gemaakt met de Azure Backup-service, worden de gegevens overgedragen naar de kluis. Voor maximale efficiëntie wordt met de service geïdentificeerd welke gegevensblokken sinds de vorige back-up zijn gewijzigd. Alleen deze worden vervolgens overgedragen.

Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.


## <a name="create-a-backup"></a>Een back-up maken
U plant als volgt een dagelijkse back-up naar een Recovery Services-kluis:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
3. Selecteer in de lijst de virtuele machine waarvan u een back-up wilt maken.
4. Klik in het gedeelte **Bewerkingen** van de VM-blade op **Back-up**. De blade **Back-up inschakelen** wordt geopend.
5. Klik in **Recovery Services-kluis** op **Nieuwe maken** en geef de naam op voor de nieuwe kluis. Een nieuwe kluis wordt gemaakt in dezelfde resourcegroep en op dezelfde locatie als de virtuele machine.
6. Klik op **Back-upbeleid**. Handhaaf voor dit voorbeeld de standaardwaarden en klik op **OK**.
7. Klik op de blade **Back-up inschakelen** op **Back-up inschakelen**. Hiermee maakt u een dagelijkse back-up op basis van het standaardschema.
10. Klik op de blade **Back-up** op **Nu een back-up maken** om een initieel herstelpunt te maken.
11. Klik op de blade **Nu een back-up maken** op het kalenderpictogram en selecteer in de kalender de laatste dag dat dit herstelpunt wordt bewaard. Klik vervolgens op **Back-up**.
12. Op de blade **Backup** voor uw VM ziet u het aantal herstelpunten dat is voltooid.

    ![Herstelpunten](./media/tutorial-backup-vms/backup-complete.png)

De eerste back-up duurt ongeveer 20 minuten. Ga nadat de back-up is voltooid verder met het volgende gedeelte van deze zelfstudie.

## <a name="restore-a-file"></a>Een bestand herstellen

Als u een bestand per ongeluk verwijdert of onbedoelde wijzigingen in een bestand aanbrengt, kunt u het bestand herstellen vanuit de back-upkluis. Bestandsherstel maakt gebruik van een script dat wordt uitgevoerd op de VM, om het herstelpunt te koppelen als een lokaal station. Deze stations blijven gedurende 12 uur gekoppeld zodat u bestanden vanaf het herstelpunt kunt kopiëren en herstellen op de VM.  

In dit voorbeeld laten we zien hoe u de standaard-nginx-webpagina herstelt /var/www/html/index.nginx-debian.html. Het openbare IP-adres van de VM is in dit voorbeeld: *13.69.75.209*. U vindt het IP-adres van uw VM met behulp van:

 ```azurecli
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Open een browser op de lokale computer, en typ het openbare IP-adres van de VM om de standaard-nginx-webpagina te zien.

    ![Schermopname met de standaard nginx-webpagina.](./media/tutorial-backup-vms/nginx-working.png)

1. SSH in uw VM.

    ```bash
    ssh 13.69.75.209
    ```

2. Verwijder /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Vernieuw de browser op de lokale computer met Ctrl+F5 om te kijken of de standaard-nginx-pagina weg is.

    ![Schermopname waarin de standaard nginx-webpagina verdwenen is.](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Meld u op de lokale computer aan bij [Azure Portal](https://portal.azure.com/).
6. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
7. Selecteer de VM in de lijst.
8. Klik in de sectie **Instellingen** van de VM-blade op **Back-up**. De blade **Back-up** wordt geopend. 
9. Selecteer in het menu boven aan de blade de optie **Bestandsherstel**. De blade **Bestandsherstel** wordt geopend.
10. In **Stap 1: Herstelpunt selecteren** selecteert u een herstelpunt in de vervolgkeuzelijst.
11. In **Stap 2: Script downloaden om naar bestanden te zoeken en ze te herstellen** klikt u op de knop **Uitvoerbaar bestand downloaden**. Sla het gedownloade bestand op de lokale computer op.
7. Klik op **Script downloaden** om het scriptbestand lokaal te downloaden.
8. Open een Bash-prompt en type het volgende, waarbij u *Linux_myVM_05-05-2017.sh* vervangt door het juiste pad en de juiste bestandsnaam voor het script dat u hebt gedownload, *azureuser* vervangt door de gebruikersnaam voor de VM en *13.69.75.209* vervangt door het openbare IP-adres voor de VM.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Open op de lokale computer een SSH-verbinding met de VM.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Voeg op de VM machtigingen voor uitvoeren toe aan het scriptbestand.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Voer op de VM het script uit om het herstelpunt te koppelen als een bestandssysteem.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. U krijgt het pad voor het koppelpunt via de uitvoer van het script. De uitvoer ziet er ongeveer als volgt uit:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Kopieer de standaard-nginx-webpagina op de VM vanaf het koppelpunt terug naar waar u het bestand hebt verwijderd.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Open op de lokale computer het browsertabblad waarop u verbonden bent met het IP-adres van de VM waarop de standaard-nginx-webpagina wordt weergegeven. Druk op Ctrl+F5 om de browserpagina te vernieuwen. U ziet nu dat de standaardpagina weer werkt.

    ![Standaard-nginx-webpagina](./media/tutorial-backup-vms/nginx-working.png)

18. Ga op de lokale computer terug naar het browsertabblad voor Azure Portal en klik in **Stap 3: De schijven ontkoppelen na het herstel** op de knop **Schijven ontkoppelen**. Als u deze stap vergeet uit te voeren, wordt de verbinding met het koppelpunt na 12 uur automatisch verbroken. Na deze 12 uur moet u een nieuw script downloaden voor het maken van een nieuw koppelpunt.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een back-up maken van een VM
> * Een dagelijkse back-up plannen
> * Een bestand herstellen vanaf een back-up

Ga naar de volgende zelfstudie voor meer informatie over het controleren van virtuele machines.

> [!div class="nextstepaction"]
> [Virtuele machines beheren](tutorial-govern-resources.md)
