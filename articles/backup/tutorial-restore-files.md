---
title: Bestanden herstellen naar een VM met Azure Backup
description: Informatie over hoe u herstelacties op bestandsniveau uitvoert op een Azure-VM met Backup en Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 8d23eb5c177464642ffcafec8877fd2649c0d4f7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073997"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Bestanden herstellen naar een virtuele machine in Azure

Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of afzonderlijke bestanden herstellen. In dit artikel wordt uitgelegd hoe u afzonderlijke bestanden kunt herstellen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een herstelpunt met een VM verbinden
> * Bestanden herstellen vanaf een herstelpunt

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.18 of nieuwer uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie vereist een Linux-VM die met Azure Backup is beschermd. U verwijdert een pagina van een webserver om een onbedoelde bestandsverwijdering en het daaropvolgende herstelproces te simuleren. Bekijk [Een back-up van een virtuele machine maken in Azure met de CLI](quick-backup-vm-cli.md) als u een Linux-VM nodig hebt die een webserver uitvoert en is beschermd met Azure Backup.

## <a name="backup-overview"></a>Overzicht van Backup

Wanneer Azure een back-up begint, maakt de back-upextensie op de VM een momentopname van een bepaald tijdstip. De back-upextensie wordt geïnstalleerd op de VM wanneer de eerste back-up wordt aangevraagd. Azure Backup kan ook een momentopname van de onderliggende opslag maken als de VM niet wordt uitgevoerd ten tijde van de back-up.

Standaard maakt Azure Backup een back-up die consistent is met een bestandssysteem. Nadat Azure Backup de momentopname heeft gemaakt, worden de gegevens overgedragen naar de Recovery Services-kluis. Voor maximale efficiëntie identificeert Azure Backup welke gegevensblokken sinds de vorige back-up zijn gewijzigd. Alleen deze worden vervolgens overgedragen.

Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.

## <a name="delete-a-file-from-a-vm"></a>Een bestand van een VM verwijderen

Als u per ongeluk een bestand verwijdert of er wijzigingen in aanbrengt, kunt u afzonderlijke bestanden herstellen vanaf een herstelpunt. Hiervoor kunt u op een herstelpunt door de bestanden bladeren waarvan een back-up is gemaakt en alleen de bestanden voor herstel selecteren die u nodig hebt. In dit voorbeeld verwijderen we een bestand van een webserver om het herstelproces op bestandsniveau te demonstreren.

1. Haal het IP-adres van uw VM op met [az vm show](/cli/azure/vm?view=azure-cli-latest#az-vm-show) om verbinding te maken met uw VM:

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Als u wilt bevestigen dat uw website momenteel werkt, opent u een webbrowser en voert u het openbare IP-adres van uw VM in. Laat het webbrowservenster geopend.

    ![Standaard-NGINX-webpagina](./media/tutorial-restore-files/nginx-working.png)

3. Maak verbinding met uw VM via SSH. Vervang *publicIpAddress* door het openbare IP-adres dat u hebt verkregen in een voorgaande opdracht:

    ```bash
    ssh publicIpAddress
    ```

4. Verwijder de standaardpagina van de webserver op */var/www/html/index.nginx-debian.html* op de volgende manier:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Vernieuw de webpagina in uw webbrowser. De webpagina wordt niet meer geladen, zoals te zien is in het volgende voorbeeld:

    ![NGINX-website laadt de standaardpagina niet meer](./media/tutorial-restore-files/nginx-broken.png)

6. Sluit de SSH-sessie op uw VM op de volgende manier:

    ```bash
    exit
    ```

## <a name="generate-file-recovery-script"></a>Bestandsherstelscript genereren

Als u uw bestanden wilt herstellen, biedt Azure Backup een op uw VM uit te voeren script waarmee uw herstelpunt als een lokale schijf wordt verbonden. U kunt door de bestanden op deze lokale schijf bladeren, bestanden naar de VM zelf herstellen en vervolgens de verbinding met het herstelpunt verbreken. Azure Backup blijft back-ups maken van uw gegevens op basis van het toegewezen beleid voor planning en retentie.

1. Als u de herstelpunten voor uw VM in een lijst wilt opnemen, gebruikt u [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). In dit voorbeeld wordt het meest recente herstelpunt geselecteerd voor de virtuele machine met de naam *myVM*, die wordt beveiligd in *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Gebruik [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp) om het script op te halen waarmee het herstelpunt met de VM wordt verbonden. In het volgende voorbeeld wordt het script opgehaald voor de VM met de naam *myVM*, die wordt beveiligd in *myRecoveryServicesVault*.

    Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen in de voorgaande opdracht:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Het script wordt gedownload en er wordt een wachtwoord weergegeven, zoals getoond in het volgende voorbeeld:

    ```output
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Gebruik Secure Copy (SCP) om het script over te dragen naar uw VM. Geef de naam van het gedownloade script op en vervang *publicIpAddress* door het openbare IP-adres van uw VM. Zorg ervoor dat u de afsluitende `:` aan het eind van de SCP-opdracht als volgt opneemt:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```

## <a name="restore-file-to-your-vm"></a>Bestand herstellen naar uw VM

Nu u het herstelscript hebt gekopieerd naar uw VM, kunt u de herstelpunten verbinden en bestanden herstellen.

1. Maak verbinding met uw VM via SSH. Vervang *publicIPAddress* als volgt door het IP-adres van uw VM:

    ```bash
    ssh publicIpAddress
    ```

2. Voeg machtigingen tot uitvoeren toe met **chmod**, zodat uw script correct wordt uitgevoerd. Geef de naam van uw eigen script op:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Voer het script uit om het herstelpunt te koppelen. Geef de naam van uw eigen script op:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Terwijl het script wordt uitgevoerd, wordt u gevraagd om een wachtwoord op te geven voor toegang tot het herstelpunt. Geef het wachtwoord op dat wordt weergegeven in de vorige opdracht [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp), waarmee het herstelscript werd gegenereerd.

    U krijgt het pad voor het herstelpunt via de uitvoer van het script. Het volgende voorbeeld toont dat het herstelpunt is gekoppeld aan */home/azureuser/myVM-20170919213536/Volume1*:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1

    ************ Open File Explorer to browse for files. ************
    ```

4. Gebruik **cp** om de standaardwebpagina van NGINX vanaf het gekoppelde herstelpunt naar de originele bestandslocatie te kopiëren. Vervang het koppelpunt */home/azureuser/myVM-20170919213536/Volume1* door uw eigen locatie:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

5. Vernieuw de webpagina in uw webbrowser. De website wordt weer correct geladen, zoals te zien is in het volgende voorbeeld:

    ![De NGINX-website wordt nu correct geladen](./media/tutorial-restore-files/nginx-restored.png)

6. Sluit de SSH-sessie op uw VM op de volgende manier:

    ```bash
    exit
    ```

7. Ontkoppel het herstelpunt van uw VM met [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-unmount-rp). In het volgende voorbeeld wordt het herstelpunt van de VM met de naam *myVM* in *myRecoveryServicesVault* ontkoppeld.

    Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen in de voorgaande opdracht:

    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een herstelpunt met een VM verbonden en voor een webserver bestanden hersteld. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een herstelpunt met een VM verbinden
> * Bestanden herstellen vanaf een herstelpunt

Ga naar de volgende zelfstudie voor meer informatie over hoe u een back-up van een Windows-server naar Azure maakt.

> [!div class="nextstepaction"]
> [Back-up maken van een Windows-server naar Azure](tutorial-backup-windows-server-to-azure.md)
