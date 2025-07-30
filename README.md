# Szablon Zabbix dla Siemens SCALANCE SC646-2C (SNMP)

Ten szablon Zabbix jest przeznaczony do kompleksowego monitorowania urządzeń Siemens SCALANCE SC646-2C za pomocą protokołu SNMP. Rozszerza standardowe możliwości monitorowania o szczegółowe elementy i reguły wykrywania, obejmujące różne aspekty, takie jak porty Ethernet, diagnostyka SFP, zasilanie, błędy systemowe oraz interfejsy mobilne.

## Wprowadzenie

Urządzenia sieciowe, takie jak przełączniki Siemens SCALANCE, są kluczowymi elementami infrastruktury przemysłowej. Skuteczne monitorowanie ich stanu i wydajności jest niezbędne do zapewnienia ciągłości działania i szybkiego reagowania na potencjalne problemy. Ten szablon Zabbix dostarcza gotowe rozwiązanie do monitorowania SCALANCE SC646-2C, wykorzystując dane SNMP.

## Funkcje

  * **Monitorowanie dostępności:** ICMP ping, dostępność agenta SNMP.
  * **Diagnostyka systemu:** Stan awarii systemu, wersja oprogramowania, numer seryjny, nazwa hosta, lokalizacja, czas pracy.
  * **Monitorowanie interfejsów sieciowych:**
      * Automatyczne wykrywanie interfejsów.
      * Status administracyjny i operacyjny.
      * Prędkość linku i tryb dupleksu.
      * Liczba błędów i odrzuconych pakietów (wejściowych/wyjściowych).
      * Stan wykrywania flapowania linku i blokady portu.
  * **Diagnostyka SFP:**
      * Automatyczne wykrywanie modułów SFP.
      * Temperatura, napięcie, prąd biasu, moc optyczna RX/TX.
      * Status modułu (obecny/brakujący/uszkodzony).
  * **Monitorowanie zasilania:** Automatyczne wykrywanie zasilaczy i ich stanu.
  * **Monitorowanie wentylatorów:** Automatyczne wykrywanie wentylatorów, ich prędkości i stanu.
  * **Wyzwalacze:** Predefiniowane alerty dla kluczowych wskaźników (np. link down, wysoka temperatura SFP, awaria zasilania).

## Wymagania

  * **Zabbix Server:** Wersja 7.0 lub nowsza.
  * **Zabbix Agent:** Nie jest wymagany na urządzeniu SCALANCE, ponieważ szablon używa SNMP.
  * **Urządzenie Siemens SCALANCE SC646-2C:** Z włączonym i skonfigurowanym dostępem SNMP (wersja 2c lub 3).
  * **SNMP Community String:** Musi być skonfigurowany na urządzeniu SCALANCE i użyty w konfiguracji hosta Zabbix.

## Instalacja i Użycie

### 1\. Importowanie szablonu

1.  Pobierz plik `SCALANCE XC(646-2c) by SNMP.xml` (lub `nowy22222.xml`, jeśli to jest ostateczna wersja) z tego repozytorium.
2.  Zaloguj się do interfejsu webowego Zabbix.
3.  Przejdź do **Configuration** \> **Templates**.
4.  Kliknij **Import** w prawym górnym rogu.
5.  Kliknij **Browse** i wybierz pobrany plik XML.
6.  Upewnij się, że opcje **Create new** i **Update existing** są zaznaczone (lub dostosuj do swoich potrzeb).
7.  Kliknij **Import**.

### 2\. Konfiguracja hosta

1.  W Zabbix przejdź do **Configuration** \> **Hosts**.
2.  Kliknij **Create host** (lub wybierz istniejący host).
3.  W zakładce **Host** wypełnij:
      * **Host name:** Nazwa hosta (np. `SCALANCE-SC646-2C-01`).
      * **Visible name:** Nazwa wyświetlana (np. `SCALANCE SC646-2C Serwerownia`).
      * **Groups:** Przypisz hosta do odpowiedniej grupy (np. `Network devices`).
4.  W zakładce **Interfaces** dodaj interfejs SNMP:
      * Kliknij **Add** obok `SNMP interfaces`.
      * **IP address:** Adres IP urządzenia SCALANCE.
      * **Port:** Zazwyczaj `161`.
      * **SNMP version:** Wybierz `SNMPv2c` (lub `SNMPv3`, jeśli tak skonfigurowano).
      * **SNMP community:** Wprowadź community string skonfigurowany na urządzeniu SCALANCE (np. `public`).
5.  W zakładce **Templates** podłącz szablon:
      * W polu **Link new templates** zacznij wpisywać `SCALANCE SC646-2C by SNMP` i wybierz szablon z listy.
      * Kliknij **Add**.
6.  Kliknij **Add** na dole formularza, aby zapisać hosta.

Po kilku minutach Zabbix powinien rozpocząć zbieranie danych z urządzenia i automatycznie wykrywać interfejsy, moduły SFP, zasilacze i wentylatory.

## Monitorowane Metryki

Szablon zbiera szeroki zakres danych, w tym:

  * **Ogólne informacje o systemie:** Nazwa, opis, lokalizacja, kontakt, numer seryjny, wersja oprogramowania, czas pracy.
  * **Stan zdrowia:** Status awarii systemu, dostępność ICMP i SNMP.
  * **Statystyki interfejsów:** Błędy, odrzucone pakiety, prędkość, status.
  * **Diagnostyka SFP:** Temperatura, napięcie, prąd, moc optyczna RX/TX.
  * **Status komponentów:** Stan wentylatorów i zasilaczy.

