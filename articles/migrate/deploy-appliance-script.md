---
title: Een Azure Migrate-toestel instellen met een script
description: Meer informatie over het instellen van een Azure Migrate-toestel met een script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537709"
---
# <a name="set-up-an-appliance-with-a-script"></a>Een toestel instellen met een script

In dit artikel wordt beschreven hoe u het [Azure Migrate-toestel](deploy-appliance.md) instelt met behulp van een PowerShell-installatiescript voor VMware-VM's en Hyper-V VM's. Als u het toestel wilt instellen voor fysieke servers, [bekijkt u dit artikel](how-to-set-up-appliance-physical.md).


U het toestel op een aantal manieren implementeren:


- Een sjabloon gebruiken voor VMware VM's (OVA) of Hyper-V VM's (VHD).
- Met behulp van een script. Dit is de methode beschreven in dit artikel. Het script biedt:
    - Een alternatief voor het instellen van het toestel met behulp van een OVA-sjabloon, voor beoordeling en agentloze migratie van VMware VM's.
    - Een alternatief voor het instellen van het toestel met behulp van een VHD-sjabloon, voor beoordeling en migratie van Hyper-V VM's.
    - Voor de beoordeling van fysieke servers (of VM's die u als fysieke servers wilt migreren), is het script de enige methode voor het instellen van het toestel.
    - Een manier om het toestel te implementeren in Azure Government.


Nadat u het toestel hebt gemaakt, controleert u of het verbinding kan maken met Azure Migrate. Vervolgens configureert u het toestel voor de eerste keer en registreert u het met het Azure Migrate-project.


## <a name="prerequisites"></a>Vereisten

Het script stelt het Azure Migrate-toestel in op een bestaande fysieke machine of VM.

- De machine die als toestel fungeert, moet Windows Server 2016 draaien, met 32 GB geheugen, acht vCPU's, ongeveer 80 GB schijfopslag en een externe virtuele switch. Het vereist een statisch of dynamisch IP-adres en toegang tot het internet.
- Voordat u het toestel implementeert, controleert u gedetailleerde toestelvereisten voor [Vm's van VMware,](migrate-appliance.md#appliance---vmware) [Hyper-V VM's](migrate-appliance.md#appliance---hyper-v)en [fysieke servers](migrate-appliance.md#appliance---physical).
- Voer het script niet uit op een bestaand Azure Migrate-toestel.

## <a name="set-up-the-appliance-for-vmware"></a>Het toestel instellen voor VMware

Als u het toestel voor VMware wilt instellen, downloadt u een zip-bestand van de Azure-portal en haalt u de inhoud eruit. U voert het PowerShell-script uit om de web-app van het toestel te starten. U stelt het toestel in en configureert het voor de eerste keer. Vervolgens registreert u het toestel met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2.  In **Discover machines** > Worden uw machines **Yes, with VMWare vSphere hypervisor****gevirtualiseerd?**
3.  Klik **op Downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestandsbeveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld gebruik voor public cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Voorbeeldgebruik voor overheidscloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Controleer de gegenereerde hashwaarden:

    - Voor de public cloud (voor de nieuwste toestelversie):

        **Algoritme** | **Hash-waarde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c

    - Voor Azure-overheid (voor de nieuwste toestelversie):

        **Algoritme** | **Hash-waarde**
          --- | ---
          MD5 | 6316bcc8bc932204295bfe3f4be3949
          

### <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webapplicatie.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Hiermee wordt een IIS-herschrijfbare module gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Config-bestanden**: %ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden**: %ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Haal het ritsbestand naar een map op de machine die het toestel host. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-toestel.
2. Start PowerShell op de machine, met beheerdersbevoegdheden (verhoogde) bevoegdheden.
3. Wijzig de PowerShell-map in de map met de inhoud die uit het gedownloade zip-bestand is gehaald.
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:
    - Voor de public cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Voor Azure-overheid:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het toestel verbinding kan maken met Azure URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds](migrate-appliance.md#government-cloud-urls.


## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het toestel voor Hyper-V wilt instellen, downloadt u een zip-bestand uit de Azure-portal en haalt u de inhoud eruit. U voert het PowerShell-script uit om de web-app van het toestel te starten. U stelt het toestel in en configureert het voor de eerste keer. Vervolgens registreert u het toestel met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2.  In **Discover machines** > Worden uw machines **Yes, with Hyper-V****gevirtualiseerd?**
3.  Klik **op Downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestandsbeveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld gebruik voor public cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Voorbeeldgebruik voor overheidscloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Controleer de gegenereerde hashwaarden:

    - Voor de public cloud (voor de nieuwste toestelversie):

        **Algoritme** | **Hash-waarde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c

    - Voor Azure-overheid (voor de nieuwste toestelversie):

        **Algoritme** | **Hash-waarde**
          --- | ---
          MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webapplicatie.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Hiermee wordt een IIS-herschrijfbare module gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Config-bestanden**: %ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden**: %ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Haal het ritsbestand naar een map op de machine die het toestel host. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-toestel.
2. Start PowerShell op de machine, met beheerdersbevoegdheden (verhoogde) bevoegdheden.
3. Wijzig de PowerShell-map in de map met de inhoud die uit het gedownloade zip-bestand is gehaald.
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:
    - Voor de public cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Voor Azure-overheid:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Zorg ervoor dat het toestel verbinding kan maken met Azure URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds](migrate-appliance.md#government-cloud-urls.



## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie over het instellen van het toestel met een sjabloon of voor fysieke servers:

- Stel het toestel in voor [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Stel het toestel in voor [fysieke servers.](how-to-set-up-appliance-physical.md)