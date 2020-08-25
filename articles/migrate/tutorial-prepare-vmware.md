---
title: VMware-VM's voorbereiden op evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van VMware-VM's op evaluatie/migratie met Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8b812924c0922d460c631baec8b0e13a9f45cd76
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109572"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware-VM's voorbereiden op evaluatie en migratie naar Azure

Dit artikel helpt u om de evaluatie en migratie van on-premises VMware-VM's naar Azure voor te bereiden met behulp van [Azure Migrate](migrate-services-overview.md).

Deze zelfstudie is de eerste in een serie die laat zien hoe u VMware-VM’s kunt evalueren en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden om te werken met Azure Migrate.
> * Bereid de evaluatie van virtuele VMware-machines voor met het hulpprogramma Azure Migrate:Server Assessment.
> * Bereid de migratie van virtuele VMware-machines voor met het hulpprogramma Azure Migrate:Server Migration. 

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Ze zijn handig als snelle proof-of-concept. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

De tabel bevat een overzicht van de taken die u moet uitvoeren in Azure. De instructies voor elke taak volgen de tabel.

**Taak** | **Details** | **Machtigingen**
--- | --- | ---
**Maak een Azure Migrate-project** | Een Azure Migrate-project voorziet in een centrale locatie voor het organiseren en beheren van evaluaties en migraties met Azure Migrate-hulpprogramma's, Microsoft-hulpprogramma's en aanbiedingen van derden. | Uw Azure-account heeft inzender- of eigenaar-machtigingen nodig voor de resourcegroep waarin het project zich bevindt.
**Apparaat registreren** | Azure Migrate gebruikt een lichtgewicht Azure Migrate-apparaat om VM's te detecteren, te evalueren met het hulpprogramma Server Assessment en om ze te migreren met behulp van migratie zonder agent met het hulpprogramma Server Migration. [Meer informatie](migrate-appliance-architecture.md#appliance-registration) over registratie. | Als u het apparaat wilt registreren, moet uw Azure-account inzender- of eigenaar-machtigingen voor het Azure-abonnement hebben.
**Azure AD-apps maken** | Bij het registreren van een apparaat maakt Azure Migrate Azure AD-apps (Azure Active Directory). <br/><br/> - De eerste app wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd en Azure Migrate. <br/><br/> - De tweede app wordt alleen gebruikt voor toegang tot KeyVault die is gemaakt in het abonnement van de gebruiker voor de migratie van VMware-VM's zonder agent.   | Uw Azure-account moet gemachtigd zijn om Azure AD-apps te maken.
**Een sleutelkluis maken** | Als u VMware-VM's wilt migreren via migratie zonder agent, maakt Azure Migrate een sleutelkluis voor het beheren van toegangssleutels voor het replicatieaccount in uw abonnement. | Als u wilt toestaan dat Azure Migrate de sleutelkluis maakt, stelt u machtigingen (eigenaar, of inzender en beheerder van gebruikerstoegang) in voor de resourcegroep waarin het Azure Migrate-project zich bevindt.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Machtigingen toewijzen voor het maken van Azure AD-apps

Als u het apparaat wilt registreren, moet uw Azure-account gemachtigd zijn om Azure AD-apps te maken. Wijs de machtigingen toe via een van de volgende methoden:

- **Methode 1: Machtigingen verlenen aan het account**: Een tenant/globale beheerder kan machtigingen verlenen aan gebruikersaccounts in de tenant om Azure AD-apps te maken en registreren.
- **Methode 2: Een rol toewijzen met de machtigingen voor een gebruikersaccount**: Een tenant/globale beheerder kan de rol van Toepassingsontwikkelaar (die de vereiste machtigingen heeft) toewijzen aan het gebruikersaccount.

> [!NOTE]
> U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="method-1-grant-permissions-to-the-account"></a>Methode 1: Machtigingen verlenen aan het account

Verleen als volgt machtigingen aan het account:

1. Zorg ervoor dat u een tenant of een globale administrator bent. Navigeer in Azure AD naar **Azure Active Directory** > **Gebruikers** > **Gebruikersinstellingen**.
2. Stel **App-registraties** in op **Ja**. Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Methode 2: Rol toepassingsontwikkelaar toewijzen

Als alternatief kan een tenant/globale beheerder de rol van toepassingsontwikkelaar toewijzen aan het account. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) over het toewijzen van een rol.

### <a name="assign-permissions-to-create-a-key-vault"></a>Machtigingen toewijzen voor het maken van een sleutelkluis

