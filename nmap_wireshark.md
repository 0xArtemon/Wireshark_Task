
---

## 🧪 **Практична робота: Аналіз мережевого сканування за допомогою `nmap` та `Wireshark`**

### 🎯 **Мета:**
Навчитися використовувати утиліту `nmap` для сканування хоста та проаналізувати особливості мережевих пакетів різних типів сканування за допомогою `Wireshark`.

---

### ⚙️ **Середовище:**
- **Kali Linux** — як скануючий хост (192.168.1.109).
- **LinuxOS(Ubuntu,Debian,etc)** — як ціль сканування (192.168.1.41). 

---

### ✅ **Крок 1: Визначення IP-адрес**

На обох машинах:

```bash
ip a | grep inet
```

<img width="815" height="87" alt="image" src="https://github.com/user-attachments/assets/bb94069c-1c20-4390-99c3-64c937230c19" />

<img width="816" height="48" alt="image" src="https://github.com/user-attachments/assets/f1c068b0-5a40-4e96-84db-5803c353a6e8" />

---

### ✅ **Крок 2: Запуск Wireshark на Kali**

На Kali Linux:

1. Запусти Wireshark.
2. Вибери інтерфейс, що відповідає NAT (наприклад, `eth0`).
3. Почни перехоплення.
4. (Опціонально) встанови фільтр:  
   ```
   ip.addr == 10.0.2.15 && ip.addr == 10.0.2.X
   ```

<img width="982" height="282" alt="image" src="https://github.com/user-attachments/assets/690a9238-d0d5-4821-bb99-4739f14d2e12" />

---

### ✅ **Крок 3: Проведення сканувань з Kali**

#### 🔹 **1. TCP Connect Scan (повне з'єднання):**

```bash
nmap -sT 10.0.2.15
```

<img width="569" height="206" alt="image" src="https://github.com/user-attachments/assets/bff34189-9dd2-4fc9-8bf5-1c2def330cc0" />

> 🔍 *Wireshark: видно повний TCP 3-way handshake (SYN → SYN/ACK → ACK)*

<img width="1280" height="238" alt="image" src="https://github.com/user-attachments/assets/3db6ae2d-7a87-407e-a921-5a04e61ab341" />

---

#### 🔹 **2. SYN Scan (половинне сканування):**

```bash
sudo nmap -sS 10.0.2.15
```

<img width="546" height="208" alt="image" src="https://github.com/user-attachments/assets/f7a5d8f6-6311-4d29-8827-7486250fbf0e" />

> 🔍 *Wireshark: видно тільки SYN → SYN/ACK → RST. Встановлення з'єднання не відбувається.*

<img width="1280" height="225" alt="image" src="https://github.com/user-attachments/assets/2da9de63-4b26-4763-8848-edce9c370498" />

---

#### 🔹 **3. UDP Scan:**

```bash
sudo nmap -sU -p 53,67,123 10.0.2.15
```

<img width="542" height="227" alt="image" src="https://github.com/user-attachments/assets/1211041c-b5f8-4e97-abca-0bed6bd1b9da" />

> 🔍 *Wireshark: ICMP Port Unreachable для закритих портів; відсутність відповіді — можливість відкритого порту.*

<img width="1280" height="264" alt="image" src="https://github.com/user-attachments/assets/bc8df19c-d856-4811-8a37-54c72049b984" />

<img width="1280" height="211" alt="image" src="https://github.com/user-attachments/assets/f1cf4133-00e6-4ac8-9358-c4b9bd3df872" />

<img width="1280" height="237" alt="image" src="https://github.com/user-attachments/assets/156c3db4-b1d6-4bea-b9a5-c590c6f21308" />

---

#### 🔹 **4. Ping Scan (без портів):**

```bash
nmap -sn 10.0.2.15
```

<img width="558" height="129" alt="image" src="https://github.com/user-attachments/assets/baa5752f-ce97-4ff3-864e-0b2c58746e6d" />

> 🔍 *Wireshark: видно ICMP echo request та echo reply.*

<img width="1280" height="240" alt="image" src="https://github.com/user-attachments/assets/09bd3b48-da17-41a3-ab20-039bd3eef8e5" />

---

#### 🔹 **5. OS Detection (змішані пакети):**

```bash
sudo nmap -O 10.0.2.15
```

<img width="1005" height="301" alt="image" src="https://github.com/user-attachments/assets/191d8a5c-7bb3-4528-b894-1b84a6021cd1" />

