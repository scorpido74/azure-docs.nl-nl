---
title: Hyper-V VM's voorbereiden op beoordeling/migratie met Azure Migrate
description: Meer informatie over hoe u zich voorbereiden op beoordeling/migratie van Hyper V VM's met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ea6290eae187ed4ead39f7307a84a54a23c19c29
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641637"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Voorbereiden op beoordeling en migratie van Hyper-V VM's naar Azure

In dit artikel wordt beschreven hoe u zich voorbereidt op de beoordeling van on-premises Hyper-V VM's met [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)en migratie van Hyper-V VM's met [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).


Deze zelfstudie is de eerste in een reeks die u laat zien hoe u Hyper-V VM's beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Machtigingen instellen voor uw Azure-account en resources om met Azure Migreren te werken.
> * Bereid on-premises Hyper-V-hosts en VM's voor op serverbeoordeling. U voorbereiden met behulp van een configuratiescript, of handmatig.
> * Bereid u voor op de implementatie van het Azure Migrate-toestel. Het apparaat wordt gebruikt om on-premises VM's te ontdekken en te beoordelen.
> * Bereid on-premises Hyper-V-hosts en VM's voor op servermigratie.


> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk voor gedetailleerde instructies de how-tos voor Hyper-V-beoordeling en migratie.


Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

### <a name="azure-permissions"></a>Azure-machtigingen

U hebt machtigingen voor Azure Migrate-implementatie nodig.

**Taak** | **Details** 
--- | --- 
**Een Azure-migratieproject maken** | Uw Azure-account heeft machtigingen voor bijdragen de bijdrage er of eigenaar nodig om een project te maken. | 
**Resourceproviders registreren** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-toestel om Hyper-V VM's te detecteren en te beoordelen met Azure Migrate Server Assessment.<br/><br/> Tijdens de registratie van apparaten worden resourceproviders geregistreerd met het abonnement dat in het toestel is gekozen. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resourceproviders wilt registreren, hebt u een rol inzender of eigenaar bij het abonnement nodig.
**Azure AD-app maken** | Wanneer azure migrate het toestel registreert, maakt azure migrate een Azure Active Directory-app (Azure AD) die wordt gebruikt voor communicatie tussen de agents die op het toestel worden uitgevoerd met hun respectieve services die op Azure worden uitgevoerd. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig om Azure AD-apps te maken (beschikbaar in de rol Toepassingsontwikkelaar).



### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken

Controleer of u machtigingen hebt om een Azure Migrate-project te maken.

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het toestel te registreren

U machtigingen toewijzen voor Azure Migrate om de Azure AD-app te maken tijdens de toestelregistratie, met een van de volgende methoden:

- Een tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de tenant om Azure AD-apps te maken en te registreren.
- Een tenant/globale beheerder kan de rol Toepassingsontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De app heeft geen andere toegangsmachtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw toestel registreert. U de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Accountmachtigingen verlenen

De tenant/globale beheerder kan als volgt machtigingen verlenen:

1. In Azure AD moet de tenant/globale beheerder naar > **gebruikersinstellingen**van Azure Active > **Directory-gebruikers**navigeren. **Azure Active Directory**
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol Toepassingsontwikkelaar toewijzen

De tenant/globale beheerder kan de rol Toepassingsontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Bereid Hyper-V voor op beoordeling

