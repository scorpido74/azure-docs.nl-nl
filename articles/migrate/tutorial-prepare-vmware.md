---
title: VMware-Vm's voorbereiden voor evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van de evaluatie/migratie van VMware-Vm's met Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677287"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware-VM's voorbereiden op evaluatie en migratie naar Azure

Dit artikel helpt u bij het voorbereiden van de evaluatie en/of migratie van on-premises virtuele VMware-machines naar Azure met behulp van [Azure migrate](migrate-services-overview.md).



Deze zelf studie is de eerste in een serie die laat zien hoe u virtuele VMware-machines kunt beoordelen en migreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid Azure voor op het werken met Azure Migrate.
> * VMware voor VM-evaluatie voorbereiden met het Azure Migrate: Server assessment tool.
> * Bereid VMware voor VM-migratie voor met de Azure Migrate: hulp programma voor server migratie. 

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Ze zijn handig als u informatie wilt over het instellen van een implementatie en een snelle proef afdruk van een concept. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

U hebt deze machtigingen nodig voor deze taken in azure, voordat u virtuele VMware-machines kunt beoordelen of migreren.

**Taak** | **Details** 
--- | --- 
**Een Azure Migrate-project maken** | Uw Azure-account heeft Inzender of eigenaars machtigingen nodig om een project te maken. 
**Resource providers registreren** | Azure Migrate maakt gebruik van een licht gewicht Azure Migrate apparaat om virtuele VMware-machines te detecteren en te beoordelen en om ze te migreren naar Azure met Azure Migrate: Server evaluatie.<br/><br/> Tijdens de registratie van het apparaat worden resource providers geregistreerd bij het abonnement dat is gekozen in het apparaat. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resource providers wilt registreren, hebt u een rol voor Inzender of eigenaar nodig voor het abonnement.
**Azure AD-apps maken** | Wanneer u het apparaat registreert, maakt Azure Migrate Azure Active Directory (Azure AD)-apps. <br/><br/> -De eerste app wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd, en hun respectieve services die worden uitgevoerd op Azure.<br/><br/> -De tweede app wordt alleen gebruikt voor toegang tot de sleutel kluis die is gemaakt in het abonnement van de gebruiker voor de migratie van agentloze VMware-VM'S. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig voor het maken van Azure AD-apps (beschikbaar in de ontwikkelaar van de toepassing).
**Een sleutelkluis maken** | Als u virtuele VMware-machines wilt migreren met agentloze migratie, maakt Azure Migrate een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement.<br/><br/> Als u de kluis wilt maken, moet u machtigingen voor roltoewijzing hebben voor de resource groep waarin het Azure Migrate-project zich bevindt.




### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)**.
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

Als u het apparaat wilt registreren, wijst u machtigingen toe voor Azure Migrate om de Azure AD-apps te maken tijdens de registratie van het apparaat. De machtigingen kunnen worden toegewezen via een van de volgende methoden:

- **Machtigingen verlenen**: een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant, om Azure AD-apps te maken en te registreren.
- **Ontwikkelaars functie voor toepassingen toewijzen**: een Tenant/globale beheerder kan de ontwikkelaar van de toepassing (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

Als u wilt dat Tenant/globale beheerder machtigingen verleent, doet u dit als volgt:

1. In azure AD moet de Tenant/globale beheerder navigeren naar **Azure Active Directory** > **gebruikers** > **instellingen**.
2. De beheerder moet **app-registraties** instellen op **Ja**. Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen

De Tenant/globale beheerder kan ook de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) over het toewijzen van een rol.

### <a name="assign-permissions-to-create-a-key-vault"></a>Machtigingen toewijzen voor het maken van een Key Vault

Als u wilt instellen dat Azure Migrate een Key Vault maakt, moet u machtigingen als volgt toewijzen:

1. Selecteer in de resource groep in de Azure Portal **toegangs beheer (IAM)**.
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.

    - Voor het uitvoeren van server evaluatie zijn **Inzender** machtigingen voldoende.
    - Als u migratie zonder agent wilt uitvoeren, moet u de machtigingen **eigenaar** (of **Inzender** en **gebruikers toegang beheerder**) hebben.

3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de resource groep.



## <a name="prepare-for-vmware-vm-assessment"></a>Voor bereiding voor VMware VM-evaluatie

