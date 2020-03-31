---
title: Taken uitvoeren onder gebruikersaccounts - Azure Batch
description: Het is handig om het gebruikersaccount te kunnen configureren waaronder u een taak wilt uitvoeren. Meer informatie over de typen gebruikersaccounts en hoe u deze configureren.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252270"
---
> [!NOTE] 
> De gebruikersaccounts die in dit artikel worden besproken, verschillen van gebruikersaccounts die om veiligheidsredenen worden gebruikt voor Extern bureaublad-protocol (RDP) of Secure Shell (SSH). 
>
> Zie [Extern bureaublad gebruiken om een Linux-vm in Azure te](../virtual-machines/virtual-machines-linux-use-remote-desktop.md)gebruiken als u verbinding wilt maken met een knooppunt waarop de virtuele Linux-machineconfiguratie wordt uitgevoerd. Zie Verbinding maken met [een Windows Server VM](../virtual-machines/windows/connect-logon.md)als u verbinding wilt maken met knooppunten met Windows Via RDP.<br /><br />
> Zie Verbinding met extern bureaublad inschakelen voor een [rol in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)als u verbinding wilt maken met een knooppunt waarop de configuratie van de cloudservice wordt uitgevoerd via RDP.
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Taken uitvoeren onder gebruikersaccounts in Batch

Een taak in Azure Batch wordt altijd uitgevoerd onder een gebruikersaccount. Taken worden standaard uitgevoerd onder standaardgebruikersaccounts, zonder beheerdersmachtigingen. Deze standaardgebruikersaccountinstellingen zijn doorgaans voldoende. Voor bepaalde scenario's is het echter handig om het gebruikersaccount te kunnen configureren waaronder u een taak wilt uitvoeren. In dit artikel worden de typen gebruikersaccounts besproken en hoe u deze configureren voor uw scenario.

## <a name="types-of-user-accounts"></a>Typen gebruikersaccounts

Azure Batch biedt twee typen gebruikersaccounts voor het uitvoeren van taken:

- **Accounts voor automatische gebruikers.** Accounts voor automatische gebruikers zijn ingebouwde gebruikersaccounts die automatisch worden gemaakt door de Batch-service. Taken worden standaard uitgevoerd onder een automatisch gebruikersaccount. U de specificatie voor automatische gebruikers configureren voor een taak om aan te geven onder welk automatisch gebruikersaccount een taak moet worden uitgevoerd. Met de specificatie voor automatische gebruikers u het hoogteniveau en de reikwijdte opgeven van het automatisch gebruikersaccount dat de taak uitvoert. 

- **Een benoemd gebruikersaccount.** U een of meer benoemde gebruikersaccounts voor een groep opgeven wanneer u de groep maakt. Elk gebruikersaccount wordt gemaakt op elk knooppunt van de groep. Naast de accountnaam geeft u het wachtwoord van het gebruikersaccount, het hoogteniveau en, voor Linux-pools, de privésleutel van ssh op. Wanneer u een taak toevoegt, u het benoemde gebruikersaccount opgeven waaronder die taak moet worden uitgevoerd.

