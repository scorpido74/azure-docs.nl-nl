---
title: Een Azure Migrate apparaat instellen in Azure Government
description: Meer informatie over het instellen van een Azure Migrate apparaat in Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c8bcecd7cca78a24d9dbf18e185c9362ed712b43
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052478"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Een apparaat instellen in Azure Government 

Volg dit artikel om een [Azure migrate apparaat](deploy-appliance.md) te implementeren voor VMware-Vm's, virtuele Hyper-V-machines en fysieke servers in een Azure Government Cloud. U voert een script uit om het apparaat te maken en controleert of het verbinding kan maken met Azure. Als u een apparaat in de open bare Cloud wilt instellen, volgt u [dit artikel](deploy-appliance-script.md).


> [!NOTE]
> De optie om een apparaat te implementeren met behulp van een sjabloon (voor VMware-Vm's en virtuele Hyper-V-machines) wordt niet ondersteund in Azure Government.


## <a name="prerequisites"></a>Vereisten

Het script stelt het Azure Migrate apparaat in op een bestaande fysieke machine of VM.

- Op de computer die zal fungeren als het apparaat, moet Windows Server 2016 worden uitgevoerd met 32 GB geheugen, acht Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch. Het vereist een statisch of dynamisch IP-adres en toegang tot internet.
- Voordat u het apparaat implementeert, moet u gedetailleerde vereisten voor apparaten voor [VMware-vm's](migrate-appliance.md#appliance---vmware), [virtuele Hyper-V-machines](migrate-appliance.md#appliance---hyper-v)en [fysieke servers](migrate-appliance.md#appliance---physical)bekijken.
- Voer het script niet uit op een bestaand Azure Migrate apparaat.

## <a name="set-up-the-appliance-for-vmware"></a>Het apparaat voor VMware instellen

Als u het apparaat voor VMware wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMWare vSphere-hypervisor**.
3.  Klik op **downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Algoritme** | **Downloaden** | **SHA256**
    --- | --- | ---
    VMware (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Het apparaat instellen voor Hyper-V

Als u het apparaat voor Hyper-V wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3.  Klik op **downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden** | **SHA256**
    --- | --- | ---
    Hyper-V (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3

          

### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Het apparaat instellen voor fysieke servers

Als u het apparaat voor VMware wilt instellen, downloadt u een zip-bestand van de Azure Portal en extraheert u de inhoud. U voert het Power shell-script uit om de web-app voor het toestel te starten. U stelt het apparaat in en configureert het voor de eerste keer. Vervolgens registreert u het apparaat met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2.  In **Discover-machines**  >  **zijn uw machines gevirtualiseerd?**, selecteer **niet gevirtualiseerd/Overig**.
3.  Klik op **downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestands beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Controleer de meest recente versie van het apparaat en de hash-waarde:

    **Scenario** | **Downloaden*** | **Hash-waarde**
    --- | --- | ---
    Fysiek (63.1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1
          

### <a name="run-the-script"></a>Het script uitvoeren

Wat gebeurt er met het script:

- Installeert agents en een webtoepassing.
- Installeert Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Hiermee wordt een herschrijf bare module van IIS gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Maakt logboek-en configuratie bestanden als volgt:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Pak het zip-bestand uit naar een map op de computer die als host moet fungeren voor het apparaat. Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start Power shell op de computer met Administrator bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Nadat het script is uitgevoerd, wordt de web-app voor het toestel gestart, zodat u het apparaat kunt instellen. Als u problemen ondervindt, controleert u de script logboeken op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log.

### <a name="verify-access"></a>Toegang controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-Url's voor [overheids Clouds](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt geïmplementeerd, moet u het voor de eerste keer configureren en het registreren bij het Azure Migrate-project.

- Stel het apparaat voor [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)in.
- Stel het apparaat voor [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)in.
- Stel het apparaat in voor [fysieke servers](how-to-set-up-appliance-physical.md).
