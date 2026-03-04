# Отчет анализа логов приложения WePrint

## 📋 Резюме

Приложение **WePrint** (`com.dothantech.android.weida`) использует **BLE (Bluetooth Low Energy)** для подключения к принтеру. Все параметры подключения найдены в логах Android.

---

## 🔌 Тип подключения

**BLE (Bluetooth Low Energy)** через `BluetoothGatt` API

**Подтверждение:**
- `addressType=BLE` (строка 2481)
- `BluetoothGatt: connect()` (строка 3647)
- `BluetoothGatt: discoverServices()` (строка 3701)

---

## 📱 Информация о принтере

### Основные параметры

- **MAC адрес:** `10:23:81:9E:66:DC`
- **Имя устройства:** `P2-Y6019220767`
- **Серия:** `DP27`
- **Внутреннее имя:** `P2N_AT`
- **Производитель:** `Printer`

### Технические характеристики

- **Версия устройства:** `2.7`
- **Версия ПО:** `3.1.20251014`
- **DPI:** `203`
- **Ширина печати:** `384` точек
- **Темнота печати:** `5` (уровень `15` из `15`)
- **Скорость печати:** `2` (уровень `1` из `5`)
- **Тип зазора:** `2` (gapLength=300)
- **Режим мотора:** `0`
- **Автовыключение:** `15` минут
- **Язык:** `17`

### Поддерживаемые параметры

- **Типы зазоров:** `[2, 3, 4, 0]`
- **Режимы мотора:** `[12]`
- **Языки:** `[97, 1]`

---

## 🔑 UUID для подключения

### Service UUID

**Основной Service UUID:** `000018f0-0000-1000-8000-00805f9b34fb`

**Источник:** Строка 3147 в логах
```
ScanResult{device=10:23:81:9E:66:DC, scanRecord=ScanRecord [
  mServiceUuids=[000018f0-0000-1000-8000-00805f9b34fb], ...
  mDeviceName=P2-Y6019220767]
```

### Другие UUID (из сканирования других устройств)

В логах также найдены UUID других BLE устройств (не принтера):
- `0000180a-0000-1000-8000-00805f9b34fb` (Device Information Service)
- `0000560f-0000-1000-8000-00805f9b34fb`
- `0000a703-0000-1000-8000-00805f9b34fb`
- `005eb7ca-86c5-4383-84bc-2cf875b73dbf`

---

## 🔄 Процесс подключения

### Шаги подключения (из логов)

1. **Сканирование устройств:**
   ```
   03-04 23:54:04.873 D TWSService: ScanResult{device=10:23:81:9E:66:DC, 
     mServiceUuids=[000018f0-0000-1000-8000-00805f9b34fb], 
     mDeviceName=P2-Y6019220767}
   ```

2. **Подключение через GATT:**
   ```
   03-04 23:54:08.663 D BluetoothGatt: connect() - device: 10:23:81:9E:66:DC, auto: false
   ```

3. **Установка соединения:**
   ```
   03-04 23:54:09.743 D BluetoothGatt: onClientConnectionState() - status=0 
     device=10:23:81:9E:66:DC
   ```
   `status=0` означает успешное подключение

4. **Обнаружение сервисов:**
   ```
   03-04 23:54:09.746 D BluetoothGatt: discoverServices() - device: 10:23:81:9E:66:DC
   ```

5. **Завершение обнаружения:**
   ```
   03-04 23:54:10.399 D BluetoothGatt: onSearchComplete() = Device=10:23:81:9E:66:DC Status=0
   ```

6. **Настройка MTU:**
   ```
   03-04 23:54:10.458 D BluetoothGatt: configureMTU() - device: 10:23:81:9E:66:DC mtu: 503
   ```

7. **Обновление параметров соединения:**
   ```
   03-04 23:54:10.187 D BluetoothGatt: onConnectionUpdated() - Device=10:23:81:9E:66:DC 
     interval=6 latency=0 timeout=500 status=0
   ```

### Отключение

```
03-04 23:54:06.632 D BluetoothGatt: onClientConnectionState() - status=8 
  device=10:23:81:9E:66:DC
03-04 23:54:06.634 I Bluetooth.Printer: onStateChange(..., Disconnected)
```

`status=8` означает отключение (GATT_CONN_TERMINATE_PEER_USER)

---

## 📊 Параметры принтера (из логов)

### Полная информация (строка 2481)

```
PrinterAddress [
  shownName=P2-Y6019220767, 
  macAddress=10:23:81:9E:66:DC, 
  addressType=BLE
], 
PrinterParam [
  deviceType=2, 
  deviceName=P2-Y6019220767, 
  deviceVersion=2.7, 
  softwareVersion=3.1.20251014, 
  deviceAddress=10:23:81:9E:66:66:DC, 
  deviceAddrType=0x1F, 
  printerDPI=203, 
  printerWidth=384, 
  printDarkness=5, 
  printDarknessLevel=15, 
  darknessCount=15, 
  darknessMinSuggested=5, 
  printSpeed=2, 
  printSpeedLevel=1, 
  speedCount=5, 
  gapType=2, 
  gapLength=300, 
  motorMode=0, 
  autoPowerOffMins=15, 
  language=17, 
  supportedGapTypes=[2, 3, 4, 0], 
  supportedMotorModes=[12], 
  supportedLanguages=[97, 1], 
  manufacturer=Printer, 
  seriesName=DP27, 
  devIntName=P2N_AT, 
  peripheralFlags=00, 
  hardwareFlags=75244211, 
  softwareFlags=02f0, 
  attributeFlags=00, 
  upgradeCRC=20e99dc9
]
```

---

## 🔍 Characteristic UUID

