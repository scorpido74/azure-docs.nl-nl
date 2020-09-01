---
title: Meer informatie over Access Control op rollen gebaseerd Kubernetes op Azure Stack edge-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes op rollen gebaseerd Access Control zich voordoet op een Azure Stack edge-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 697c686b61a86cb01327364ad73f30f88e2e151d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268071"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-gpu-device"></a>Op rollen gebaseerd Access Control op Kubernetes op uw Azure Stack Edge GPU-apparaat


Wanneer u een compute-rol configureert op uw Azure Stack edge-apparaat, wordt er een Kubernetes-cluster gemaakt. U kunt op rollen gebaseerd toegangs beheer (RBAC) gebruiken om de toegang tot de cluster bronnen op uw apparaat te beperken.

In deze artikelen vindt u een overzicht van het RBAC-systeem dat door Kubernetes wordt geleverd en hoe Kubernetes RBAC is geïmplementeerd op uw Azure Stack edge-apparaat. 

## <a name="rbac-for-kubernetes"></a>RBAC voor Kubernetes

Met Kubernetes RBAC kunt u gebruikers of groepen gebruikers toewijzen, toestemming geven voor zaken zoals het maken of wijzigen van resources of Logboeken voor het uitvoeren van werk belastingen van toepassingen. Deze machtigingen kunnen worden ingesteld op een enkele naam ruimte of worden verleend in het hele cluster. 

Bij het instellen van het Kubernetes-cluster wordt één gebruiker gemaakt die overeenkomt met dit cluster en wordt de gebruiker van de Cluster beheerder genoemd.  Een `kubeconfig` bestand is gekoppeld aan de gebruiker van de Cluster beheerder. Het `kubeconfig` bestand is een tekst bestand met alle configuratie-informatie die nodig is om verbinding te maken met het cluster om de gebruiker te verifiëren.

## <a name="namespaces-types"></a>Typen naam ruimten

Kubernetes-resources, zoals peulen en implementaties, worden logisch gegroepeerd in een naam ruimte. Deze groeperingen bieden een manier om een Kubernetes-cluster logisch te verdelen en de toegang te beperken tot het maken, weer geven of beheren van resources. Gebruikers kunnen alleen communiceren met resources binnen hun toegewezen naam ruimten.

Naam ruimten zijn bedoeld voor gebruik in omgevingen met veel gebruikers verspreid over meerdere teams of projecten. Voor clusters met een paar van tien gebruikers hoeft u helemaal geen naam ruimten te maken of te bedenken. Begin met het gebruik van naam ruimten wanneer u de functies nodig hebt die ze bieden.

Zie [Kubernetes-naam ruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)voor meer informatie.


Uw Azure Stack edge-apparaat heeft de volgende naam ruimten:

- **Systeem naam ruimte** : deze naam ruimte is de belangrijkste bronnen, zoals netwerk functies, zoals DNS en proxy, of het Kubernetes-dash board. Normaal gesp roken implementeert u uw eigen toepassingen niet in deze naam ruimte. Gebruik deze naam ruimte om problemen met een Kubernetes-cluster op te sporen. 

    Uw apparaat bevat meerdere naam ruimten voor het systeem en de namen die overeenkomen met deze systeem naam ruimten zijn gereserveerd. Hier volgt een lijst met de gereserveerde systeem naam ruimten: 
    - uitvoeren-systeem
    - metallb-systeem
    - DBE-naam ruimte
    - standaardinstelling
    - kubernetes-dash board
    - standaardinstelling
    - uitvoeren-node-lease
    - uitvoeren-openbaar
    - iotedge
    - Azure-boog

    Zorg ervoor dat u geen gereserveerde namen gebruikt voor gebruikers naam ruimten die u maakt. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Gebruikers naam ruimte** : Dit zijn de naam ruimten die u kunt maken via **kubectl** om toepassingen lokaal te implementeren.
 
- **IOT Edge naam ruimte** : u maakt verbinding met deze `iotedge` naam ruimte om toepassingen te implementeren via IOT Edge.

- **Azure ARC-naam ruimte** : u maakt verbinding met deze `azure-arc` naam ruimte om toepassingen te implementeren via Azure Arc. 

## <a name="namespaces-and-users"></a>Naam ruimten en gebruikers

In de praktijk is het belang rijk dat u het cluster opsplitst in meerdere naam ruimten. 

- **Meerdere gebruikers**: als u meerdere gebruikers hebt, kunnen deze gebruikers met meerdere naam ruimten hun toepassingen en services in hun specifieke naam ruimten van elkaar isoleren. 
- **Eén gebruiker**: zelfs als er één gebruiker is, kan de gebruiker met meerdere naam ruimten meerdere versies van de toepassingen uitvoeren in hetzelfde Kubernetes-cluster.

### <a name="roles-and-rolebindings"></a>Rollen en RoleBindings

