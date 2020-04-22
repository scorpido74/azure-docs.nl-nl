---
title: 'Zelfstudie: IoT Edge-apparaat configureren - Machine Learning op Azure IoT Edge'
description: In deze zelfstudie configureert u een Azure Virtual Machine met Linux als een Azure IoT Edge-apparaat dat fungeert als een transparante gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 353ed321ce3b6161b28bf67d852a81f809880603
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733010"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Zelfstudie: Een IoT Edge-apparaat configureren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel configureren we een Virtuele Azure-machine met Linux als een IoT Edge-apparaat dat fungeert als een transparante gateway. Met een transparante gatewayconfiguratie kunnen apparaten verbinding maken met Azure IoT Hub via de gateway zonder te weten dat de gateway bestaat. Tegelijkertijd is een gebruiker die interactie heeft met de apparaten in Azure IoT Hub niet op de hoogte van het tussenliggende gateway-apparaat. Uiteindelijk zullen we edge analytics toevoegen aan ons systeem door IoT Edge-modules toe te voegen aan de transparante gateway.

De stappen in dit artikel worden meestal uitgevoerd door een cloudontwikkelaar.

## <a name="create-certificates"></a>Certificaten maken

Om een apparaat als gateway te laten functioneren, moet het veilig verbinding kunnen maken met downstream-apparaten. Met Azure IoT Edge u een openbare sleutelinfrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval staan we een downstream IoT-apparaat toe om verbinding te maken met een IoT Edge-apparaat dat fungeert als een transparante gateway. Om een redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het IoT Edge-apparaat bevestigen. Zie Azure [IoT Edge-certificaatgebruiksgegevens](iot-edge-certs.md)voor meer informatie over hoe IoT Edge-apparaten certificaten gebruiken.

In deze sectie maken we de zelfondertekende certificaten met behulp van een Docker-afbeelding die we vervolgens bouwen en uitvoeren. We hebben ervoor gekozen om een Docker-afbeelding te gebruiken om deze stap te voltooien, omdat het aantal stappen dat nodig is om de certificaten op de Windows-ontwikkelingsmachine te maken aanzienlijk vermindert. Zie [Democertificaten maken om iot edge-apparaatfuncties te testen](how-to-create-test-certificates.md) om te begrijpen wat we hebben geautomatiseerd met de Docker-afbeelding.

1. Meld u aan bij uw ontwikkel-VM.

2. Maak een nieuwe map met `c:\edgeCertificates`het pad en de naam .

3. Als deze nog niet actief is, start u **Docker voor Windows** in het menu Windows Start.

4. Open Visual Studio Code.

5. Selecteer **Map bestand** > **openen...** en kies **C:\\bron\\\\IoTEdgeAndMlSample CreateCertificates**.

6. Klik in het deelvenster Explorer met de rechtermuisknop op **dockerfile** en kies **Afbeelding maken**.