Als u Azure Migrate een sleutelkluis wilt laten maken, moet u machtigingen als volgt toewijzen:

1. Selecteer **Toegangsbeheer (IAM)** in de resourcegroep in Azure Portal.
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.

    - Als u Serverevaluatie wilt uitvoeren, zijn machtigingen voor een **Inzender** voldoende.
    - Als u migratie zonder agent van servers wilt uitvoeren, moet u **eigenaarsmachtigingen** (of machtigingen voor **inzender** en **beheerder voor gebruikerstoegang**) hebben.

3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de resourcegroep.

## <a name="prepare-for-assessment"></a>Evaluatie voorbereiden

Ter voorbereiding op de VMware VM-evaluatie moet u het volgende doen:

1. **Controleer de VMware-instellingen**. Zorg ervoor dat de vCenter Server en VM's die u wilt migreren, voldoen aan de vereisten.
2. **Stel machtigingen in voor de evaluatie**. Azure Migrate gebruikt een vCenter-account voor toegang tot de vCenter Server om VM's te detecteren en te evalueren.
3. **Controleer de apparaatvereisten**. Controleer de implementatievereisten voor het Azure Migrate-apparaat voordat u deze in de volgende zelfstudie implementeert.

### <a name="verify-vmware-settings"></a>De VMware-instellingen controleren

