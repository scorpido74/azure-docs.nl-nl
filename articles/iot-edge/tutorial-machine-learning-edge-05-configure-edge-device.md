---
title: 'Zelf studie: IoT Edge apparaat-Machine Learning configureren op Azure IoT Edge'
description: In deze zelf studie configureert u een virtuele Azure-machine waarop Linux wordt uitgevoerd als een Azure IoT Edge apparaat dat als transparante gateway fungeert.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5be7b66a51113121ed755d8ad9cea3518577f2e7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706955"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Zelf studie: een IoT Edge apparaat configureren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel wordt een virtuele Azure-machine met Linux zodanig geconfigureerd dat deze een Azure IoT Edge apparaat is dat als transparante gateway fungeert. Met de transparante gateway configuratie kunnen apparaten via de gateway verbinding maken met Azure IoT Hub zonder te weten dat de gateway bestaat. Op hetzelfde moment is een gebruiker met de apparaten in IoT Hub niet op de hoogte van het tussenliggende gateway apparaat. Uiteindelijk gebruiken we de transparante gateway om Edge Analytics toe te voegen aan ons systeem door IoT Edge modules toe te voegen aan de gateway.

De stappen in dit artikel worden doorgaans uitgevoerd door een Cloud ontwikkelaar.

## <a name="generate-certificates"></a>Certificaten genereren

Een apparaat kan alleen als gateway functioneren als er een veilige verbinding kan worden gemaakt met downstream-apparaten. Met Azure IoT Edge kunt u een open bare-sleutel infrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval kunnen we een downstream-apparaat verbinding laten maken met een IoT Edge apparaat dat als transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het IoT Edge apparaat bevestigen. Zie voor meer informatie over hoe IoT Edge-apparaten certificaten gebruiken [Azure IOT Edge details van certificaat gebruik](iot-edge-certs.md).

In deze sectie maken we de zelfondertekende certificaten met behulp van een docker-installatie kopie die we vervolgens bouwen en uitvoeren. We hebben ervoor gekozen om een docker-installatie kopie te gebruiken om deze stap te volt ooien omdat het aantal stappen dat nodig is voor het maken van de certificaten op de Windows-ontwikkel computer aanzienlijk wordt gereduceerd. Zie [certificaten genereren met Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) voor meer informatie over het maken van de certificaten zonder een container te gebruiken. Het [genereren van certificaten met Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) bevat de set instructies die we hebben geautomatiseerd met de docker-installatie kopie.

1. Meld u aan bij de virtuele machine voor ontwikkeling.

2. Open een opdracht regel prompt en voer de volgende opdracht uit om een map op de virtuele machine te maken.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Start **docker voor Windows** vanuit het menu Start van Windows.

4. Open Visual Studio Code.

5. Selecteer **bestand** > **map openen...** en kies **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Klik met de rechter muisknop op de dockerfile en kies **installatie kopie maken**.

7. Accepteer in het dialoog venster de standaard waarde voor de naam van de installatie kopie en de tag: **createcertificates: meest recent**.

8. Wacht tot de build is voltooid.

    > [!NOTE]
    > Mogelijk wordt er een waarschuwing weer gegeven over een ontbrekende open bare sleutel. Het is veilig om deze waarschuwing te negeren. Op dezelfde manier wordt een beveiligings waarschuwing weer gegeven waarin u de machtigingen voor uw installatie kopie kunt controleren/opnieuw instellen. deze installatie kopie kan veilig worden genegeerd.

9. Voer in het venster Visual Studio code Terminal de container createcertificates uit.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vraagt om toegang tot het station **c:\\** . Selecteer **delen**.

11. Geef uw referenties op wanneer u hierom wordt gevraagd.

12. Nadat de container is uitgevoerd, controleert u de volgende bestanden in **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\-certificaten\\Azure-IOT-test-only. root. ca. cert. pem
    * c:\\edgeCertificates\\-certificaten\\New-Edge-Device-Full-chain. cert. pem
    * c:\\edgeCertificates\\-certificaten\\New-Edge-device. cert. pem
    * c:\\edgeCertificates\\-certificaten\\New-Edge-device. cert. pfx
    * c:\\edgeCertificates\\privé\\New-Edge-device. key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Certificaten uploaden naar Azure Key Vault

