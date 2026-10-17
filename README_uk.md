# HP5883 / QMC5883P драйвер магнітометра для STM32F103

[Read me in English](README.md) | **Читати мене Українською**

Легковагий, ефективний **неблокуючий драйвер на С** для сучасного 3-осьового цифрового магнітного компаса **HP5883 (QMC5883P)**, побудований на базі бібліотеки **STM32CubeHAL** для мікроконтролера STM32F103C8T6 (Blue Pill).

На відміну від старих сенсорів серій QMC5883L/HMC5883L, ця сучасна китайська ревізія кремнію має повністю перероблену карту регістрів, іншу I2C-адресу та кастомні налаштування оверсемплінгу. Цей репозиторій надає апаратно верифіковану реалізацію, адаптовану під ці специфічні особливості заліза.

### 🚀 Особливості реалізації:
- **Апаратна адаптація:** Повна підтримка унікальної 7-бітної I2C адреси `0x2C` та внутрішньої структури регістрів чіпа HP5883.
- **Неблокуючий дизайн:** Опитування датчика реалізовано без використання затримок типу `HAL_Delay()`, на базі системних тіків `HAL_GetTick()`.
- **Динамічне автокалібрування:** Вбудований алгоритм компенсації магнітних завад (Hard-Iron), який на льоту відстежує екстремуми осей \(X/Y\) та обнуляє зміщення нуля.
- **Максимальна фільтрація:** Задіяно максимальний коефіцієнт апаратної передискретизації (`OSR2 = 8, OSR1 = 8`) для досягнення найвищої стабільності кута азимута.

---

## 🛠️ Hardware & Toolchain Profile

- **Target MCU:** STM32F103C8T6 (ARM Cortex-M3 @ 72 MHz)
- **Sensor:** HP5883 / QMC5883P (Marked as *HP5883 5702*)
- **IDE:** STM32CubeIDE (GCC Compiler toolchain)
- **Bus Profile:** I2C1 Standard Mode (100 kHz)
- **Verification Hardware:** OWON SDS210S Digital Oscilloscope

---

## 🔌 Hardware Connection (Pinout)

Connect the sensor directly to the STM32F103 I2C1 peripheral pins:

| Sensor Pin | STM32F103 Pin | Description |
| :---: | :---: | :--- |
| **VCC** | **3.3V** | Main Power Supply |
| **GND** | **GND** | Common Ground |
| **SCL** | **PB6** | I2C1 Clock (5V Tolerant) |
| **SDA** | **PB7** | I2C1 Data (5V Tolerant) |

---

## ⚙️ Driver Architecture Details

### 1. Register Relocation Fix
The driver explicitly maps the relocated register topology of the **QMC5883P**:
- Data registers start at `0x01` (instead of `0x00`).
- Status register moved to `0x09` (instead of `0x06`).
- Control registers shifted to `0x0A` and `0x0B`.

### 2. High-Fidelity Signal Filtering
The initialization configuration writes `0xC3` into `REG_CTRL1 (0x0A)`. This maps to maximum hardware over-sampling (`OSR1=8, OSR2=8`) at a stable `10 Hz` Output Data Rate (ODR) in Continuous Mode, minimizing digit jitter on the azimuth calculations.

---


## 🤝 Автори та подяки

Розроблено як навчальний проєкт і вагомий елемент портфоліо, присвячений алгоритмам навігації для автономних наземних безпілотних транспортних засобів (UGV). Особлива подяка інженерній спільноті **Nanit Robot Academy** за підтримку та організацію робочого середовища й інструментарію.


