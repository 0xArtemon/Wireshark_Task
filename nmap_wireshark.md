
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

Kali:
<img width="815" height="87" alt="image" src="https://github.com/user-attachments/assets/bb94069c-1c20-4390-99c3-64c937230c19" />

Ubuntu:
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

---

### ✅ **Крок 3: Проведення сканувань з Kali**

#### 🔹 **1. TCP Connect Scan (повне з'єднання):**

```bash
nmap -sT 10.0.2.15
```

> 🔍 *Wireshark: видно повний TCP 3-way handshake (SYN → SYN/ACK → ACK)*

---

#### 🔹 **2. SYN Scan (половинне сканування):**

```bash
sudo nmap -sS 10.0.2.15
```

> 🔍 *Wireshark: видно тільки SYN → SYN/ACK → RST. Встановлення з'єднання не відбувається.*

---

#### 🔹 **3. UDP Scan:**

```bash
sudo nmap -sU -p 53,67,123 10.0.2.15
```

> 🔍 *Wireshark: ICMP Port Unreachable для закритих портів; відсутність відповіді — можливість відкритого порту.*

---

#### 🔹 **4. Ping Scan (без портів):**

```bash
nmap -sn 10.0.2.15
```

> 🔍 *Wireshark: видно ICMP echo request та echo reply.*

---

#### 🔹 **5. OS Detection (змішані пакети):**

```bash
sudo nmap -O 10.0.2.15
```

> 🔍 *Wireshark: складна послідовність TCP/ICMP пакетів — можна вивчати кожен.*

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

---