Om onze certificaten veilig op te slaan en ze toegankelijk te maken vanaf meerdere apparaten, zullen we de certificaten uploaden naar Azure Key Vault. Zoals u kunt zien in de bovenstaande lijst, hebben we twee typen certificaat bestanden: PFX en PEM. De PFX wordt behandeld als Key Vault certificaten die worden geüpload naar Key Vault. De PEM-bestanden zijn tekst zonder opmaak en wij behandelen ze als Key Vault geheimen. We gebruiken de Key Vault gekoppeld aan de Azure Machine Learning-werk ruimte die we hebben gemaakt door de [Azure notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)uit te voeren.

1. Navigeer vanuit het [Azure Portal](https://portal.azure.com)naar uw Azure machine learning-werk ruimte.

2. Ga naar de pagina overzicht van de werk ruimte Azure Machine Learning en zoek de naam van de **Key Vault**.

    ![Sleutel kluis naam kopiëren](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Upload de certificaten op de ontwikkel computer naar Key Vault. Vervang **\<subscriptionId\>** en **\<sleutel kluisnaam\>** door de gegevens van uw resource.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Meld u aan bij Azure als u hierom wordt gevraagd.

5. Het script wordt gedurende enkele minuten uitgevoerd met uitvoer met daarin de nieuwe Key Vault vermeldingen.

    ![Key Vault script uitvoer](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Een IoT Edge-apparaat maken

Als u een Azure IoT Edge apparaat wilt verbinden met een IoT-hub, maakt u eerst een identiteit voor het apparaat in de hub. We nemen de connection string van de apparaat-id in de Cloud en gebruiken deze om de runtime op het IoT Edge apparaat te configureren. Zodra het apparaat is geconfigureerd en verbinding maakt met de hub, kunnen we modules implementeren en berichten verzenden. We kunnen ook de configuratie van het fysieke IoT Edge apparaat wijzigen door de configuratie van de overeenkomstige apparaat-id in IoT hub te wijzigen.

Voor deze zelf studie maken we de nieuwe apparaat-id met Visual Studio code. U kunt deze stappen ook uitvoeren met behulp van de [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)of [Azure cli](how-to-register-device.md#register-with-the-azure-cli).

1. Open Visual Studio code op de ontwikkel computer.

2. Open het frame **Azure IOT Hub-apparaten** in de weer gave Visual Studio code Explorer.

3. Klik op het weglatings teken en selecteer **IOT edge apparaat maken**.

4. Geef het apparaat een naam. Voor het gemak gebruiken we **aaTurbofanEdgeDevice** zodat deze worden gesorteerd op alle client apparaten die we eerder hebben gemaakt via de apparaat-harnas om de test gegevens te verzenden.

5. Het nieuwe apparaat wordt weer gegeven in de lijst met apparaten.

    ![Nieuwe aaTurbofanEdgeDevice weer geven in VS code Explorer](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Virtuele Azure-machine implementeren

We gebruiken de [Azure IOT Edge op Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) -installatie kopie van de Azure Marketplace om ons IOT edge-apparaat voor deze zelf studie te maken. Met de Azure IoT Edge op de Ubuntu-installatie kopie worden de meest recente Azure IoT Edge runtime en de bijbehorende afhankelijkheden van het opstarten geïnstalleerd. We implementeren de virtuele machine met behulp van een Power shell-script, `Create-EdgeVM.ps1`; een resource manager-sjabloon, `IoTEdgeVMTemplate.json`; en een shell script `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Programmatische implementatie inschakelen

Als u de installatie kopie van de Marketplace in een implementatie met een script wilt gebruiken, moet u de installatie kopie op een programmatische manier implementeren.

1. Meld u aan bij Azure Portal.

1. Selecteer **Alle services**.

1. Voer in de zoek balk **Marketplace**in en selecteer deze.

1. Voer in de zoek balk de optie **Azure IOT Edge in op Ubuntu**.

1. Selecteer de **software die u wilt implementeren via een programma?** De Hyper link aan de slag.

1. Selecteer de knop **inschakelen** en vervolgens **Opslaan**.

    ![Programmatische implementatie voor VM inschakelen](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. U ziet een melding dat de taak is voltooid.

### <a name="create-virtual-machine"></a>Virtuele machine maken

Voer vervolgens het script uit om de virtuele machine voor uw IoT Edge-apparaat te maken.

1. Open een Power shell-venster en navigeer naar de **EdgeVM** -map.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Voer het script uit om de virtuele machine te maken.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Wanneer u hierom wordt gevraagd, geeft u waarden op voor elke para meter. Voor het abonnement, de resource groep en de locatie raden we u aan om hetzelfde te gebruiken als voor alle resources in deze zelf studie.

    * **Azure-abonnements-id**: gevonden in de Azure Portal
    * **Naam van resource groep**: naam voor het onthouden van het groeperen van de resources voor deze zelf studie
    * **Locatie**: Azure-locatie waar de virtuele machine wordt gemaakt. Bijvoorbeeld westus2 of northeurope. Zie alle [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie.
    * **AdminUsername**: de naam voor het beheerders account dat u gebruikt om u aan te melden bij de virtuele machine
    * **AdminPassword**: het wacht woord dat moet worden ingesteld voor de AdminUsername op de virtuele machine

4. Als u wilt dat het script de virtuele machine kan instellen, moet u zich aanmelden bij Azure met de referenties die zijn gekoppeld aan het Azure-abonnement dat u gebruikt.

5. Het script bevestigt de gegevens voor het maken van de virtuele machine. Selecteer **y** of **Enter** om door te gaan.

6. Het script wordt gedurende enkele minuten uitgevoerd, omdat de volgende stappen worden uitgevoerd:

    * De resource groep maken als deze nog niet bestaat
    * De virtuele machine maken
    * NSG-uitzonde ringen voor de VM toevoegen voor poort 22 (SSH), 5671 (AMQP), 5672 (AMPQ) en 443 (SSL)
    * De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)installeren)

7. Het script voert de SSH-connection string uit om verbinding te maken met de virtuele machine. Kopieer de connection string voor de volgende stap.

    ![SSH-connection string voor VM kopiëren](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

In de volgende secties wordt de virtuele machine van Azure geconfigureerd die we hebben gemaakt. De eerste stap is om verbinding te maken met de virtuele machine.

1. Open een opdracht prompt en plak de SSH-connection string die u hebt gekopieerd uit de script uitvoer. Voer uw eigen gegevens in voor gebruikers naam, achtervoegsel en regio volgens de waarden die u hebt opgegeven voor het Power shell-script in de vorige sectie.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Wanneer u wordt gevraagd om de authenticiteit van de host te valideren, typt u **Ja** en selecteert u **Enter**.

3. Geef uw wacht woord op wanneer u hierom wordt gevraagd.

4. In Ubuntu wordt een welkomst bericht weer gegeven. vervolgens ziet u een prompt als `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Key Vault certificaten downloaden

Eerder in dit artikel hebben we certificaten geüpload naar Key Vault om ze beschikbaar te maken voor het IoT Edge apparaat en het Leaf-apparaat. Dit is een downstream-apparaat dat gebruikmaakt van het IoT Edge-apparaat als een gateway om te communiceren met IoT Hub. Verderop in de zelf studie wordt het blad apparaat behandeld. In deze sectie downloadt u de certificaten naar het IoT Edge-apparaat.

1. Meld u vanuit de SSH-sessie op de virtuele Linux-machine aan bij Azure met de Azure CLI.

    ```bash
    az login
    ```

1. U wordt gevraagd een browser te openen om te <https://microsoft.com/devicelogin> en een unieke code op te geven. U kunt deze stappen uitvoeren op uw lokale computer. Sluit het browser venster wanneer u klaar bent met verifiëren.

1. Wanneer u bent geverifieerd, wordt de virtuele Linux-machine aangemeld en worden uw Azure-abonnementen weer geven.

1. ASet het Azure-abonnement dat u wilt gebruiken voor Azure CLI-opdrachten.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Maak een map op de virtuele machine voor de certificaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Down load de certificaten die u hebt opgeslagen in de sleutel kluis: New-Edge-Device-Full-chain. cert. pem, New-Edge-device. key. pem en Azure-IOT-test-only. root. ca. cert. pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>De configuratie van de IoT Edge-apparaatconfiguratie bijwerken

De IoT Edge runtime gebruikt het bestand/etc/iotedge/config.yaml om de configuratie te behouden. Er moeten drie stukjes gegevens worden bijgewerkt in dit bestand:

* **Apparaat Connection String**: de Connection String van de identiteit van dit apparaat in IOT hub
* **Certificaten:** de certificaten die moeten worden gebruikt voor verbindingen die zijn gemaakt met downstream-apparaten
* **Hostnaam:** de Fully QUALIFIED domain name (FQDN) van de virtuele machine IOT edge apparaat.

De *Azure IOT Edge op de Ubuntu* -installatie kopie die is gebruikt voor het maken van de IOT Edge VM, wordt geleverd met een shell script dat config. yaml bijwerkt met de Connection String.

1. Klik in Visual Studio code met de rechter muisknop op het apparaat IoT Edge en selecteer **apparaat verbindings reeks kopiëren**.

    ![connection string kopiëren uit Visual Studio code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Voer in uw SSH-sessie de opdracht uit om het bestand config. yaml bij te werken met uw apparaat connection string.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vervolgens worden de certificaten en de hostnaam bijgewerkt door de configuratie. yaml rechtstreeks te bewerken.

1. Open het bestand config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Werk de sectie certificaten van config. yaml bij door de eerste `#` te verwijderen en het pad zodanig in te stellen dat het bestand eruitziet als in het volgende voor beeld:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Zorg ervoor dat de "certificaten:" geen witruimte heeft en dat elk van de certificaten wordt voorafgegaan door twee spaties.

    Als u met de rechter muisknop op nano klikt, wordt de inhoud van het klem bord op de huidige cursor positie geplakt. Als u de teken reeks wilt vervangen, gebruikt u de pijlen voor het toetsen bord om te navigeren naar de teken reeks die u wilt vervangen, verwijdert u de teken reeks en klikt u met de rechter muisknop om uit de buffer te plakken.

3. Ga in het Azure Portal naar de virtuele machine. Kopieer de DNS-naam (FQDN van de computer) uit de sectie **overzicht** .

4. Plak de FQDN in de sectie hostname van het bestand config. yml. Zorg ervoor dat de naam alleen uit kleine letters bestaat.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Sla het bestand op en sluit het (`Ctrl + X`, `Y``Enter`).

6. Start de iotedge-daemon opnieuw op.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Controleer de status van de IoT Edge daemon (na de opdracht, typt u ': q ' om af te sluiten).

    ```bash
    systemctl status iotedge
    ```

8. Als er fouten worden weer geven (met een gekleurd bericht met een\[fout\]) in de status, raadpleegt u daemon-logboeken voor gedetailleerde informatie over de fout.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Volgende stappen

We hebben zojuist een Azure-VM geconfigureerd als Azure IoT Edge transparante gateway. We zijn begonnen met het genereren van test certificaten, die we hebben geüpload naar Azure Key Vault. We hebben vervolgens een script en Resource Manager-sjabloon gebruikt om de virtuele machine te implementeren met de installatie kopie ' Ubuntu Server 16,04 LTS + Azure IoT Edge runtime ' van Azure Marketplace. Het script duurde de extra stap voor het installeren van de Azure CLI ([Azure cli installeren met apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Met de virtuele machine die is aangesloten via SSH, zijn aangemeld bij Azure, gedownloade certificaten van Key Vault en verschillende updates voor de configuratie van de IoT Edge runtime gemaakt door het bestand config. yaml bij te werken. Zie [hoe een IOT edge apparaat kan worden gebruikt als een gateway](iot-edge-as-gateway.md)voor meer informatie over het gebruik van IOT Edge als een gateway. Voor meer informatie over het configureren van een IoT Edge apparaat als een transparante gateway raadpleegt [u een IOT edge apparaat configureren om te fungeren als een transparante gateway](how-to-create-transparent-gateway.md).

Ga door naar het volgende artikel om IoT Edge-modules te bouwen.

> [!div class="nextstepaction"]
> [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md)
