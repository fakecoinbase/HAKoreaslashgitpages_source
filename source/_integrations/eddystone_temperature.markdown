---
title: 에디스톤(Eddystone)
description: Instructions on how to integrate Eddystone beacons with Home Assistant in order to receive temperature data.
logo: eddystone.png
ha_category:
  - DIY
ha_release: 0.42
ha_iot_class: Local Polling
---

<div class='videoWrapper'>
<iframe width="776" height="437" src="https://www.youtube.com/embed/mb_gWNrQcS8 frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

`eddystone_temperature` 센서 플랫폼은 [Eddystone](https://en.wikipedia.org/wiki/Eddystone_(Google)) 비콘을 통해 전송된 Bluetooth LE 알림에서 온도 정보를 읽습니다. 비콘은 UID 프레임 (식별)과 TLM 프레임 (온도)을 전송하도록 설정해야합니다. Eddystone 프로토콜을 지원하고 온도 센서가 있으며 TLM 프레임을 전송할 수 있는 모든 비콘은 이 플랫폼과 호환됩니다. 예를 들어 [Gimbal](https://store.gimbal.com/collections/beacons/), [Estimote](https://estimote.com/) 또는 [kontakt.io](https://kontakt.io/ ). 더 많은 제조업체는 Google의 [this overview](https://developers.google.com/beacons/eddystone#beacon_manufacturers)를 참조하십시오.

## 요구사항

이 플랫폼은 `bluez`를 사용하여 Bluetooth LE 장치를 검색하므로 **a Linux OS with bluez installed**가 필요합니다.

```bash
sudo apt-get install libbluetooth-dev
```

Bluetooth LE 장치를 검색하려면 특별한 권한이 필요합니다. 이것을 파이썬 실행 파일에 부여하려면 다음을 실행하십시오.

```bash
sudo apt-get install libcap2-bin
sudo setcap 'cap_net_raw,cap_net_admin+eip' $(readlink -f $(which python3))
```

설치시 Eddystone 비콘을 사용하려면 `configuration.yaml` 파일에 다음을 추가하십시오 :

```yaml
# Example configuration.yaml entry
sensor:
  - platform: eddystone_temperature
    bt_device_id: 0  # optional
    beacons:
      living_room:
        namespace: "112233445566778899AA"
        instance: "000000000001"
      kitchen:
        namespace: "112233445566778899AA"
        instance: "000000000002"
```

{% configuration %}
bt_device_id:
  description: 스캔에 사용해야하는 Bluetooth 장치의 ID (hci*X*). `hcitool dev`를 사용하여 해당 기기를 찾을 수 있습니다.
  required: false
  default: 0
  type: integer
beacons:
  description: 모니터링해야할 비콘.
  required: true
  type: list
  keys:
    entry:
      description: 비콘의 이름.
      required: true
      type: list
      keys:
        namespace:
          description: 16진 표기법으로된 비콘의 네임 스페이스 ID입니다. 정확히 20자 여야합니다(10 bytes).
          required: true
          type: string
        instance:
          description: 16진 표기법으로된 비콘의 인스턴스 ID. 정확히 12자 (6 바이트) 여야합니다.
          required: true
          type: string
        name:
          description: 비콘의 친숙한 이름.
          required: false
          type: string
{% endconfiguration %}