U Hyper-V handmatig voorbereiden op VM-beoordeling of een configuratiescript gebruiken. De voorbereidingsstappen zijn als volgt:
- [Verifiëren](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Hyper-V-hostinstellingen en zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) zijn geopend op Hyper-V-hosts.
- PowerShell-remoting instellen op elke host, zodat het Azure Migrate-toestel PowerShell-opdrachten op de host kan uitvoeren via een WinRM-verbinding.
- Delegerreferenties als VM-schijven zich bevinden op externe SMB-shares.
- Stel een account in dat het toestel zal gebruiken om VM's te ontdekken op Hyper-V-hosts.
- Stel Hyper-V Integration Services in op elke VM die u wilt ontdekken en beoordelen. De standaardinstellingen wanneer u Integration Services inschakelt, zijn voldoende voor Azure Migrate.

    ![Integratieservices inschakelen](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Voorbereiden met een script

Het script doet het volgende:

- Hiermee controleert u of u het script uitvoert op een ondersteunde PowerShell-versie.
- Controleert of u (de gebruiker die het script uitvoert) beheerdersbevoegdheden hebt voor de Hyper-V-host.
- Hiermee u een lokaal gebruikersaccount (niet beheerder) maken dat de Azure Migrate-service gebruikt om te communiceren met de Hyper-V-host. Dit gebruikersaccount wordt toegevoegd aan deze groepen op de host:
    - Extern beheer van gebruikers
    - Hyper-V-beheerders
    - Gebruikers van prestatiemonitor
- Hiermee controleert u of de host een ondersteunde versie van Hyper-V en de Hyper-V-rol uitvoert.
- Hiermee wordt de WinRM-service ingeschakeld en worden poorten 5985 (HTTP) en 5986 (HTTPS) geopend op de host (die nodig zijn voor het verzamelen van metagegevens).
- Hiermee U PowerShell-remoting op de host inschakelen.
- Hiermee controleert u of de Hyper-V Integration Services is ingeschakeld op alle VM's die door de host worden beheerd.
- Hiermee schakelt u CredSSP op de host in indien nodig in.

Voer het script als volgt uit:

1. Zorg ervoor dat PowerShell-versie 4.0 of hoger op de Hyper-V-host is geïnstalleerd.
2. Download het script in het [Microsoft Download center](https://aka.ms/migrate/script/hyperv). Het script is cryptografisch ondertekend door Microsoft.
3. Valideer de scriptintegriteit met MD5- of SHA256-hashbestanden. Hashtag waarden zijn hieronder. Voer deze opdracht uit om de hash voor het script te genereren:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Voorbeeldgebruik:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    Nadat u de scriptintegriteit hebt gevaliderd, voert u het script op elke Hyper-V-host uit met deze PowerShell-opdracht:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtagwaarden

Hashwaarden zijn:

| **Hash** | **Waarde** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256 (SHA256)** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Handmatig voorbereiden

Volg de procedures in deze sectie om Hyper-V handmatig voor te bereiden, in plaats van het script te gebruiken.

### <a name="verify-powershell-version"></a>PowerShell-versie verifiëren

Zorg ervoor dat PowerShell-versie 4.0 of hoger op de Hyper-V-host is geïnstalleerd.



### <a name="set-up-an-account-for-vm-discovery"></a>Een account instellen voor vm-detectie

Azure Migrate heeft machtigingen nodig om on-premises VM's te ontdekken.

- Stel een domein- of lokaal gebruikersaccount in met beheerdersmachtigingen voor de Hyper-V hosts/cluster.

    - Je hebt één account nodig voor alle hosts en clusters die je in de detectie wilt opnemen.
    - Het account kan een lokaal of domeinaccount zijn. We raden aan beheerdersmachtigingen te hebben voor de Hyper-V-hosts of -clusters.
    - Als u administratormachtigingen niet wilt toewijzen, zijn de volgende machtigingen ook nodig:
        - Extern beheer van gebruikers
        - Hyper-V-beheerders
        - Gebruikers van prestatiemonitor

### <a name="verify-hyper-v-host-settings"></a>Hyper-V-hostinstellingen verifiëren

1. Controleer [de hyper-v-hostvereisten](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) voor serverbeoordeling.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-hyper-v.md#port-access) geopend zijn op Hyper-V hosts.

### <a name="enable-powershell-remoting-on-hosts"></a>PowerShell-remoting inschakelen op hosts

PowerShell-remoting instellen op elke host, als volgt:

1. Open op elke host een PowerShell-console als beheerder.
2. Voer deze opdracht uit:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Integratieservices inschakelen op VM's

Integratieservices moeten op elke VM worden ingeschakeld, zodat Azure Migrate informatie over het besturingssysteem op de VM kan vastleggen.

Op VM's die u wilt ontdekken en beoordelen, schakelt u [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in op elke VM.


### <a name="enable-credssp-on-hosts"></a>CredSSP inschakelen op hosts

Als de host VM's met schijven heeft die zich op SMB-shares bevinden, voltooit u deze stap op de host.

- U deze opdracht op afstand uitvoeren op alle Hyper-V-hosts.
- Als u nieuwe hostknooppunten toevoegt aan een cluster, worden ze automatisch toegevoegd voor detectie, maar moet u CredSSP handmatig inschakelen op de nieuwe knooppunten indien nodig.

Inschakelen als volgt:

1. Identificeer Hyper-V-hosts met Hyper-V VM's met schijven op SMB-shares.
2. Voer de volgende opdracht uit op elke geïdentificeerde Hyper-V-host:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Wanneer u het apparaat instelt, bent u klaar met het instellen van CredSSP door [het apparaat in te schakelen.](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds) Dit wordt beschreven in de volgende tutorial in deze serie.


## <a name="prepare-for-appliance-deployment"></a>Voorbereiden op de implementatie van apparaten

Bereid u voor voordat u het Azure Migrate-toestel en de beginbeoordeling in de volgende zelfstudie instelt, zich voorbereiden op de implementatie van apparaten.

1. [Controleer](migrate-appliance.md#appliance---hyper-v) de vereisten van het toestel.
2. Controleer de Azure URL's die het toestel nodig heeft om toegang te krijgen in de [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls) Als u een op URL gebaseerde firewall of proxy gebruikt, moet u ervoor zorgen dat deze toegang tot de vereiste URL's mogelijk maakt.
3. Bekijk de gegevens die het toestel verzamelt tijdens de detectie en beoordeling.
4. [Controleer](migrate-appliance.md#collected-data---hyper-v) de vereisten voor poorttoegang voor het toestel.




## <a name="prepare-for-hyper-v-migration"></a>Voorbereiden op Hyper-V-migratie

1. [Beoordeling](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V-hostvereisten voor migratie en de Azure-URL's waarvoor Hyper-V-hosts en clusters toegang nodig hebben voor VM-migratie.
2. [Bekijk](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor Hyper-V VM's die u naar Azure wilt migreren.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Azure-accountmachtigingen instellen.
> * Voorbereidhyper-v hosts en VM's voor beoordeling en migratie.
> * Voorbereid op de implementatie van het Azure Migrate-toestel.

Ga verder naar de volgende zelfstudie om een Azure Migrate-project te maken, het toestel te implementeren en HyperV VM's te ontdekken en te beoordelen op migratie naar Azure.

> [!div class="nextstepaction"]
> [Hyper-V VM's beoordelen](./tutorial-assess-hyper-v.md)
