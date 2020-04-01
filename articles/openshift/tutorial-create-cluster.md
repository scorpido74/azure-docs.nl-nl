---
title: Zelfstudie - Een Azure Red Hat OpenShift-cluster maken
description: Meer informatie over het maken van een Microsoft Azure Red Hat OpenShift-cluster met de Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455295"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Zelfstudie: Een Azure Red Hat OpenShift-cluster maken

Deze zelfstudie is deel één van een serie. U leert hoe u een Microsoft Azure Red Hat OpenShift-cluster maakt met behulp van het Azure CLI, deze schaalt en vervolgens verwijdert om resources op te schonen.

In deel één van de serie leer je hoe je:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken
> * [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
> * [Een Azure Red Hat OpenShift-cluster verwijderen](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Deze zelfstudie vereist versie 2.0.65 van de Azure CLI.

Voor u met deze zelfstudie begint:

Zorg ervoor dat u [uw ontwikkelomgeving](howto-setup-environment.md)hebt ingesteld, waaronder:
- Het installeren van de nieuwste CLI (versie 2.0.65 of hoger)
- Een tenant maken als u er nog geen hebt
- Een Azure-toepassingsobject maken als u er nog geen hebt
- Een beveiligingsgroep maken
- Een Active Directory-gebruiker maken om zich aan te melden bij het cluster.

## <a name="step-1-sign-in-to-azure"></a>Stap 1: Aanmelden bij Azure

Als u de Azure CLI lokaal uitvoert, opent u `az login` een opdrachtshell voor Bash en voert u deze uit om u aan te melden bij Azure.

```azurecli
az login
```

 Als u toegang hebt tot `az account set -s {subscription ID}` meerdere `{subscription ID}` abonnementen, voert u de vervanging uit van het abonnement dat u wilt gebruiken.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Stap 2: Een Azure Red Hat OpenShift-cluster maken

Stel in een opdrachtvenster bash de volgende variabelen in:

> [!IMPORTANT]
> Kies een naam voor uw cluster die uniek is en alle kleine letters of clustercreatie mislukken.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Kies een locatie om uw cluster te maken. Zie [Ondersteunde regio's](supported-resources.md#azure-regions)voor een lijst met azure-regio's die OpenShift op Azure ondersteunt. Bijvoorbeeld: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Stel `APPID` in op de waarde die u hebt opgeslagen in stap 5 van [Een Azure AD-app-registratie maken](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Stel 'GROUPID' in op de waarde die u hebt opgeslagen in stap 10 van [Een Azure AD-beveiligingsgroep maken.](howto-aad-app-configuration.md#create-an-azure-ad-security-group)

```bash
GROUPID=<group ID value>
```

Stel `SECRET` in op de waarde die u hebt opgeslagen in stap 8 van [Een clientgeheim maken.](howto-aad-app-configuration.md#create-a-client-secret)

```bash
SECRET=<secret value>
```

Instellen `TENANT` op de waarde van de tenant-id die u hebt opgeslagen in stap 7 van [Een nieuwe tenant maken](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Maak de resourcegroep voor het cluster. Voer de volgende opdracht uit vanuit dezelfde Bash-shell die u hebt gebruikt om de bovenstaande variabelen te definiëren:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Optioneel: het virtuele netwerk van het cluster verbinden met een bestaand virtueel netwerk

Als u het virtuele netwerk (VNET) van het cluster dat u maakt niet via peering hoeft aan te sluiten op een bestaand VNET, slaat u deze stap over.

Als u naar een netwerk buiten het standaardabonnement kijkt, moet u in dat abonnement ook de provider Microsoft.ContainerService registreren. Voer hiervoor de onderstaande opdracht in dat abonnement uit. Als het VNET dat u peert zich in hetzelfde abonnement bevindt, u de registratiestap overslaan.

`az provider register -n Microsoft.ContainerService --wait`

Download eerst de id van het bestaande VNET. De id is van `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`het formulier: .

Als u de netwerknaam of de brongroep waartoe de bestaande VNET behoort niet kent, gaat u naar het [blade voor virtuele netwerken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) en klikt u op uw virtuele netwerk. De virtuele netwerkpagina wordt weergegeven en wordt de naam van het netwerk en de brongroep vermeld waartoe het behoort.

Definieer een VNET_ID variabele met de volgende OPDRACHT CLI in een BASH-shell:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Bijvoorbeeld: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Optioneel: het cluster verbinden met Azure-bewaking

Download eerst de id van de **bestaande** werkruimte voor logboekanalyse. De id is van het formulier:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Als u de naam van de log-analytics-werkruimte of de brongroep waartoe de bestaande werkruimte voor logboekanalyse behoort, niet kent, gaat u naar de [werkruimte log-Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) en klikt u op uw logboekanalysewerkruimten. De werkruimtepagina log-analytics wordt weergegeven en wordt de naam van de werkruimte en de brongroep weergegeven waartoe deze behoort.

_Een werkruimte voor logboekanalyse maken: [Logboekanalysewerkruimte maken](../azure-monitor/learn/quick-create-workspace-cli.md)_

Definieer een WORKSPACE_ID variabele met de volgende OPDRACHT CLI in een BASH-shell:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Het cluster maken

U bent nu klaar om een cluster te maken. Als volgt wordt het cluster gemaakt in de opgegeven Azure AD-tenant, wordt het Azure AD-app-object en het geheim opgegeven dat als beveiligingsprincipal kan worden gebruikt en de beveiligingsgroep die de leden bevat die beheerderstoegang tot het cluster hebben.

> [!IMPORTANT]
> Zorg ervoor dat u de juiste machtigingen voor de Azure AD-app correct hebt toegevoegd, zoals [hier beschreven](howto-aad-app-configuration.md#add-api-permissions) voordat u het cluster maakt

Als u uw cluster **niet** naar een virtueel netwerk wilt peeren of **azure-controle niet** wilt, gebruikt u de volgende opdracht:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Als **u** uw cluster naar een virtueel netwerk kijkt, `--vnet-peer` gebruikt u de volgende opdracht die de vlag toevoegt:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Als u Azure Monitoring met uw cluster **wilt,** gebruikt u de volgende opdracht die de `--workspace-id` vlag toevoegt:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Als u een foutmelding krijgt dat de hostnaam niet beschikbaar is, kan dit zijn omdat uw clusternaam niet uniek is. Probeer de registratie van uw oorspronkelijke app te verwijderen en de stappen opnieuw uit te voeren met een andere clusternaam in [Een nieuwe app-registratie maken,](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)waarbij u de stap van het maken van een nieuwe gebruikers- en beveiligingsgroep weglaat.




Na een paar `az openshift create` minuten, zal voltooid.

### <a name="get-the-sign-in-url-for-your-cluster"></a>De aanmeldings-URL voor uw cluster ophalen

Download de URL om u aan te melden bij uw cluster door de volgende opdracht uit te voeren:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Zoek naar `publicHostName` de in de output, bijvoorbeeld:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

De aanmeldings-URL voor `https://` uw `publicHostName` cluster wordt gevolgd door de waarde.  Bijvoorbeeld: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  U gebruikt deze URI in de volgende stap als onderdeel van de app registratie redirect URI.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Stap 3: Uw app-registratie redirect URI bijwerken

Nu u de aanmeldings-URL voor het cluster hebt, stelt u de gebruikersinterface voor app-registratie omleiden:

1. Open het [mes app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klik op het registratieobject van uw app.
3. Klik op **Een omleiding uri toevoegen**.
4. Controleer of **TYPE** **web** is en stel de `https://<public host name>/oauth2callback/Azure%20AD` **OMLEIDINGsURI** in met het volgende patroon: . Bijvoorbeeld: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klik **op Opslaan**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Stap 4: Aanmelden bij de OpenShift-console

U zich nu aanmelden bij de OpenShift-console voor uw nieuwe cluster. Met de [OpenShift Web Console](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) u de inhoud van uw OpenShift-projecten visualiseren, doorbladeren en beheren.

U hebt een nieuwe browserinstantie nodig die de identiteit die u normaal gebruikt om u aan te melden bij de Azure-portal, niet in de cache heeft opgeslagen.

1. Open een *incognitovenster* (Chrome) of *InPrivate-venster* (Microsoft Edge).
2. Navigeer naar de url van aanmelding die u hierboven hebt verkregen, bijvoorbeeld:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Meld u aan met de gebruikersnaam die u hebt gemaakt in stap 3 van [Een nieuwe Azure Active Directory-gebruiker maken.](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)

Er wordt een **dialoogvenster Permissies** aangevraagd. Klik **namens uw organisatie op Toestemming** en klik vervolgens op **Accepteren**.

U bent nu aangemeld bij de clusterconsole.

![Schermafbeelding van de OpenShift-clusterconsole](./media/aro-console.png)

 Meer informatie over [het gebruik van de OpenShift-console](https://docs.openshift.com/aro/getting_started/developers_console.html) om afbeeldingen te maken en te bouwen in de Red Hat [OpenShift-documentatie.](https://docs.openshift.com/aro/welcome/index.html)

## <a name="step-5-install-the-openshift-cli"></a>Stap 5: Installeer de OpenShift CLI

De [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (of *OC Tools)* bieden opdrachten voor het beheren van uw toepassingen en hulpprogramma's op een lager niveau voor interactie met de verschillende onderdelen van uw OpenShift-cluster.

Klik in de OpenShift-console op het vraagteken in de rechterbovenhoek met uw aanmeldingsnaam en selecteer **Hulpmiddelen voor opdrachtregel .**  Volg de **nieuwste releasekoppeling** om de ondersteunde oc CLI voor Linux, MacOS of Windows te downloaden en te installeren.

> [!NOTE]
> Als u het vraagtekenpictogram niet in de rechterbovenhoek ziet, selecteert u *Servicecatalogus* of *Toepassingsconsole* in de vervolgkeuzelijst linksboven.
>
> Afwisselend u de oc CLI direct [downloaden.](https://www.okd.io/download.html)

De pagina **Hulpmiddelen voor opdrachtregelbiedt** een opdracht van het formulier `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Klik op de knop *Kopiëren naar klembord* om deze opdracht te kopiëren.  Stel in een terminalvenster [uw pad in](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) om uw lokale installatie van de oc-gereedschappen op te nemen. Meld u vervolgens aan bij het cluster met de oc CLI-opdracht die u hebt gekopieerd.

Als u de tokenwaarde niet krijgen met behulp van `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`de bovenstaande stappen, krijgt u de tokenwaarde op uit:.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Red Hat OpenShift-cluster maken

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [De schaal van een Azure Red Hat OpenShift-cluster wijzigen](tutorial-scale-cluster.md)
