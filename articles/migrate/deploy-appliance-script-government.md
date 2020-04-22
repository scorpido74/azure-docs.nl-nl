---
title: Een Azure Migrate-toestel instellen in Azure Government
description: Meer informatie over het instellen van een Azure Migrate-toestel in Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726733"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Een toestel instellen in Azure Government 

Volg dit artikel om een [Azure Migrate-toestel](deploy-appliance.md) voor VMware VM's, Hyper-V VM's en fysieke servers te implementeren in een Azure Government-cloud. U voert een script uit om het toestel te maken en controleert of het verbinding kan maken met Azure. Als u een toestel in de openbare cloud wilt instellen, volgt [u dit artikel](deploy-appliance-script.md).


> [!NOTE]
> De optie om een toestel te implementeren met behulp van een sjabloon (voor VMware VM's en Hyper-V VM's) wordt niet ondersteund in Azure Government.


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
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Controleer de gegenereerde hashwaarden. Voor de nieuwste toestelversie:

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
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Controleer of het toestel verbinding kan maken met Azure-URL's voor [overheidsclouds.](migrate-appliance.md#government-cloud-urls)


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
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Controleer de gegenereerde hashwaarden. Voor de nieuwste toestelversie:

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
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Controleer of het toestel verbinding kan maken met Azure-URL's voor [overheidsclouds.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Het toestel instellen voor fysieke servers

Als u het toestel voor VMware wilt instellen, downloadt u een zip-bestand van de Azure-portal en haalt u de inhoud eruit. U voert het PowerShell-script uit om de web-app van het toestel te starten. U stelt het toestel in en configureert het voor de eerste keer. Vervolgens registreert u het toestel met het Azure Migrate-project.

### <a name="download-the-script"></a>Het script downloaden

1.  Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2.  In **Machines ontdekken** > Worden uw machines **Not virtualized/Other****gevirtualiseerd?**
3.  Klik **op Downloaden**om het zip-bestand te downloaden. 


### <a name="verify-file-security"></a>Bestandsbeveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Controleer de gegenereerde hashwaarden. Voor de nieuwste toestelversie:

    **Algoritme** | **Hash-waarde**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

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
4. Voer het script **AzureMigrateInstaller.ps1**als volgt uit:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, controleert u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Toegang verifiëren

Controleer of het toestel verbinding kan maken met Azure-URL's voor [overheidsclouds.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Volgende stappen

Nadat u het toestel hebt geïmplementeerd, moet u het toestel voor de eerste keer configureren en registreren bij het Azure Migrate-project.

- Stel het toestel in voor [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Stel het toestel in voor [fysieke servers.](how-to-set-up-appliance-physical.md)