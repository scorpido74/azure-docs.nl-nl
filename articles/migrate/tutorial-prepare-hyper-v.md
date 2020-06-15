---
title: Hyper-V-VM's voorbereiden op evaluatie/migratie met Azure Migrate
description: Meer informatie over Hyper-V-VM's voorbereiden op evaluatie/migratie met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 22fd5bc87494eb2fc162828363e7ca70afe1bbf0
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84322162"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V-VM's voorbereiden op evaluatie en migratie naar Azure

In dit artikel wordt beschreven hoe u de evaluatie van on-premises Hyper-V-VM’s voorbereidt met [Azure Migrate: Serverevaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool) en migratie van Hyper-V-VM's met [Azure Migrate: servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool).


Deze zelfstudie is de eerste in een serie die laat zien hoe u Hyper-V-VM’s kunt evalueren en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en -resources om met Azure Migrate te werken.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor serverevaluatie. U kunt voorbereiden met behulp van een configuratiescript of dit handmatig doen.
> * Bereid u voor op de implementatie van het Azure Migrate-apparaat. Het apparaat wordt gebruikt om on-premises virtuele machines te detecteren en te evalueren.
> * On-premises Hyper-V-hosts en virtuele machines voorbereiden voor servermigratie.


> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de procedures voor de evaluatie en migratie van Hyper-V voor gedetailleerde instructies.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

### <a name="azure-permissions"></a>Azure-machtigingen

U moet machtigingen instellen voor Azure Migrate-implementatie.

**Taak** | **Details** 
--- | --- 
**Maak een Azure Migrate-project** | Uw Azure-account heeft Inzender- of Eigenaarsmachtigingen nodig om een project te maken. | 
**Resourceprovider registreren** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-apparaat om Hyper-V-VM’s te detecteren en te evalueren met de evaluatie van Azure Migrate-server.<br/><br/> Tijdens de registratie van het apparaat worden resourceproviders geregistreerd bij het abonnement dat is gekozen in het apparaat. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resourceproviders wilt registreren, hebt u de rol Inzender of Eigenaar nodig voor het abonnement.
**Azure AD-app maken** | Bij het registreren van het apparaat maakt Azure Migrate een Azure Active Directory (Azure AD)-app die wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd, met de services die worden uitgevoerd op Azure. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig voor het maken van Azure AD-apps (beschikbaar in de Toepassingsontwikkelaar).



### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

Controleer of u gemachtigd bent om een Azure Migrate-project te maken.

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

U kunt aan de hand van een van de volgende methoden machtigingen voor Azure Migrate toewijzen om de Azure AD-app te maken tijdens de registratie van het apparaat:

- Een tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de tenant om Azure AD-apps te maken en registreren.
- Een tenant/globale beheerder kan de rol van Toepassingsontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De app heeft geen andere toegangsmachtigingen voor het abonnement dan de hierboven beschreven machtigingen.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Accountmachtigingen toekennen

De tenant/globale beheerder kan machtigingen als volgt verlenen:

1. In Azure AD moet de tenant/globale beheerder navigeren naar **Azure Active Directory** > **Gebruikers** > **Gebruikersinstellingen**.
2. De beheerder moet **App-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol toepassingsontwikkelaar toewijzen

Een tenant/globale beheerder kan de rol van Toepassingsontwikkelaar toewijzen aan het account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Hyper-V voorbereiden op evaluatie

