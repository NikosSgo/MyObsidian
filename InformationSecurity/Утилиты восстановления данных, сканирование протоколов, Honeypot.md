## **1. Утилиты восстановления данных (Data Recovery Tools)**

**Восстановление данных** — процесс извлечения информации с повреждённых, неисправных или отформатированных носителей.

### **A. Причины потери данных:**
*   **Логические:** Удаление, форматирование, повреждение файловой системы, вирусы.
*   **Физические:** Повреждение поверхности диска, электроники, падение головок HDD.
*   **Программные:** Ошибки ПО, сбои при записи.

### **B. Этапы восстановления:**
1.  **Диагностика:** Определение типа повреждения.
2.  **Создание образа:** Работа с копией диска (dd, ddrescue).
3.  **Восстановление структуры:** Восстановление разделов, файловой системы.
4.  **Извлечение данных:** Восстановление файлов по сигнатурам.

### **C. Утилиты восстановления:**

#### **1. Работа с дисками и создание образов:**
*   **dd (GNU coreutils):** Базовое клонирование.
  ```bash
  dd if=/dev/sda of=/backup/sda.img bs=4M conv=noerror,sync
  ```
*   **ddrescue (GNU):** Лучше для повреждённых дисков, сохраняет прогресс.
  ```bash
  ddrescue -d -r3 /dev/sda /backup/sda.img /backup/logfile.log
  ```
*   **TestDisk:** Восстановление разделов, загрузочных секторов.
  ```bash
  testdisk /dev/sda  # Интерфейс на основе меню
  ```

#### **2. Восстановление файловых систем:**
*   **fsck/e2fsck (ext2/3/4):** Проверка и восстановление ФС.
  ```bash
  fsck -y /dev/sda1
  e2fsck -f -y /dev/sda1
  ```
*   **ntfsfix/ntfs-3g (NTFS):**
  ```bash
  ntfsfix /dev/sda1
  ```
*   **xfs_repair (XFS):**
  ```bash
  xfs_repair /dev/sda1
  ```

#### **3. Восстановление удалённых файлов:**
*   **PhotoRec (с TestDisk):** Восстановление по сигнатурам (карусель файлов).
  ```bash
  photorec /dev/sda1
  ```
  *   Восстанавливает **300+ форматов** по заголовкам файлов.
  *   Игнорирует ФС, работает с сырыми данными.
*   **extundelete (ext3/4):** Восстановление удалённых файлов в ext*.
  ```bash
  extundelete --restore-all /dev/sda1
  ```
*   **Scalpel/Foremost:** Восстановление по сигнатурам, настраиваемое.
  ```bash
  foremost -t jpg,pdf,doc -i /dev/sda1 -o /recovery/
  ```
  ```bash
  # Настройка сигнатур в /etc/scalpel/scalpel.conf
  scalpel /dev/sda1 -o /recovery/
  ```

#### **4. Коммерческие решения:**
*   **R-Studio:** Поддержка многих ФС, RAID, сетевые диски.
*   **UFS Explorer:** Аналогично, хорошая поддержка экзотических ФС.
*   **GetDataBack (NTFS/FAT):** Эффективен для Windows.
*   **Disk Drill:** Простой интерфейс, базовые функции бесплатно.

#### **5. Восстановление в Windows:**
*   **Recuva:** Бесплатная, от разработчиков CCleaner.
*   **MiniTool Power Data Recovery:**
*   **EaseUS Data Recovery Wizard:**

#### **6. Специализированные утилиты:**
*   **chntpw/Offline NT Password Editor:** Сброс паролей Windows.
*   **gpart:** Восстановление разделов методом догадок.
*   **sleuthkit/autopsy:** Форензика, работа с образами дисков.
  ```bash
  autopsy  # Веб-интерфейс на порту 9999
  ```

### **D. Практические шаги восстановления:**
```bash
# 1. Подключить диск только для чтения
hdparm -r1 /dev/sda

# 2. Создать образ повреждённого диска
ddrescue -d /dev/sda disk.img disk.log

# 3. Попытаться восстановить структуру
testdisk disk.img  # Восстановить разделы

# 4. Восстановить файлы из образа
photorec disk.img

# 5. Если известна ФС - использовать специфичные утилиты
extundelete --restore-all /dev/sda1
```

---

## **2. Сканирование протоколов (Protocol Scanning & Analysis)**

**Сканирование протоколов** — анализ сетевого трафика для понимания структуры, обнаружения аномалий и проблем безопасности.

### **A. Пассивное сканирование (сниффинг):**

#### **1. Базовые снифферы:**
*   **tcpdump:** Консольный, мощный, фильтры BPF.
  ```bash
  tcpdump -i eth0 -n 'port 80'
  tcpdump -i eth0 -w capture.pcap  # Запись в файл
  tcpdump -r capture.pcap 'host 192.168.1.1'  # Чтение
  ```
*   **Wireshark (GUI):** Графический, глубокий анализ, декодирование протоколов.
  ```bash
  wireshark -k -i eth0  # Запуск
  tshark -i eth0 -f "tcp port 443"  # Консольная версия
  ```

