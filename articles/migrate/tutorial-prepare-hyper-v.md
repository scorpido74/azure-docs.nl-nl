---
title: Hyper-V-VM's voorbereiden op evaluatie/migratie met Azure Migrate
description: Meer informatie over Hyper-V-VM's voorbereiden op evaluatie/migratie met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109617"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V-VM's voorbereiden op evaluatie en migratie naar Azure

Dit artikel helpt u bij het voorbereiden op het evalueren en migreren van on-premises Hyper-V-VM’s naar Azure met behulp van [Azure Migrate: Serverevaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool) en [Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool).


Deze zelfstudie is de eerste in een serie die laat zien hoe u Hyper-V-VM’s kunt evalueren en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid Azure voor om te werken met Azure Migrate.
> * Evalueren van Hyper-V-VM's voorbereiden.
> * Migreren van Hyper-V-VM's voorbereiden 

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

De tabel bevat een overzicht van de taken die u moet uitvoeren in Azure. Volg de instructies in de tabel.

**Taak** | **Details** | **Machtigingen**
--- | --- | ---
**Maak een Azure Migrate-project** | Een Azure Migrate-project voorziet in een centrale locatie voor het organiseren en beheren van evaluaties en migraties met Azure Migrate-tools, Microsoft-hulpprogramma's en producten van derden. | Uw Azure-account heeft Inzender- of Eigenaar-machtigingen nodig voor de resourcegroep waarin het project zich bevindt.
**Apparaat registreren** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-apparaat om Hyper-V-VM’s te detecteren en te evalueren. [Meer informatie](migrate-appliance-architecture.md#appliance-registration). | Als u het apparaat wilt registreren, moet uw Azure-account Inzender- of Eigenaar-machtigingen voor het Azure-abonnement hebben.
**Azure AD-app maken** | Bij het registreren van het apparaat maakt Azure Migrate een Azure Active Directory (Azure AD)-app die wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd en Azure Migrate. | Uw Azure-account moet gemachtigd zijn om Azure AD-apps te maken.
**Een VM maken**: | U hebt machtigingen nodig voor het maken van een virtuele machine in de resourcegroep en het virtuele netwerk, en voor het schrijven naar een beheerde Azure-schijf. | Uw Azure-account heeft de rol Inzender voor virtuele machines nodig.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

Controleer of u gemachtigd bent om een Azure Migrate-project te maken.

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Machtigingen toewijzen voor het maken van Azure AD-apps

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
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol toepassingsontwikkelaar toewijzen

Een tenant/globale beheerder kan de rol van Toepassingsontwikkelaar toewijzen aan het account. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

### <a name="assign-azure-account-permissions"></a>Machtigingen voor het Azure-account toewijzen

Wijs de rol Inzender voor virtuele machines toe aan het account, zodat u gemachtigd bent voor het volgende:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijf naar een door Azure beheerde schijf. 


### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

[Stel een Azure-netwerk in](../virtual-network/manage-virtual-network.md#create-a-virtual-network). On-premises machines worden gerepliceerd naar beheerde Azure-schijven. Wanneer u naar Azure een failover voor migratie uitvoert, worden er virtuele Azure-machines gemaakt op basis van deze beheerde schijven en gekoppeld aan het Azure-netwerk dat u hebt ingesteld.


## <a name="prepare-for-assessment"></a>Evaluatie voorbereiden

U kunt Hyper-V voor de VM-evaluatie handmatig voorbereiden of een configuratiescript gebruiken. Dit zijn de voorbereidende stappen. Als u met behulp van een script voorbereidt, worden die stappen automatisch geconfigureerd.

**Stap** | **Script** | **Handmatig**
--- | --- | ---
**Verifieer Hyper-V-host-vereisten** | Definieer controles om na te gaan of op de host een ondersteunde versie van Hyper-V en de Hyper-V-rol wordt uitgevoerd.<br/><br/> Hiermee schakelt u de WinRM-service in en opent u poort 5985 (HTTP) en 5986 (HTTPS) op de host (vereist voor de verzameling metagegevens). | [Vereisten voor de Hyper-V-host](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) voor serverevaluatie bevestigen.<br/><br/> Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op Hyper-V-hosts.
**PowerShell-versie bevestigen** | Controleert of u het script uitvoert op een ondersteunde PowerShell-versie. | Controleer of u PowerShell versie 4.0 of hoger uitvoert op de Hyper-V-host.
**Een account maken** | Controleert of u (de gebruiker die het script uitvoert) beheerdersbevoegdheden op de Hyper-V-host hebt.<br/><br/>  Hiermee kunt u een lokale gebruikersaccount (geen beheerder) maken dat de Azure Migrate-service gebruikt om te communiceren met de Hyper-V-host. Dit gebruikersaccount wordt toegevoegd aan deze groepen op de host:<br/><br/> - Gebruikers van extern beheer<br/><br/> - Hyper-V-beheerders<br/><br/>- Prestatiemetergebruikers | Stel een domein- of lokale gebruikersaccount in met beheerdersmachtigingen op de Hyper-V-hosts/het cluster.<br/><br/> - U hebt één individueel account nodig voor alle hosts en clusters die u wilt toevoegen in de detectie.<br/><br/> - Het account kan een lokaal of domeinaccount zijn. Het is raadzaam dat het beheerdersmachtigingen heeft op de Hyper-V-hosts of -clusters.<br/><br/> Als u geen beheerdersmachtigingen wilt toewijzen, zijn de volgende machtigingen nodig: Gebruikers van extern beheer, Hyper-V-administrators en Prestatiemetergebruikers.
**Externe communicatie met PowerShell inschakelen** | Hiermee stelt u externe communicatie van PowerShell in op elke host, zodat het Azure Migrate-apparaat PowerShell-opdrachten op de host kan uitvoeren via een WinRM-verbinding.| U stelt dit in door op elke host een PowerShell-console als beheerder te openen en voer deze opdracht uit:<br/><br/>``` Enable-PSRemoting -force ```
**Hyper-V-integratieservices instellen** | Hiermee wordt gecontroleerd of de Hyper-V-integratieservices zijn ingeschakeld op alle VM's die worden beheerd door de host. |  [Schakel Hyper-V-integratieservices in](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) op elke VM.<br/><br/> Als u werkt met Windows Server 2003, [volgt u deze instructies](prepare-windows-server-2003-migration.md).
**Referenties delegeren als VM-schijven zich op externe NFS-shares bevinden** | Neem het delegeren van referenties op in het script. | [Schakel CredSSP in](#enable-credssp-to-delegate-credentials) om referenties te delegeren.

### <a name="run-the-script"></a>Het script uitvoeren

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

#### <a name="hashtag-values"></a>Hashtagwaarden

Hashwaarden zijn:

| **Hash** | **Waarde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>CredSSP inschakelen voor het delegeren van referenties

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

1. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) vereisten voor de Hyper-V-host voor migratie en de Azure-URL's waar Hyper-V-hosts en-clusters toegang nodig hebben voor VM-migratie.
2. [Beoordeel](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor virtuele Hyper-V-machines die u naar Azure wilt migreren.
3. U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure migreert.
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de VM migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
    - Bekijk de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * Hyper-V-hosts en -VM's voorbereiden op evaluatie en migratie.
> * Voorbereid op de implementatie van het Azure Migrate-apparaat.

Ga door naar de volgende zelfstudie om een Azure Migrate-project te maken, het apparaat te implementeren en Hyper-V-VM’s te detecteren en te evalueren voor migratie naar Azure.

> [!div class="nextstepaction"]
> [Assess Hyper-V VMs](./tutorial-assess-hyper-v.md)
