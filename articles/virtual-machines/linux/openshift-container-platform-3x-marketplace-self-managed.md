---
title: OpenShift Container Platform 3.11 Zelfbeheerde Marketplace-aanbieding implementeren in Azure
description: Implementeer openshift containerplatform 3.11 Zelfbeheerde Marketplace-aanbieding in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 235efd746562ea4bd52b9cb57da0d8165d60de02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561317"
---
# <a name="configure-prerequisites"></a>Vereisten configureren

Voordat u de Marketplace-aanbieding gebruikt om een zelfbeheerd OpenShift Container Platform 3.11-cluster in Azure te implementeren, moeten een aantal vereisten worden geconfigureerd.  Lees het artikel [met de vereisten van OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) voor instructies voor het maken van een ssh-sleutel (zonder wachtwoordzin), Azure-sleutelkluis, sleutelkluisgeheim en een serviceprincipal.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implementeren met behulp van de Marketplace-aanbieding

De eenvoudigste manier om een zelfbeheerd OpenShift Container Platform 3.11-cluster in Azure te implementeren, is door de [Azure Marketplace-aanbieding](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)te gebruiken.

Deze optie is de eenvoudigste, maar het heeft ook beperkte aanpassingsmogelijkheden. De Marketplace-aanbieding implementeert OpenShift Container Platform 3.11.82 en bevat de volgende configuratieopties:

- **Hoofdknooppunten:** Drie (3) masterknooppunten met configureerbaar instantietype.
- **Infra-knooppunten**: Drie (3) Infra-knooppunten met configureerbaar instantietype.
- **Knooppunten**: Het aantal knooppunten (tussen 1 en 9) en het instantietype zijn configureerbaar.
- **Schijftype:** beheerde schijven worden gebruikt.
- **Netwerken:** Ondersteuning voor nieuw of bestaand netwerk- en aangepaste CIDR-reeks.
- **CNS**: CNS kan worden ingeschakeld.
- **Statistieken**: Hawkular Metrics kunnen worden ingeschakeld.
- **Logging**: EFK Logging kan worden ingeschakeld.
- **Azure Cloud Provider**: Standaard ingeschakeld, kan worden uitgeschakeld.

