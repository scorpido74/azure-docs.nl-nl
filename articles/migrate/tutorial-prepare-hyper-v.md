---
title: Virtuele Hyper-V-machines voorbereiden voor evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van de evaluatie/migratie van Hyper-V-Vm's met Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f93528e2a35661f8a233aea476a958a079d7cd59
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196266"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Voor bereiding voor de evaluatie en migratie van virtuele Hyper-V-machines naar Azure

In dit artikel wordt beschreven hoe u de evaluatie en migratie van on-premises virtuele Hyper-V-machines naar Azure voorbereidt met [Azure migrate](migrate-services-overview.md).

[Azure migrate](migrate-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden.

Deze zelf studie is de eerste in een serie die laat zien hoe u virtuele Hyper-V-machines kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en-resources om met Azure Migrate te werken.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor Server evaluatie.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor server migratie.


> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de procedures voor de evaluatie en migratie van Hyper-V voor meer informatie.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

### <a name="azure-permissions"></a>Azure-machtigingen

U moet machtigingen instellen voor Azure Migrate-implementatie.

- Machtigingen voor uw Azure-account om een Azure Migrate project te maken.
- Machtigingen voor uw account om het Azure Migrate apparaat te registreren. Het apparaat wordt gebruikt voor Hyper-V-detectie en-migratie. Tijdens de registratie van het apparaat maakt Azure Migrate twee Azure Active Directory (Azure AD)-apps waarmee het apparaat op unieke wijze wordt geïdentificeerd:
    - De eerste app communiceert met Azure Migrate service-eind punten.
    - De tweede app heeft toegang tot een Azure Key Vault die tijdens de registratie is gemaakt voor het opslaan van Azure AD-app-informatie en toestel configuratie-instellingen.



### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

Controleer of u gemachtigd bent om een Azure Migrate project te maken.

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

Met een van de volgende methoden kunt u machtigingen voor Azure Migrate toewijzen om de Azure AD-apps te maken tijdens de registratie van het apparaat:

- Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
- Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

Het is een goed idee dat:

- De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
- U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen:

1. In azure AD moet de Tenant/globale beheerder naar **Azure Active Directory** > **gebruikers** > **gebruikers instellingen**navigeren.
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>De evaluatie van Hyper-V voorbereiden

Ga als volgt te werk om de Hyper-V-evaluatie voor te bereiden:

1. Controleer de instellingen voor de Hyper-V-host.
2. Externe communicatie van Power shell instellen op elke host, zodat het Azure Migrate apparaat Power shell-opdrachten op de host kan uitvoeren via een WinRM-verbinding.
3. Als de VM-schijven zich in externe SMB-opslag bevinden, is delegering van referenties nodig.
    - Schakel CredSSP-delegering in, zodat het Azure Migrate apparaat kan fungeren als de client, en de referenties voor een host delegeren.
    - U schakelt elke host in als gemachtigde voor het apparaat, zoals hieronder wordt beschreven.
    - Wanneer u het apparaat later instelt, schakelt u delegering op het apparaat in.
4. Controleer de vereisten van apparaten en de URL/poort toegang die nodig is voor het apparaat.
5. Stel een account in dat door het apparaat wordt gebruikt voor het detecteren van Vm's.
6. Stel Hyper-V-integratie Services in op elke virtuele machine die u wilt detecteren en beoordelen.


U kunt deze instellingen hand matig configureren met behulp van de onderstaande procedures. U kunt ook het configuratie script voor de vereisten voor Hyper-V uitvoeren.

### <a name="hyper-v-prerequisites-configuration-script"></a>Configuratie script voor vereisten voor Hyper-V

Met het script valideert u Hyper-V-hosts en configureert u de instellingen die u nodig hebt om virtuele Hyper-V-machines te detecteren en te evalueren. Dit doet u als volgt:

- Controleert of u het script uitvoert op een ondersteunde Power shell-versie.
- Controleert of u (de gebruiker die het script uitvoert) beheerders bevoegdheden op de Hyper-V-host heeft.
- Hiermee kunt u een lokaal gebruikers account (geen beheerder) maken dat wordt gebruikt voor de Azure Migrate-service om te communiceren met de Hyper-V-host. Dit gebruikers account wordt toegevoegd aan deze groepen op de host:
    - Gebruikers van extern beheer
    - Hyper-V-Administrators
    - Prestatie meter gebruikers
- Controleert of op de host een ondersteunde versie van Hyper-V en de Hyper-V-functie wordt uitgevoerd.
- Hiermee schakelt u de WinRM-service in en opent u poort 5985 (HTTP) en 5986 (HTTPS) op de host (vereist voor de verzameling meta gegevens).
- Externe communicatie met Power shell is ingeschakeld op de host.
- Hiermee wordt gecontroleerd of de Hyper-V-integratie service is ingeschakeld op alle Vm's die worden beheerd door de host.
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

4.  Nadat u de script integriteit hebt gevalideerd, voert u het script uit op elke Hyper-V-host met de volgende Power shell-opdracht:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Hashtag-waarden

Hash-waarden zijn:

| **Hash** | **Waarde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Instellingen voor Hyper-V-hosts controleren

1. Controleer de vereisten voor de [Hyper-V-host](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) voor Server evaluatie.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#assessment-port-requirements) zijn geopend op Hyper-V-hosts.

### <a name="enable-powershell-remoting-on-hosts"></a>Externe communicatie van Power shell op hosts inschakelen

Stel als volgt Power shell Remoting in op elke host:

1. Open op elke host een Power shell-console als beheerder.
2. Voer deze opdracht uit:

    ```
    Enable-PSRemoting -force
    ```

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

Wanneer u het apparaat instelt, moet u CredSSP instellen door [het in te scha kelen op het apparaat](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Dit wordt beschreven in de volgende zelf studie in deze reeks.


### <a name="verify-appliance-settings"></a>Instellingen voor toestellen controleren

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Controleer](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) de vereisten van het apparaat.
2. [Bekijk](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) de Azure-url's die het apparaat nodig heeft om toegang te krijgen.
3. Bekijk de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-hyper-v.md#assessment-port-requirements) de toegangs vereisten voor poorten voor het apparaat.


### <a name="set-up-an-account-for-vm-discovery"></a>Een account instellen voor de detectie van VM'S

Azure Migrate heeft machtigingen nodig om on-premises virtuele machines te detecteren.

- Stel een domein-of lokale gebruikers account in met beheerders machtigingen op de Hyper-V-hosts/het cluster.

    - U hebt één account nodig voor alle hosts en clusters die u wilt toevoegen in de detectie.
    - Het account kan een lokaal of een domein account zijn. Het is raadzaam dat het beheerders machtigingen heeft op de Hyper-V-hosts of-clusters.
    - Als u geen beheerders machtigingen wilt toewijzen, zijn de volgende machtigingen nodig:
        - Gebruikers van extern beheer
        - Hyper-V-Administrators
        - Prestatie meter gebruikers

### <a name="enable-integration-services-on-vms"></a>Integratie Services op Vm's inschakelen

Integratie Services moet zijn ingeschakeld op elke VM, zodat Azure Migrate informatie over het besturings systeem op de virtuele machine kan vastleggen.

Op Vm's die u wilt detecteren en evalueren, schakelt u de [Hyper-V-integratie Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in op elke virtuele machine.

## <a name="prepare-for-hyper-v-migration"></a>De migratie van Hyper-V voorbereiden

1. [Controleren](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Vereisten voor de Hyper-V-host voor migratie.
2. [Bekijk](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) de vereisten voor virtuele Hyper-V-machines die u naar Azure wilt migreren.
3. [Noteer](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) de Azure-Url's waaraan Hyper-V-hosts en-clusters toegang nodig hebben voor VM-migratie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * De Hyper-V-hosts en virtuele machines zijn voor bereid voor evaluatie en migratie.

Ga door naar de volgende zelf studie om een Azure Migrate project te maken en de virtuele machines van Hyper-V te evalueren voor migratie naar Azure

> [!div class="nextstepaction"]
> [Virtuele Hyper-V-machines evalueren](./tutorial-assess-hyper-v.md)