7. Accepteer in het dialoogvenster de standaardwaarde voor de afbeeldingsnaam en -tag: **maakcertificaten: laatste**.

    ![Certificaten maken in Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Wacht tot de build is voltooid.

    > [!NOTE]
    > Mogelijk ziet u een waarschuwing voor een ontbrekende openbare sleutel. Het is veilig om deze waarschuwing te negeren. Op dezelfde manier ziet u een beveiligingswaarschuwing die u aanbeveelt om machtigingen voor uw afbeelding te controleren/resetten, wat veilig is om te negeren voor deze afbeelding.

9. Voer in het terminalvenster van Visual Studio Code de container createcertificates uit.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker zal vragen om toegang tot de **c:\\ ** drive. Selecteer **Delen**.

11. Geef uw referenties op wanneer daarom wordt gevraagd.

12. Zodra de container klaar is met werken, controleert u op de volgende bestanden in **\\c: edgeCertificates**:

    * c:\\edgeCertificates\\\\certs azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\\\certs new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\\\certs new-edge-device.cert.pem
    * c:\\edgeCertificates\\\\certs new-edge-device.cert.pfx
    * c:\\\\edgeCertificaten\\private new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Certificaten uploaden naar Azure Key Vault

Om onze certificaten veilig op te slaan en toegankelijk te maken vanaf meerdere apparaten, uploaden we de certificaten naar Azure Key Vault. Zoals u zien in de bovenstaande lijst, hebben we twee soorten certificaatbestanden: PFX en PEM. We behandelen de PFX als Key Vault-certificaten die moeten worden geüpload naar Key Vault. De PEM bestanden zijn platte tekst en we zullen ze behandelen als Key Vault geheimen. We gebruiken de Key Vault die is gekoppeld aan de Azure Machine Learning-werkruimte die we hebben gemaakt door de [Azure-notitieblokken](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)uit te voeren.

1. Navigeer vanuit de [Azure-portal](https://portal.azure.com)naar uw Azure Machine Learning-werkruimte.

2. Zoek op de overzichtspagina van de Azure Machine Learning-werkruimte de naam van de **Key Vault.**

    ![Naam van de sleutelkluis kopiëren](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Upload de certificaten op uw ontwikkelingsmachine naar Key Vault. Vervang ** \<de\> naam van abonnementen** en ** \<keyvaults\> ** door uw brongegevens.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Meld u desgevraagd aan bij Azure.

5. Het script wordt enkele minuten uitgevoerd met uitvoer die de nieuwe Key Vault-vermeldingen bevat.

    ![Key Vault-scriptuitvoer](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Een IoT Edge-apparaat maken

Als we een Azure IoT Edge-apparaat willen aansluiten op een IoT-hub, maken we eerst een identiteit voor het apparaat in de hub. We nemen de verbindingstekenreeks van de apparaatidentiteit in de cloud en gebruiken deze om de runtime op ons IoT Edge-apparaat te configureren. Zodra een geconfigureerd apparaat verbinding maakt met de hub, kunnen we modules implementeren en berichten verzenden. We kunnen ook de configuratie van het fysieke IoT Edge-apparaat wijzigen door de bijbehorende apparaatidentiteit in de IoT-hub te wijzigen.

Voor deze zelfstudie maken we de nieuwe apparaatidentiteit met Behulp van Visual Studio Code. U deze stappen ook uitvoeren met de [Azure-portal](how-to-register-device.md#register-in-the-azure-portal)of [Azure CLI.](how-to-register-device.md#register-with-the-azure-cli)

1. Open Visual Studio Code op uw ontwikkelingsmachine.

2. Vouw het **Azure IoT Hub-frame** uit vanuit de weergave Visual Studio Code Explorer.

3. Klik op de ellips en selecteer **IoT Edge Device maken**.

4. Geef het apparaat een naam. Voor het gemak gebruiken we de naam **aaTurbofanEdgeDevice,** zodat het sorteert naar de top van de genoemde apparaten.

5. Het nieuwe apparaat wordt weergegeven in de lijst met apparaten.

    ![Bekijk nieuwe aaTurbofanEdgeDevice in VS Code explorer](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Azure Virtual Machine implementeren

We gebruiken de [Azure IoT Edge op Ubuntu-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) van de Azure Marketplace om ons IoT Edge-apparaat voor deze zelfstudie te maken. De Azure IoT Edge op Ubuntu-afbeelding installeert de nieuwste Azure IoT Edge-runtime en de afhankelijkheden ervan op het opstarten. We implementeren de VM met `Create-EdgeVM.ps1`behulp van een PowerShell-script; een resourcemanagersjabloon; `IoTEdgeVMTemplate.json` en een shell `install packages.sh`script, .

### <a name="enable-programmatic-deployment"></a>Programmatische implementatie inschakelen

Als u de afbeelding van de Marketplace wilt gebruiken in een gescripte implementatie, moeten we programmatische implementatie voor de afbeelding inschakelen.

1. Meld u aan bij Azure Portal.

1. Selecteer **Alle services**.

1. Voer in de zoekbalk Marketplace in en selecteer **deze**.

1. Voer in de zoekbalk van Marketplace **Azure IoT Edge op Ubuntu in**en selecteer deze.

1. Selecteer de hyperlink **Aan de slag** om programmatisch te implementeren.

1. Selecteer de knop **Inschakelen** en **sla op.**

    ![Programmatische implementatie voor VM inschakelen](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. U ziet een melding van succes.

### <a name="create-virtual-machine"></a>Virtuele machine maken

Voer vervolgens het script uit om de virtuele machine voor uw IoT Edge-apparaat te maken.

1. Open een PowerShell-venster en navigeer naar de **EdgeVM-map.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Voer het script uit om de virtuele machine te maken.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Geef desgevraagd waarden op voor elke parameter. Voor abonnement, resourcegroep en locatie raden we je aan hetzelfde te gebruiken als voor alle bronnen in deze zelfstudie.

    * **Azure-abonnements-id**: gevonden in de Azure-portal
    * **Naam resourcegroep:** gedenkwaardige naam voor het groeperen van de resources voor deze zelfstudie
    * **Locatie**: Azure-locatie waar de virtuele machine wordt gemaakt. Bijvoorbeeld Westus2 of Noord-Europa. Zie alle [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie.
    * **AdminUsername**: de naam voor het beheerdersaccount dat u gebruikt om u aan te melden bij de virtuele machine
    * **AdminPassword**: het wachtwoord in te stellen voor de AdminUsername op de virtuele machine

4. Als u wilt dat het script de VM wilt instellen, moet u zich aanmelden bij Azure met de referenties die zijn gekoppeld aan het Azure-abonnement dat u gebruikt.

5. Het script bevestigt de informatie voor het maken van uw VM. Selecteer **y** of **Enter** om door te gaan.

6. Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    * De resourcegroep maken als deze nog niet bestaat
    * De virtuele machine maken
    * NSG-uitzonderingen toevoegen voor de VM voor poorten 22 (SSH), 5671 (AMQP), 5672 (AMPQ) en 443 (TLS)
    * De [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Het script geeft de SSH-verbindingstekenreeks voor verbinding met de VM. Kopieer de verbindingstekenreeks voor de volgende stap.

    ![SSH-verbindingstekenreeks kopiëren voor VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Verbinding maken met uw IoT Edge-apparaat

De volgende secties configureren de Azure virtuele machine die we hebben gemaakt. De eerste stap is om verbinding te maken met de virtuele machine.

1. Open een opdrachtprompt en plak de SSH-verbindingstekenreeks die u hebt gekopieerd uit de scriptuitvoer. Voer uw eigen gegevens in voor gebruikersnaam, achtervoegsel en regio op basis van de waarden die u in de vorige sectie aan het PowerShell-script hebt geleverd.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Wanneer u wordt gevraagd de authenticiteit van de host te valideren, typt u **ja** en selecteert u **Enter**.

3. Geef uw wachtwoord op wanneer u daarom wordt gevraagd.

4. Ubuntu toont een welkomstbericht en dan `<username>@<machinename>:~$`moet je een prompt zien zoals .

## <a name="download-key-vault-certificates"></a>Key Vault-certificaten downloaden

Eerder in dit artikel hebben we certificaten geüpload naar Key Vault om ze beschikbaar te maken voor ons IoT Edge-apparaat en ons leaf-apparaat. Het leaf-apparaat is een downstream-apparaat dat het IoT Edge-apparaat gebruikt als gateway om te communiceren met IoT Hub.

We zullen later in de tutorial omgaan met het bladapparaat. Download in deze sectie de certificaten naar het IoT Edge-apparaat.

1. Meld je vanaf de SSH-sessie op de virtuele Linux-machine aan bij Azure met de Azure CLI.

    ```azurecli
    az login
    ```

1. U wordt gevraagd om een <https://microsoft.com/devicelogin> browser te openen om een unieke code te verstrekken. U deze stappen uitvoeren op uw lokale machine. Sluit het browservenster wanneer u klaar bent met authenticeren.

1. Wanneer u dit met succes verifieert, meldt de Linux-VM zich aan en geeft u een lijst van uw Azure-abonnementen.

1. Stel het Azure-abonnement in dat u wilt gebruiken voor Azure CLI-opdrachten.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Maak een map op de VM voor de certificaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Download de certificaten die u in de sleutelkluis hebt opgeslagen: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem en azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>De configuratie van het IoT Edge-apparaat bijwerken

De IoT Edge-runtime `/etc/iotedge/config.yaml` gebruikt het bestand om de configuratie voort te zetten. We moeten drie stukjes informatie in dit bestand bijwerken:

* **Tekenreeks apparaatverbinding:** de verbindingstekenreeks van de identiteit van dit apparaat in IoT-hub
* **Certificaten:** de certificaten die moeten worden gebruikt voor verbindingen met downstream-apparaten
* **Hostname:** de volledig gekwalificeerde domeinnaam (FQDN) van het VM IoT Edge-apparaat.

De *Azure IoT Edge op Ubuntu-afbeelding* die we hebben gebruikt om de IoT Edge VM te maken, wordt geleverd met een shellscript dat de config.yaml bijwerkt met de verbindingstekenreeks.

1. Klik in Visual Studio Code met de rechtermuisknop op het IoT Edge-apparaat en selecteer **Vervolgens Apparaatverbindingstekenreeks kopiëren**.

    ![Verbindingstekenreeks kopiëren vanuit Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Voer in uw SSH-sessie de opdracht uit om het config.yaml-bestand bij te werken met de tekenreeks van de apparaatverbinding.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vervolgens zullen we de certificaten en hostname bijwerken door de config.yaml direct te bewerken.

1. Open het config.yaml-bestand.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Werk de sectie certificaten van de config.yaml bij door de voorloop te `#` verwijderen en het pad in te stellen, zodat het bestand er als volgt uitziet:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Zorg ervoor dat de **certificaten:** lijn heeft geen voorafgaande witruimte en dat elk van de geneste certificaten is ingesprongen door twee spaties.

    Als u met de rechtermuisknop klikt in nano, plakt u de inhoud van het klembord op de huidige cursorpositie. Als u de tekenreeks wilt vervangen, gebruikt u de toetsenbordpijlen om naar de tekenreeks te navigeren die u wilt vervangen, verwijdert u de tekenreeks en klikt u met de rechtermuisknop om vanuit de buffer te plakken.

3. Navigeer in de Azure-portal naar uw virtuele machine. Kopieer de DNS-naam (FQDN van de machine) uit de sectie **Overzicht.**

4. Plak de FQDN in de hostname sectie van de config.yml. Zorg ervoor dat de naam is allemaal kleine letters.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Het bestand opslaan`Ctrl + X`en `Y` `Enter`sluiten ( , , ).

6. Start de iotedge daemon opnieuw.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Controleer de status van de IoT Edge Daemon (typ na de opdracht ":q" om af te sluiten).

    ```bash
    systemctl status iotedge
    ```

8. Als u fouten ziet (gekleurde\[\]tekst die vooraf is gefixeerd met FOUT" in de status Onderzoek daemon logs voor gedetailleerde foutinformatie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Volgende stappen

We zijn net klaar met het configureren van een Azure VM als Azure IoT Edge Transparent Gateway. We zijn begonnen met het genereren van testcertificaten die we hebben geüpload naar Azure Key Vault. Vervolgens hebben we een script- en Resource Manager-sjabloon gebruikt om de VM te implementeren met de afbeelding 'Ubuntu Server 16.04 LTS + Azure IoT Edge-runtime' van de Azure Marketplace. Met de VM up and running hebben we verbinding gemaakt via SSH, hebben we ons aangemeld bij Azure en certificaten gedownload van Key Vault. We hebben verschillende updates gemaakt voor de configuratie van de IoT Edge Runtime door het config.yaml-bestand bij te werken.

Zie Voor meer informatie [hoe een IoT Edge-apparaat als gateway kan worden gebruikt](iot-edge-as-gateway.md) en Een [IoT Edge-apparaat configureren om als transparante gateway te fungeren.](how-to-create-transparent-gateway.md)

Ga verder naar het volgende artikel om IoT Edge-modules te bouwen.

> [!div class="nextstepaction"]
> [Aangepaste IoT Edge-modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md)