Klik linksboven in de Azure-portal op **Een resource maken,** voer 'openshift containerplatform' in het zoekvak in en druk op Enter.

   ![Nieuwe resourcezoeken](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

De resultatenpagina wordt geopend met **Red Hat OpenShift Container Platform 3.11 Self-Managed** in de lijst. 

   ![Nieuw zoekresultaat voor resources](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klik op de aanbieding om de details van de aanbieding te bekijken. Als u deze aanbieding wilt implementeren, klikt u op **Maken**. De gebruikersinterface om de benodigde parameters in te voeren, wordt weergegeven. Het eerste scherm **Basics** is het Basics-blad.

   ![Titelpagina aanbieden](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Basics**

Als u hulp wilt krijgen bij een van de invoerparameters, zweeft u over de ***i*** naast de parameternaam.

Voer waarden in voor de invoerparameters en klik op **OK**.

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Gebruikersnaam VM-beheerder | De beheerdergebruiker die op alle VM-exemplaren moet worden gemaakt |
| SSH-openbare sleutel voor beheerdersgebruiker | SSH-openbare sleutel die wordt gebruikt om in te loggen op VM - mag geen wachtwoordzin hebben |
| Abonnement | Azure-abonnement om cluster te implementeren |
| Resourcegroep | Een nieuwe resourcegroep maken of een bestaande lege resourcegroep selecteren voor clusterbronnen |
| Locatie | Azure-regio om cluster te implementeren in |

   ![Bied basics blade](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastructuurinstellingen**

Voer waarden in voor de invoerparameters en klik op **OK**.

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Voorvoegsel OCP-clusternaam | Clustervoorvoegsel dat wordt gebruikt om hostnamen voor alle knooppunten te configureren. Tussen 1 en 20 tekens |
| Grootte van hoofdknooppunt | Accepteer de standaardvm-grootte of klik op **Grootte wijzigen** om een ander VM-formaat te selecteren.  De juiste VM-grootte selecteren voor uw werkbelasting |
| Grootte van het infrastructuurknooppunt | Accepteer de standaardvm-grootte of klik op **Grootte wijzigen** om een ander VM-formaat te selecteren.  De juiste VM-grootte selecteren voor uw werkbelasting |
| Aantal toepassingsknooppunten | Accepteer de standaardvm-grootte of klik op **Grootte wijzigen** om een ander VM-formaat te selecteren.  De juiste VM-grootte selecteren voor uw werkbelasting |
| Grootte van toepassingsknooppunt | Accepteer de standaardvm-grootte of klik op **Grootte wijzigen** om een ander VM-formaat te selecteren.  De juiste VM-grootte selecteren voor uw werkbelasting |
| Bastion Host Grootte | Accepteer de standaardvm-grootte of klik op **Grootte wijzigen** om een ander VM-formaat te selecteren.  De juiste VM-grootte selecteren voor uw werkbelasting |
| Nieuw of bestaand virtueel netwerk | Een nieuwe vNet (Standaard) maken of een bestaande vNet gebruiken |
| Kies standaard CIDR-instellingen of pas IP-bereik (CIDR) aan | Accepteer standaard CIDR-bereiken of Selecteer **Aangepast IP-bereik** en voer aangepaste CIDR-gegevens in.  Standaardinstellingen maken vNet met CIDR van 10.0.0.0/14, hoofdsubnet met 10.1.0.0/16, infrasubnet met 10.2.0.0/16 en reken- en cns-subnet met 10.3.0.0/16 |
| Naam sleutelkluisbronbrongroep | De naam van de resourcegroep die de sleutelkluis bevat |
| Naam sleutelkluis | De naam van de Key Vault die het geheim met de ssh private key bevat.  Alleen alfanumerieke tekens en streepjes zijn toegestaan en liggen tussen de 3 en 24 tekens |
| Geheime naam | De naam van het geheim dat de ssh privésleutel bevat.  Alleen alfanumerieke tekens en streepjes zijn toegestaan |

   ![Bieden infrastructuur blad](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Grootte wijzigen**

Als u een ander VM-formaat wilt selecteren, klikt u op ***Grootte wijzigen***.  Het VM-selectievenster wordt geopend.  Selecteer de gewenste VM-grootte en klik op **Selecteren**.

   ![VM-grootte selecteren](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Bestaand virtueel netwerk**

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Bestaande naam van virtueel netwerk | Naam van de bestaande vNet |
| Subnetnaam voor hoofdknooppunten | Naam van bestaand subnet voor hoofdknooppunten.  Moet ten minste 16 IP-adressen bevatten en RFC 1918 volgen |
| Subnetnaam voor infraknooppunten | Naam van bestaand subnet voor infraknooppunten.  Moet ten minste 32 IP-adressen bevatten en RFC 1918 volgen |
| Subnetnaam voor compute- en cns-knooppunten | Naam van bestaand subnet voor compute- en cns-knooppunten.  Moet ten minste 32 IP-adressen bevatten en RFC 1918 volgen |
| Resourcegroep voor het bestaande virtuele netwerk | Naam van de resourcegroep die de bestaande vNet bevat |

   ![Bieden infrastructuur bestaande vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Aangepast IP-bereik**

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Adresbereik voor het virtuele netwerk | Aangepaste CIDR voor de vNet |
| Adresbereik voor het subnet met de hoofdknooppunten | Aangepaste CIDR voor master subnet |
| Adresbereik voor het subnet met de infrastructuurknooppunten | Aangepaste CIDR voor subnet infrastructuur |
| Adresbereik voor subnet met de compute- en cns-knooppunten | Aangepaste CIDR voor de compute- en cns-knooppunten |

   ![Aangepaste IP-reeks infrastructuur aanbieden](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Waarden voor de invoerparameters invoeren en op **OK** klikken

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Gebruikerswachtwoord voor OpenShift-beheerders | Wachtwoord voor de eerste OpenShift-gebruiker.  Deze gebruiker zal ook de clusterbeheerder zijn |
| Het gebruikerswachtwoord van OpenShift-beheerders bevestigen | Het gebruikerswachtwoord van de OpenShift-beheerder opnieuw typen |
| Gebruikersnaam van Red Hat Subscription Manager | Gebruikersnaam om toegang te krijgen tot uw Red Hat-abonnement of organisatie-id.  Deze referentie wordt gebruikt om de RHEL-instantie te registreren bij uw abonnement en wordt niet opgeslagen door Microsoft of Red Hat |
| Gebruikerswachtwoord van Red Hat Subscription Manager | Wachtwoord om toegang te krijgen tot uw Red Hat-abonnement of activeringssleutel.  Deze referentie wordt gebruikt om de RHEL-instantie te registreren bij uw abonnement en wordt niet opgeslagen door Microsoft of Red Hat |
| Red Hat Subscription Manager OpenShift Pool ID | Groep-ID met openshiftcontainerplatformrechten. Zorg ervoor dat u voldoende rechten hebt op OpenShift Container Platform voor de installatie van het cluster |
| Red Hat Subscription Manager OpenShift Pool ID voor Broker / Master Nodes | Groep-ID met OpenShift Container Platform-rechten voor Broker / Master-knooppunten. Zorg ervoor dat u voldoende rechten hebt op OpenShift Container Platform voor de installatie van het cluster. Als u geen broker/ master pool-id gebruikt, voert u de pool-id voor toepassingsknooppunten in |
| Azure Cloud Provider configureren | Configureer OpenShift om Azure Cloud Provider te gebruiken. Noodzakelijk als u Azure-schijf moet koppelen voor permanente volumes.  Standaard is Ja |
| GUID azure AD-serviceprincipalclient-id | Azure AD Service Principal Client ID GUID - ook bekend als AppID. Alleen nodig als Azure Cloud Provider configureren is ingesteld op **Ja** |
| Azure AD Service Principal Client ID Secret | Azure AD Service Principal Client ID Secret. Alleen nodig als Azure Cloud Provider configureren is ingesteld op **Ja** |
 
   ![OpenShift-blad aanbieden](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Aanvullende instellingen**

Met het blade Extra Settings u CNS configureren voor glusterfs-opslag, logboekregistratie, metrische gegevens en het subdomein Router.  De standaard wordt geen van deze opties geïnstalleerd en gebruikt nip.io als het subdomein van de router voor testdoeleinden. Als u CNS inschakelt, worden drie extra compute nodes geïnstalleerd met drie extra aangesloten schijven die glusterfs-pods hosten.  

Waarden voor de invoerparameters invoeren en op **OK** klikken

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| Containernative storage configureren (CNS) | Installeert CNS in het OpenShift-cluster en schakelt het in als opslag. Wordt standaard weergegeven als Azure Provider is uitgeschakeld |
| Clusterlogboekregistratie configureren | Installeert EFK-logboekfunctionaliteit in het cluster.  Infra-knooppunten op de juiste grootte om EFK-pods te hosten |
| Statistieken voor het cluster configureren | Installeert Hawkular-statistieken in het OpenShift-cluster.  Grootte infraknooppunten op de juiste manier om Hawkular metrics pods te hosten |
| Standaarddomein routersub | Selecteer nipio voor het testen of op maat om uw eigen subdomein in te voeren voor productie |
 
   ![Extra blad aanbieden](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Extra instellingen - Extra parameters**

| Invoerparameter | Parameterbeschrijving |
|-----------------------|-----------------|
| (CNS) Knooppuntgrootte | De standaardknooppuntgrootte accepteren of **de grootte wijzigen** selecteren om een nieuwe virtuele machinegrootte te selecteren |
| Voer uw aangepaste subdomein in | Het aangepaste routeringsdomein dat moet worden gebruikt voor het blootstellen van toepassingen via de router op het OpenShift-cluster.  Zorg ervoor dat u de juiste DNS-vermelding met jokertekens maakt] |
 
   ![Extra cns Installeren aanbieden](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Samenvatting**

Validatie vindt in dit stadium plaats om te controleren of het kernquotum voldoende is om het totale aantal vm's dat voor het cluster is geselecteerd, te implementeren.  Bekijk alle parameters die zijn ingevoerd.  Als de ingangen acceptabel zijn, klikt u op **OK** om door te gaan.

   ![Overzichtsblad aanbieden](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Kopen**

Bevestig contactgegevens op de pagina Kopen en klik op **Kopen** om de gebruiksvoorwaarden te accepteren en de implementatie van het cluster OpenShift Container Platform te starten.

   ![Aankoopblad aanbieden](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Verbinding maken met het OpenShift-cluster

Wanneer de implementatie is voltooid, haalt u de verbinding op uit het uitvoergedeelte van de implementatie. Maak verbinding met de OpenShift-console met uw browser met de URL van de **OpenShift-console**. u ook SSH naar de Bastion host. Hieronder volgt een voorbeeld waarbij de gebruikersnaam van de beheerder clusterbeheerder is en de ip-IP DNS-fqdn van het bastion bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht Verwijderen van de [AZ-groep](/cli/azure/group) om de brongroep, het OpenShift-cluster en alle gerelateerde bronnen te verwijderen wanneer deze niet meer nodig zijn.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na implementatie](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-implementatie in Azure oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met OpenShift Container Platform](https://docs.openshift.com)
- 