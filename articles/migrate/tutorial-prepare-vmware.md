---
title: VMware VM's voorbereiden op beoordeling/migratie met Azure Migrate
description: Meer informatie over hoe u zich voorbereiden op beoordeling/migratie van VMware VM's met Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677287"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware-VM's voorbereiden op evaluatie en migratie naar Azure

Met dit artikel u zich voorbereiden op beoordeling en/of migratie van on-premises Vm's vMware naar Azure met [Azure Migrate.](migrate-services-overview.md)



Deze zelfstudie is de eerste in een reeks die u laat zien hoe u VMware VM's beoordelen en migreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid Azure voor om met Azure Migrate te werken.
> * VMware voorbereiden op VM-beoordeling met het Azure Migrate:Server Assessment-hulpprogramma.
> * VMware voorbereiden op VM-migratie met het hulpprogramma Azure Migreren:Servermigratie. 

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Ze zijn handig als je leert hoe je een implementatie instelt, en als een snelle proof-of-concept. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

U hebt deze machtigingen nodig voor deze taken in Azure, voordat u VMware VM's beoordelen of migreren.

**Taak** | **Details** 
--- | --- 
**Een Azure-migratieproject maken** | Uw Azure-account heeft inzender- of eigenaarmachtigingen nodig om een project te maken. 
**Resourceproviders registreren** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-toestel om VMware VM's te detecteren en te beoordelen en deze te migreren naar Azure met Azure Migrate:Server Assessment.<br/><br/> Tijdens de registratie van apparaten worden resourceproviders geregistreerd met het abonnement dat in het toestel is gekozen. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resourceproviders wilt registreren, hebt u een rol inzender of eigenaar bij het abonnement nodig.
**Azure AD-apps maken** | Bij de registratie van het toestel maakt Azure Migrate Azure Active Directory (Azure AD)-apps. <br/><br/> - De eerste app wordt gebruikt voor communicatie tussen de agents die op het toestel worden uitgevoerd en hun respectieve services die op Azure worden uitgevoerd.<br/><br/> - De tweede app wordt uitsluitend gebruikt om toegang te krijgen tot KeyVault die is gemaakt in het abonnement van de gebruiker voor agentloze VMware VM-migratie. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig om Azure AD-apps te maken (beschikbaar in de rol Toepassingsontwikkelaar).
**Een sleutelkluis maken** | Als u Vm's met VMware wilt migreren met behulp van agentloze migratie, maakt Azure Migrate een Key Vault om toegangssleutels tot het replicatieopslagaccount in uw abonnement te beheren.<br/><br/> Als u de kluis wilt maken, hebt u machtigingen voor toewijzing van rollen nodig voor de resourcegroep waarin het Azure Migrate-project zich bevindt.




### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het toestel te registreren

Als u het toestel wilt registreren, wijst u machtigingen toe voor Azure Migrate om de Azure AD-apps te maken tijdens de registratie van apparaten. De machtigingen kunnen worden toegewezen met behulp van een van de volgende methoden:

- **Machtigingen verlenen**: een tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de tenant om Azure AD-apps te maken en te registreren.
- **Rol toepassingsontwikkelaar toewijzen:** een tenant/globale beheerder kan de rol Toepassingsontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De apps hebben geen andere toegangsmachtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw toestel registreert. U de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Accountmachtigingen verlenen

Als u wilt dat tenant/globale beheerder machtigingen verleent, doet u dit als volgt:

1. In Azure AD moet de tenant/globale beheerder naar > **gebruikersinstellingen**van Azure Active > **Directory-gebruikers**navigeren. **Azure Active Directory**
2. De beheerder moet **app-registraties** instellen op **Ja**. Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Rol Toepassingsontwikkelaar toewijzen

De tenant/globale beheerder kan ook de rol Toepassingsontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) over het toewijzen van een rol.

### <a name="assign-permissions-to-create-a-key-vault"></a>Machtigingen toewijzen om een sleutelkluis te maken

Als u Azure Migrate wilt inschakelen om een sleutelkluis te maken, wijst u als volgt machtigingen toe:

1. Selecteer **Access control (IAM) in**de brongroep in de Azure-portal .
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.

    - Om serverbeoordeling uit te voeren, zijn de machtigingen **voor bijdragen** voldoende.
    - Als u agentloze servermigratie wilt uitvoeren, moet u machtigingen voor **eigenaar** (of **inzender** en **gebruikerstoegangsbeheerder)** hebben.

3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de brongroep.



## <a name="prepare-for-vmware-vm-assessment"></a>Voorbereiden op VMware VM-beoordeling