U kunt Hyper-V voor de VM-evaluatie handmatig voorbereiden of een configuratiescript gebruiken. De voorbereidingsstappen zijn als volgt:
- [Bevestig](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) de instellingen voor Hyper-V-hosts en zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op Hyper-V-hosts.
- Externe communicatie van PowerShell instellen op elke host, zodat het Azure Migrate-apparaat PowerShell-opdrachten op de host kan uitvoeren via een WinRM-verbinding.
- Referenties delegeren als VM-schijven zich op externe NFS-shares bevinden.
- Stel een account in dat het apparaat gebruikt voor het detecteren van VM's op Hyper-V-hosts.
- Stel Hyper-V-integratieservices in op elke virtuele machine die u wilt detecteren en evalueren. De standaardinstellingen voor het inschakelen van integratieservices zijn voldoende voor Azure Migrate.

    ![Integratieservices inschakelen](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Voorbereiden met een script

Het script doet het volgende:

- Controleert of u het script uitvoert op een ondersteunde PowerShell-versie.
- Controleert of u (de gebruiker die het script uitvoert) beheerdersbevoegdheden op de Hyper-V-host hebt.
- Hiermee kunt u een lokale gebruikersaccount (geen beheerder) maken dat de Azure Migrate-service gebruikt om te communiceren met de Hyper-V-host. Dit gebruikersaccount wordt toegevoegd aan deze groepen op de host:
    - Gebruikers van extern beheer
    - Hyper-V-beheerders
    - Prestatiemetergebruikers
- Controleert of op de host een ondersteunde versie van Hyper-V en de Hyper-V-rol wordt uitgevoerd.
- Hiermee schakelt u de WinRM-service in en opent u poort 5985 (HTTP) en 5986 (HTTPS) op de host (vereist voor de verzameling metagegevens).
- Externe communicatie met PowerShell is ingeschakeld op de host.
- Hiermee wordt gecontroleerd of de Hyper-V-integratieservices zijn ingeschakeld op alle VM's die worden beheerd door de host.
- Hiermee schakelt u CredSSP in op de host, indien nodig.

Voer het script als volgt uit:

1. Zorg ervoor dat PowerShell-versie 4.0 of hoger is geïnstalleerd op de Hyper-V-host.
2. Download het script via het [Microsoft Downloadcenter](https://aka.ms/migrate/script/hyperv). Het script is cryptografisch ondertekend door Microsoft.
3. Valideer de scriptintegriteit met behulp van MD5 of SHA256 hash-bestanden. De hashtagwaarden staan hieronder. Gebruik deze opdracht om de hash voor het script te genereren:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Gebruiksvoorbeeld:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Nadat u de scriptintegriteit hebt gevalideerd, voert u het script uit op elke Hyper-V-host met de volgende PowerShell-opdracht:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtagwaarden

Hashwaarden zijn:

| **Hash** | **Waarde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Handmatig voorbereiden

Volg de procedures in deze sectie om Hyper-V handmatig voor te bereiden, in plaats van het script te gebruiken.

### <a name="verify-powershell-version"></a>PowerShell-versie bevestigen

Zorg ervoor dat PowerShell-versie 4.0 of hoger is geïnstalleerd op de Hyper-V-host.



### <a name="set-up-an-account-for-vm-discovery"></a>Een account voor detectie van virtuele machines instellen

Azure Migrate heeft machtigingen nodig om on-premises virtuele machines te detecteren.

- Stel een domein- of lokale gebruikersaccount in met beheerdersmachtigingen op de Hyper-V-hosts/het cluster.

    - U hebt één individuele account nodig voor alle hosts en clusters die u wilt toevoegen in de detectie.
    - Het account kan een lokale of een domeinaccount zijn. Het is raadzaam dat het beheerdersmachtigingen heeft op de Hyper-V-hosts of -clusters.
    - Als u geen beheerdersmachtigingen wilt toewijzen, zijn de volgende machtigingen nodig:
        - Gebruikers van extern beheer
        - Hyper-V-beheerders
        - Prestatiemetergebruikers

### <a name="verify-hyper-v-host-settings"></a>Instellingen voor Hyper-V-hosts bevestigen

1. [Vereisten voor de Hyper-V-host](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) voor serverevaluatie bevestigen.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op Hyper-V-hosts.

### <a name="enable-powershell-remoting-on-hosts"></a>Externe communicatie met PowerShell inschakelen op hosts

Stel als volgt externe communicatie van PowerShell in op elke host:

1. Open op elke host een PowerShell-console als beheerder.
2. Voer deze opdracht uit:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Integratieservices inschakelen op VM’s

Integratieservices moet zijn ingeschakeld op elke VM, zodat Azure Migrate informatie over het besturingssysteem op de virtuele machine kan vastleggen.

Schakel [Hyper-V-integratieservices](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in op elke virtuele machine die u wilt detecteren en evalueren.


### <a name="enable-credssp-on-hosts"></a>CredSSP inschakelen op hosts

Als de host VM's heeft met schijven die zich NFS-shares bevinden, voert u deze stap uit op de host.

- U kunt deze opdracht op afstand uitvoeren op alle Hyper-V-hosts.
- Als u nieuwe host-knooppunten op een cluster toevoegt, worden deze automatisch toegevoegd voor detectie, maar moet u CredSSP handmatig inschakelen op de nieuwe knooppunten, indien nodig.

Schakel als volgt in:

1. Identificeer Hyper-V-hosts waarop Hyper-V-VM's worden uitgevoerd met schijven op NFS-shares.
2. Voer de volgende opdracht uit op elke geïdentificeerde Hyper-V-host:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Wanneer u het apparaat instelt, voltooit u de instelling van CredSSP door [deze in te schakelen op het apparaat](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Dit wordt beschreven in de volgende zelfstudie in deze serie.


## <a name="prepare-for-appliance-deployment"></a>Implementatie van apparaten voorbereiden

Voordat u het Azure Migrate-apparaat instelt en de evaluatie begint in de volgende zelfstudie, moet u de implementatie van het apparaat voorbereiden.

1. Apparaatvereisten [bevestigen](migrate-appliance.md#appliance---hyper-v).
2. Bekijk de Azure-URL's die het apparaat nodig heeft voor toegang tot de [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds. Als u een op een URL gebaseerde firewall of proxy gebruikt, moet u ervoor zorgen dat deze toegang tot de vereiste URL's biedt.
3. Beoordeel de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Beoordeel](migrate-appliance.md#collected-data---hyper-v) de toegangsvereisten voor de poort voor het apparaat.




## <a name="prepare-for-hyper-v-migration"></a>De migratie van Hyper-V voorbereiden

1. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) vereisten voor de Hyper-V-host voor migratie en de Azure-URL's waar Hyper-V-hosts en-clusters toegang nodig hebben voor VM-migratie.
2. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor virtuele Hyper-V-machines die u naar Azure wilt migreren.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * Hyper-V-hosts en -VM's voorbereiden op evaluatie en migratie.
> * Voorbereid op de implementatie van het Azure Migrate-apparaat.

Ga door naar de volgende zelfstudie om een Azure Migrate-project te maken, het apparaat te implementeren en Hyper-V-VM’s te detecteren en te evalueren voor migratie naar Azure.

> [!div class="nextstepaction"]
> [Assess Hyper-V VMs](./tutorial-assess-hyper-v.md)
