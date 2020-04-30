---
title: 'Zelf studie: een generieke python-client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een apparaat met een Python-Client-App verbindt met uw Azure IoT Central-toepassing. U maakt een sjabloon voor een apparaat door een mogelijkheidsprofiel te importeren en weer gaven toe te voegen waarmee u kunt communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6c44c81db78fa76eeaf4b7181cca34fb8e81523
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758184"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Zelf studie: een client toepassing maken en verbinden met uw Azure IoT Central-toepassing (python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingen bouwers en ontwikkel aars van apparaten.*

In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een python-client toepassing verbindt met uw Azure IoT Central-toepassing. Met de python-toepassing wordt het gedrag van een omgevings sensor apparaat gesimuleerd. U gebruikt een voor beeld van een _mogelijkheidsprofiel_ om een _sjabloon_ voor het apparaat te maken in IOT Central. U voegt weer gaven toe aan de sjabloon voor het apparaat om een operator in staat te stellen te communiceren met een apparaat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een mogelijkheidsprofiel om een sjabloon voor een apparaat te maken.
> * Standaard-en aangepaste weer gaven toevoegen aan een sjabloon voor een apparaat.
> * Een sjabloon voor een apparaat publiceren en een echt apparaat toevoegen aan uw IoT Central-toepassing.
> * Maak de python-apparaatcode en voer deze uit en zie verbinding maken met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie weer geven die vanaf het apparaat is verzonden.
> * Gebruik een weer gave om apparaateigenschappen te beheren.
> * U kunt synchrone en asynchrone opdrachten aanroepen om het apparaat te beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon voor **aangepaste toepassingen** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkel machine met [python](https://www.python.org/) versie 3,7 of hoger is geïnstalleerd. U kunt uitvoeren `python3 --version` op de opdracht regel om uw versie te controleren. Python is beschikbaar voor een groot aantal besturings systemen. In de instructies in deze zelf studie wordt ervan uitgegaan dat u de opdracht **python3** uitvoert vanaf de opdracht prompt van Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Een Python-toepassing maken

De volgende stappen laten zien hoe u een python-client toepassing maakt die verbinding maakt met het apparaat dat u aan de toepassing hebt toegevoegd. Met deze python-toepassing wordt het gedrag van een echt apparaat gesimuleerd.

1. Navigeer in uw opdracht regel omgeving naar de `environmental-sensor` map die u eerder hebt gemaakt.

1. Voer de volgende opdrachten uit om de vereiste bibliotheken te installeren:

    ```cmd
    pip install azure-iot-device
    ```

1. Maak in de map een bestand met de `environmental-sensor` naam **environmental_sensor. py** .

1. Voeg de volgende `import` -instructies toe aan het begin van het bestand **environmental_sensor. py** :

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

1. Voeg de volgende asynchrone `main` functie-en variabelen declaraties toe aan het bestand:

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

    Werk de tijdelijke `{your Scope ID}`aanduidingen `{your Device ID}`, en `{your Primary Key}` met de waarden die u eerder hebt genoteerd, bij. In een echte toepassing is het niet mogelijk om deze informatie in de toepassing op te lossen.

    Alle volgende functie definities en code zijn genest binnen de `main` functie.

1. Voeg de volgende twee functies toe aan `main` de functie om het apparaat te registreren en te verbinden met uw IOT Central-toepassing. Registratie maakt gebruik van de Azure Device Provisioning Service:

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

1. Voeg de volgende functie binnen de `main` functie toe om telemetrie te verzenden naar uw IOT Central-toepassing:

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

    De namen van de telemetrie-`temp` items `humid`(en) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

1. Voeg de volgende functies toe binnen `main` de functie voor het afhandelen van opdrachten die worden aangeroepen vanuit uw IOT Central-toepassing:

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

    De namen van de opdrachten (`blink`, `turnon` `turnoff`, en `rundiagnostics`) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

    Op dit moment gebruikt IoT Central niet het antwoord schema dat is gedefinieerd in het functionaliteits model van het apparaat. Voor een synchrone opdracht kan de nettolading van de reactie een wille keurige geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retour neren, gevolgd door de gerapporteerde eigenschap bijwerken wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschaps update is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de reactie lading weer geven in de opdracht geschiedenis.

1. Voeg de volgende functies toe binnen `main` de functie voor het afhandelen van eigenschaps updates die zijn verzonden vanuit uw IOT Central-toepassing:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

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

    Wanneer de operator een schrijf bare eigenschap in de IoT Central toepassing instelt, gebruikt de toepassing een door het apparaat dubbele gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een dubbele gerapporteerde eigenschap van het apparaat. Wanneer IoT Central de gerapporteerde eigenschaps waarde ontvangt, wordt de eigenschappen weergave bijgewerkt met de status **gesynchroniseerd**.

    De namen van de eigenschappen (`name` en `brightness`) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

1. Voeg de volgende functies toe binnen `main` de functie om de toepassing te beheren:

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
        await device_client.patch_twin_reported_properties({'state': 'true'})
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

1. Sla het bestand **environmental_sensor. py** op.

## <a name="run-your-python-application"></a>Uw python-toepassing uitvoeren

Voer de volgende opdracht uit in de opdracht regel omgeving om de client toepassing van het apparaat te starten:

```cmd
python3 environmental_sensor.py
```

U kunt zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en dat telemetrie wordt verzonden:

![De client toepassing uitvoeren](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

![De client toepassing observeren](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de basis beginselen van het maken van een apparaat met behulp van node. js hebt geleerd, kunt u als apparaat voor ontwikkel aars het volgende doen:

- Meer informatie over het verbinden van een echt apparaat met IoT Central in het [MXChip IOT DevKit-apparaat verbinden met uw Azure IOT Central Application](./howto-connect-devkit.md) procedures-artikel.
- Lees [verbinding maken met Azure IOT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IOT Central en hoe IOT Central verbindingen met apparaten beveiligt.

Als u liever door wilt gaan met de set IoT Central zelf studies en meer wilt weten over het bouwen van een IoT Central oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)