1. [Controleer de VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) voor de evaluatie.
2. [Controleer](migrate-support-matrix-vmware.md#port-access-requirements) of de poorten die u nodig hebt, zijn geopend op vCenter Server.
3. Controleer op vCenter Server of uw account over de machtigingen beschikt om een virtuele machine te maken met behulp van een OVA-bestand. Dit is nodig wanneer u met behulp van een OVA-bestand het Azure Migrate-apparaat als een VMware-VM implementeert.
4. U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure migreert.

    - Voor sommige besturingssystemen worden deze wijzigingen automatisch door Azure Migrate aangebracht. 
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de virtuele machine migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
    - Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.


### <a name="set-up-permissions-for-assessment"></a>Machtigingen instellen voor de evaluatie

Azure Migrate moet toegang hebben tot de vCenter Server, zodat het Azure Migrate-apparaat VM's kan detecteren voor evaluatie en migratie zonder agent. Stel als volgt een account in:

1. Open in de vSphere-webclient **Beheer** > **Toegang** > **SSO-gebruikers en -groepen**.
2. Klik in **Gebruikers** op het pictogram **Nieuwe gebruiker**.
3. Typ de details van de nieuwe gebruiker.
4. Selecteer machtigingen in overeenstemming met de tabelwaarden.

    **Functie** | **Accountmachtigingen**
    --- | ---
    [VM's evalueren](tutorial-assess-vmware.md) | Voor de evaluatie heeft het account alleen-lezen toegang nodig.
    [Apps, functies en onderdelen van VM's detecteren](how-to-discover-applications.md) | Als u app-detectie wilt gebruiken, is het nodig dat bij het alleen-lezen account dat wordt gebruikt voor de evaluatie, bevoegdheden zijn ingeschakeld voor **Virtuele machines** > **Gastbewerkingen**.
    [Afhankelijkheden op VM's analyseren (zonder agent)](how-to-create-group-machine-dependencies-agentless.md) | Als u afhankelijkheden wilt analyseren, is het nodig dat bij het alleen-lezenaccount dat wordt gebruikt voor de evaluatie, bevoegdheden zijn ingeschakeld voor **Virtuele machines** > **Gastbewerkingen**.
    
> [!NOTE]
> Raadpleeg [dit artikel](set-discovery-scope.md#assign-a-role-for-assessment) als u de detectie van virtuele machines voor evaluatie tot een bepaald bereik wilt beperken.

### <a name="verify-appliance-settings-for-assessment"></a>De apparaatinstellingen controleren voor evaluatie

In de [volgende zelfstudie](tutorial-assess-vmware.md) stelt u het Azure Migrate-apparaat in en begint u met de evaluatie. Voordat u dat doet, moet u de vereisten voor het apparaat als volgt controleren: 

1. [Bekijk de vereisten](migrate-appliance.md#appliance---vmware) voor het implementeren van het Azure Migrate-apparaat.
2. Bekijk de Azure-URL's die het apparaat nodig heeft voor toegang tot de [openbare](migrate-appliance.md#public-cloud-urls) clouds en [overheidsclouds](migrate-appliance.md#government-cloud-urls).
3. [Let op](migrate-support-matrix-vmware.md#port-access-requirements) de poorten die door het apparaat worden gebruikt.
4. [Controleer de gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.

## <a name="prepare-for-agentless-vmware-migration"></a>Voorbereiden van VMware-migratie zonder agent

U kunt VMware-VM's migreren met [migratie zonder agent of op basis van een agent](server-migrate-overview.md). In deze sectie vindt u een overzicht van de vereisten voor migratie zonder agent.

1. [Bepaal](server-migrate-overview.md#compare-migration-methods) of u migratie zonder agent wilt gebruiken.
2. [Controleer](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) hypervisorvereisten voor machines die u wilt migreren.
3. [Controleer](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) de vereisten voor VMware-VM's die u wilt migreren met migratie zonder agent.
4. [Controleer de vereisten](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) van het Azure Migrate-apparaat voor migratie zonder agent.
5. Let op de URL-toegang die vereist is voor [openbare](migrate-appliance.md#public-cloud-urls) clouds en [overheidsclouds](migrate-appliance.md#government-cloud-urls).
6. Controleer de [toegangsvereisten](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) voor de poort.
7. Stel machtigingen voor migratie zonder agent in, zoals is beschreven in de volgende procedure.


### <a name="assign-permissions-to-an-account"></a>Machtigingen toewijzen aan een account

Het Azure Migrate apparaat maakt verbinding met de vCenter Server voor het detecteren en migreren van VM's via migratie zonder agent. U kunt de machtigingen toewijzen die het apparaat nodig heeft voor een gebruikersaccount, of een rol maken met de machtigingen en die rol toewijzen aan een gebruikersaccount.

1. Open in de vSphere-webclient **Beheer** > **Toegang** > **SSO-gebruikers en -groepen**.
2. Klik in **Gebruikers** op het pictogram **Nieuwe gebruiker**.
3. Typ de details van de nieuwe gebruiker.
4. [Deze machtigingen](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) toewijzen

#### <a name="create-a-role-and-assign-to-an-account"></a>Een rol maken en aan een account toewijzen

U kunt ook een account maken. Maak vervolgens een rol en wijs deze als volgt toe aan het account:

1. Meld u aan bij de Sphere-webclient als vCenter Server-beheerder
2. Selecteer de vCenter Server-instantie > **Rol maken**.
3. Geef een rolnaam op, bijvoorbeeld <em>Azure_Migrate</em>, en wijs de [vereiste machtigingen toe](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) aan de rol.

    ![vCenter Server-accountbevoegdheden](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Maak nu een account en wijs de rol toe aan het account.

> [!NOTE]
> Raadpleeg [dit artikel](set-discovery-scope.md#assign-a-role-for-agentless-migration) als u de detectie van virtuele machines voor migratie zonder agent tot een bepaald bereik wilt beperken.



## <a name="prepare-for-agent-based-vmware-migration"></a>Voorbereiden van VMware-migratie op basis van een agent

U kunt VMware-VM's migreren met [migratie zonder agent of op basis van een agent](server-migrate-overview.md). In deze sectie vindt u een overzicht van de vereisten voor migratie op basis van een agent.

1. [Bepaal](server-migrate-overview.md#compare-migration-methods) of u migratie op basis van een agent wilt gebruiken.
1. [Controleer](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) hypervisorvereisten voor machines die u wilt migreren.
2. [Controleer](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) de vereisten voor VMware-VM's, inclusief de installatie van de Mobility-service op elke VM die u wilt migreren.
3. Voor migratie op basis van een agent wordt een replicatie-apparaat gebruikt:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatievereisten voor het replicatie-apparaat.
    - [Bekijk de opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
    - Let op de URL-toegang die vereist is voor [openbare](migrate-replication-appliance.md#url-access) clouds en [overheidsclouds](migrate-replication-appliance.md#azure-government-url-access).
    - Controleer de [toegangsvereisten](migrate-replication-appliance.md#port-access) voor het replicatie-apparaat.
4. U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure kunt migreren. Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * De machtigingen voor Azure ingesteld.
> * VMware voorbereid op evaluatie en migratie.


Ga door naar de tweede zelfstudie om een Azure Migrate-project in te stellen en VMware-VM's te evalueren voor migratie naar Azure.

> [!div class="nextstepaction"]
> [VMware-VM's evalueren](./tutorial-assess-vmware.md)
