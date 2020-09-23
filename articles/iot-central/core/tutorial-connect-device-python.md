---
title: 'Zelfstudie: een generieke Python-client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een apparaat met een Python-client-app verbindt met uw Azure IoT Central-toepassing. U maakt een apparaatsjabloon door een apparaatondersteuningsprofiel te importeren en weergaven toe te voegen waarmee u kunt communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 64b44fa2bdb9221d83715d3214da80e82d791e9b
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017622"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Zelfstudie: Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een Python-clienttoepassing verbindt met uw Azure IoT Central-toepassing. De Python-toepassing simuleert het gedrag van een omgevingssensorapparaat. U gebruikt een voorbeeld van een _apparaatondersteuningsprofiel_ om _een apparaatsjabloon_ te maken in IoT Central. U voegt weergaven toe aan de apparaatsjabloon zodat een operator met een apparaat kan communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatondersteuningsprofiel importeren om een apparaatsjabloon te maken.
> * Standaardweergaven en aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * Een apparaatsjabloon publiceren en een echt apparaat toevoegen aan uw IoT Central-toepassing.
> * De Python-apparaatcode maken, deze uitvoeren en kijken hoe deze verbinding maakt met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie bekijken die vanaf het apparaat wordt verzonden.
> * Een weergave gebruiken om apparaateigenschappen te beheren.
> * Synchrone en asynchrone opdrachten aanroepen om het apparaat te beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon **Aangepaste toepassing**. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md). De toepassing moet op of na 14-07-2020 zijn gemaakt.
* Een ontwikkelmachine waarop [Python](https://www.python.org/) versie 3.7 of hoger is geïnstalleerd. Voer `python3 --version` uit op de opdrachtprompt om uw versie te controleren. Python is beschikbaar voor een groot aantal verschillende besturingssystemen. In de instructies in deze zelfstudie wordt ervan uitgegaan dat u de opdracht **python3** uitvoert vanaf de Windows-opdrachtprompt.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Een Python-toepassing maken

In de volgende stappen ziet u hoe u een Python-clienttoepassing maakt die verbinding maakt met het echte apparaat dat u aan de toepassing hebt toegevoegd. Deze Python-toepassing simuleert het gedrag van een echt apparaat.

1. Ga in uw opdrachtregelprogramma naar de map `environmental-sensor` die u eerder hebt gemaakt.

1. Voer de volgende opdrachten uit om de vereiste bibliotheken te installeren:

    ```cmd
    pip install azure-iot-device
    ```

1. Maak een bestand met de naam **environmental_sensor.py** in de map `environmental-sensor`.

1. Voeg de volgende `import`-instructies toe aan het begin van het bestand **environmental_sensor.py**:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Voeg de volgende asynchrone functie `main` en variabelendeclaraties aan het bestand toe:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Werk de tijdelijke aanduidingen voor `{your Scope ID}`, `{your Device ID}` en `{your Primary Key}` bij met de waarden die u eerder hebt genoteerd. In een echte toepassing moet u deze informatie niet in de code van de toepassing vastleggen.

    Alle volgende functiedefinities en code zijn genest binnen de functie `main`.

1. Voeg de volgende twee functies toe binnen de functie `main` om het apparaat te registreren en te verbinden met uw IoT Central-toepassing. Registratie maakt gebruik van de Azure Device Provisioning Service:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Voeg de volgende twee functies toe binnen de functie `main` om het apparaat te registreren en te verbinden met uw IoT Central-toepassing:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    De namen van de telemetriegegevens (`temp` en `humid`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende functies toe binnen de functie `main` om opdrachten te verwerken die worden aangeroepen vanaf uw IoT Central-toepassing:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    De namen van de opdrachten (`blink`, `turnon`, `turnoff` en `rundiagnostics`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

    Op dit moment gebruikt IoT Central niet het antwoordschema dat is gedefinieerd in het apparaatondersteuningsprofiel. Voor een synchrone opdracht kan de nettolading van het antwoord een willekeurige geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retourneren, gevolgd door de gerapporteerde eigenschapsupdate wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschapsupdate is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de nettolading van het antwoord weergeven in de opdrachtgeschiedenis.

1. Voeg de volgende functies toe binnen de functie `main` om eigenschapsupdates te verwerken die zijn verzonden vanaf uw IoT Central-toepassing. Het bericht dat het apparaat verzendt als reactie op de [update van de beschrijfbare eigenschap](concepts-telemetry-properties-commands.md#writeable-property-types) moet de velden `av` en `ac` bevatten. Het veld `ad` is optioneel:

    ```python
      async def name_setting(value, version):
        await asyncio.sleep(1)
        print(f'Setting name value {value} - {version}')
        await device_client.patch_twin_reported_properties({'name' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      async def brightness_setting(value, version):
        await asyncio.sleep(5)
        print(f'Setting brightness value {value} - {version}')
        await device_client.patch_twin_reported_properties({'brightness' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      settings = {
        'name': name_setting,
        'brightness': brightness_setting
      }

      # define behavior for receiving a twin patch
      async def twin_patch_listener():
        while True:
          patch = await device_client.receive_twin_desired_properties_patch() # blocking
          to_update = patch.keys() & settings.keys()
          await asyncio.gather(
            *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
          )
    ```

    Wanneer de operator een schrijfbare eigenschap instelt in de IoT Central-toepassing, gebruikt de toepassing een door de apparaatdubbel gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een door de apparaatdubbel gerapporteerde eigenschap. Wanneer IoT Central de gerapporteerde eigenschapswaarde ontvangt, wordt de eigenschapsweergave bijgewerkt met de status **gesynchroniseerd**.

    De namen van de eigenschappen (`name` en `brightness`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende functies toe binnen de functie `main` om de toepassing te beheren:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true', 'processorArchitecture': 'ARM', 'swVersion': '1.0.0'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Sla het bestand **environmental_sensor.py** op.

## <a name="run-your-python-application"></a>Uw Python-toepassing uitvoeren

Als u de apparaatclienttoepassing wilt uitvoeren, voert u de volgende opdracht uit in uw opdrachtregelprogramma:

```cmd
python3 environmental_sensor.py
```

U kunt zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en begint met het verzenden van telemetrie:

![De clienttoepassing uitvoeren](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

![De clienttoepassing observeren](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Onbewerkte gegevens weergeven

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Volgende stappen

U, als apparaatontwikkelaar, kent nu de basisbeginselen van het maken van een apparaat met behulp van Python. Hierna volgen enkele voorgestelde volgende stappen:

* Lees [Wat zijn apparaatsjablonen?](./concepts-device-templates.md) voor meer informatie over de rol van apparaatsjablonen wanneer u uw apparaatcode implementeert.
* Lees [Verbinding maken met Azure IoT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IoT Central en hoe IoT Central apparaatverbindingen beveiligt.

Als u liever wilt doorgaan met de set zelfstudies over IoT Central en meer wilt leren over het bouwen van een IoT Central-oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)