Kubernetes heeft het concept van rol-en functie binding waarmee u machtigingen kunt verlenen aan gebruikers of resources op naam ruimte niveau en op cluster niveau. 

- **Rollen**: u kunt machtigingen definiëren voor gebruikers als een **rol** en vervolgens **rollen** gebruiken om machtigingen te verlenen binnen een naam ruimte. 
- **RoleBindings**: Zodra u de functies hebt gedefinieerd, kunt u **RoleBindings** gebruiken om rollen toe te wijzen voor een bepaalde naam ruimte. 

Met deze benadering kunt u een enkel Kubernetes-cluster logisch scheiden, met gebruikers die alleen toegang hebben tot de toepassings resources in hun toegewezen naam ruimte. 

## <a name="rbac-on-azure-stack-edge"></a>RBAC op Azure Stack rand

In de huidige implementatie van RBAC kunt u met Azure Stack Edge de volgende acties uitvoeren vanuit een beperkte Power shell-runs Pace:

- Maak naam ruimten.  
- Maak extra gebruikers.
- Verleen u beheerders toegang tot de naam ruimten die u hebt gemaakt. Denk eraan dat u geen toegang hebt tot de rol cluster beheerder of een weer gave van de resources in het hele cluster.
- Haal `kubeconfig` het bestand op met informatie voor toegang tot het Kubernetes-cluster.


Het Azure Stack edge-apparaat heeft meerdere systeem naam ruimten en u kunt gebruikers naam ruimten maken met `kubeconfig` bestanden voor toegang tot deze naam ruimten. De gebruikers hebben volledige controle over deze naam ruimten en kunnen gebruikers maken of wijzigen, of gebruikers toegang verlenen. Alleen de Cluster beheerder heeft volledige toegang tot systeem naam ruimten en cluster bronnen. Een `aseuser` heeft alleen-lezen toegang tot systeem naam ruimten.

Hier volgt een diagram waarin de implementatie van RBAC op Azure Stack edge-apparaat wordt weer gegeven.

![RBAC op Azure Stack edge-apparaat](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

In dit diagram hebben Anne, Bob en Chuck alleen toegang tot toegewezen gebruikers naam ruimten, in dit geval, `ns1` `ns2` en `ns3` respectievelijk. Binnen deze naam ruimten hebben ze beheerders toegang. De Cluster beheerder van de andere kant heeft beheerders toegang tot systeem naam ruimten en cluster bronnen.

U kunt `kubectl` opdrachten gebruiken om naam ruimten te maken, gebruikers toe te wijzen, gebruikers toe te wijzen of bestanden te downloaden `kubeconfig` . Hier volgt een werk stroom op hoog niveau:

1. Een naam ruimte en een gebruiker maken.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Maak een gebruiker.  

    `New-HcsKubernetesUser -UserName`  

3. Koppel de naam ruimte aan de gebruiker die u hebt gemaakt.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Sla de gebruikers configuratie op naar `C:\Users\<username>\.kube` .  

5. Installeer `kubectl` en begin met het implementeren van toepassingen in `kubectl` . 

Voor gedetailleerde stapsgewijze instructies gaat u naar [het Access Kubernetes-cluster via kuebctl op uw Azure stack-rand](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Wanneer u met naam ruimten en gebruikers op uw Azure Stack edge-apparaten werkt, gelden de volgende voor behoud:

- U mag geen bewerkingen uitvoeren zoals het maken van gebruikers, het verlenen of intrekken van naam ruimte toegang aan de gebruiker voor een van de naam ruimten van het systeem. Voor beelden van naam ruimten van het systeem zijn `kube-system` , `metallb-system` , `kubernetes-dashboard` , `default` ,, `kube-node-lease` `kube-public` . Systeem naam ruimten bevatten ook de naam ruimten die zijn gereserveerd voor implementatie typen, zoals `iotedge` (IOT Edge naam ruimte) en `azure-arc` (Azure Arc namespace).
- U kunt gebruikers naam ruimten maken en binnen die naam ruimten extra gebruikers maken en toegang tot de naam ruimte voor deze gebruikers verlenen of intrekken.
- U mag geen naam ruimten maken met namen die identiek zijn aan die voor een wille keurige systeem naam ruimte. De namen voor systeem naam ruimten zijn gereserveerd.  
- U mag geen gebruikers naam ruimten maken met namen die al worden gebruikt door andere gebruikers naam ruimten. Als u bijvoorbeeld een `test-ns` andere naam ruimte hebt gemaakt, kunt u deze niet maken `test-ns` .
- U mag geen gebruikers maken met namen die al zijn gereserveerd. `aseuser`Is bijvoorbeeld een gereserveerde cluster beheerder en kan niet worden gebruikt.


## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot een Kubernetes-cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md)voor informatie over het maken van een gebruiker, het maken van een naam ruimte en het verlenen van gebruikers toegang tot de naam ruimte.

