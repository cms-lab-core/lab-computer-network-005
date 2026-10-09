# ARP: cache и Neighbor Unreachability Detection (NUD)

Lab 005: реальный ARP Request/Reply → ICMP, затем **второй capture без flush** и контролируемый эксперимент с `STALE` neighbor entry.

### Важная особенность Linux

Повторный `ping` не гарантирует нулевого ARP: даже существующая IP→MAC запись может перейти из `REACHABLE` в `STALE`, а при использовании стать `DELAY` и затем `PROBE`. В `PROBE` возможны unicast ARP-запросы к известному MAC. Также нужно различать запросы `pc1` и `pc2` по sender/target IP.

Таймеры на `pc1`, `eth1`:

```bash
cat /proc/sys/net/ipv4/neigh/eth1/base_reachable_time_ms
cat /proc/sys/net/ipv4/neigh/eth1/delay_first_probe_time
cat /proc/sys/net/ipv4/neigh/eth1/gc_stale_time
```

`base_reachable_time_ms` — база рандомизированного reachable-интервала (часто 30000 мс, примерно 15–45 с без другого подтверждения); `delay_first_probe_time` обычно 5 с. **`gc_stale_time` не является TTL записи.** Значения зависят от ядра/стенда; лабораторная читает, но не требует менять sysctl.

### Три настоящих захвата

1. `captures/arp-icmp.pcap`: первый ping после `ip neigh flush`.
2. `captures/arp-repeat.pcap`: повторный ping **без flush**.
3. `captures/arp-stale.pcap`: `lladdr` сохранён, состояние записи установлено в `STALE`, затем длительный ping.

`PcapViewer` выводит обычные notebook-таблицы и поля ARP для статического рендера. CHECK проверяет живую конфигурацию и связность, **не нулевое число ARP**. Все 4 вопроса имеют отдельные strict answer cells и REVIEW-ONLY AI-review.

`eth0` — management plane; не изменяем.

Источник по таймерам: `arp(7)` (Linux man-pages), по состояниям: `ip-neighbour(8)`.
