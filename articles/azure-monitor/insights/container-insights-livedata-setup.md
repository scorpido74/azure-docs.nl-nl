---
title: Setup Azure Monitor voor containers live data (preview) | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de real-time weer gave van container Logboeken (stdout/stderr) en gebeurtenissen kunt instellen zonder kubectl te gebruiken met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: cf42eea99e437a76bb437b23f6eaffae1f1f3bc6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063761"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>De functie voor live data (preview) instellen

Als u live data (preview) wilt weer geven met Azure Monitor voor containers uit Azure Kubernetes service (AKS)-clusters, moet u verificatie configureren om toegang te verlenen tot uw Kubernetes-gegevens. Met deze beveiligings configuratie kan realtime toegang krijgen tot uw gegevens via de Kubernetes-API rechtstreeks in de Azure Portal.

Deze functie ondersteunt drie verschillende methoden voor het beheren van de toegang tot de logboeken, gebeurtenissen en metrische gegevens:

- AKS zonder Kubernetes RBAC-autorisatie ingeschakeld
- AKS ingeschakeld met Kubernetes RBAC-autorisatie
- AKS ingeschakeld met Azure Active Directory (AD) op SAML gebaseerde eenmalige aanmelding

Deze instructies vereisen zowel beheerders toegang tot uw Kubernetes-cluster als het configureren om Azure Active Directory (AD) te gebruiken voor gebruikers verificatie, beheerders toegang tot Azure AD.  

In dit artikel wordt uitgelegd hoe u verificatie configureert om de toegang tot de functie Live data (preview) te beheren vanuit het cluster:

