---
title: Zelfstudie - RBAC-rollen (role-based access control) configureren in Azure Kubernetes Service (AKS) met Ansible
description: Meer informatie over het gebruik van Ansible om RBAC te configureren in het AKS-cluster (Azure Kubernetes Service)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836963"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Zelfstudie: RBAC-rollen (role-based access control) configureren in Azure Kubernetes Service (AKS) met Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan worden geconfigureerd om [Azure Active Directory (AD)](/azure/active-directory/) te gebruiken voor gebruikersverificatie. Nadat u bent geconfigureerd, gebruikt u uw Azure AD-verificatietoken om u aan te melden bij het AKS-cluster. De RBAC kan worden gebaseerd op de identiteit van een gebruiker of directory group membership.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een AKS-cluster met Azure AD maken
> * Een RBAC-rol in het cluster configureren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **De RedHat OpenShift-bibliotheek installeren** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Azure AD configureren voor AKS-verificatie

Bij het configureren van Azure AD voor AKS-verificatie worden twee Azure AD-toepassingen geconfigureerd. Deze bewerking moet worden voltooid door een Azure-tenantbeheerder. Zie [Azure Active Directory integreren met AKS](/azure/aks/aad-integration#create-the-server-application)voor meer informatie. 

Haal bij de Azure-tenantbeheerder de volgende waarden op:

- Server-app geheim
- Server-app-id
- Client-app-id 
- Tenant-id

Deze waarden zijn nodig om het voorbeelddraaiboek uit te voeren.  

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

In deze sectie maakt u een AKS met de [Azure AD-toepassing](#configure-azure-ad-for-aks-authentication).

Hier volgen enkele belangrijke opmerkingen om rekening mee te houden bij het werken met de voorbeelddraaimap:

- Het draaiboek `ssh_key` `~/.ssh/id_rsa.pub`laadt van . Als u het wijzigt, gebruikt u de single-line indeling - te beginnen met "ssh-rsa" (zonder de aanhalingstekens).
- De `client_id` `client_secret` waarden en waarden `~/.azure/credentials`worden geladen uit , dat is de standaard referentiebestand. U deze waarden instellen op uw serviceprincipal of deze waarden laden vanuit omgevingsvariabelen:

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

## <a name="get-the-azure-ad-object-id"></a>De Azure AD-object-id

Als u een RBAC-binding wilt maken, moet u eerst de Azure AD-object-id oppakken. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Voer in het zoekveld boven aan `Azure Active Directory`de pagina . 

1. Klik op `Enter`.

1. Selecteer **Gebruikers**in het menu **Beheren** .

1. Zoek in het veld Naam naar uw account.

1. Selecteer **in** de kolom Naam de koppeling naar uw account.

1. Kopieer in de sectie **Identiteit** de **object-id**.

    ![Kopieer de Azure AD-object-id.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC-binding maken

In deze sectie maakt u een rolbinding of clusterrolbinding in AKS. 

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

Vervang `<your-aad-account>` de tijdelijke aanduiding door uw Azure AD-tenantobject-id . [Object ID](#get-the-azure-ad-object-id)

Sla het volgende draaiboek op - waarmee uw `aks-kube-deploy.yml`nieuwe rol wordt geïmplementeerd in AKS - als:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Het voorbeeldvan het voorbeelddraaiboek uitvoeren

In deze sectie vindt u de volledige voorbeelddraaimap waarin de taken in dit artikel worden aangeschreven. 

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

Vervang `vars` in de sectie de volgende tijdelijke aanduidingen door uw Azure AD-gegevens:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Voer het volledige draaiboek uit met de `ansible-playbook` opdracht:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>De resultaten verifiëren

In deze sectie gebruikt u de kubectllijst die de knooppunten in dit artikel maken.

Voer de volgende opdracht in bij een terminalprompt:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

De opdracht leidt u naar een verificatiepagina. Meld u aan met uw Azure-account.

Eenmaal geverifieerd, kubectl lijsten van de knooppunten op dezelfde manier als de volgende resultaten:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die in dit artikel zijn gemaakt. 

Sla de volgende `cleanup.yml`code op als :

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

Voer de playbook `ansible-playbook` uit met de opdracht:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)