Voor de voor bereiding op de VMware VM-evaluatie moet u het volgende doen:

- **Controleer de VMware-instellingen**. Zorg ervoor dat de vCenter Server en Vm's die u wilt migreren, voldoen aan de vereisten.
- **Stel een account in voor evaluatie**. Azure Migrate gebruikt dit account voor toegang tot de vCenter Server om Vm's te ontdekken voor evaluatie.
- **Controleer de vereisten**van het apparaat. Controleer de implementatie vereisten voor het Azure Migrate apparaat voordat u het implementeert.

### <a name="verify-vmware-settings"></a>VMware-instellingen verifiëren

1. [Controleren](migrate-support-matrix-vmware.md#vmware-requirements) Vereisten voor de VMware-Server voor evaluatie.
2. [Zorg ervoor](migrate-support-matrix-vmware.md#port-access) dat de poorten die u nodig hebt, zijn geopend op vCenter Server.
3. Zorg er op vCenter Server voor dat uw account machtigingen heeft voor het maken van een virtuele machine met behulp van een bestand met eicellen. U implementeert het Azure Migrate-apparaat als een VMware-VM met behulp van een bestand met eicellen.


### <a name="set-up-an-account-for-assessment"></a>Een account instellen voor evaluatie

Azure Migrate moet toegang hebben tot de vCenter Server om Vm's te ontdekken voor de evaluatie en migratie zonder agent.

- Als u van plan bent toepassingen te detecteren of afhankelijkheden te visualiseren, moet u een vCenter Server account met alleen-lezen toegang maken naast de bevoegdheden die zijn ingeschakeld voor**gast bewerkingen**voor **virtuele machines** > .

  ![machtigingen voor vCenter Server-account](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Als u niet van plan bent om toepassings detectie en de visualisatie van de afhankelijkheid van agents uit te voeren, stelt u een alleen-lezen-account in voor de vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>De instellingen van het toestel controleren op evaluatie

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Verifiëren](migrate-appliance.md#appliance---vmware) Vereisten van Azure Migrate apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
3. [Bekijk gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#port-access) de toegangs vereisten voor poorten voor het apparaat.




## <a name="prepare-for-agentless-vmware-migration"></a>Voorbereiden van VMware-migratie zonder agent

Bekijk de vereisten voor de migratie van virtuele VMware-machines in [agents](server-migrate-overview.md) .

1. [Controleren](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Vereisten voor VMware-Server.
2. [Controleer de machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) die Azure migrate nodig hebt om toegang te krijgen tot de vCenter Server.
3. [Controleren](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Vereisten voor VMware-Vm's.
4. [Bekijk](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) de vereisten voor het Azure migrate apparaat.
5. Noteer de URL-toegang die vereist is voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
6. Controleer de toegangs vereisten voor de [poort](migrate-support-matrix-vmware-migration.md#agentless-ports) .

## <a name="prepare-for-agent-based-vmware-migration"></a>Voorbereiden voor VMware-migratie op basis van agents

Bekijk de vereisten voor de migratie van virtuele VMware [-machines op basis](server-migrate-overview.md) van een agent.

1. [Controleren](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Vereisten voor VMware-Server.
2. [De machtigingen controleren](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate moet toegang hebben tot de vCenter Server.
2. [Controleren](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Vereisten voor VMware-Vm's, inclusief de installatie van de Mobility-service op elke virtuele machine die u wilt migreren.
3. Voor migratie op basis van een agent wordt een replicatie-apparaat gebruikt:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatie vereisten voor het replicatie apparaat.
    - [Bekijk de opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
    - Noteer de URL-toegang die vereist is voor [open bare](migrate-replication-appliance.md#url-access) en [overheids](migrate-replication-appliance.md#azure-government-url-access) Clouds.
    - Controleer de toegangs vereisten voor de [poort](migrate-replication-appliance.md#port-access) voor het replicatie apparaat.
    
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel Azure-machtigingen in.
> * VMware is voor bereid voor evaluatie en migratie.


Ga door naar de tweede zelf studie voor het instellen van een Azure Migrate project en evalueer VMware-Vm's voor migratie naar Azure.

> [!div class="nextstepaction"]
> [Virtuele VMware-machines beoordelen](./tutorial-assess-vmware.md)