**ВНИМАНИЕ:** Characteristic UUID не найден в логах напрямую. Для его определения требуется:

1. Подключиться к принтеру через BLE
2. Выполнить `discoverServices()`
3. Найти Characteristic в Service `000018f0-0000-1000-8000-00805f9b34fb`
4. Определить Characteristic с правами `WRITE` или `WRITE_NO_RESPONSE`

**Типичные Characteristic UUID для принтеров:**
- `0000fff1-0000-1000-8000-00805f9b34fb` (Write)
- `0000fff2-0000-1000-8000-00805f9b34fb` (Notify)
- Или кастомные UUID в формате `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`

---

## 📝 Команды печати

**ВНИМАНИЕ:** Команды печати (ESC-POS/TSPL) не найдены в логах. Для их перехвата требуется:

1. Использовать Frida для перехвата `BluetoothGatt.writeCharacteristic()`
2. Или использовать HCI Snoop Log для анализа BLE трафика
3. Или использовать Wireshark для анализа пакетов

**Ожидаемый формат:**
- Принтер серии DP27 обычно использует **TSPL** (TSC Print Language)
- Команды могут быть в формате: `SIZE`, `GAP`, `TEXT`, `PRINT`, и т.д.

---

## 💻 Пример кода для подключения

### Android (Java/Kotlin)

```java
import android.bluetooth.BluetoothAdapter;
import android.bluetooth.BluetoothDevice;
import android.bluetooth.BluetoothGatt;
import android.bluetooth.BluetoothGattCallback;
import android.bluetooth.BluetoothGattCharacteristic;
import android.bluetooth.BluetoothGattService;
import android.bluetooth.BluetoothManager;
import android.bluetooth.BluetoothProfile;
import android.content.Context;
import java.util.UUID;

public class PrinterConnection {
    private static final String PRINTER_MAC = "10:23:81:9E:66:DC";
    private static final UUID SERVICE_UUID = UUID.fromString("000018f0-0000-1000-8000-00805f9b34fb");
    // Characteristic UUID нужно определить после discoverServices()
    // private static final UUID CHARACTERISTIC_WRITE_UUID = UUID.fromString("...");
    
    private BluetoothGatt bluetoothGatt;
    private BluetoothGattCharacteristic writeCharacteristic;
    
    public void connect(Context context) {
        BluetoothManager bluetoothManager = 
            (BluetoothManager) context.getSystemService(Context.BLUETOOTH_SERVICE);
        BluetoothAdapter adapter = bluetoothManager.getAdapter();
        
        BluetoothDevice device = adapter.getRemoteDevice(PRINTER_MAC);
        
        bluetoothGatt = device.connectGatt(context, false, new BluetoothGattCallback() {
            @Override
            public void onConnectionStateChange(BluetoothGatt gatt, int status, int newState) {
                if (newState == BluetoothProfile.STATE_CONNECTED) {
                    // Подключено, начинаем обнаружение сервисов
                    gatt.discoverServices();
                }
            }
            
            @Override
            public void onServicesDiscovered(BluetoothGatt gatt, int status) {
                if (status == BluetoothGatt.GATT_SUCCESS) {
                    BluetoothGattService service = gatt.getService(SERVICE_UUID);
                    if (service != null) {
                        // Найти Characteristic с правами WRITE
                        for (BluetoothGattCharacteristic characteristic : 
                             service.getCharacteristics()) {
                            int props = characteristic.getProperties();
                            if ((props & BluetoothGattCharacteristic.PROPERTY_WRITE) != 0 ||
                                (props & BluetoothGattCharacteristic.PROPERTY_WRITE_NO_RESPONSE) != 0) {
                                writeCharacteristic = characteristic;
                                break;
                            }
                        }
                    }
                }
            }
        });
    }
    
    public void sendPrintData(byte[] data) {
        if (writeCharacteristic != null && bluetoothGatt != null) {
            writeCharacteristic.setValue(data);
            // Использовать WRITE_NO_RESPONSE для быстрой передачи
            bluetoothGatt.writeCharacteristic(writeCharacteristic);
        }
    }
}
```

---

## 🎯 Следующие шаги

1. **Определить Characteristic UUID:**
   - Подключиться к принтеру
   - Выполнить `discoverServices()`
   - Найти Characteristic с правами WRITE в Service `000018f0-0000-1000-8000-00805f9b34fb`

2. **Перехватить команды печати:**
   - Использовать Frida для перехвата `writeCharacteristic()`
   - Или использовать HCI Snoop Log

3. **Проанализировать формат команд:**
   - Определить язык команд (TSPL/ESC-POS/ZPL)
   - Составить библиотеку команд

---

## 📄 Источники данных

Все данные взяты из файла `out/app_logs.txt`:
- Строка 2481: Параметры принтера
- Строка 3147: Service UUID из ScanResult
- Строка 3647: Процесс подключения
- Строка 3701: Обнаружение сервисов
- Строка 3767: Настройка MTU

---

## ✅ Выводы

1. ✅ **Тип подключения:** BLE (Bluetooth Low Energy)
2. ✅ **MAC адрес:** `10:23:81:9E:66:DC`
3. ✅ **Service UUID:** `000018f0-0000-1000-8000-00805f9b34fb`
4. ✅ **MTU:** `503` байта
5. ⚠️ **Characteristic UUID:** Требуется дополнительное определение
6. ⚠️ **Команды печати:** Требуется перехват через Frida или HCI Snoop

---

**Дата анализа:** 2025-03-04  
**Версия приложения:** com.dothantech.android.weida (PID 5796)  
**Версия принтера:** DP27 v2.7 (P2N_AT)
