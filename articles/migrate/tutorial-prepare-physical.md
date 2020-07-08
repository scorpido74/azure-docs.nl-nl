---
title: Fysieke servers voorbereiden voor evaluatie/migratie met Azure Migrate
description: Leer hoe u voorbereidingen treft voor evaluatie/migratie van fysieke servers met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 31db91b512a4532cca144dc012282ea58a87514f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113248"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Voorbereiden op evaluatie en migratie van fysieke servers naar Azure

In dit artikel wordt beschreven hoe u voorbereidingen treft voor evaluatie van on-premises fysieke servers met [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](./migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s). 

Deze zelfstudie is de eerste in een serie die laat zien hoe u fysieke servers evalueert met Azure Migrate. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en -resources om met Azure Migrate te werken.
> * Bereid on-premises fysieke servers voor op serverevaluatie.


> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de procedures voor de evaluatie van fysieke servers voor gedetailleerde instructies.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure-for-server-assessment"></a>Azure voorbereiden op serverevaluatie

Stel Azure in om te werken met Azure Migrate. 

**Taak** | **Details** 
--- | --- 
**Maak een Azure Migrate-project** | Uw Azure-account heeft Inzender- of Eigenaarsmachtigingen nodig om een project te maken. 
**Resourceproviders registreren (alleen voor evaluatie)** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-apparaat om machines te detecteren en te evalueren met Azure Migrate:Server Assessment.<br/><br/> Tijdens de registratie van het apparaat worden resourceproviders geregistreerd bij het abonnement dat is gekozen in het apparaat. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resourceproviders wilt registreren, hebt u de rol Inzender of Eigenaar nodig voor het abonnement.
**Een Azure AD-app maken (alleen voor evaluatie)** | Bij het registreren van het apparaat maakt Azure Migrate een Azure AD-app (Azure Active Directory) die wordt gebruikt voor communicatie tussen de agents die op het apparaat worden uitgevoerd, met de services die worden uitgevoerd op Azure. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig voor het maken van Azure AD-apps (beschikbaar in de Toepassingsontwikkelaar).


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
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Rol toepassingsontwikkelaar toewijzen

Een tenant/globale beheerder kan de rol van Toepassingsontwikkelaar toewijzen aan het account. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="prepare-azure-for-physical-server-migration"></a>Azure voorbereiden op migratie van een fysieke server

Bereid Azure voor op het migreren van fysieke servers, met behulp van Server Migration.

**Taak** | **Details**
--- | ---
**Maak een Azure Migrate-project** | Uw Azure-account heeft Inzender- of Eigenaarsmachtigingen nodig om een project te maken.
**Machtigingen verifiëren voor uw Azure-account** | U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.
**Een Azure-netwerk maken** | Stel in Azure een netwerk in.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.


### <a name="assign-azure-account-permissions"></a>Machtigingen voor Azure-accounts toewijzen

Wijs de rol van Inzender voor virtuele machines toe aan het Azure-account. Deze biedt machtigingen voor het volgende:
  - Het maken van een VM in de geselecteerde resourcegroep.
  - Het maken van een VM in het geselecteerde virtuele netwerk.
  - Schrijf naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

[Stel](../virtual-network/manage-virtual-network.md#create-a-virtual-network) een virtueel Azure-netwerk (VNet) in. Wanneer u repliceert naar Azure, worden virtuele Azure-machines gemaakt en gekoppeld aan het Azure VNet dat u opgeeft wanneer u de migratie instelt.


## <a name="prepare-for-physical-server-assessment"></a>Voorbereiden op evaluatie van fysieke servers

Voor de voorbereiding op de evaluatie van fysieke servers moet u de fysieke serverinstellingen verifiëren en de instellingen voor de implementatie van apparaten verifiëren:

### <a name="verify-physical-server-settings"></a>De instellingen van fysieke servers verifiëren

1. Verifieer [de vereisten voor fysieke servers](migrate-support-matrix-physical.md#physical-server-requirements) voor serverevaluatie.
2. Controleer of de [vereiste poorten](migrate-support-matrix-physical.md#port-access) open staan op fysieke servers.


### <a name="verify-appliance-settings"></a>De apparaatinstellingen verifiëren

Voordat u het Azure Migrate-apparaat instelt en de evaluatie begint in de volgende zelfstudie, moet u de implementatie van het apparaat voorbereiden.

1. [Verifieer](migrate-appliance.md#appliance---physical) de apparaatvereisten voor fysieke servers.
2. Bekijk de Azure-URL's die het apparaat nodig heeft voor toegang tot de [openbare](migrate-appliance.md#public-cloud-urls) clouds en [overheidsclouds](migrate-appliance.md#government-cloud-urls).
3. [Beoordeel](migrate-appliance.md#collected-data---vmware) de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Houd er rekening mee](migrate-support-matrix-physical.md#port-access) dat voor poorttoegang evaluatie van fysieke servers vereist is.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Een account voor de detectie van fysieke servers instellen

Azure Migrate heeft machtigingen nodig om on-premises servers te detecteren.

- **Windows:** U moet een domeinbeheerder of lokale beheerder zijn op alle Windows-servers die u wilt detecteren. Het gebruikersaccount moet worden toegevoegd aan deze groepen: Gebruikers van extern beheer, prestatiemetergebruikers en gebruikers van prestatielogboeken.
- **Linux:** U hebt een hoofdaccount nodig op de Linux-servers die u wilt detecteren.

## <a name="prepare-for-physical-server-migration"></a>Voorbereiden op de migratie van fysieke servers

Controleer de vereisten voor migratie van fysieke servers.

> [!NOTE]
> Wanneer u fysieke machines gaat migreren, wordt voor Azure Migrate:Server Migration dezelfde replicatiearchitectuur gebruikt als voor op agents gebaseerd herstel na noodgevallen in de Azure Site Recovery-service, en een aantal onderdelen deelt dezelfde codebasis. Bepaalde inhoud wordt mogelijk gekoppeld aan Site Recovery-documentatie.

1. [Controleer](migrate-support-matrix-physical-migration.md#physical-server-requirements) de vereisten van de fysieke server voor de migratie.
2. Voor Azure Migrate:Server Migration wordt een replicatieserver gebruikt voor de migratie van de fysieke server:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatievereisten voor het replicatieapparaat en de [opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
    - Controleer de vereiste [Azure-URL's](migrate-appliance.md#url-access) voor het replicatieapparaat voor toegang tot openbare clouds en overheidsclouds.
    - Controleer de toegangsvereisten [port] (migrate-replication-appliance.md#port-access) voor het replicatieapparaat.
3. U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure migreert.
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de virtuele machine migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
    - Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel de machtigingen voor het Azure-account in.
> * Fysieke servers voorbereid op evaluatie.

Ga door naar de volgende zelfstudie om een Azure Migrate-project te maken en fysieke servers te evalueren voor migratie naar Azure

> [!div class="nextstepaction"]
> [Fysieke servers evalueren](./tutorial-assess-physical.md)