- Op rollen gebaseerd toegangs beheer (RBAC) ingeschakeld AKS-cluster
- Azure Active Directory geïntegreerde AKS-cluster. 

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [persoonlijke clusters](https://azure.microsoft.com/updates/aks-private-cluster/) , worden niet ondersteund met deze functie. Deze functie is afhankelijk van het rechtstreeks openen van de Kubernetes-API via een proxy server vanuit uw browser. Door netwerk beveiliging in te scha kelen, wordt dit verkeer geblokkeerd door de Kubernetes-API van deze proxy te blok keren. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, inclusief Azure China. Het is momenteel niet beschikbaar in de Amerikaanse overheid van Azure.

## <a name="authentication-model"></a>Verificatie model

De functies van live data (preview) maken gebruik van de Kubernetes-API, identiek aan het opdracht regel programma `kubectl`. In de Kubernetes API-eind punten wordt gebruikgemaakt van een zelfondertekend certificaat, dat niet kan worden gevalideerd door de browser. Deze functie maakt gebruik van een interne proxy om het certificaat te valideren met de AKS-service, zodat het verkeer wordt vertrouwd.

De Azure Portal vraagt u uw aanmeldings referenties voor een Azure Active Directory cluster te valideren en u te omleiden naar de installatie van de client registratie tijdens het maken van het cluster (en opnieuw geconfigureerd in dit artikel). Dit gedrag is vergelijkbaar met het verificatie proces dat `kubectl`vereist. 

>[!NOTE]
>Autorisatie voor uw cluster wordt beheerd door Kubernetes en het beveiligings model waarin het is geconfigureerd. Gebruikers die deze functie gebruiken, hebben toestemming nodig om de Kubernetes-configuratie (*kubeconfig*) te downloaden, vergelijkbaar met het uitvoeren van `az aks get-credentials -n {your cluster name} -g {your resource group}`. Dit configuratie bestand bevat het autorisatie-en verificatie token voor de **Azure Kubernetes-service cluster**gebruikersrol, in het geval van Azure RBAC-ingeschakelde en AKS-clusters waarvoor geen RBAC-autorisatie is ingeschakeld. Het bevat informatie over Azure AD en client registratiegegevens wanneer AKS is ingeschakeld met Azure Active Directory (AD) op SAML gebaseerde eenmalige aanmelding.

>[!IMPORTANT]
>Gebruikers van deze functies hebben [Azure Kubernetes-cluster](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) gebruikersrol voor het cluster nodig om de `kubeconfig` te kunnen downloaden en deze functie te kunnen gebruiken. Gebruikers hebben **geen** Inzender toegang tot het cluster nodig om deze functie te gebruiken. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-cluster zonder RBAC ingeschakeld

Als u een Kubernetes-cluster dat niet is geconfigureerd met Kubernetes RBAC-autorisatie of geïntegreerd met Azure AD eenmalige aanmelding hebt, hoeft u niet aan de volgende stappen. Dit komt omdat u standaard beheerders machtigingen hebt in een niet-RBAC-configuratie.

## <a name="configure-kubernetes-rbac-authentication"></a>RBAC-verificatie Kubernetes configureren

Wanneer u de RBAC-autorisatie Kubernetes inschakelt, worden er twee gebruikers gebruikt: **clusterUser** en **clusterAdmin** om toegang te krijgen tot de Kubernetes-API. Dit is vergelijkbaar met het uitvoeren van `az aks get-credentials -n {cluster_name} -g {rg_name}` zonder de optie beheer. Dit betekent dat de **clusterUser** toegang moet krijgen tot de eind punten in de KUBERNETES-API.

Het volgende voorbeeld wordt laten zien hoe het configureren van cluster rol binding met deze sjabloon yaml-configuratie.

1. Kopieer en plak het yaml-bestand en sla deze op als LogReaderRBAC.yaml.  

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

2. Voer de volgende opdracht uit om uw configuratie bij te werken: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Als u een eerdere versie van het `LogReaderRBAC.yaml`-bestand hebt toegepast op het cluster, werkt u het bij door de nieuwe code te kopiëren en plakken die in stap 1 hierboven wordt weer gegeven, en voert u de opdracht uit stap 2 uit om deze toe te passen op uw cluster.

## <a name="configure-ad-integrated-authentication"></a>Geïntegreerde AD-verificatie configureren 

Een AKS-cluster dat is geconfigureerd voor het gebruik van Azure Active Directory (AD) voor gebruikers verificatie, maakt gebruik van de aanmeldings referenties van de persoon die toegang heeft tot deze functie. In deze configuratie kunt u zich aanmelden bij een AKS-cluster met behulp van uw Azure AD-verificatie token.

De registratie van de Azure AD-client moet opnieuw worden geconfigureerd zodat de Azure Portal autorisatie pagina's omleiden als een vertrouwde omleidings-URL. Gebruikers uit Azure AD worden vervolgens rechtstreeks toegang verleend tot dezelfde Kubernetes API-eind punten via **ClusterRoles** en **ClusterRoleBindings**. 

Raadpleeg de [Kubernetes-documentatie](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)voor meer informatie over geavanceerde beveiligings instellingen in Kubernetes. 

>[!NOTE]
>Als u een nieuw cluster met RBAC-functionaliteit wilt maken, raadpleegt u [Azure Active Directory integreren met de Azure Kubernetes-service](../../aks/azure-ad-integration.md) en volgt u de stappen voor het configureren van Azure AD-verificatie. Tijdens de stappen voor het maken van de client toepassing, worden in deze sectie de twee omleidings-Url's beschreven die u moet maken voor Azure Monitor voor containers die overeenkomen met die in stap 3 hieronder.

### <a name="client-registration-reconfiguration"></a>Client registratie opnieuw configureren

1. Zoek de client registratie voor uw Kubernetes-cluster in azure AD onder **Azure Active Directory > app-registraties** in de Azure Portal.

2. Selecteer **verificatie** in het linkerdeel venster. 

3. Voeg twee omleidings-Url's toe aan deze lijst als typen **webtoepassingen** . De eerste basis-URL-waarde moet `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn en de tweede basis-URL-waarde moet `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`zijn.

    >[!NOTE]
    >Als u deze functie in azure China gebruikt, moet de eerste basis-URL-waarde `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn en moet de tweede basis-URL-waarde `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`zijn. 
    
4. Nadat u de omleidings-Url's hebt geregistreerd, selecteert u onder **impliciete toekenning**de opties **toegangs tokens** en **id-tokens** en slaat u de wijzigingen op.

>[!NOTE]
>Het configureren van verificatie met Azure Active Directory voor eenmalige aanmelding kan alleen worden uitgevoerd tijdens de eerste implementatie van een nieuw AKS-cluster. U kunt eenmalige aanmelding niet configureren op voor een AKS-cluster die al zijn geïmplementeerd.
  
>[!IMPORTANT]
>Als u Azure AD voor gebruikers verificatie opnieuw hebt geconfigureerd met behulp van de bijgewerkte URI, wist u de cache van uw browser om te controleren of het bijgewerkte verificatie token is gedownload en toegepast.

## <a name="grant-permission"></a>Machtiging verlenen

Aan elk Azure AD-account moet machtigingen worden verleend voor de juiste Api's in Kubernetes om toegang te krijgen tot de functie Live data (preview). De stappen voor het verlenen van het Azure Active Directory account zijn vergelijkbaar met de stappen die worden beschreven in de sectie [KUBERNETES RBAC-verificatie](#configure-kubernetes-rbac-authentication) . Voordat u de yaml-configuratie sjabloon toepast op uw cluster, vervangt u **clusterUser** onder **ClusterRoleBinding** door de gewenste gebruiker. 

>[!IMPORTANT]
>Als de gebruiker aan wie u de RBAC-binding hebt verleend, zich in dezelfde Azure AD-Tenant bevindt, wijst u machtigingen toe op basis van de userPrincipalName. Als de gebruiker zich in een andere Azure AD-Tenant bevindt, wordt er een query voor en gebruikt de objectId-eigenschap.

Zie [RBAC-binding maken](../../aks/azure-ad-integration-cli.md#create-rbac-binding)voor meer informatie over het configureren van uw AKS-cluster **ClusterRoleBinding**.

## <a name="next-steps"></a>Volgende stappen

Nu u verificatie hebt ingesteld, kunt u [metrische gegevens](container-insights-livedata-metrics.md), [implementaties](container-insights-livedata-deployments.md)en [gebeurtenissen en logboeken](container-insights-livedata-overview.md) in realtime weer geven vanuit uw cluster.
