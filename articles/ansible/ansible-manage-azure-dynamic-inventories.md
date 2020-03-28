---
title: Zelfstudie - Dynamische inventarissen van uw Azure-resources configureren met Ansible
description: Informatie over het beheren van dynamische voorraden in Azure met Ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamische voorraad
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247860"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Zelfstudie: Dynamische inventarissen van uw Azure-resources configureren met Ansible

Ansible kan worden gebruikt om voorraadinformatie uit verschillende bronnen (met inbegrip van cloudbronnen zoals Azure) in een *dynamische voorraad* op te halen. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configureer twee virtuele testmachines. 
> * Tag een van de virtuele machines
> * Nginx installeren op de gelabelde virtuele machines
> * Een dynamische voorraad configureren met de geconfigureerde Azure-resources

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>De testVM's maken

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Maak een Azure-resourcegroep om de virtuele machines voor deze zelfstudie in op te slaan.

    > [!IMPORTANT]  
    > De Azure-resourcegroep die u in deze stap u maakt, moet een naam hebben die volledig uit kleine letters bestaat. Anders mislukt het genereren van de dynamische voorraad.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Maak twee virtuele Linux-machines in Azure met een van de volgende technieken:

    - **Ansible-playbook**: in het artikel [Een eenvoudige virtuele machine maken in Azure met Ansible](./ansible-create-vm.md) wordt geïllustreerd hoe u een virtuele machine maakt op basis van een Ansible-playbook. Als u een playbook gebruikt voor het definiëren van een van de of beide virtuele machines, moet u ervoor zorgen dat de SSH-verbinding wordt gebruikt in plaats van een wachtwoord.

    - **Azure CLI**: voer elk van de volgende opdrachten in de Cloud Shell uit om de twee virtuele machines te maken:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Een virtuele machine taggen

U kunt [tags gebruiken om uw Azure-resources te organiseren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) met behulp van door de gebruiker gedefinieerde categorieën. 

### <a name="using-ansible-version--28"></a>Ansible-versie < 2.8 gebruiken
Voer de volgende [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-opdracht in om de virtuele machine `ansible-inventory-test-vm1` met de sleutel `nginx` te taggen:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Ansible-versie >= 2,8 gebruiken
Voer de volgende [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-opdracht in om de virtuele machine `ansible-inventory-test-vm1` met de sleutel `Ansible=nginx` te taggen:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Een dynamische voorraad genereren

Zodra u uw virtuele machines hebt gedefinieerd (en getagd), is het tijd om de dynamische voorraad te genereren.

### <a name="using-ansible-version--28"></a>Ansible-versie < 2.8 gebruiken

Ansible biedt een Python-script met de naam [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) dat een dynamische voorraad van uw Azure-resources genereert. In de volgende stappen wordt het gebruik van het `azure_rm.py`-script om verbinding te maken met de twee virtuele testmachines van Azure behandeld:

1. Gebruik de GNU `wget`-opdracht om het `azure_rm.py`-script op te halen:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Gebruik de `chmod`-opdracht om de machtigingen voor toegang tot het `azure_rm.py`-script te wijzigen. De volgende opdracht maakt gebruik van de parameter `+x` om uitvoering van het opgegeven bestand (`azure_rm.py`) toe te staan:

    ```python
    chmod +x azure_rm.py
    ```

1. Gebruik de [ansible-opdracht](https://docs.ansible.com/ansible/2.4/ansible.html) om verbinding te maken met de resourcegroep: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Als u verbinding hebt, ziet u resultaten die vergelijkbaar zijn met de volgende uitvoer:

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Ansible versie >= 2.8

Vanaf Ansible 2.8 biedt Ansible een [Azure-plug-in voor dynamische voorraad.](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py) De volgende stappen lopen u door met behulp van de plug-in:

1. De voorraadplug-in vereist een configuratiebestand. Het configuratiebestand moet `azure_rm` eindigen in en `yml` `yaml`een extensie van een van beide of . Sla voor dit zelfstudievoorbeeld het `myazure_rm.yml`volgende draaiboek op als:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Voer de volgende opdracht uit om VM's in de resourcegroep te pingen:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Wanneer u de vorige opdracht uitvoert, u de volgende fout ontvangen:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Als u de fout 'hostsleutelverificatie' ontvangt, voegt u de volgende regel toe aan het ansible-configuratiebestand. Het ansible-configuratiebestand `/etc/ansible/ansible.cfg` bevindt zich op of `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Wanneer u het draaiboek uitvoert, ziet u resultaten die vergelijkbaar zijn met de volgende uitvoer:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>De VM-tag inschakelen

### <a name="if-youre-using-ansible--28"></a>Als u Ansible < 2.8 gebruikt,

- Zodra u een tag hebt ingesteld, moet u die tag "inschakelen". Een manier om een tag in te schakelen `AZURE_TAGS` is `export` door de tag te exporteren naar een omgevingsvariabele via de opdracht:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Voer de volgende opdracht uit:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    U ziet nu slechts één virtuele machine (de machine `AZURE_TAGS` waarvan de tag overeenkomt met de waarde die naar de omgevingsvariabele wordt geëxporteerd):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Als u Ansible >= 2,8 gebruikt

- Voer de `ansible-inventory -i myazure_rm.yml --graph` opdracht uit om de volgende uitvoer te krijgen:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- U ook de volgende opdracht uitvoeren om de verbinding met de Nginx-vm te testen:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Nginx instellen op de getagde virtuele machine

Het doel van tags is om de mogelijkheid in te schakelen snel en eenvoudig met subgroepen van uw virtuele machines te werken. Stel dat u Nginx bijvoorbeeld wilt installeren op virtuele machines waarop u een tag van `nginx` hebt toegewezen. In de volgende stappen ziet u hoe u dit gemakkelijk kunt doen:

1. Maak een `nginx.yml`bestand met de naam :

   ```console
   code nginx.yml
   ```

1. Plak de volgende voorbeeldcode in de editor:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Sla het bestand op en sluit de editor af.

1. Voer de playbook `ansible-playbook` uit met de opdracht:

   - Ansible < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Nadat u het draaiboek hebt uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Nginx-installatie testen

In deze sectie wordt één techniek geïllustreerd om te testen of Nginx op uw virtuele machine is geïnstalleerd.

1. Gebruik de opdracht [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) om het IP-adres van de virtuele machine `ansible-inventory-test-vm1` op te halen. De geretourneerde waarde (het IP-adres van de virtuele machine) wordt vervolgens als de parameter gebruikt voor de SSH-opdracht om verbinding te maken met de virtuele machine.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Terwijl u verbinding hebt met de virtuele machine `ansible-inventory-test-vm1`, voert u de opdracht [nginx - v](https://nginx.org/en/docs/switches.html) uit om te bepalen of Nginx is geïnstalleerd.

    ```console
    nginx -v
    ```

1. Zodra u de opdracht `nginx -v` uitvoert, ziet u de Nginx-versie (tweede regel) die aangeeft dat Nginx is geïnstalleerd.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Klik `<Ctrl>D` op de toetsenbordcombinatie om de SSH-sessie los te koppelen.

1. Het uitvoeren van de `ansible-inventory-test-vm2` voorgaande stappen voor de virtuele machine levert een informatief bericht op dat aangeeft waar u Nginx krijgen (wat impliceert dat u het op dit punt niet hebt geïnstalleerd):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Snelstart: Linux virtuele machines configureren in Azure met Ansible](./ansible-create-vm.md)