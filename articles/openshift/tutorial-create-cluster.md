---
title: Zelf studie-een Azure Red Hat open Shift-cluster maken | Microsoft Docs
description: Meer informatie over het maken van een Microsoft Azure Red Hat open Shift-cluster met behulp van de Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 813d3115d8df7227bde89a73a73bcae270f09bbb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771339"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Zelf studie: een Azure Red Hat open Shift-cluster maken

Deze zelfstudie is deel één van een serie. U leert hoe u een Microsoft Azure Red Hat open Shift-cluster maakt met behulp van Azure CLI, hoe u het kunt schalen en hoe u het kunt verwijderen om de resources op te schonen.

In deel één van de serie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken
> * [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
> * [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Voor deze zelf studie is versie 2.0.65 van Azure CLI vereist.

Voor u met deze zelfstudie begint:

Zorg ervoor dat u [uw ontwikkel omgeving hebt ingesteld](howto-setup-environment.md), met inbegrip van:
- De nieuwste CLI (versie 2.0.65 of hoger) installeren
- Een Tenant maken als u er nog geen hebt
- Een Azure-toepassing-object maken als u er nog geen hebt
- Een beveiligings groep maken
- Een Active Directory gebruiker maken om zich aan te melden bij het cluster.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, opent u een bash-opdracht shell en voert u `az login` uit om u aan te melden bij Azure.

```bash
az login
```

 Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` vervangen `{subscription ID}` uit met het abonnement dat u wilt gebruiken.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Stap 2: een Azure Red Hat open Shift-cluster maken

Stel in een bash-opdracht venster de volgende variabelen in:

> [!IMPORTANT]
> Kies een unieke naam voor het cluster en de kleine letters of het maken van het cluster zal mislukken.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Kies een locatie om uw cluster te maken. Zie [ondersteunde regio's](supported-resources.md#azure-regions)voor een lijst met Azure-regio's die openshift op Azure ondersteunen. Bijvoorbeeld: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Stel `APPID` in op de waarde die u hebt opgeslagen in stap 5 van [een Azure AD-App-registratie maken](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Stel GROUPID in op de waarde die u hebt opgeslagen in stap 10 van [een Azure AD-beveiligings groep maken](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Stel `SECRET` in op de waarde die u hebt opgeslagen in stap 8 van [een client geheim maken](howto-aad-app-configuration.md#create-a-client-secret).

```bash
SECRET=<secret value>
```

Stel `TENANT` in op de waarde van de Tenant-ID die u hebt opgeslagen in stap 7 van [een nieuwe Tenant maken](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Maak de resource groep voor het cluster. Voer de volgende opdracht uit vanaf dezelfde bash-shell die u hebt gebruikt voor het definiëren van de bovenstaande variabelen:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Optioneel: verbinding maken tussen het virtuele netwerk van het cluster en een bestaand virtueel netwerk

Als u geen verbinding hoeft te maken met het virtuele netwerk (VNET) van het cluster dat u hebt gemaakt met een bestaand VNET via peering, kunt u deze stap overs Laan.

Als peering met een netwerk buiten het standaard abonnement in dat abonnement, moet u ook de provider micro soft. container service registreren. Als u dit wilt doen, voert u de onderstaande opdracht uit in dat abonnement. Als de VNET-peering zich in hetzelfde abonnement bevindt, kunt u de registratie stap overs Laan.

`az provider register -n Microsoft.ContainerService --wait`

Haal eerst de id op van het bestaande VNET. De id heeft de volgende indeling: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Als u de netwerk naam of de resource groep waarvan het bestaande VNET deel uitmaakt niet weet, gaat u naar de [Blade virtuele netwerken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) en klikt u op uw virtuele netwerk. De pagina virtueel netwerk wordt weer gegeven en de naam van het netwerk en de resource groep waartoe deze behoort, worden vermeld.

Definieer een VNET_ID variabele met behulp van de volgende CLI-opdracht in een BASH-shell:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Bijvoorbeeld: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Optioneel: Verbind het cluster met Azure-bewaking

Haal eerst de id op van de **bestaande** werk ruimte voor logboek analyse. De id heeft de volgende vorm:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Als u de naam van de werk ruimte van log Analytics of de resource groep waartoe de bestaande log-Analytics-werk ruimte behoort, niet weet, gaat u naar de [werk ruimte logboek analyse](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) en klikt u op uw log Analytics-werk ruimten. De pagina werk ruimte voor logboek analyse wordt weer gegeven en de naam van de werk ruimte en de resource groep waartoe deze behoort, worden vermeld.

_Een werk ruimte voor logboek analyse maken Zie de [werk ruimte logboek maken-analyse](../azure-monitor/learn/quick-create-workspace-cli.md)_

Definieer een WORKSPACE_ID variabele met behulp van de volgende CLI-opdracht in een BASH-shell:

```bash
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Het cluster maken

U bent nu klaar om een cluster te maken. Met de volgende opdracht maakt u het cluster in de opgegeven Azure AD-Tenant, geeft u het Azure AD-App-object en het geheim op dat moet worden gebruikt als beveiligings-principal en de beveiligings groep die de leden bevat die beheerders toegang tot het cluster hebben.

> [!IMPORTANT]
> Zorg ervoor dat u de juiste machtigingen hebt toegevoegd voor de Azure AD-app, zoals [hier wordt beschreven](howto-aad-app-configuration.md#add-api-permissions) voordat u het cluster maakt

Als u uw cluster **niet** aan een virtueel netwerk wilt **koppelen of als u geen Azure** -bewaking wilt, gebruikt u de volgende opdracht:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Als u uw cluster aan een virtueel **netwerk wilt koppelen** , gebruikt u de volgende opdracht waarmee de `--vnet-peer` vlag wordt toegevoegd:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Als u Azure-bewaking met uw cluster **wilt** gebruiken, gebruikt u de volgende opdracht waarmee de `--workspace-id` vlag wordt toegevoegd:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Als er een fout bericht wordt weer geven dat de hostnaam niet beschikbaar is, kan het zijn dat de naam van uw cluster niet uniek is. Probeer de oorspronkelijke app-registratie te verwijderen en de stappen opnieuw uit te voeren met een andere cluster naam in [een nieuwe app-registratie maken](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), waarbij u de stap voor het maken van een nieuwe gebruiker en beveiligings groep weglaat.




Na een paar minuten is `az openshift create` voltooid.

### <a name="get-the-sign-in-url-for-your-cluster"></a>De aanmeldings-URL voor uw cluster ophalen

Haal de URL op om u aan te melden bij uw cluster door de volgende opdracht uit te voeren:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Zoek naar de `publicHostName` in de uitvoer, bijvoorbeeld: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

De aanmeldings-URL voor uw cluster wordt `https://` gevolgd door de `publicHostName` waarde.  Bijvoorbeeld: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  U gebruikt deze URI in de volgende stap als onderdeel van de app registratie omleidings-URI.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Stap 3: de omleidings-URI voor de app-registratie bijwerken

Nu u de aanmeldings-URL voor het cluster hebt, stelt u de gebruikers interface voor het omleiden van de app-registratie in:

1. Open de [blade app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klik op uw app-registratie object.
3. Klik op **een omleidings-URI toevoegen**.
4. Zorg ervoor dat het **type** **Web** is en stel de **omleidings-URI** in met het volgende patroon: `https://<public host name>/oauth2callback/Azure%20AD`. Bijvoorbeeld: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klik op **Opslaan**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Stap 4: Meld u aan bij de open Shift-console

U bent nu klaar om u aan te melden bij de open Shift-console voor het nieuwe cluster. Met de [webconsole](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) van open Shift kunt u de inhoud van uw open Shift-projecten visualiseren, bekijken en beheren.

U hebt een nieuw browser exemplaar nodig dat de identiteit die u normaal gesp roken gebruikt om u aan te melden bij de Azure Portal.

1. Open een *incognito* -venster (Chrome) of *InPrivate* Window (micro soft Edge).
2. Ga naar de aanmeldings-URL die u hierboven hebt verkregen, bijvoorbeeld: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Meld u aan met de gebruikers naam die u hebt gemaakt in stap 3 van [een nieuwe Azure Active Directory gebruiker maken](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Er wordt een dialoog venster met **machtigingen gevraagd** weer gegeven. Klik op **toestemming namens uw organisatie** en klik vervolgens op **accepteren**.

U bent nu aangemeld bij de cluster console.

![Scherm opname van de console open Shift cluster](./media/aro-console.png)

 Meer informatie over [het gebruik van de open Shift-console](https://docs.openshift.com/aro/getting_started/developers_console.html) om installatie kopieën te maken en te bouwen in de [Red Hat open Shift](https://docs.openshift.com/aro/welcome/index.html) -documentatie.

## <a name="step-5-install-the-openshift-cli"></a>Stap 5: open Shift CLI installeren

De open [SHIFT cli](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (of *OC-hulpprogram ma's*) bieden opdrachten voor het beheren van uw toepassingen en hulpprogram ma's op lagere niveaus voor interactie met de verschillende onderdelen van uw open Shift-cluster.

Klik in de open Shift-console op het vraag teken in de rechter bovenhoek op uw aanmeldings naam en selecteer **opdracht regel Programma's**.  Volg de **meest recente release** koppeling om de ondersteunde OC CLI voor Linux, MacOS of Windows te downloaden en te installeren.

> [!NOTE]
> Als u het vraag teken niet in de rechter bovenhoek ziet, selecteert u *Service catalogus* of *toepassings console* in de vervolg keuzelijst linksboven.
>
> U kunt [de OC-cli](https://www.okd.io/download.html) ook rechtstreeks downloaden.

De pagina **opdracht regel Programma's** bevat een opdracht van het formulier `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Klik op de knop *kopiëren naar klem bord* om deze opdracht te kopiëren.  Stel in een Terminal venster [uw pad](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) in om uw lokale installatie van de hulpprogram ma's van OC op te laten staan. Meld u vervolgens aan bij het cluster met behulp van de OC CLI-opdracht die u hebt gekopieerd.

Als u de token waarde niet kunt ophalen met behulp van de bovenstaande stappen, haalt u de token waarde op van: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
