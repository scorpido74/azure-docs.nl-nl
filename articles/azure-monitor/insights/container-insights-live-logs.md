---
title: Azure Monitor voor container Logboeken in realtime weer geven | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de real-time weer gave van container Logboeken (stdout/stderr) en gebeurtenissen zonder kubectl te gebruiken met Azure Monitor voor containers.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389790"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Logboeken en gebeurtenissen in realtime weer geven (preview)
Azure Monitor voor containers bevat een functie die momenteel als preview-versie beschikbaar is. deze biedt een live weer gave in uw Azure Kubernetes service (AKS)-container Logboeken (stdout/stderr) en gebeurtenissen zonder kubectl-opdrachten uit te voeren. Wanneer u een van beide opties selecteert, wordt er een nieuw deel venster weer gegeven onder de tabel prestatie gegevens in de weer gave **knoop punten**, **controllers**en **containers** . Hierin worden live logboeken en gebeurtenissen weer gegeven die door de container-Engine zijn gegenereerd voor verdere hulp bij het oplossen van problemen in real-time.

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, inclusief Azure China. Het is momenteel niet beschikbaar in de Amerikaanse overheid van Azure.

>[!NOTE]
>De gebruikersrol **Azure Kubernetes service-cluster** toegang tot de cluster bron is vereist om deze functie te kunnen gebruiken. Meer [informatie over de gebruikersrol Azure Kubernetes-cluster](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Live-logboeken ondersteunen drie verschillende methoden om de toegang tot de logboeken te beheren:

1. AKS zonder Kubernetes RBAC-autorisatie is ingeschakeld
2. AKS ingeschakeld met Kubernetes RBAC-autorisatie
3. AKS ingeschakeld met Azure Active Directory (AD) op SAML gebaseerde eenmalige aanmelding

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-cluster zonder RBAC ingeschakeld
 
Als u een Kubernetes-cluster hebt dat niet is geconfigureerd met Kubernetes RBAC-autorisatie of geïntegreerd met Azure AD, hoeft u deze stappen niet uit te voeren. Omdat de Kubernetes-autorisatie gebruikmaakt van de uitvoeren-API, zijn alleen-lezen-machtigingen vereist.

## <a name="kubernetes-rbac-authorization"></a>RBAC-autorisatie Kubernetes
Als u Kubernetes RBAC-autorisatie hebt ingeschakeld, moet u cluster functie binding Toep assen. De volgende voorbeeld stappen laten zien hoe u de binding van een cluster functie configureert vanuit deze yaml-configuratie sjabloon. 

1. Kopieer en plak het yaml-bestand en sla het op als LogReaderRBAC. yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
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

2. Als u deze voor de eerste keer configureert, past u de binding van de cluster regel toe door de volgende opdracht uit te voeren: `kubectl create -f LogReaderRBAC.yaml`. Als u eerder ondersteuning hebt ingeschakeld voor de preview-versie van Live logboeken voordat er live gebeurtenis logboeken werden geïntroduceerd, voert u de volgende opdracht uit om uw configuratie bij te werken: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>AKS configureren met Azure Active Directory

AKS kan worden geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikers verificatie. Als u deze voor de eerste keer configureert, raadpleegt u [Azure Active Directory integreren met de Azure Kubernetes-service](../../aks/azure-ad-integration.md). Geef tijdens de stappen voor het maken van de [client toepassing](../../aks/azure-ad-integration.md#create-the-client-application)het volgende op:

-  **Omleidings-URI**: er **moeten twee typen** webtoepassingen worden gemaakt. De eerste basis-URL-waarde moet `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn en de tweede basis-URL-waarde moet `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn.
- Selecteer na het registreren van de toepassing op de pagina **overzicht** de optie **verificatie** in het linkerdeel venster. Geef op de pagina **verificatie** onder **Geavanceerde instellingen** impliciet **toegangs tokens** en **id-tokens** toe en sla uw wijzigingen op.

>[!NOTE]
>Als u deze functie gebruikt in azure China-regio, moet de waarde van de eerste basis-URL `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn en moet de waarde van de tweede basis-URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` zijn.

>[!NOTE]
>Het configureren van verificatie met Azure Active Directory voor eenmalige aanmelding kan alleen worden uitgevoerd tijdens de eerste implementatie van een nieuw AKS-cluster. U kunt eenmalige aanmelding niet configureren voor een AKS-cluster dat al is geïmplementeerd.
  
>[!IMPORTANT]
>Als u Azure AD voor gebruikers verificatie opnieuw hebt geconfigureerd met behulp van de bijgewerkte URI, wist u de cache van uw browser om te controleren of het bijgewerkte verificatie token is gedownload en toegepast.   

## <a name="view-live-logs-and-events"></a>Live logboeken en gebeurtenissen weer geven

U kunt real-time logboek gebeurtenissen weer geven wanneer deze worden gegenereerd door de container Engine vanuit de weer gave **knoop punten**, **controllers**en **containers** . In het deel venster Eigenschappen selecteert u de optie **Live data weer geven (preview)** en wordt een deel venster weer gegeven onder de tabel prestatie gegevens, waarin u Logboeken en gebeurtenissen in een doorlopende stroom kunt weer geven.

![Deel venster Eigenschappen van knoop punt opties voor Live logboeken weer geven](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Logboek-en gebeurtenis berichten zijn beperkt op basis van het bron type dat is geselecteerd in de weer gave.

| Weergeven | Resourcetype | Logboek of gebeurtenis | Gegevens die worden gepresenteerd |
|------|---------------|--------------|----------------|
| Knooppunten | Knooppunt | Gebeurtenis | Wanneer een knoop punt is geselecteerd, worden de gebeurtenissen niet gefilterd en worden Kubernetes-gebeurtenissen voor het hele cluster weer gegeven. De titel van het deel venster toont de naam van het cluster. |
| Knooppunten | Pod | Gebeurtenis | Wanneer een Pod is geselecteerd, worden de gebeurtenissen gefilterd op de naam ruimte. De titel van het deel venster toont de naam ruimte van de pod. | 
| Fungeren | Pod | Gebeurtenis | Wanneer een Pod is geselecteerd, worden de gebeurtenissen gefilterd op de naam ruimte. De titel van het deel venster toont de naam ruimte van de pod. |
| Fungeren | Regelaar | Gebeurtenis | Wanneer een controller is geselecteerd, worden de gebeurtenissen gefilterd op de naam ruimte. De titel van het deel venster toont de naam ruimte van de controller. |
| Knoop punten/controllers/containers | Container | Logboeken | De titel van het deel venster toont de naam van de pod waarin de container is gegroepeerd. |

Als het AKS-cluster met behulp van AAD is geconfigureerd met SSO, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browser sessie. Selecteer uw account en voltooi de verificatie met Azure.  

Nadat de verificatie is gelukt, wordt het deel venster Live-logboek weer gegeven in het onderste gedeelte van het middelste deel venster. Als de status indicator ophalen een groen vinkje bevat dat helemaal rechts in het deel venster staat, betekent dit dat er gegevens kunnen worden opgehaald.
    
  ![Het deel venster Live logs met opgehaalde gegevens](./media/container-insights-live-logs/live-logs-pane-01.png)  

In de zoek balk kunt u filteren op belang rijke woorden om die tekst in het logboek of de gebeurtenis te markeren, en in de zoek balk helemaal rechts ziet u hoeveel resultaten overeenkomen met het filter.

  ![Filter voor deel venster van Live logs](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Tijdens het weer geven van gebeurtenissen kunt u de resultaten ook beperken met behulp van de **filter** Pill rechts van de zoek balk. Afhankelijk van de resource die u hebt geselecteerd, wordt in de Pill een Pod, naam ruimte of cluster weer gegeven waaruit u kunt kiezen.  

Als u de functie voor automatisch schuiven wilt onderbreken en het gedrag van het deel venster wilt beheren en u hand matig door de nieuwe gegevens wilt schuiven, klikt u op de **Schuif** optie. Als u AutoScroll opnieuw wilt inschakelen, klikt u gewoon opnieuw op de **Schuif** optie. U kunt ook het ophalen van logboek-of gebeurtenis gegevens onderbreken door te klikken op de **pauze** optie en wanneer u klaar bent om verder te gaan. Klik gewoon op **afspelen**.  

![Live logboeken deel venster Live weer gave onderbreken](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

U kunt naar Azure Monitor logboeken gaan om historische container logboeken te bekijken door **container logboeken weer geven** te selecteren in de vervolg keuzelijst **weergave in Analytics**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Kubernetes service Health weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor en het controleren van andere aspecten van uw AKS-cluster.

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken voor het evalueren of aanpassen van waarschuwingen, het visualiseren of analyseren van uw clusters.