## Wyzwalacze

Szablon zawiera predefiniowane wyzwalacze, które generują alerty w przypadku wykrycia problemów, takich jak:

  * Urządzenie niedostępne (ICMP lub SNMP).
  * Wysoka strata pakietów lub czas odpowiedzi ICMP.
  * Urządzenie w stanie awarii.
  * Brak połączenia SINEMA RC.
  * Zmiana nazwy systemu, typu urządzenia lub numeru seryjnego.
  * Błędy lub odrzucone pakiety na interfejsach.
  * Wykrycie flapowania linku.
  * Zablokowany port.
  * Wentylator w stanie krytycznym (uszkodzony).
  * Zasilacz nieaktywny.
  * Temperatura SFP powyżej progu ostrzegawczego/krytycznego.
  * Niska moc optyczna RX/TX SFP.
  * Napięcie lub prąd SFP poza zakresem.
  * Brakujący lub uszkodzony moduł SFP.

## Reguły Odkrywania

Szablon wykorzystuje reguły niskopoziomowego odkrywania (LLD) do automatycznego wykrywania i monitorowania:

  * **Wentylatorów:** Na podstawie `snMspsDiagnosticsFanTable`.
  * **Interfejsów sieciowych:** Na podstawie `ifTable` (tylko interfejsy typu `ethernetCsmacd`).
  * **Zasilaczy:** Na podstawie `snMspsDiagnosticsPowerSupplyTable`.
  * **Diagnostyki SFP:** Na podstawie `snMspsDiagnosticsTemperatureEntry`.
  * **Modułów SFP:** Na podstawie `snMspsSfpTable`.

Dzięki LLD, Zabbix automatycznie tworzy elementy monitorujące i wyzwalacze dla każdego wykrytego komponentu, co znacznie upraszcza konfigurację.

## Mapy Wartości

Szablon używa map wartości Zabbix do tłumaczenia numerycznych wartości SNMP na bardziej zrozumiałe tekstowe stany, np.:

  * `FASTPATH-BOXSERVICES-PRIVATE-MIB::boxServicesFanItemState`: Tłumaczy stan wentylatora na `operational`, `failed`, `powering` itp.
  * `IF administrative status` / `IF operational status`: Tłumaczy status interfejsu na `up`, `down`, `testing` itp.
  * `SNMP_FAULT_STATE`: Tłumaczy stan awarii na `noFault`, `fault`.
  * `SNMP_TRUTH_VALUE`: Tłumaczy `1` na `true`, `2` na `false`.
  * `zabbix.host.available`: Tłumaczy dostępność hosta Zabbix na `available`, `not available`, `unknown`.

## Sugerowane Wykresy

Aby w pełni wykorzystać dane zbierane przez szablon, zaleca się stworzenie następujących wykresów w Zabbix:

1.  **Ogólny Stan Urządzenia:**

      * **Dostępność ICMP i Czas Odpowiedzi:** Wykres liniowy pokazujący `icmppingloss` i `icmppingsec`.
      * **Stan Awarii Systemu:** Wykres stanu lub liniowy dla `snmp.get[snMspsDiagnosticsSystemFaultState.0]`.
      * **Dostępność Agenta SNMP:** Wykres stanu dla `zabbix[host,snmp,available]`.

2.  **Wydajność Sieci i Interfejsów (dla każdego interfejsu):**

      * **Błędy i Odrzucone Pakiety:** Wykres liniowy dla `net.if.in.errors`, `net.if.out.errors`, `net.if.in.discards`, `net.if.out.discards`.
      * **Status Operacyjny i Administracyjny:** Wykres stanu dla `net.if.status[ifOperStatus.{#SNMPINDEX}]` i `net.if.status[ifAdminStatus.{#SNMPINDEX}]`.
      * **Prędkość Linku i Tryb Dupleksu:** Wykres liniowy dla `net.if.speed[ifSpeed.{#SNMPINDEX}]` i `net.if.duplex[ifMauDplxStatus.{#SNMPINDEX}]`.
      * **Stan Wykrywania Flapowania Linku:** Wykres stanu dla `snmp.get[snMspsPortFaultMaskState.{#SNMPINDEX}]`.

3.  **Diagnostyka Modułów SFP (dla każdego modułu SFP):**

      * **Moc Optyczna RX/TX:** Wykres liniowy dla `sfp.rx.power` i `sfp.tx.power`.
      * **Temperatura Modułu SFP:** Wykres liniowy dla `sfp.temp.value`.
      * **Napięcie i Prąd Biasu SFP:** Wykres liniowy dla `sfp.voltage.value` i `sfp.bias.current`.

4.  **Monitorowanie Zasilania i Wentylatorów:**

      * **Stan Zasilaczy:** Wykres stanu dla `sensor.psu.status[snMspsDiagnosticsPowerSupplyState.{#SNMPINDEX}]`.
      * **Prędkość Wentylatorów:** Wykres liniowy dla `sensor.fan.speed[snMspsDiagnosticsFanSpeed.{#SNMPINDEX}]`.
      * **Stan Wentylatorów:** Wykres stanu dla `sensor.fan.status[snMspsDiagnosticsFanState.{#SNMPINDEX}]`.