#### **2. Анализ протоколов прикладного уровня:**
*   **Driftnet:** Извлечение изображений из трафика.
  ```bash
  driftnet -i eth0
  ```
*   **Ettercap:** MITM, анализ протоколов, извлечение паролей.
  ```bash
  ettercap -T -i eth0 -M arp /192.168.1.1// /192.168.1.2//
  ```
*   **Dsniff:** Набор утилит для анализа трафика.
  ```bash
  # Извлечение паролей
  arpspoof -i eth0 -t 192.168.1.1 192.168.1.2
  dsniff -i eth0
  ```

#### **3. Анализ DNS:**
*   **dnstop:** Статистика DNS-запросов.
  ```bash
  dnstop -l 5 eth0
  ```
*   **DNSwalk:** Проверка DNS-серверов на ошибки.

#### **4. Анализ VoIP:**
*   **Vomit:** Конвертация голосового трафика в WAV.
*   **Wireshark:** Встроенные VoIP-анализоры.

### **B. Активное сканирование протоколов:**

#### **1. Обнаружение сервисов:**
*   **nmap:** `-sV` определение версий служб.
  ```bash
  nmap -sV -p 1-65535 192.168.1.1
  nmap -sV --script banner 192.168.1.1
  ```

#### **2. Анализ SSL/TLS:**
*   **SSLScan:** Проверка поддерживаемых шифров, сертификатов.
  ```bash
  sslscan example.com:443
  ```
*   **testssl.sh:** Подробная проверка уязвимостей SSL/TLS.
  ```bash
  ./testssl.sh example.com
  ```
*   **openssl s_client:** Ручная проверка.
  ```bash
  openssl s_client -connect example.com:443 -tls1_2
  ```

#### **3. Анализ сетевых протоколов:**
*   **SNMPwalk:** Опрос SNMP-устройств.
  ```bash
  snmpwalk -v2c -c public 192.168.1.1
  ```
*   **SMBClient/SMBMap:** Анализ SMB/CIFS.
  ```bash
  smbclient -L //192.168.1.1 -N
  smbmap -H 192.168.1.1
  ```
*   **rpcclient:** Работа с RPC-сервисами.
  ```bash
  rpcclient -U "" -N 192.168.1.1
  ```

### **C. Мониторинг сетевого трафика:**
*   **ntop/ntopng:** Веб-интерфейс, статистика, анализ.
  ```bash
  ntopng -i eth0 -w 3000  # Веб-интерфейс на порту 3000
  ```
*   **iftop:** Трафик в реальном времени по соединениям.
  ```bash
  iftop -i eth0 -n
  ```
*   **nethogs:** Трафик по процессам.
  ```bash
  nethogs eth0
  ```
*   **bmon:** Расширенный мониторинг интерфейсов.

### **D. Обнаружение аномалий:**
*   **Snort/Suricata:** NIDS с анализом протоколов.
  ```bash
  snort -i eth0 -c /etc/snort/snort.conf
  ```
*   **Bro/Zeek:** Анализ протоколов, генерация логов.
  ```bash
  zeek -i eth0
  ```

---

## **3. Honeypot (Ловушки для атакующих)**

**Honeypot** — специально созданная уязвимая система для привлечения и изучения атакующих.

### **A. Классификация honeypot:**

#### **По уровню взаимодействия:**
1.  **Low-interaction:** Эмуляция сервисов (порты, баннеры). Безопасно, но ограничено.
   *   **Примеры:** Kippo (SSH), Dionaea (много протоколов).
2.  **Medium-interaction:** Частичная эмуляция поведения.
3.  **High-interaction:** Реальные системы с мониторингом. Опасны, но информативны.
   *   **Пример:** Полноценная система с мониторингом всех действий.

#### **По цели:**
1.  **Research Honeypot:** Для изучения тактик атакующих.
2.  **Production Honeypot:** Для защиты реальной сети, обнаружения вторжений.

### **B. Популярные honeypot-системы:**

#### **1. Многосервисные/сетевые:**
*   **Cowrie (бывший Kippo):** SSH и Telnet honeypot.
  ```bash
  # Установка и запуск
  git clone https://github.com/cowrie/cowrie
  cd cowrie
  virtualenv --python=python3 cowrie-env
  source cowrie-env/bin/activate
  pip install -r requirements.txt
  ./start.sh
  ```
*   **Dionaea:** Эмуляция множества протоколов (SMB, HTTP, FTP, SQL).
  ```bash
  apt install dionaea
  systemctl start dionaea
  ```
*   **Conpot:** ICS/SCADA honeypot (модбус, сименс).
*   **Glastopf:** Веб-приложение honeypot, эмулирует уязвимости.
*   **Honeyd:** Создание виртуальных сетей с множеством хостов.
  ```bash
  honeyd -d -f honeyd.conf
  ```

