---
title: Problemen oplossen met OpenShift Container Platform 3.11-implementatie in Azure
description: Problemen oplossen met de implementatie van OpenShift Container Platform 3.11 in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: bd83a1ca731d81edb76a3c1bc07113ce96adb9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066594"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Problemen oplossen met OpenShift Container Platform 3.11-implementatie in Azure

Als het OpenShift-cluster niet wordt geïmplementeerd, biedt de Azure-portal een foutuitvoer. De output kan moeilijk te lezen zijn, waardoor het moeilijk is om het probleem te identificeren. Scan deze uitvoer snel naar exitcode 3, 4 of 5. Het volgende geeft informatie over deze drie exitcodes:

- Exit code 3: Uw Red Hat Abonnement Gebruikersnaam / Wachtwoord of Organisatie-ID / Activeringssleutel is onjuist
- Exit code 4: Je Red Hat Pool ID is onjuist of er zijn geen rechten beschikbaar
- Exitcode 5: Kan docker thin poolvolume niet inrichten

Voor alle andere exitcodes maakt u via ssh verbinding met de host(s) om de logbestanden te bekijken.

**OpenShift Container Platform 3.11**

SSH aan de ansible playbook host. Gebruik de bastionhost voor de sjabloon of de Marketplace-aanbieding. Vanuit het bastion u SSH naar alle andere knooppunten in het cluster (master, infra, CNS, compute). Je moet root zijn om de logbestanden te bekijken. Root is standaard uitgeschakeld voor SSH-toegang, dus gebruik root niet naar SSH naar andere knooppunten.

**OKD**

SSH aan de ansible playbook host. Gebruik de master-0-host voor de OKD-sjabloon (versie 3.9 en eerder). Gebruik de bastionhost voor de OKD-sjabloon (versie 3.10 en hoger). Van de ansible playbook host, u SSH naar alle andere knooppunten in het cluster (master, infra, CNS, compute). Je moet root (sudo su -) om de log bestanden te bekijken. Root is standaard uitgeschakeld voor SSH-toegang, dus gebruik root niet naar SSH naar andere knooppunten.

## <a name="log-files"></a>Logboekbestanden

De logbestanden (stderr en stdout) voor de `/var/lib/waagent/custom-script/download/0` hostvoorbereidingsscripts bevinden zich op alle hosts. Als er een fout is opgetreden tijdens de voorbereiding van de host, bekijkt u deze logboekbestanden om de fout te bepalen.

Als de voorbereidingsscripts succesvol zijn verlopen, moeten de logbestanden in de `/var/lib/waagent/custom-script/download/1` map van de ansible playbook-host worden onderzocht. Als de fout is opgetreden tijdens de daadwerkelijke installatie van OpenShift, wordt de fout weergegeven in het stdout-bestand. Gebruik deze informatie om contact op te nemen met Ondersteuning voor verdere hulp.

Voorbeelduitvoer

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

De meest voorkomende fouten tijdens de installatie zijn:

1. Private key heeft wachtwoordzin
2. Sleutelkluisgeheim met privésleutel is niet correct gemaakt
3. Servicehoofdreferenties zijn onjuist ingevoerd
4. Serviceprincipal heeft geen toegang tot de brongroep

### <a name="private-key-has-a-passphrase"></a>Private Key heeft een wachtwoordzin

U ziet een fout dat toestemming is geweigerd voor ssh. ssh naar de ansible playbook host om te controleren op een wachtwoordzin op de prive-sleutel.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Sleutelkluisgeheim met privésleutel is niet correct gemaakt

De privésleutel wordt gekopieerd naar de ansible playbook host - ~/.ssh/id_rsa. Controleer of dit bestand juist is. Test door een SSH-sessie te openen naar een van de clusterknooppunten van de ansible playbook-host.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Servicehoofdreferenties zijn onjuist ingevoerd

Bij het verstrekken van de invoer aan de sjabloon of Marketplace-aanbieding werd de onjuiste informatie verstrekt. Zorg ervoor dat u de juiste appId (clientId) en wachtwoord (clientSecret) gebruikt voor de serviceprincipal. Controleer dit door de volgende azure cli-opdracht uit te geven.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Serviceprincipal heeft geen toegang tot de brongroep

Als de Azure-cloudprovider is ingeschakeld, moet de gebruikte serviceprincipal toegang hebben tot de brongroep. Controleer dit door de volgende azure cli-opdracht uit te geven.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Extra hulpmiddelen

Voor sommige fouten u ook de volgende opdrachten gebruiken om meer informatie te krijgen:

1. systemctl \<status service>
2. journalctl -xe