> 🔍 *Wireshark: складна послідовність TCP/ICMP пакетів — можна вивчати кожен.*

<img width="1280" height="428" alt="image" src="https://github.com/user-attachments/assets/29a99692-11d1-41b8-8a21-b13f9543286e" />

---

### 📌 **Пояснення для звіту (коротко):**

| Тип сканування | Протокол | Порти | Особливість у Wireshark |
|----------------|----------|-------|--------------------------|
| `-sT` (Connect) | TCP      | 1-1000 | Повний handshake         |
| `-sS` (SYN)     | TCP      | 1-1000 | SYN → SYN/ACK → RST     |
| `-sU` (UDP)     | UDP      | 53,67,123 | ICMP port unreachable або silence |
| `-sn` (Ping)    | ICMP     | -     | echo request/reply       |
| `-O` (OS detect)| TCP/ICMP | -     | Мікс SYN, NULL, FIN      |

---

### 📝 **Домашнє/Додаткове:**
- Спробуй сканування з `-sX`, `-sF` і поясни, як вони виглядають у Wireshark.
- Увімкни `firewalld` або `ufw` на Kali та подивись, як зміниться відповідь на сканування.

Спочатку з вимкненим `ufw`:

<img width="553" height="207" alt="image" src="https://github.com/user-attachments/assets/b85a49c1-6663-42bd-bd6d-ed75caf391bc" />

<img width="1280" height="238" alt="image" src="https://github.com/user-attachments/assets/2bb2009c-00e2-4e23-915a-db79c05e5384" />

<img width="564" height="210" alt="image" src="https://github.com/user-attachments/assets/7786fbbd-e95b-46ae-ab46-774304d494fc" />

<img width="1280" height="239" alt="image" src="https://github.com/user-attachments/assets/ba01d864-8bef-4f1d-a611-145f111e9524" />

Тепер увімкнемо `ufw`:

<img width="270" height="35" alt="image" src="https://github.com/user-attachments/assets/f2bf1342-3b2b-4e14-9f24-2fd174a7ed17" />

<img width="552" height="179" alt="image" src="https://github.com/user-attachments/assets/3cd34624-8660-4ca9-a378-d7fefe912821" />

<img width="1280" height="239" alt="image" src="https://github.com/user-attachments/assets/2ccd3e23-1920-4c7f-8353-018577f1e0ac" />

<img width="538" height="175" alt="image" src="https://github.com/user-attachments/assets/4abf6c95-cf87-4f44-b947-ccd3c45541a4" />

<img width="1280" height="238" alt="image" src="https://github.com/user-attachments/assets/f6e3d255-1502-4a13-a27c-8bc68f90947d" />

### Аналіз трафіку та результатів сканування

1. **Вигляд сканувань `-sF` (FIN) та `-sX` (Xmas) у Wireshark:**
   * Під час **FIN-сканування (`-sF`)** сканер надсилає TCP-пакети лише з одним прапорцем `[FIN]`, а під час **Xmas-сканування (`-sX`)** — пакети з нестандартною комбінацією прапорців `[FIN, PSH, URG]`.
   * **На закриті порти** цільова система відповідає пакетом **`[RST, ACK]`**.
   * **На відкриті порти** цільова система **не відповідає взагалі** (ігнорує пакет згідно зі стандартом RFC 793), через що Nmap позначає їх як `open|filtered`.

2. **Зміна поведінки після увімкнення фаєрволу (`ufw`):**
   * **До увімкнення:** У Wireshark чітко видно різницю між портами — закриті порти миттєво повертали `[RST, ACK]`, а відкриті мовчали.
   * **Після увімкнення:** Фаєрвол у режимі `DROP` мовчки відкидає всі вхідні пакети. У Wireshark зникли відповіді `[RST, ACK]` навіть для закритих портів (залишилися лише односторонні запити та повторні `TCP Retransmission`), через що всі порти почали визначатися однаково — як `open|filtered (no-response)`.

---

### 📝 **Додаткова демонстрація, як telnet передає логін/пароль у відкритому вигляді:**

<img width="419" height="221" alt="image" src="https://github.com/user-attachments/assets/f0296382-473d-43e4-a264-e948bb8e8076" />

<img width="1280" height="506" alt="image" src="https://github.com/user-attachments/assets/3ca00f38-9a96-44b4-9df9-f38a973c8e6f" />
