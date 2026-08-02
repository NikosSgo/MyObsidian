**Сетевой стек Linux** — часть ядра, которая реализует сокеты, TCP, UDP, IP, маршрутизацию и передачу пакетов драйверам сетевых устройств.

Обычное приложение не формирует TCP-сегменты самостоятельно. Оно передаёт ядру поток байтов или UDP-дейтаграмму через [[Сокеты|Socket API]], а ядро добавляет транспортный и сетевой заголовки.

```text
приложение → libc → системный вызов → сокет ядра
           → TCP/UDP → IPv4/IPv6 → драйвер → сетевая карта
```

## Почему TCP находится в `net/ipv4`

Каталоги ядра организованы не строго по уровням OSI. `net/ipv4` исторически содержит реализацию семейства **INET**, поэтому рядом находятся IPv4 и общая часть TCP/UDP.

- TCP не является частью IPv4: это отдельный транспортный протокол.
- При передаче TCP-сегмент становится полезной нагрузкой IPv4- или IPv6-пакета.
- Большая часть общей логики TCP лежит в `net/ipv4`, но используется и IPv6.
- Специфичная для IPv6 обработка находится в `net/ipv6`, например в `tcp_ipv6.c` и `udp.c`.

Связь протоколов задаётся регистрацией обработчиков: поле **Protocol / Next Header** в IP-заголовке определяет, передать данные TCP, UDP, ICMP или другому протоколу.

## Основные каталоги

- `net/socket.c` — системные вызовы и общий Socket API ядра.
- `net/core` — общие сетевые структуры, очереди, `sk_buff` и передача данных сетевым устройствам.
- `net/ipv4` — IPv4 и общая реализация TCP/UDP.
- `net/ipv6` — IPv6 и специфичные для него части транспортных протоколов.
- `net/netfilter` — фильтрация пакетов, firewall и NAT.
- `net/ethernet`, `net/bridge`, `net/packet` — Ethernet, Linux bridge и packet sockets.
- `drivers/net` — драйверы сетевых карт и виртуальных интерфейсов.
- `include/net` — внутренние заголовки сетевого стека.
- `include/uapi/linux` — структуры и константы, доступные программам user space.
- `Documentation/networking` — документация сетевой подсистемы.

## Где находится IP

### IPv4

- `net/ipv4/af_inet.c` — регистрация семейства `AF_INET` и связывание сокетов с TCP, UDP, ICMP и RAW IP.
- `net/ipv4/ip_input.c` — обработка входящего IPv4-пакета: проверка заголовка, Netfilter и решение, предназначен ли пакет этому компьютеру.
- `net/ipv4/ip_output.c` — создание и отправка IPv4-пакета, добавление IP-заголовка и передача его следующему уровню.
- `net/ipv4/ip_forward.c` — пересылка чужих пакетов, когда Linux работает как роутер; здесь уменьшается TTL.
- `net/ipv4/route.c` — поиск маршрута для входящих и исходящих пакетов, работа с `dst_entry` и кэшем маршрута.
- `net/ipv4/fib_frontend.c` — добавление и удаление маршрутов, связь FIB с интерфейсами и адресами.
- `net/ipv4/fib_trie.c` — структура таблицы маршрутизации и поиск наиболее длинного совпавшего префикса.
- `net/ipv4/fib_rules.c` — правила policy routing, которые видны через `ip rule`.
- `net/ipv4/ip_fragment.c` — сборка входящих фрагментов IPv4.
- `net/ipv4/icmp.c` — ICMPv4: ошибки, Echo Request/Reply и сообщения о недоступности.
- `net/ipv4/ip_sockglue.c` — параметры уровня IP, доступные через `setsockopt()`.
- `net/ipv4/arp.c` — ARP и поиск MAC-адреса следующего узла для IPv4.

Упрощённый путь входящего IPv4-пакета:

```text
ip_rcv()
  ↓ проверка IPv4-заголовка и Netfilter
поиск маршрута
  ├─ пакет этому компьютеру → ip_local_deliver() → TCP/UDP/ICMP
  └─ пакет нужно переслать  → ip_forward() → ip_output()
```

Исходящий TCP/UDP-сегмент попадает в `ip_queue_xmit()` или `ip_make_skb()`. Ядро ищет маршрут, добавляет IPv4-заголовок и передаёт пакет через `ip_local_out()` и `ip_output()` сетевому интерфейсу.

### IPv6

