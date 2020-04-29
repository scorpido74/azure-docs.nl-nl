---
title: Virtuele Hyper-V-machines voorbereiden voor evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van de evaluatie/migratie van Hyper-V-Vm's met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ea6290eae187ed4ead39f7307a84a54a23c19c29
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81641637"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Voor bereiding voor de evaluatie en migratie van virtuele Hyper-V-machines naar Azure

In dit artikel wordt beschreven hoe u de evaluatie van on-premises virtuele Hyper-V-machines voorbereidt met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool)en migratie van Hyper-v-vm's met [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool).


Deze zelf studie is de eerste in een serie die laat zien hoe u virtuele Hyper-V-machines kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en-resources om met Azure Migrate te werken.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor Server evaluatie. U kunt voorbereiden met behulp van een configuratie script of hand matig.
> * Bereid u voor op de implementatie van het Azure Migrate apparaat. Het apparaat wordt gebruikt om on-premises virtuele machines te detecteren en te evalueren.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor server migratie.


> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de procedures voor de evaluatie en migratie van Hyper-V voor meer informatie.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

### <a name="azure-permissions"></a>Azure-machtigingen

U moet machtigingen instellen voor Azure Migrate-implementatie.

**Taak** | **Details** 
--- | --- 
**Een Azure Migrate-project maken** | Uw Azure-account heeft Contribute of eigenaars machtigingen nodig om een project te maken. | 
**Resource providers registreren** | Azure Migrate maakt gebruik van een licht gewicht Azure Migrate apparaat om virtuele Hyper-V-machines te detecteren en te beoordelen met de evaluatie van Azure Migrate server.<br/><br/> Tijdens de registratie van het apparaat worden resource providers geregistreerd bij het abonnement dat is gekozen in het apparaat. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resource providers wilt registreren, hebt u een rol voor Inzender of eigenaar nodig voor het abonnement.
**Azure AD-app maken** | Bij het registreren van het apparaat maakt Azure Migrate een Azure Active Directory Azure AD-app die wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd, met de services die worden uitgevoerd op Azure. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig voor het maken van Azure AD-apps (beschikbaar in de ontwikkelaar van de toepassing).



### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

Controleer of u gemachtigd bent om een Azure Migrate project te maken.

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)**.
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

U kunt aan de hand van een van de volgende methoden machtigingen voor Azure Migrate toewijzen om de Azure AD-app te maken tijdens de registratie van het apparaat:

- Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
- Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De app heeft geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen:

1. In azure AD moet de Tenant/globale beheerder navigeren naar **Azure Active Directory** > **gebruikers** > **instellingen**.
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Hyper-V voorbereiden op evaluatie

