# DDos-Guard-Hack
The solution of the geo-distributed network case

# Конфигурация сети: Москва и Гонконг - DebugDreamTeam

## Описание
Этот файл README содержит инструкции по настройке GRE-туннелей, BGP-сессий для DDoS-Guard, статической маршрутизации и фильтрации префиксов для двух локаций: **Москва** и **Гонконг**. Конфигурации разработаны для обеспечения эффективной маршрутизации, защиты от DDoS-атак и контроля трафика через BGP.

## Локации и сети

### 1. Москва
- **GRE-туннель**: настроен между локальным IP-адресом 103.110.10.33 и удалённым IP-адресом 41.204.224.255.
- **BGP для DDoS-Guard**: используется для маршрутизации трафика через защиту от DDoS.
- **Статическая маршрутизация**: настроены статические маршруты для сети и внешнего интернет-шлюза.
- **Фильтрация префиксов**: настраивается фильтрация определённых сетей для BGP.
- **Autonomous System Path Prepending**: метод в протоколе BGP, с помощью которого автономная система (AS) может искусственно удлинить путь до своих сетей. Цель — повлиять на выбор маршрута у соседних систем, заставив их выбрать альтернативный, более короткий маршрут.

#### Шаги настройки:
1. **Создание GRE-туннеля**:
   ```
   set interfaces gr-0/0/0 unit 1 tunnel source 103.110.10.33
   set interfaces gr-0/0/0 unit 1 tunnel destination 41.204.224.255
   set interfaces gr-0/0/0 unit 1 family inet address 198.18.101.130/30
   ```

2. **Создание BGP-сессии для DDOS-GUARD**:
   ```
   set protocols bgp group Ddos-Guard type external
   set protocols bgp group Ddos-Guard local-preference 100
   set protocols bgp group Ddos-Guard family inet unicast
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 description to-Ddos
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 local-preference 100
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 local-address 198.18.101.130
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 multi-hop ttl 16
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 peer-as 49612
   ```

3. **Настройка статической маршрутизации**:
   ```
   set routing-option static route 198.18.100.0/24 next-hop 198.18.101.128
   set routing-option static route 0.0.0.0/0 next-hop 41.204.224.225
   ```

4. **Настройка политики для BGP (экспорт)**:
   ```
   set policy-options prefix-list OUR-ADDRESES 113.13.10.0/24
   set policy-options policy-statement POL-ACCEPT-ALL term accept-all from prefix-list OUR-ADDRESES
   set policy-options policy-statement POL-ACCEPT-ALL term accept-all then accept
   ```

5. **Настройка политики для BGP (импорт)**:
   ```
   set policy-options policy-statement POL-DEFAULT-ACCEPT term default-accept from route-filter 0.0.0.0/0 exact
   set policy-options policy-statement POL-DEFAULT-ACCEPT term default-accept then accept
   ```

6. **Фильтрация сетей в BGP для DDOS-GUARD**:
   ```
   set policy-options prefix-list OUR-NETS 113.13.12.0/24
   set policy-options prefix-list OUR-NETS 113.13.13.0/24
   set policy-options policy-statement POL-SPECIFIC-ROUTES term access-nets from prefix-list OUR-NETS
   set policy-options policy-statement POL-SPECIFIC-ROUTES term access-nets then accept
   set policy-options policy-statement POL-SPECIFIC-ROUTES term reject-nets then reject
   ```

7. **Удаление сетей из BGP**:
   ```
   delete policy-options prefix-list our-nets 113.13.10.0/24
   delete policy-options prefix-list our-nets 113.13.12.0/24
   ```
8. **Организация ASPP для BGP DDOS-GUARD**:
   ```
   set policy-options policy-statement POL-PREPEND term prependt from route-filter 198.18.100.1/24 exact
   set policy-options policy-statement POL-PREPEND term prependt then as-path-prepend "49612 49612 49612"
   set policy-options policy-statement POL-PREPEND term prependt then accept
   ```
9. **Применение политики для BGP DDOS-GUARD (экспорт)**:
   ```
   set protocols bgp group Ddos-Guard export POL-PREPEND
   ```


### 2. Гонконг
- **GRE-туннель**: настроен между локальным IP-адресом 103.110.10.33 и удалённым IP-адресом 41.204.224.255.
- **BGP для DDoS-Guard**: используется для маршрутизации трафика через защиту от DDoS.
- **Статическая маршрутизация**: настроены статические маршруты для сети и внешнего интернет-шлюза.
- **Фильтрация префиксов**: настраивается фильтрация определённых сетей для BGP.
- **Autonomous System Path Prepending**: метод в протоколе BGP, с помощью которого автономная система (AS) может искусственно удлинить путь до своих сетей. Цель — повлиять на выбор маршрута у соседних систем, заставив их выбрать альтернативный, более короткий маршрут.

#### Шаги настройки:
1. **Создание GRE-туннеля**:
   ```
   set interfaces gr-0/0/0 unit 1 tunnel source 205.23.23.23
   set interfaces gr-0/0/0 unit 1 tunnel destination 41.204.224.255
   set interfaces gr-0/0/0 unit 1 family inet address 198.18.101.134/30
   ```

2. **Создание BGP-сессии для DDOS-GUARD**:
   ```
   set protocols bgp group Ddos-Guard type external
   set protocols bgp group Ddos-Guard local-preference 100
   set protocols bgp group Ddos-Guard family inet unicast
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 description to-Ddos
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 local-preference 100
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 local-address 198.18.101.134
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 multi-hop ttl 16
   set protocols bgp group Ddos-Guard neighbor 198.18.100.1 peer-as 49612
   ```

3. **Настройка статической маршрутизации**:
   ```
   set routing-option static route 198.18.100.0/24 next-hop 198.18.101.128
   set routing-option static route 0.0.0.0/0 next-hop 41.204.224.225
   ```

4. **Настройка политики для BGP (экспорт и импорт)**:
   Настройки аналогичны Москве.

5. **Фильтрация сетей в BGP для DDOS-GUARD**:
   Настройки аналогичны Москве.

6. **Удаление сетей из BGP**:
   ```
   delete policy-options prefix-list our-nets 113.13.10.0/24
   delete policy-options prefix-list our-nets 113.13.12.0/24
   ```
7. **Организация ASPP для BGP DDOS-GUARD**:
   ```
   set policy-options policy-statement POL-PREPEND term prependt from route-filter 198.18.100.1/24 exact
   set policy-options policy-statement POL-PREPEND term prependt then as-path-prepend "49612 49612 49612"
   set policy-options policy-statement POL-PREPEND term prependt then accept
   ```
8. **Применение политики для BGP DDOS-GUARD (экспорт)**:
   ```
   set protocols bgp group Ddos-Guard export POL-PREPEND
   ``` 

## Используемые технологии
- **GRE (Generic Routing Encapsulation)**: используется для создания туннеля между двумя удалёнными точками.
- **BGP (Border Gateway Protocol)**: протокол маршрутизации для обмена маршрутами между автономными системами.
- **Статическая маршрутизация**: настройка маршрутов вручную для улучшения управления сетевым трафиком.
- **Политики маршрутизации**: используются для фильтрации и управления передаваемыми маршрутами между сетями.
- **ASPP (Autonomous System Path Prepending)**: используется для управления входящим трафиком, чтобы уменьшить нагрузку на конкретные каналы связи 
