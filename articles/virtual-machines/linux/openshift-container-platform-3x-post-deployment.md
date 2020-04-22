---
title: OpenShift Container Platform 3.11 in Azure-post-deploymenttaken
description: Extra taken voor na een OpenShift Container Platform 3.11 cluster is geïmplementeerd.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 8d76588ae9124d34902659cc0149063400b6e766
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759532"
---
# <a name="post-deployment-tasks"></a>Taken na implementatie

Nadat u een OpenShift-cluster hebt geïmplementeerd, u extra items configureren. Dit artikel behandelt:

- Eenmalige aanmelding configureren met Azure Active Directory (Azure AD)
- Azure Monitor-logboeken configureren om OpenShift te controleren
- Statistieken en logboekregistratie configureren
- Open Service Broker voor Azure (OSBA) installeren

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Eenmalige aanmelding configureren met Azure Active Directory

Als u Azure Active Directory wilt gebruiken voor verificatie, moet u eerst een Azure AD-appregistratie maken. Dit proces omvat twee stappen: het maken van de app registratie, en het configureren van machtigingen.

### <a name="create-an-app-registration"></a>Een app-registratie maken

Met deze stappen wordt de Azure CLI gebruikt om de app-registratie te maken en de GUI (portal) om de machtigingen in te stellen. Om de app-registratie te maken, heb je de volgende vijf stukjes informatie nodig:

- Weergavenaam: naam app-registratie (bijvoorbeeld OCPAzureAD)
- Startpagina: URL van de OpenShift-console (bijvoorbeeld `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Id URI: URL van de `https://masterdns343khhde.westus.cloudapp.azure.com/console`OpenShift-console (bijvoorbeeld )
- Url van antwoord: openbare URL en de `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`naam van de app-registratie masteren (bijvoorbeeld )
- Wachtwoord: Veilig wachtwoord (gebruik een sterk wachtwoord)

In het volgende voorbeeld wordt een app-registratie gemaakt met behulp van de voorgaande gegevens:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Als de opdracht succesvol is, krijgt u een JSON-uitvoer die vergelijkbaar is met:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Neem nota van de eigenschap appId die van de opdracht voor een recentere stap wordt teruggekeerd.

In Azure Portal:

1. Selecteer **Azure Active Directory** > **App Registration**.
2. Zoek naar uw app-registratie (bijvoorbeeld OCPAzureAD).
3. Klik in de resultaten op de app-registratie.
4. Selecteer **onder Instellingen**Vereiste **machtigingen**.
5. Selecteer Onder **Vereiste machtigingen**de optie **Toevoegen**.

   ![App-registratie](media/openshift-post-deployment/app-registration.png)

6. Klik op Stap 1: Selecteer API en klik vervolgens op **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory).** Klik onderin op **Selecteren.**

   ![API voor app-registratie selecteren](media/openshift-post-deployment/app-registration-select-api.png)

7. In stap 2: Selecteer Machtigingen, selecteer **Aanmelden en lees gebruikersprofiel** onder **Gedelegeerde machtigingen**en klik vervolgens op **Selecteren**.

   ![Toegang tot app-registratie](media/openshift-post-deployment/app-registration-access.png)

8. Selecteer **Done**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift configureren voor Azure AD-verificatie

Als u OpenShift wilt configureren om Azure AD als verificatieprovider te gebruiken, moet het bestand /etc/origin/master/master-config.yaml op alle hoofdknooppunten worden bewerkt.

Zoek de tenant-id met de volgende opdracht CLI:

```azurecli
az account show
```

Zoek in het yaml-bestand de volgende regels:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

De volgende regels direct na de voorgaande regels invoegen:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Zorg ervoor dat de tekst correct wordt uitgelijnd onder identityProviders. Zoek de tenant-id met de volgende opdracht CLI:```az account show```

Start de OpenShift-hoofdservices opnieuw op alle hoofdknooppunten:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

In de OpenShift-console ziet u nu twee opties voor verificatie: htpasswd_auth en [Appregistratie].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>OpenShift bewaken met Azure Monitor-logboeken

Er zijn drie manieren om de loganalytics-agent toe te voegen aan OpenShift.
- Installeer de Log Analytics-agent voor Linux rechtstreeks op elk OpenShift-knooppunt
- Azure Monitor VM-extensie inschakelen op elk OpenShift-knooppunt
- De Log Analytics-agent installeren als een OpenShift-daemon-set

Lees de volledige [instructies](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) voor meer details.

## <a name="configure-metrics-and-logging"></a>Statistieken en logboekregistratie configureren

Op basis van de branch kunnen de Azure Resource Manager-sjablonen voor OpenShift Container Platform en OKD invoerparameters bevatten voor het inschakelen van metrische gegevens en logboekregistratie als onderdeel van de installatie.

Het OpenShift Container Platform Marketplace-aanbod biedt ook een optie om metrics en logging in te schakelen tijdens de installatie van het cluster.

Als metrics / logging niet is ingeschakeld tijdens de installatie van het cluster, kunnen ze gemakkelijk worden ingeschakeld na het feit.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider in gebruik

SSH naar het bastionknooppunt of eerste hoofdknooppunt (op basis van sjabloon en vertakking in gebruik) met behulp van de referenties die tijdens de implementatie zijn verstrekt. Geef de volgende opdracht uit:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider niet in gebruik

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Open Service Broker voor Azure (OSBA) installeren

Met Open Service Broker voor Azure of OSBA u Azure Cloud Services rechtstreeks vanuit OpenShift inrichten. OSBA in een Open Service Broker API-implementatie voor Azure. De Open Service Broker API is een spec die een gemeenschappelijke taal definieert voor cloudproviders die cloudnative toepassingen kunnen gebruiken om cloudservices te beheren zonder lock-in.

Volg de instructies hier om OSBA op https://github.com/Azure/open-service-broker-azure#openshift-project-templateOpenShift te installeren: . 
> [!NOTE]
> Voer alleen de stappen uit in de sectie OpenShift-projectsjabloon en niet de hele sectie Installeren.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met OpenShift Container Platform](https://docs.openshift.com)