U kunt Hyper-V voor de VM-evaluatie hand matig voorbereiden of een configuratie script gebruiken. Voorbereidende stappen zijn als volgt:
- [Verifiëren](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Instellingen voor Hyper-V-hosts, en zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op hyper-v-hosts.
- Externe communicatie van Power shell instellen op elke host, zodat het Azure Migrate apparaat Power shell-opdrachten op de host kan uitvoeren via een WinRM-verbinding.
- Referenties delegeren als VM-schijven zich op externe SMB-shares bevinden.
- Stel een account in dat het apparaat gebruikt voor het detecteren van Vm's op Hyper-V-hosts.
- Stel Hyper-V-integratie Services in op elke virtuele machine die u wilt detecteren en beoordelen. De standaard instellingen voor het inschakelen van integratie Services zijn voldoende voor Azure Migrate.

    ![Integratie Services inschakelen](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Voorbereiden met een script

Het script doet het volgende:

- Controleert of u het script uitvoert op een ondersteunde Power shell-versie.
- Controleert of u (de gebruiker die het script uitvoert) beheerders bevoegdheden op de Hyper-V-host heeft.
- Hiermee kunt u een lokaal gebruikers account (geen beheerder) maken dat de Azure Migrate-service gebruikt om te communiceren met de Hyper-V-host. Dit gebruikers account wordt toegevoegd aan deze groepen op de host:
    - Extern beheer van gebruikers
    - Hyper-V-beheerders
    - Prestatie meter gebruikers
- Controleert of op de host een ondersteunde versie van Hyper-V en de Hyper-V-functie wordt uitgevoerd.
- Hiermee schakelt u de WinRM-service in en opent u poort 5985 (HTTP) en 5986 (HTTPS) op de host (vereist voor de verzameling meta gegevens).
- Externe communicatie met Power shell is ingeschakeld op de host.
- Hiermee wordt gecontroleerd of de Hyper-V-integratie Services zijn ingeschakeld op alle Vm's die worden beheerd door de host.
- Hiermee schakelt u CredSSP in op de host, indien nodig.

Voer het script als volgt uit:

1. Zorg ervoor dat Power shell-versie 4,0 of hoger is geïnstalleerd op de Hyper-V-host.
2. Down load het script vanuit het [micro soft Download centrum](https://aka.ms/migrate/script/hyperv). Het script is cryptografisch ondertekend door micro soft.
3. Valideer de script integriteit met behulp van MD5 of SHA256 hash-bestanden. De hashtag-waarden staan hieronder. Voer deze opdracht uit om de hash voor het script te genereren:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Voor beeld van gebruik:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    Nadat u de script integriteit hebt gevalideerd, voert u het script uit op elke Hyper-V-host met de volgende Power shell-opdracht:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtag-waarden

Hash-waarden zijn:

| **Eigenaar** | **Waarde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Hand matig voorbereiden

Volg de procedures in deze sectie om Hyper-V hand matig voor te bereiden, in plaats van het script te gebruiken.

### <a name="verify-powershell-version"></a>Power shell-versie controleren

Zorg ervoor dat Power shell-versie 4,0 of hoger is geïnstalleerd op de Hyper-V-host.



### <a name="set-up-an-account-for-vm-discovery"></a>Een account instellen voor de detectie van VM'S

Azure Migrate heeft machtigingen nodig om on-premises virtuele machines te detecteren.

- Stel een domein-of lokale gebruikers account in met beheerders machtigingen op de Hyper-V-hosts/het cluster.

    - U hebt één account nodig voor alle hosts en clusters die u wilt toevoegen in de detectie.
    - Het account kan een lokaal of een domein account zijn. Het is raadzaam dat het beheerders machtigingen heeft op de Hyper-V-hosts of-clusters.
    - Als u geen beheerders machtigingen wilt toewijzen, zijn de volgende machtigingen nodig:
        - Extern beheer van gebruikers
        - Hyper-V-beheerders
        - Prestatie meter gebruikers

### <a name="verify-hyper-v-host-settings"></a>Instellingen voor Hyper-V-hosts controleren

1. Controleer de vereisten voor de [Hyper-V-host](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) voor Server evaluatie.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op Hyper-V-hosts.

### <a name="enable-powershell-remoting-on-hosts"></a>Externe communicatie van Power shell op hosts inschakelen

Stel als volgt Power shell Remoting in op elke host:

1. Open op elke host een Power shell-console als beheerder.
2. Voer deze opdracht uit:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Integratie Services op Vm's inschakelen

Integratie Services moet zijn ingeschakeld op elke VM, zodat Azure Migrate informatie over het besturings systeem op de virtuele machine kan vastleggen.

Op Vm's die u wilt detecteren en evalueren, schakelt u de [Hyper-V-integratie Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in op elke virtuele machine.


### <a name="enable-credssp-on-hosts"></a>CredSSP inschakelen op hosts

Als de host Vm's met schijven bevindt op SMB-shares, voert u deze stap uit op de host.

- U kunt deze opdracht op afstand uitvoeren op alle Hyper-V-hosts.
- Als u nieuwe host-knoop punten op een cluster toevoegt, worden deze automatisch toegevoegd voor detectie, maar moet u CredSSP hand matig inschakelen op de nieuwe knoop punten, indien nodig.

Schakel het volgende in:

1. Identificeer Hyper-V-hosts waarop Hyper-V-Vm's worden uitgevoerd met schijven op SMB-shares.
2. Voer de volgende opdracht uit op elke geïdentificeerde Hyper-V-host:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Wanneer u het apparaat instelt, voltooit u CredSSP door [het in te scha kelen op het apparaat](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Dit wordt beschreven in de volgende zelf studie in deze reeks.


## <a name="prepare-for-appliance-deployment"></a>Voorbereiden voor de implementatie van het apparaat

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Controleer](migrate-appliance.md#appliance---hyper-v) de vereisten van het apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds. Als u een op een URL gebaseerde firewall of proxy gebruikt, moet u ervoor zorgen dat deze toegang tot de vereiste Url's biedt.
3. Bekijk de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Controleer](migrate-appliance.md#collected-data---hyper-v) de toegangs vereisten voor de poort voor het apparaat.




## <a name="prepare-for-hyper-v-migration"></a>De migratie van Hyper-V voorbereiden

1. [Controleren](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) De vereisten voor de Hyper-V-host voor migratie en de Azure-Url's waarmee Hyper-V-hosts en-clusters toegang nodig hebben voor VM-migratie.
2. [Bekijk](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor virtuele Hyper-V-machines die u naar Azure wilt migreren.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * De Hyper-V-hosts en virtuele machines zijn voor bereid voor evaluatie en migratie.
> * Voor bereiding op de implementatie van het Azure Migrate apparaat.

Ga door naar de volgende zelf studie om een Azure Migrate project te maken, het apparaat te implementeren en virtuele Hyper-V-machines te detecteren en te evalueren voor migratie naar Azure.

> [!div class="nextstepaction"]
> [Virtuele Hyper-V-machines evalueren](./tutorial-assess-hyper-v.md)