> [!IMPORTANT] 
> De Batch-serviceversie 2017-01-01.4.0 introduceert een baanbrekende wijziging die vereist dat u uw code bijwerkt om die versie te bellen. Als u code migreert van een oudere versie van Batch, moet u er rekening mee houden dat de eigenschap **runElevated** niet langer wordt ondersteund in de REST API- of Batch-clientbibliotheken. Gebruik de eigenschap nieuwe **userIdentity** van een taak om hoogteniveau op te geven. Zie de sectie getiteld [Uw code bijwerken naar de nieuwste batchclientbibliotheek](#update-your-code-to-the-latest-batch-client-library) voor snelle richtlijnen voor het bijwerken van uw batchcode als u een van de clientbibliotheken gebruikt.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Toegang tot gebruikersaccounts tot bestanden en mappen

Zowel een automatisch gebruikersaccount als een benoemd gebruikersaccount hebben lees-/schrijftoegang tot de werkmap, de gedeelde directory en de takenmap voor meerdere instanties. Beide typen accounts hebben leestoegang tot de start- en jobvoorbereidingsmappen.

Als een taak wordt uitgevoerd onder hetzelfde account dat is gebruikt voor het uitvoeren van een begintaak, heeft de taak lees-schrijftoegang tot de taakmap starten. Als een taak wordt uitgevoerd onder hetzelfde account als voor het uitvoeren van een taakvoorbereidingstaak, heeft de taak lees-schrijftoegang tot de taaklijst voor taakvoorbereiding. Als een taak wordt uitgevoerd onder een ander account dan de taak voor het starten of de taakvoorbereiding, heeft de taak alleen leestoegang tot de desbetreffende map.

Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md#files-and-directories)voor meer informatie over het openen van bestanden en mappen vanuit een taak.

## <a name="elevated-access-for-tasks"></a>Verhoogde toegang voor taken 

Het hoogteniveau van het gebruikersaccount geeft aan of een taak wordt uitgevoerd met verhoogde toegang. Zowel een automatisch gebruikersaccount als een benoemd gebruikersaccount kunnen worden uitgevoerd met verhoogde toegang. De twee opties voor hoogteniveau zijn:

- **Niet-beheerder:** De taak wordt uitgevoerd als een standaardgebruiker zonder verhoogde toegang. Het standaardhoogteniveau voor een batchgebruikersaccount is altijd **Niet-beheerder**.
- **Beheerder:** De taak wordt uitgevoerd als een gebruiker met verhoogde toegang en werkt met volledige beheerdersmachtigingen. 

## <a name="auto-user-accounts"></a>Accounts voor automatische gebruikers

Taken worden standaard uitgevoerd in Batch onder een automatisch gebruikersaccount, als standaardgebruiker zonder verhoogde toegang en met taakbereik. Wanneer de specificatie voor automatische gebruikers is geconfigureerd voor taakbereik, maakt de batchservice alleen een automatischgebruikersaccount voor die taak.

Het alternatief voor taakbereik is het bereik van de groep. Wanneer de specificatie voor automatische gebruikers voor een taak is geconfigureerd voor het bereik van de groep, wordt de taak uitgevoerd onder een automatisch gebruikersaccount dat beschikbaar is voor elke taak in de groep. Zie de sectie getiteld Een taak uitvoeren als de automatische gebruiker met een poolbereik voor meer informatie over het bereik van de groep.   

De standaardscope is anders op Windows- en Linux-knooppunten:

- In Windows-knooppunten worden taken standaard uitgevoerd onder taakbereik.
- Linux-knooppunten draaien altijd onder het bereik van de pool.

Er zijn vier mogelijke configuraties voor de specificatie van de automatische gebruiker, die elk overeenkomt met een uniek automatisch gebruikersaccount:

- Niet-beheerderstoegang met taakbereik (de standaardspecificatie voor automatische gebruikers)
- Beheerderstoegang (verhoogde) toegang met taakbereik
- Niet-beheerderstoegang met poolbereik
- Beheerderstoegang met poolbereik

> [!IMPORTANT] 
> Taken die onder taakbereik worden uitgevoerd, hebben de facto geen toegang tot andere taken op een knooppunt. Een kwaadwillende gebruiker met toegang tot het account kan deze beperking echter omzeilen door een taak in te dienen die wordt uitgevoerd met beheerdersbevoegdheden en toegang krijgt tot andere taakmappen. Een kwaadwillende gebruiker kan rdp of SSH ook gebruiken om verbinding te maken met een knooppunt. Het is belangrijk om de toegang tot uw Batch-accountsleutels te beschermen om een dergelijk scenario te voorkomen. Als u vermoedt dat uw account is gecompromitteerd, moet u uw sleutels regenereren.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Een taak uitvoeren als een automatische gebruiker met verhoogde toegang

U de specificatie voor automatische gebruikers configureren voor beheerdersbevoegdheden wanneer u een taak met verhoogde toegang moet uitvoeren. Een starttaak heeft bijvoorbeeld verhoogde toegang nodig om software op het knooppunt te installeren.

> [!NOTE] 
> In het algemeen is het het beste om verhoogde toegang alleen te gebruiken wanneer dat nodig is. Best practices raden aan om het minimale voorrecht toe te kennen dat nodig is om het gewenste resultaat te bereiken. Als een starttaak bijvoorbeeld software installeert voor de huidige gebruiker, in plaats van voor alle gebruikers, u mogelijk voorkomen dat u verhoogde toegang tot taken krijgt. U de specificatie voor automatische gebruikers configureren voor het bereik van de groep en niet-beheerderstoegang voor alle taken die onder hetzelfde account moeten worden uitgevoerd, inclusief de starttaak. 
>
>

In de volgende codefragmenten ziet u hoe u de specificatie voor automatische gebruikers configureert. In de voorbeelden wordt `Admin` het hoogteniveau `Task`ingesteld op en op het bereik . Taakbereik is de standaardinstelling, maar is hier opgenomen omwille van bijvoorbeeld.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Een taak uitvoeren als een automatische gebruiker met een poolbereik

Wanneer een knooppunt is ingericht, worden twee groepbrede automatische gebruikersaccounts gemaakt op elk knooppunt in de groep, een met verhoogde toegang en een zonder verhoogde toegang. Als u het bereik van de automatische gebruiker instelt om de ruimte voor een bepaalde taak te bundelen, wordt de taak uitgevoerd onder een van deze twee groepbrede automatische gebruikersaccounts. 

Wanneer u het poolbereik voor de automatische gebruiker opgeeft, worden alle taken die worden uitgevoerd met beheerderstoegang uitgevoerd onder hetzelfde automatische gebruikersaccount voor een hele groep. Taken die zonder beheerdersmachtigingen worden uitgevoerd, worden ook uitgevoerd onder één automatisch gebruikersaccount voor meerdere onderdelen. 

> [!NOTE] 
> De twee groepbrede automatische gebruikersaccounts zijn afzonderlijke accounts. Taken die onder het beheeraccount voor de hele groep worden uitgevoerd, kunnen geen gegevens delen met taken die onder het standaardaccount worden uitgevoerd en vice versa. 
>
>

Het voordeel van het uitvoeren onder hetzelfde auto-gebruikersaccount is dat taken gegevens kunnen delen met andere taken die op hetzelfde knooppunt worden uitgevoerd.

Het delen van geheimen tussen taken is een scenario waarin het uitvoeren van taken onder een van de twee groepbrede automatische gebruikersaccounts nuttig is. Stel dat een starttaak een geheim moet inrichten op het knooppunt dat andere taken kunnen gebruiken. U de Windows Data Protection API (DPAPI) gebruiken, maar hiervoor zijn beheerdersrechten vereist. In plaats daarvan u het geheim op gebruikersniveau beschermen. Taken die onder hetzelfde gebruikersaccount worden uitgevoerd, hebben toegang tot het geheim zonder verhoogde toegang.

Een ander scenario waarin u taken wilt uitvoeren onder een automatisch gebruikersaccount met een poolbereik, is een MPI-bestandsshare (Message Passing Interface). Een MPI-bestandsshare is handig wanneer de knooppunten in de MPI-taak aan dezelfde bestandsgegevens moeten werken. Het hoofdknooppunt maakt een bestandsshare waartoe de onderliggende knooppunten toegang hebben als ze onder hetzelfde auto-gebruikersaccount worden uitgevoerd. 

In het volgende codefragment wordt het bereik van de automatische gebruiker ingesteld op het bundelen van het bereik voor een taak in Batch .NET. Het hoogteniveau wordt weggelaten, zodat de taak wordt uitgevoerd onder het standaard zwembadbrede autogebruikersaccount.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Benoemde gebruikersaccounts

U benoemde gebruikersaccounts definiëren wanneer u een groep maakt. Een benoemd gebruikersaccount heeft een naam en wachtwoord die u opgeeft. U het hoogteniveau voor een benoemd gebruikersaccount opgeven. Voor Linux-knooppunten u ook een SSH-privésleutel leveren.

Er bestaat een benoemd gebruikersaccount op alle knooppunten in de groep en is beschikbaar voor alle taken die op die knooppunten worden uitgevoerd. U een willekeurig aantal benoemde gebruikers definiëren voor een groep. Wanneer u een taak of taakverzameling toevoegt, u opgeven dat de taak wordt uitgevoerd onder een van de benoemde gebruikersaccounts die in de groep zijn gedefinieerd.

Een benoemd gebruikersaccount is handig wanneer u alle taken in een taak onder hetzelfde gebruikersaccount wilt uitvoeren, maar deze wilt isoleren van taken die tegelijkertijd in andere taken worden uitgevoerd. U bijvoorbeeld voor elke taak een benoemde gebruiker maken en de taken van elke taak uitvoeren onder dat benoemde gebruikersaccount. Elke taak kan vervolgens een geheim delen met zijn eigen taken, maar niet met taken die in andere taken worden uitgevoerd.

U ook een benoemd gebruikersaccount gebruiken om een taak uit te voeren die machtigingen instelt voor externe bronnen, zoals bestandsshares. Met een benoemd gebruikersaccount bepaalt u de gebruikersidentiteit en u die gebruikersidentiteit gebruiken om machtigingen in te stellen.  

Named user accounts enable password-less SSH between Linux nodes. U een benoemd gebruikersaccount gebruiken met Linux-knooppunten die taken met meerdere instanties moeten uitvoeren. Elk knooppunt in de groep kan taken uitvoeren onder een gebruikersaccount dat is gedefinieerd in de hele groep. Zie [Meerdere\-instantietaken gebruiken om MPI-toepassingen uit te voeren](batch-mpi.md)voor meer informatie over taken met meerdere instantie.

### <a name="create-named-user-accounts"></a>Benoemde gebruikersaccounts maken

Als u benoemde gebruikersaccounts wilt maken in Batch, voegt u een verzameling gebruikersaccounts toe aan de groep. In de volgende codefragmenten ziet u hoe u benoemde gebruikersaccounts maakt in .NET, Java en Python. Deze codefragmenten laten zien hoe u zowel beheerders- als niet-beheerdersaccounts met de naam in een groep maken. De voorbeelden maken groepen met behulp van de cloudserviceconfiguratie, maar u gebruikt dezelfde aanpak bij het maken van een Windows- of Linux-groep met behulp van de configuratie van de virtuele machine.

#### <a name="batch-net-example-windows"></a>Voorbeeld van Batch .NET (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET voorbeeld (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Voorbeeld van Batch Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Voorbeeld van Batch Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Een taak uitvoeren onder een benoemd gebruikersaccount met verhoogde toegang

Als u een taak wilt uitvoeren als een verhoogde gebruiker, stelt u de eigenschap **UserIdentity** van `Admin`de taak in op een benoemd gebruikersaccount dat is gemaakt met de eigenschap **ElevationLevel** ingesteld op .

Dit codefragment geeft aan dat de taak moet worden uitgevoerd onder een benoemd gebruikersaccount. Dit benoemde gebruikersaccount is gedefinieerd in de groep toen de groep werd gemaakt. In dit geval is het benoemde gebruikersaccount gemaakt met beheerdersmachtigingen:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uw code bijwerken naar de nieuwste Batch-clientbibliotheek

De Batch-serviceversie 2017-01-01.4.0 introduceert een baanbrekende wijziging, waarbij de **eigenschap runElevated** die beschikbaar is in eerdere versies wordt vervangen door de eigenschap **userIdentity.** De volgende tabellen bieden een eenvoudige toewijzing die u gebruiken om uw code bij te werken uit eerdere versies van de clientbibliotheken.

### <a name="batch-net"></a>Batch .NET

| Als uw code wordt gebruikt...                  | Werk het bij aan....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`niet opgegeven | Geen update vereist                                                                                               |

### <a name="batch-java"></a>Batch Java

| Als uw code wordt gebruikt...                      | Werk het bij aan....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`niet opgegeven | Geen update vereist                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Als uw code wordt gebruikt...                      | Werk het bij aan....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Waar <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Waar <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`niet opgegeven | Geen update vereist                                                                                                                                  |


## <a name="next-steps"></a>Volgende stappen

* Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md)voor een diepgaand overzicht van Batch.