- `net/ipv6/af_inet6.c` — регистрация семейства `AF_INET6`.
- `net/ipv6/ip6_input.c` — обработка входящих IPv6-пакетов и доставка локальным протоколам.
- `net/ipv6/ip6_output.c` — формирование, отправка и пересылка IPv6-пакетов.
- `net/ipv6/route.c` и `net/ipv6/ip6_fib.c` — IPv6-маршрутизация и таблица FIB.
- `net/ipv6/exthdrs.c` — обработка Extension Headers IPv6.
- `net/ipv6/icmp.c` — ICMPv6.
- `net/ipv6/addrconf.c` — IPv6-адреса, SLAAC и обработка Router Advertisement.
- `net/ipv6/ndisc.c` — NDP: поиск соседей и MAC-адреса, Router Solicitation/Advertisement.
- `net/ipv6/reassembly.c` — сборка фрагментов IPv6 на конечном узле.

Общие структуры IP находятся в `include/net/ip.h`, `include/net/ipv6.h`, `include/net/route.h` и `include/net/ip_fib.h`. Форматы заголовков, доступные user space, находятся в `include/uapi/linux/ip.h` и `include/uapi/linux/ipv6.h`.

## Где находится TCP

- `net/ipv4/tcp.c` — отправка и чтение потока, настройки TCP-сокета.
- `net/ipv4/tcp_input.c` — входящие сегменты, ACK, состояния соединения, потери и SACK.
- `net/ipv4/tcp_output.c` — создание и отправка сегментов, проверка `rwnd` и `cwnd`, повторная передача.
- `net/ipv4/tcp_timer.c` — RTO, delayed ACK, keepalive и другие таймеры.
- `net/ipv4/tcp_ipv4.c` — подключение TCP к IPv4, поиск сокета и входная функция `tcp_v4_rcv()`.
- `net/ipv6/tcp_ipv6.c` — подключение той же логики TCP к IPv6.
- `net/ipv4/tcp_cong.c` — инфраструктура алгоритмов управления перегрузкой.
- `net/ipv4/tcp_cubic.c` — реализация CUBIC.

Состояние соединения хранится в `struct tcp_sock`: номера `snd_una`, `snd_nxt`, `rcv_nxt`, окна `snd_wnd`, `snd_cwnd` и другие параметры [[Алгоритм скользящего окна TCP|скользящего окна]].

## Где находится UDP

- `net/ipv4/udp.c` — отправка, получение и поиск IPv4 UDP-сокета.
- `net/ipv6/udp.c` — UDP поверх IPv6.
- `include/net/udp.h` и `include/linux/udp.h` — внутренние структуры и функции UDP.

UDP значительно проще TCP: ядро формирует дейтаграмму, вычисляет checksum, передаёт её IP и на приёме находит сокет по адресам и портам.

## Путь отправки TCP

При вызове `send()` данные примерно проходят такую цепочку:

```text
send()
  ↓
net/socket.c: sock_sendmsg()
  ↓
net/ipv4/af_inet.c: inet_sendmsg()
  ↓
net/ipv4/tcp.c: tcp_sendmsg()
  ↓
net/ipv4/tcp_output.c: tcp_write_xmit()
  ↓
net/ipv4/ip_output.c: ip_queue_xmit()
  ↓
net/core/dev.c: dev_queue_xmit()
  ↓
drivers/net/... → сетевая карта
```

Для UDP вместо `tcp_sendmsg()` вызывается `udp_sendmsg()`.

## Путь входящего пакета

```text
сетевая карта → драйвер → NAPI
  ↓
net/core → ip_rcv() → ip_local_deliver()
  ↓
tcp_v4_rcv() / udp_rcv()
  ↓
поиск сокета по IP-адресам и портам
  ↓
очередь сокета → recv() → приложение
```

Для TCP после `tcp_v4_rcv()` вызывается обработка состояния соединения: проверяются номера, ACK, окно, порядок сегментов и необходимость повторной передачи.

## Граница с user space

Программа работает с ядром через BSD Socket API:

```text
socket()  bind()  listen()  accept()  connect()
send()    recv()  shutdown()  setsockopt()
```

`libc` предоставляет обёртки над системными вызовами. Сам TCP находится не в `libc`, а в ядре. Программа не получает прямого доступа к `struct tcp_sock`, очередям и таймерам, но может управлять разрешёнными параметрами через `setsockopt()` и читать состояние через `getsockopt()`, например `TCP_INFO`.

Экспортируемые определения находятся в:

- `include/uapi/linux/socket.h`;
- `include/uapi/linux/in.h`;
- `include/uapi/linux/tcp.h`;
- `include/uapi/linux/udp.h`.

**QUIC — важное исключение:** обычно его логика находится в библиотеке или приложении user space. Ядро предоставляет UDP-сокет, IP и драйвер, а handshake, шифрование, потоки и повторные передачи реализует сам QUIC.