Om u voor te bereiden op VMware VM-beoordeling, moet u:

- **VMware-instellingen verifiëren**. Zorg ervoor dat de vCenterserver en VM's die u wilt migreren, voldoen aan de vereisten.
- **Een account instellen voor beoordeling**. Azure Migrate gebruikt dit account om toegang te krijgen tot de vCenterServer om VM's te detecteren voor beoordeling.
- **Controleer de toestelvereisten**. Controleer de implementatievereisten voor het Azure Migrate-toestel voordat u het implementeert.

### <a name="verify-vmware-settings"></a>VMware-instellingen verifiëren

1. [Controleren](migrate-support-matrix-vmware.md#vmware-requirements) VMware-serververeisten voor beoordeling.
2. [Zorg ervoor](migrate-support-matrix-vmware.md#port-access) dat de poorten die u nodig hebt, zijn geopend op vCenter Server.
3. Controleer op vCenter Server of uw account machtigingen heeft om een VM te maken met een OVA-bestand. U implementeert het Azure Migrate-toestel als VMware-vm met behulp van een OVA-bestand.


### <a name="set-up-an-account-for-assessment"></a>Een account instellen voor beoordeling

Azure Migrate moet toegang krijgen tot de vCenterServer om VM's te detecteren voor beoordeling en agentless migratie.

- Als u van plan bent toepassingen te ontdekken of afhankelijkheid op een agentloze manier te visualiseren, maakt u een vCenter Server-account met alleen-lezen toegang, samen met bevoegdheden die zijn ingeschakeld voor**gastbewerkingen voor** **virtuele machines.** > 

  ![vCenter Server-accountrechten](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Als u niet van plan bent om toepassingsdetectie en agentless afhankelijkheidsvisualisatie uit te brengen, stelt u een alleen-lezen account in voor de vCenterServer.

### <a name="verify-appliance-settings-for-assessment"></a>Toestelinstellingen controleren voor beoordeling

Bereid u voor voordat u het Azure Migrate-toestel en de beginbeoordeling in de volgende zelfstudie instelt, zich voorbereiden op de implementatie van apparaten.

1. [Verifiëren](migrate-appliance.md#appliance---vmware) Azure Migrate toestelvereisten.
2. Controleer de Azure URL's die het toestel nodig heeft om toegang te krijgen in de [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
3. [Bekijk de gegevens](migrate-appliance.md#collected-data---vmware) die het toestel verzamelt tijdens detectie en beoordeling.
4. [Opmerking poorttoegangsvereisten](migrate-support-matrix-vmware.md#port-access) voor het toestel.




## <a name="prepare-for-agentless-vmware-migration"></a>Voorbereiden op agentloze VMware-migratie

Bekijk de vereisten voor [agentloze migratie](server-migrate-overview.md) van Vm's zonder software.

1. [Beoordeling](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware-serververeisten.
2. [Controleer de machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) die Azure Migrate nodig heeft om toegang te krijgen tot de vCenter Server.
3. [Beoordeling](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) VMware VM's vereisten.
4. [Controleer](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) de vereisten voor Azure Migrate appliance.
5. Let op de URL-toegang die vereist is voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
6. [Poorttoegangsvereisten](migrate-support-matrix-vmware-migration.md#agentless-ports) bekijken.

## <a name="prepare-for-agent-based-vmware-migration"></a>Voorbereiden op vmware-migratie op basis van agenten

Bekijk de vereisten voor [agentmatige migratie](server-migrate-overview.md) van VMware VM's.

1. [Beoordeling](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware-serververeisten.
2. [De machtigingen controleren](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate moet toegang krijgen tot de vCenterServer.
2. [Beoordeling](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VMware VM's vereisten, waaronder de installatie van de Mobility service op elke VM die u wilt migreren.
3. Op agentgebaseerde migratie wordt gebruikt een replicatietoestel:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatievereisten voor het replicatietoestel.
    - [Bekijk de opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het toestel.
    - Let op de URL-toegang die vereist is voor [openbare](migrate-replication-appliance.md#url-access) en [overheidsclouds.](migrate-replication-appliance.md#azure-government-url-access)
    - Controleer de vereisten [voor poorttoegang](migrate-replication-appliance.md#port-access) voor het replicatietoestel.
    
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Azure-machtigingen instellen.
> * VMware voorbereid op beoordeling en migratie.


Ga verder naar de tweede zelfstudie om een Azure Migrate-project in te stellen en VMware VM's te beoordelen op migratie naar Azure.

> [!div class="nextstepaction"]
> [VMware VM's beoordelen](./tutorial-assess-vmware.md)
