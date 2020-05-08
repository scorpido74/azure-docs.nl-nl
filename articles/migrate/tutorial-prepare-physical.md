---
title: Fysieke servers voorbereiden voor evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van de evaluatie/migratie van fysieke servers met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b7bde5df943a35bfcf08ace3b454a26dae8c1d89
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901416"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Voor bereiding voor de evaluatie en migratie van fysieke servers naar Azure

In dit artikel wordt beschreven hoe u de evaluatie van on-premises fysieke servers kunt voorbereiden met [Azure migrate](migrate-services-overview.md).

[Azure migrate](migrate-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden. 

Deze zelf studie is de eerste in een serie die laat zien hoe u fysieke servers kunt beoordelen met Azure Migrate. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en-resources om met Azure Migrate te werken.
> * On-premises fysieke servers voorbereiden voor Server evaluatie.


> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Bekijk voor gedetailleerde instructies de evaluatie van de fysieke servers met uitleg.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure-for-server-assessment"></a>Azure voorbereiden voor Server evaluatie

Stel Azure in om met Azure Migrate te werken. 

**Taak** | **Details** 
--- | --- 
**Een Azure Migrate-project maken** | Uw Azure-account heeft Inzender of eigenaars machtigingen nodig om een project te maken. 
**Resource providers registreren (alleen evaluatie)** | Azure Migrate gebruikt een licht gewicht Azure Migrate apparaat om computers te detecteren en te beoordelen met Azure Migrate: Server evaluatie.<br/><br/> Tijdens de registratie van het apparaat worden resource providers geregistreerd bij het abonnement dat is gekozen in het apparaat. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resource providers wilt registreren, hebt u een rol voor Inzender of eigenaar nodig voor het abonnement.
**Een Azure AD-app maken (alleen evaluatie)** | Bij het registreren van het apparaat maakt Azure Migrate een Azure Active Directory Azure AD-app die wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd, met de services die worden uitgevoerd op Azure. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig voor het maken van Azure AD-apps (beschikbaar in de ontwikkelaar van de toepassing).


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


## <a name="prepare-azure-for-physical-server-migration"></a>Azure voorbereiden voor de migratie van fysieke servers

Bereid Azure voor om fysieke servers te migreren met server migratie.

**Taak** | **Details**
--- | ---
**Een Azure Migrate-project maken** | Uw Azure-account heeft Contribute of eigenaars machtigingen nodig om een project te maken.
**Controleer de machtigingen voor uw Azure-account** | Uw Azure-account heeft machtigingen nodig om een virtuele machine te maken en te schrijven naar een Azure Managed disk.
**Een Azure-netwerk maken** | Stel een netwerk in op Azure.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)**.
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


### <a name="assign-azure-account-permissions"></a>Machtigingen voor een Azure-account toewijzen

Wijs de rol Inzender voor virtuele machines toe aan het Azure-account. Dit biedt machtigingen voor het volgende:
  - Het maken van een VM in de geselecteerde resourcegroep.
  - Het maken van een VM in het geselecteerde virtuele netwerk.
  - Schrijven naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

[Stel](../virtual-network/manage-virtual-network.md#create-a-virtual-network) een Azure Virtual Network (VNet) in. Wanneer u naar Azure repliceert, worden er Azure-Vm's gemaakt en gekoppeld aan het Azure-VNet dat u opgeeft wanneer u de migratie hebt ingesteld.


## <a name="prepare-for-physical-server-assessment"></a>Evaluatie van fysieke server voorbereiden

Voor de voor bereiding op de beoordeling van de fysieke server moet u de instellingen voor de fysieke server controleren en instellingen controleren voor de implementatie van het apparaat:

### <a name="verify-physical-server-settings"></a>Controleer de instellingen van de fysieke server

1. Controleer de vereisten voor de [fysieke server](migrate-support-matrix-physical.md#physical-server-requirements) voor Server evaluatie.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-physical.md#port-access) op fysieke servers zijn geopend.


### <a name="verify-appliance-settings"></a>Instellingen voor toestellen controleren

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Controleer](migrate-appliance.md#appliance---physical) de vereisten voor apparaten voor fysieke servers.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.
3. [Controleer](migrate-appliance.md#collected-data---vmware) of het apparaat wordt verzameld tijdens de detectie en evaluatie.
4. Beoordeling van de fysieke server vereisten voor toegang [tot poort.](migrate-support-matrix-physical.md#port-access)


### <a name="set-up-an-account-for-physical-server-discovery"></a>Een account instellen voor detectie van fysieke servers

Azure Migrate heeft machtigingen nodig om on-premises servers te detecteren.

- **Windows:** Stel een lokale gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Het gebruikers account moet worden toegevoegd aan de volgende groepen:-extern beheer gebruikers-prestatie meter gebruikers-prestaties logboek gebruikers
- **Linux:** U hebt een hoofd account nodig op de Linux-servers die u wilt detecteren.

## <a name="prepare-for-physical-server-migration"></a>Voorbereiden van de migratie van de fysieke server

Bekijk de vereisten voor migratie van fysieke servers.

> [!NOTE]
> Bij het migreren van fysieke machines, Azure Migrate: Server migratie maakt gebruik van dezelfde replicatie architectuur als herstel na nood gevallen in de Azure Site Recovery-service en sommige onderdelen delen dezelfde code basis. Sommige inhoud kan worden gekoppeld aan Site Recovery documentatie.

- [Controleer](migrate-support-matrix-physical-migration.md#physical-server-requirements) de vereisten voor de fysieke server voor migratie.
- Azure Migrate: Server migratie maakt gebruik van een replicatie server voor de migratie van fysieke servers:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatie vereisten voor het replicatie apparaat en de [Opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
    - Bekijk de [Azure-url's](migrate-appliance.md#url-access) die vereist zijn voor het replicatie apparaat om toegang te krijgen tot open bare en overheids Clouds.
    - Controleer de toegangs vereisten voor [Port] (migrate-Replication-Appliance. MD # Port-Access) voor het replicatie apparaat.




## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * Voor bereide fysieke servers voor evaluatie.

Ga door naar de volgende zelf studie om een Azure Migrate project te maken en fysieke servers te beoordelen voor migratie naar Azure

> [!div class="nextstepaction"]
> [Fysieke servers evalueren](./tutorial-assess-physical.md)