#### **2. Специализированные:**
*   **SSH:** Kippo, Cowrie.
*   **SMTP:** Mailoney, SpamHAT.
*   **HTTP:** Glastopf, Google Hack Honeypot (GHP).
*   **SMB:** Dionaea, Impacket.
*   **IoT:** HoneyThing (роутеры), IoTPot.

#### **3. Промышленные решения:**
*   **T-Pot:** Все-в-одном дистрибутив на базе Docker.
  ```bash
  # Установка T-Pot
  git clone https://github.com/telekom-security/tpotce
  cd tpotce/iso/installer/
  ./install.sh
  ```
  Включает: Cowrie, Dionaea, ELK, Suricata и др.
*   **Modern Honey Network (MHN):** Централизованное управление honeypot.
*   **Critical Stack:** Коммерческая платформа.

### **C. Развёртывание honeypot:**
```bash
# 1. Создание изолированной сети
# Использование отдельного VLAN или DMZ

# 2. Установка минимальной системы
apt install docker.io
git clone https://github.com/cowrie/cowrie-docker
cd cowrie-docker

# 3. Настройка (изменение портов, баннеров)
vim cowrie.cfg  # Порт 2222 вместо 22

# 4. Запуск
docker-compose up -d

# 5. Мониторинг логов
docker logs -f cowrie
tail -f /var/log/cowrie/cowrie.log
```

### **D. Анализ данных с honeypot:**

#### **1. Что собирать:**
*   IP-адреса атакующих.
*   Используемые эксплойты и уязвимости.
*   Команды, выполняемые на системе.
*   Загружаемые файлы (малварь).
*   Время атаки, геолокация.

#### **2. Инструменты анализа:**
*   **ELK Stack (Elasticsearch, Logstash, Kibana):** Визуализация.
*   **Splunk:** Коммерческое решение.
*   **Maltrail:** Обнаружение вредоносного трафика.
*   **VirusTotal API:** Проверка скачанных файлов.

#### **3. Пример анализа в ELK:**
```bash
# 1. Настройка Logstash для Cowrie
input {
  file {
    path => "/var/log/cowrie/cowrie.json"
    codec => json
  }
}

# 2. Индексация в Elasticsearch
# 3. Дашборды в Kibana
```

### **E. Юридические и этические аспекты:**
1.  **Информирование:** Размещение предупреждения о мониторинге.
2.  **Ответственность:** Если ваш honeypot используется для атаки на третьих лиц.
3.  **Сбор данных:** Соответствие GDPR/ФЗ-152 при сборе IP и данных.
4.  **Изоляция:** Honeypot не должен иметь доступа к реальной сети.

### **F. Практическое применение:**
1.  **Обнаружение сканирования:** Кто сканирует вашу сеть.
2.  **Сбор Threat Intelligence:** Новые эксплойты, IP ботнетов.
3.  **Изучение тактик:** Как работают атакующие.
4.  **Обман атакующего:** Отвлечение от реальных систем.
5.  **Тестирование IDS/IPS:** Проверка правил обнаружения.

### **G. Honeypot как часть безопасности:**
```bash
# Архитектура:
# Интернет → Фаервол → DMZ (Honeypot) → Внутренняя сеть

# Правила iptables для перенаправления атак на honeypot:
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination honeypot_ip:2222
iptables -t nat -A PREROUTING -p tcp --dport 445 -j DNAT --to-destination honeypot_ip:445
```

---

## **4. Комплексный пример: Мониторинг и защита сети**

```bash
# 1. Развернуть honeypot (Cowrie) на порту 2222
# 2. Настроить перенаправление с порта 22 на honeypot
iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222

# 3. Мониторить атаки в реальном времени
tail -f /var/log/cowrie/cowrie.log | grep "login attempt"

# 4. Автоматически блокировать атакующие IP
# В скрипте /usr/local/bin/block_attacker.sh:
#!/bin/bash
ATTACKER_IP=$(grep "SSH login attempt" /var/log/cowrie/cowrie.log | tail -1 | awk '{print $5}')
iptables -A INPUT -s $ATTACKER_IP -j DROP

# 5. Анализировать скачанные файлы
find /cowrie/dl -type f -exec file {} \; | grep -i "executable"
```

---

## **5. Современные тренды**

1.  **Deception Technology:** Продвинутые honeypot для предприятий (Acalvio, TrapX).
2.  **Cloud Honeypot:** Развертывание в облаке (AWS, Azure).
3.  **IoT Honeypot:** Для устройств Интернета вещей.
4.  **Контейнеризация:** Docker-образы honeypot для быстрого развертывания.
5.  **ИИ в анализе:** Автоматическая классификация атак.

---

**Итог:** 
1.  **Восстановление данных** требует понимания ФС и правильного выбора инструментов.
2.  **Сканирование протоколов** — основа сетевой безопасности и диагностики.
3.  **Honeypot** — мощный инструмент для проактивной безопасности, но требующий осторожности.

**Золотое правило:** Все инструменты должны использоваться **только в легальных целях** и на системах, находящихся под вашим контролем или с явного разрешения владельца.