---
title: Zelf studie-functies op basis van op rollen gebaseerde toegangs beheer (RBAC) configureren in azure Kubernetes service (AKS) met behulp van Ansible
description: Meer informatie over het gebruik van Ansible voor het configureren van RBAC in azure Kubernetes service (AKS)-cluster
keywords: ansible, azure, devops, bash, Cloud shell, Playbook, AKS, container, AKS, kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1be123eb06bd2679169478daf27a7148d2a8b055
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156870"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Zelf studie: functies op basis van op rollen gebaseerde toegangs beheer (RBAC) configureren in azure Kubernetes service (AKS) met behulp van Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan worden geconfigureerd om [Azure Active Directory (AD)](/azure/active-directory/) te gebruiken voor gebruikers verificatie. Eenmaal geconfigureerd, gebruikt u uw Azure AD-verificatie token om u aan te melden bij het AKS-cluster. De RBAC kan worden gebaseerd op de identiteit of het lidmaatschap van de Directory groep van een gebruiker.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een Azure AD-AKS-cluster maken
> * Een RBAC-rol in het cluster configureren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installeer de - van de RedHat** -open Shift-bibliotheek `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Azure AD configureren voor AKS-verificatie

Bij het configureren van Azure AD voor AKS-verificatie, worden twee Azure AD-toepassingen geconfigureerd. Deze bewerking moet worden uitgevoerd door een beheerder van een Azure-Tenant. Zie [Azure Active Directory integreren met AKS](/azure/aks/aad-integration#create-the-server-application)voor meer informatie. 

Haal de volgende waarden op in de Azure-Tenant beheerder:

- Server app-geheim
- Server App-ID
- Client-App-ID 
- Tenant-id

Deze waarden zijn nodig om de voorbeeld Playbook uit te voeren.  

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

In deze sectie maakt u een AKS met de [Azure AD-toepassing](#configure-azure-ad-for-aks-authentication).

Hier volgen enkele belang rijke opmerkingen waarmee u rekening moet houden wanneer u werkt met de voor beeld-Playbook:

- De Playbook laadt `ssh_key` van `~/.ssh/id_rsa.pub`. Als u het wijzigt, gebruikt u de indeling met één regel, te beginnen met ' ssh-rsa ' (zonder de aanhalings tekens).
- De waarden `client_id` en `client_secret` worden geladen vanuit `~/.azure/credentials`, het standaard referentie bestand. U kunt deze waarden instellen voor uw service-principal of deze waarden laden vanuit omgevings variabelen:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Sla het volgende playbook op als `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>De object-ID van Azure AD ophalen

Als u een RBAC-binding wilt maken, moet u eerst de object-ID van Azure AD ophalen. 

1. Meld u aan bij de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Voer in het zoek veld boven aan de pagina `Azure Active Directory`in. 

1. Klik op `Enter`.

1. Selecteer in het menu **beheren** de optie **gebruikers**.

1. Zoek in het veld naam naar uw account.

1. Selecteer in de kolom **naam** de koppeling naar uw account.

1. Kopieer de **object-id**in het gedeelte **identiteit** .

    ![Kopieer de Azure AD-object-ID.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC-binding maken

In deze sectie maakt u een functie binding of cluster functie binding in AKS. 

Sla het volgende playbook op als `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Vervang de tijdelijke aanduiding `&lt;your-aad-account>` door de [object-id](#get-the-azure-ad-object-id)van uw Azure AD-Tenant.

Sla de volgende Playbook op die uw nieuwe rol implementeert naar AKS-als `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>De voorbeeld Playbook uitvoeren

In deze sectie vindt u de volledige voorbeeld Playbook die de taken aanroept die in dit artikel worden gemaakt. 

Sla het volgende playbook op als `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Vervang in het gedeelte `vars` de volgende tijdelijke aanduidingen door uw Azure AD-gegevens:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Voer de volledige Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>De resultaten controleren

In deze sectie gebruikt u kubectl om een lijst te maken met de knoop punten die in dit artikel worden gemaakt.

Voer de volgende opdracht in bij een terminal prompt:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Met deze opdracht wordt u naar een verificatie pagina geleid. Meld u aan met uw Azure-account.

Na verificatie worden de knoop punten op vergelijk bare wijze in kubectl weer gegeven als de volgende resultaten:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla de volgende code op als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)
