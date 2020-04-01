---
title: OpenShift Container Platform 3.11 in Azure-vereisten
description: Voorwaarden voor het implementeren van OpenShift Container Platform 3.11 in Azure.
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
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 8767a6ee6218223280ea6219e22540c53d1e89be
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409124"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Algemene voorwaarden voor het implementeren van OpenShift Container Platform 3.11 in Azure

In dit artikel worden veelvoorkomende vereisten beschreven voor het implementeren van OpenShift Container Platform of OKD in Azure.

De installatie van OpenShift maakt gebruik van Ansible playbooks. Ansible gebruikt Secure Shell (SSH) om verbinding te maken met alle clusterhosts om installatiestappen uit te voeren.

Wanneer ansible de SSH-verbinding maakt met de externe hosts, kan het geen wachtwoord invoeren. Om deze reden kan de privésleutel geen wachtwoord (wachtwoordzin) hebben of mislukt de implementatie.

Omdat de virtuele machines (VM's) implementeren via Azure Resource Manager-sjablonen, wordt dezelfde openbare sleutel gebruikt voor toegang tot alle VM's. De bijbehorende privésleutel moet op de VM staan die ook alle playbooks uitvoert. Om deze actie veilig uit te voeren, wordt een Azure-sleutelkluis gebruikt om de privésleutel door te geven aan de VM.

Als er behoefte is aan permanente opslag voor containers, zijn aanhoudende volumes vereist. OpenShift ondersteunt Azure virtual hard disks (VHD's) voor permanente volumes, maar Azure moet eerst worden geconfigureerd als cloudprovider.

In dit model, OpenShift:

- Hiermee maakt u een VHD-object in een Azure-opslagaccount of een beheerde schijf.
- Monteert de VHD op een VM en maakt het volume op.
- Monteert het volume op de pod.

Om deze configuratie te laten werken, heeft OpenShift machtigingen nodig om deze taken in Azure uit te voeren. Hiervoor wordt een serviceprincipal gebruikt. De serviceprincipal is een beveiligingsaccount in Azure Active Directory waaraan machtigingen zijn verleend voor resources.

De serviceprincipal moet toegang hebben tot de opslagaccounts en VM's die deel uitmaken van het cluster. Als alle OpenShift-clusterresources worden geïmplementeerd in één resourcegroep, kan de serviceprincipal machtigingen voor die resourcegroep krijgen.

In deze handleiding wordt beschreven hoe u de artefacten maakt die zijn gekoppeld aan de vereisten.

> [!div class="checklist"]
> * Maak een sleutelkluis om SSH-sleutels voor het OpenShift-cluster te beheren.
> * Maak een serviceprincipal voor gebruik door de Azure Cloud Provider.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 
Meld u aan bij uw Azure-abonnement met de opdracht [AZ-aanmelding](/cli/azure/reference-index) en volg de aanwijzingen op het scherm of klik op **Proberen** om Cloud Shell te gebruiken.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. U moet een speciale resourcegroep gebruiken om de sleutelkluis te hosten. Deze groep staat los van de resourcegroep waarin de OpenShift-clusterbronnen worden geïmplementeerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *keyvaultrg* op de *locatie Eastus geaald:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken
Maak een sleutelkluis om de SSH-toetsen voor het cluster op te slaan met de opdracht [AZ Keyvault create.](/cli/azure/keyvault) De naam van de sleutelkluis moet wereldwijd uniek zijn en moet zijn ingeschakeld voor het implementeren van sjablonen of de implementatie mislukt met de fout 'KeyVaultParameterReferenceSecretRetrieveFailed'.

In het volgende voorbeeld wordt een keyvault met de naam *keyvault* in de *keyvaultrg-brongroep:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Een SSH-sleutel maken 
Er is een SSH-sleutel nodig om de toegang tot het OpenShift-cluster te beveiligen. Maak een SSH-sleutelpaar `ssh-keygen` met behulp van de opdracht (op Linux of macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Uw SSH-sleutelpaar kan geen wachtwoord / wachtwoordzin hebben.

Zie [SSH-sleutels maken op Windows](/azure/virtual-machines/linux/ssh-from-windows)voor meer informatie over SSH-sleutels in Windows. Zorg ervoor dat u de privésleutel exporteert in OpenSSH-formaat.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>De SSH-privésleutel opslaan in Azure Key Vault
De OpenShift-implementatie gebruikt de SSH-sleutel die u hebt gemaakt om de toegang tot de OpenShift-master te beveiligen. Als u de implementatie wilt inschakelen om de SSH-sleutel veilig op te halen, slaat u de sleutel op in Key Vault met behulp van de volgende opdracht:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Een service-principal maken 
OpenShift communiceert met Azure met behulp van een gebruikersnaam en wachtwoord of een serviceprincipal. Een Azure-serviceprincipal is een beveiligingsidentiteit die u gebruiken met apps, services en automatiseringsprogramma's zoals OpenShift. U beheert en definieert de machtigingen voor welke bewerkingen de serviceprincipal in Azure kan uitvoeren. Het is het beste om de machtigingen van de serviceprincipal te scopen voor specifieke resourcegroepen in plaats van het hele abonnement.

Maak een serviceprincipal met [az ad sp create-for-rbac](/cli/azure/ad/sp) en uitvoer de referenties die OpenShift nodig heeft.

In het volgende voorbeeld wordt een serviceprincipal aanmaken en worden machtigingen voor bijdragen aan een resourcegroep met de naam *openshiftrg*toewijst.

Maak eerst de resourcegroep met de naam *openshiftrg:*

```azurecli
az group create -l eastus -n openshiftrg
```

Serviceprincipal maken:

```azurecli
az group show --name openshiftrg --query id
```

Sla de uitvoer van de opdracht en het gebruik op in plaats van $scope in volgende opdracht

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Let op de eigenschap appId en het wachtwoord dat is geretourneerd uit de opdracht:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Zorg ervoor dat u het beveiligde wachtwoord opschrijft, omdat het niet mogelijk zal zijn om dit wachtwoord opnieuw op te halen.

Zie [Een Azure-serviceprincipal maken met Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)voor meer informatie over serviceprincipals.

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Vereisten die alleen van toepassing zijn op de sjabloon ResourceManager

Geheimen moeten worden gemaakt voor de SSH private key **(sshPrivateKey),** Azure AD client secret **(aadClientSecret),** OpenShift admin password **(openshiftPassword)** en Red Hat Subscription Manager password of activation key **(rhsmPasswordOrActivationKey).**  Bovendien, als aangepaste TLS / SSL-certificaten worden gebruikt, moeten zes extra geheimen worden gemaakt - **routingcafile**, **routingcertfile**, **routingkeyfile,** **mastercafile,** **mastercertfile**en **masterkeyfile**.  Deze parameters zullen nader worden toegelicht.

De sjabloon verwijst naar specifieke geheime namen, dus u **moet** de hierboven vermelde vetgedrukte namen gebruiken (hoofdlettergevoelig).

### <a name="custom-certificates"></a>Aangepaste certificaten

Standaard implementeert de sjabloon een OpenShift-cluster met zelfondertekende certificaten voor de OpenShift-webconsole en het routeringsdomein. Als u aangepaste TLS/SSL-certificaten wilt gebruiken, stelt u 'routingCertType' in op 'custom' en 'masterCertType' op 'custom'.  U hebt de bestanden CA, Cert en Key in .pem-formaat nodig voor de certificaten.  Het is mogelijk om aangepaste certificaten te gebruiken voor de ene, maar niet voor de andere.

Je moet deze bestanden opslaan in Key Vault-geheimen.  Gebruik dezelfde Key Vault als die welke wordt gebruikt voor de privésleutel.  In plaats van 6 extra ingangen voor de geheime namen te vereisen, is de sjabloon hardgecodeerd om specifieke geheime namen te gebruiken voor elk van de TLS/SSL-certificaatbestanden.  Sla de certificaatgegevens op met behulp van de gegevens uit de volgende tabel.

| Geheime naam      | Certificaatbestand   |
|------------------|--------------------|
| mastercabestand     | ca-bestand onder master     |
| mastercertbestand   | cert-bestand master   |
| masterkeyfile    | hoofdsleutelbestand    |
| routingcabestand    | CA-bestanden routeren    |
| routingcertfile  | routering CERT-bestand  |
| routingkeyfile   | Sleutelbestand routeren   |

Maak de geheimen met de Azure CLI. Hieronder is een voorbeeld.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel ging over de volgende onderwerpen:
> [!div class="checklist"]
> * Maak een sleutelkluis om SSH-sleutels voor het OpenShift-cluster te beheren.
> * Maak een serviceprincipal voor gebruik door de Azure Cloud Solution Provider.

Implementeer vervolgens een OpenShift-cluster:

- [OpenShift-containerplatform implementeren](./openshift-container-platform-3x.md)
- [OpenShift Container Platform Self-Managed Marketplace-aanbieding implementeren](./openshift-container-platform-3x-marketplace-self-managed.md)
