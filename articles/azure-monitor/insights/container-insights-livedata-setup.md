---
title: Azure-monitor instellen voor containers Live Data (voorbeeld) | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de realtime weergave van containerlogboeken (stdout/stderr) en gebeurtenissen instellen zonder kubectl met Azure Monitor voor containers te gebruiken.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275371"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>De functie Live Data (voorbeeld) instellen

Als u Live Data (preview) wilt bekijken met Azure Monitor voor containers van AKS-clusters (Azure Kubernetes Service), moet u verificatie configureren om toestemming te verlenen voor toegang tot uw Kubernetes-gegevens. Deze beveiligingsconfiguratie biedt realtime toegang tot uw gegevens via de Kubernetes API rechtstreeks in de Azure-portal.

Deze functie ondersteunt de volgende methoden om de toegang tot de logboeken, gebeurtenissen en statistieken te beheren:

- AKS zonder Kubernetes RBAC autorisatie ingeschakeld
- AKS ingeschakeld met Kubernetes RBAC-autorisatie
    - AKS geconfigureerd met ** [clusterrolbindingclusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS ingeschakeld met Azure Active Directory (AD) SAML-gebaseerde single-sign op

Deze instructies vereisen zowel beheerderstoegang tot uw Kubernetes-cluster als het configureren om Azure Active Directory (AD) te gebruiken voor gebruikersverificatie, beheerderstoegang tot Azure AD.  

In dit artikel wordt uitgelegd hoe u verificatie configureert om de toegang tot de functie Live Data (preview) vanuit het cluster te beheren:

- RBAC-cluster (Role-based access control)
- Azure Active Directory-geïntegreerd AKS-cluster. 

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [privéclusters,](https://azure.microsoft.com/updates/aks-private-cluster/) worden niet ondersteund met deze functie. Deze functie is afhankelijk van directe toegang tot de Kubernetes API via een proxyserver vanuit uw browser. Als u netwerkbeveiliging inschakelt om de Kubernetes API van deze proxy te blokkeren, wordt dit verkeer geblokkeerd. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, waaronder Azure China. Het is momenteel niet beschikbaar in Azure US Government.

## <a name="authentication-model"></a>Verificatiemodel

De functies Live Data (preview) maken gebruik `kubectl` van de Kubernetes API, identiek aan het command-line tool. De Kubernetes API-eindpunten maken gebruik van een zelfondertekend certificaat, dat uw browser niet kan valideren. Deze functie maakt gebruik van een interne proxy om het certificaat te valideren met de AKS-service, zodat het verkeer wordt vertrouwd.

De Azure-portal vraagt u uw inloggegevens voor een Azure Active Directory-cluster te valideren en u om te leiden naar de instelling voor clientregistratie tijdens het maken van het cluster (en opnieuw geconfigureerd in dit artikel). Dit gedrag is vergelijkbaar met `kubectl`het verificatieproces dat vereist is door . 

>[!NOTE]
>Autorisatie voor uw cluster wordt beheerd door Kubernetes en het beveiligingsmodel waarmee het is geconfigureerd. Gebruikers die toegang hebben tot deze functie hebben toestemming nodig om `az aks get-credentials -n {your cluster name} -g {your resource group}`de Kubernetes-configuratie *(kubeconfig)* te downloaden, vergelijkbaar met het uitvoeren van . Dit configuratiebestand bevat het autorisatie- en verificatietoken voor **Azure Kubernetes Service Cluster User Role**, in het geval van Azure RBAC-enabled en AKS-clusters zonder rbac-autorisatie ingeschakeld. Het bevat informatie over Azure AD en clientregistratiegegevens wanneer AKS is ingeschakeld met Azure Active Directory (AD) SAML-gebaseerde single-sign.It contains information about Azure AD and client registration details when AKS is enabled with Azure Active Directory (AD) SAML-based single-sign on.

>[!IMPORTANT]
>Gebruikers van deze functies vereisen [Azure Kubernetes Cluster User](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) `kubeconfig` Role naar het cluster om de functie te downloaden en te gebruiken. Gebruikers hebben **geen** toegang tot het cluster nodig om deze functie te gebruiken. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>ClusterMonitoringUser gebruiken met RBAC-clusters

Om te voorkomen dat aanvullende configuratiewijzigingen moeten worden toegepast om de Kubernetes-gebruikersrolbinding **samereteigenschap** toe te staanGebruikerstoegang tot de functie Live Data (preview) na [het inschakelen van RBAC-autorisatie,](#configure-kubernetes-rbac-authorization) heeft AKS een nieuwe Kubernetes-clusterrolbinding toegevoegd, genaamd **clusterMonitoringUser.** Deze clusterrolbinding heeft alle benodigde machtigingen out-of-the-box om toegang te krijgen tot de Kubernetes API en de eindpunten voor het gebruik van de functie Live Data (preview).

Als u de functie Live Data (preview) met deze nieuwe gebruiker wilt gebruiken, moet u lid zijn van de [rol inzender op](../../role-based-access-control/built-in-roles.md#contributor) de AKS-clusterbron. Azure Monitor voor containers, wanneer ingeschakeld, is geconfigureerd om te verifiëren met behulp van deze gebruiker standaard. Als de clusterMonitoringUser-rolbinding niet op een cluster bestaat, wordt **clusterGebruiker** in plaats daarvan gebruikt voor verificatie.

AKS heeft deze nieuwe rolbinding in januari 2020 uitgebracht, zodat clusters die voor januari 2020 zijn gemaakt, deze niet hebben. Als u een cluster hebt dat vóór januari 2020 is gemaakt, kan de nieuwe **clustermonitoringgebruiker** aan een bestaand cluster worden toegevoegd door een PUT-bewerking op het cluster uit te voeren of een andere bewerking op het cluster uit te voeren, waarbij een PUT-bewerking op het cluster wordt uitgevoerd, zoals het bijwerken van de clusterversie.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-cluster zonder RBAC ingeschakeld

Als u een Kubernetes-cluster hebt dat niet is geconfigureerd met Kubernetes RBAC-autorisatie of is geïntegreerd met Azure AD- Dit komt omdat u standaard beheerdersmachtigingen hebt in een niet-RBAC-configuratie.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC-autorisatie configureren

Wanneer u Kubernetes RBAC-autorisatie inschakelt, worden twee gebruikers gebruikt: **clusterUser** en **clusterAdmin** om toegang te krijgen tot de Kubernetes API. Dit is vergelijkbaar `az aks get-credentials -n {cluster_name} -g {rg_name}` met hardlopen zonder de administratieve optie. Dit betekent dat de **clusterGebruiker** toegang moet krijgen tot de eindpunten in kubernetes API.

In de volgende voorbeeldstappen wordt uitgelegd hoe u clusterrolbinding configureert vanuit deze yaml-configuratiesjabloon.

1. Kopieer en plak het yaml-bestand en sla het op als LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Voer de volgende opdracht uit `kubectl apply -f LogReaderRBAC.yaml`om uw configuratie bij te werken: .

>[!NOTE] 
> Als u een vorige versie `LogReaderRBAC.yaml` van het bestand op uw cluster hebt toegepast, werkt u het bij door de nieuwe code in stap 1 hierboven te kopiëren en te plakken en voert u de opdracht uit die in stap 2 wordt weergegeven om het toe te passen op uw cluster.

## <a name="configure-ad-integrated-authentication"></a>Ad-geïntegreerde verificatie configureren 

Een AKS-cluster dat is geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikersverificatie, maakt gebruik van de inloggegevens van de persoon die toegang heeft tot deze functie. In deze configuratie u zich aanmelden bij een AKS-cluster met behulp van uw Azure AD-verificatietoken.

Azure AD-clientregistratie moet opnieuw worden geconfigureerd om de Azure-portal toe te staan autorisatiepagina's om te leiden als een vertrouwde omleidings-URL. Gebruikers van Azure AD krijgen vervolgens rechtstreeks toegang tot dezelfde Kubernetes API-eindpunten via **ClusterRollen** en **ClusterRoleBindings.** 

Voor meer informatie over geavanceerde beveiligingsinstellingen in Kubernetes, bekijk de [Kubernetes-documentatie](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Zie Azure Active Directory integreren met Azure [Kubernetes Service](../../aks/azure-ad-integration.md) en volg de stappen om Azure AD-verificatie te configureren als u een nieuw cluster met RBAC maakt. Tijdens de stappen om de clienttoepassing te maken, markeert een notitie in die sectie de twee omleidings-URL's die u moet maken voor Azure Monitor voor containers die overeenkomen met die welke zijn opgegeven in stap 3 hieronder.

### <a name="client-registration-reconfiguration"></a>Herconfiguratie clientregistratie

1. Zoek de clientregistratie voor uw Kubernetes-cluster in Azure AD onder **Azure Active Directory > App-registraties** in de Azure-portal.

2. Selecteer **Verificatie** in het linkerdeelvenster. 

3. Voeg twee omleidings-URL's toe aan deze lijst als **webtoepassingstypen.** De URL-waarde van `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de eerste basis moet `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`zijn en de URL-waarde van de tweede basis moet zijn .

    >[!NOTE]
    >Als u deze functie in Azure China gebruikt, moet `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de URL-waarde van `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`de eerste basis zijn en moet de URL-waarde van het tweede basisbasis zijn . 
    
4. Nadat u de omleidings-URL's hebt geregistreerd, selecteert u onder **Impliciete toekenning**de opties **Access-tokens** en **ID-tokens** en slaat u vervolgens uw wijzigingen op.

>[!NOTE]
>Het configureren van verificatie met Azure Active Directory voor één aanmelding kan alleen worden uitgevoerd tijdens de eerste implementatie van een nieuw AKS-cluster. U geen aanmelding met één melding configureren voor een AKS-cluster dat al is geïmplementeerd.
  
>[!IMPORTANT]
>Als u Azure AD opnieuw hebt geconfigureerd voor gebruikersverificatie met behulp van de bijgewerkte URI, moet u de cache van uw browser wissen om ervoor te zorgen dat het bijgewerkte verificatietoken wordt gedownload en toegepast.

## <a name="grant-permission"></a>Toestemming

Elk Azure AD-account moet toestemming krijgen voor de juiste API's in Kubernetes om toegang te krijgen tot de functie Live Data (preview). De stappen om het Azure Active Directory-account toe te kennen zijn vergelijkbaar met de stappen die worden beschreven in de sectie [Kubernetes RBAC-verificatie.](#configure-kubernetes-rbac-authorization) Voordat u de yaml-configuratiesjabloon op uw cluster toepast, vervangt **u clusterGebruiker** onder **ClusterRoleBinding** door de gewenste gebruiker. 

>[!IMPORTANT]
>Als de gebruiker waarvoor u de RBAC-binding verleent zich in dezelfde Azure AD-tenant bevindt, wijst u machtigingen toe op basis van de userPrincipalName. Als de gebruiker zich in een andere Azure AD-tenant bevindt, u de eigenschap objectId opvragen en gebruiken.

Zie [RBAC-binding maken](../../aks/azure-ad-integration-cli.md#create-rbac-binding)voor extra hulp bij het configureren van uw AKS-clusterclusterrolbinding. **ClusterRoleBinding**

## <a name="next-steps"></a>Volgende stappen

Nu u setup-verificatie hebt ingesteld, u [statistieken,](container-insights-livedata-metrics.md) [implementaties](container-insights-livedata-deployments.md)en [gebeurtenissen en logboeken](container-insights-livedata-overview.md) in realtime bekijken vanuit uw cluster.
