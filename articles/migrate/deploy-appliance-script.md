---
title: Een Azure Migrate-toestel instellen met een script
description: Meer informatie over het instellen van een Azure Migrate-toestel met een script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676302"
---
# <a name="set-up-an-appliance-with-a-script"></a>Een toestel instellen met een script

Volg dit artikel om een [Azure Migrate-toestel](deploy-appliance.md) te maken voor de beoordeling/migratie van Vm's vMware en Hyper-VMs. U voert een script uit om een toestel te maken en controleert of het verbinding kan maken met Azure. 

U het toestel voor VMware- en Hyper-V-VM's implementeren met behulp van een script of een sjabloon gebruiken die u downloadt van de Azure-portal. Het gebruik van een script is handig als u geen VM maken met de gedownloade sjabloon.

- Als u een sjabloon wilt gebruiken, volgt u de zelfstudies voor [VMware](tutorial-prepare-vmware.md) of [Hyper-V.](tutorial-prepare-hyper-v.md)
- Als u een toestel voor fysieke servers wilt instellen, u alleen een script gebruiken. Volg [dit artikel](how-to-set-up-appliance-physical.md).
- Volg [dit artikel](deploy-appliance-script-government.md)om een toestel in een Azure Government-cloud in te stellen.

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
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Controleer de gegenereerde hashwaarde. Voor de nieuwste toestelversie:

    **Algoritme** | **Hash-waarde**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c



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

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Controleer of het toestel verbinding kan maken met Azure-URL's voor de [openbare](migrate-appliance.md#public-cloud-urls) cloud.

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
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Controleer de gegenereerde hashwaarden. Voor de nieuwste toestelversie:

    **Algoritme** | **Hash-waarde**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c

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
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Controleer of het toestel verbinding kan maken met Azure-URL's voor de [openbare](migrate-appliance.md#public-cloud-urls) cloud.

## <a name="next-steps"></a>Volgende stappen

Nadat u het toestel hebt geïmplementeerd, moet u het toestel voor de eerste keer configureren en registreren bij het Azure Migrate-project.

- Stel het toestel in voor [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
