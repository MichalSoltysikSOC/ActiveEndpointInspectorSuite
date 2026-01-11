Nazwa narzędzia: ActivePublicIPChecker.exe

Wersja: 1.0
Suma kontrolna SHA256: F13CCD52C8FA3A359442C0518B39AFC49377CB65028DC0BDE006DE31549A41A2
Rozmiar pliku: 46,5 KB
Napisane w języku C#, z przeznaczeniem dla platformy .NET Framework.
Skompilowane jako aplikacja Windows GUI w postaci pliku wykonywalnego .exe z nagłówkiem pliku MZ.

Autor: Michał Sołtysik
Cybersecurity Analyst & Consultant | Forensics Examiner | SOC Trainer | Cyber Warfare Organizer
Analityk i Konsultant ds. Bezpieczeństwa Cybernetycznego | Ekspert ds. Informatyki Śledczej | Trener SOC (Centrum Operacji Bezpieczeństwa Cybernetycznego) | Organizator Działań w Cyberwojnie
Oficjalna strona: https://michalsoltysik.com/
LinkedIn: https://www.linkedin.com/in/michal-soltysik-ssh-soc/
Treści z zakresu cyberbezpieczeństwa: https://www.youtube.com/playlist?list=PL0RdRWQWldOAAKBqOVEutxKMP-a6CNoLY
Accredible: https://www.credential.net/profile/michalsoltysik/wallet
Credly: https://www.credly.com/users/michal-soltysik
Email: me@michalsoltysik.com

Cel: Active Public IP Checker to narzędzie Windows GUI zaprojektowane do triage sieciowego punktu końcowego. Enumeruje aktywne połączenia TCP, odfiltrowuje niepubliczne zdalne punkty końcowe, koreluje każde połączenie z procesem, który je posiada, oraz opcjonalnie wzbogaca publiczne adresy IP o dane reputacyjne z AbuseIPDB w celu wsparcia tworzenia linii bazowej oraz dalszego threat huntingu.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - korzystanie z API AbuseIPDB:

(1) Narzędzie opcjonalnie integruje się z API AbuseIPDB w celu wzbogacenia publicznych adresów IP o kontekst reputacyjny oraz geolokalizacyjny.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowy komunikat informacyjny dotyczący korzystania z AbuseIPDB, opisujący limity planów API, ograniczenia szybkości oraz warunki dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować ten komunikat, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikat, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, ograniczenia licencyjne oraz przyjmują pełną odpowiedzialność za sposób wykorzystania zintegrowanej usługi.
(5) Sprawdzenia reputacyjne adresów IP są opcjonalne. Gdy klucz API nie zostanie podany, narzędzie pozostaje w pełni funkcjonalne w zakresie enumeracji połączeń i procesów, a jedynie końcowe kolumny reputacyjne pozostają niewypełnione.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług AbuseIPDB. Limity API oraz warunki licencyjne mogą ulegać zmianom w czasie.

Działanie aplikacji:

(1) Enumeruje wszystkie aktywne połączenia TCP bezpośrednio z systemu Windows przy użyciu biblioteki iphlpapi.dll (GetExtendedTcpTable), zarówno dla IPv4, jak i IPv6.
(2) Filtruje połączenia, pozostawiając wyłącznie te, w których zdalny punkt końcowy jest publicznym adresem IP, z wyłączeniem:

- Prywatnych zakresów RFC1918.
- Adresów loopback oraz link-local.
- Zakresów dokumentacyjnych i testowych.
- Adresów multicast, zarezerwowanych oraz nienawigowalnych.

(3) Dla każdego pozostałego połączenia zbiera:

- Publiczny adres IP (zdalny punkt końcowy).						- Public IP address (remote endpoint).
- Port źródłowy (port lokalny).								- Source port (local port).
- Port docelowy (port zdalny).								- Destination port (remote port).
- Protokół (TCPv4 lub TCPv6).								- Protocol (TCPv4 or TCPv6).
- Stan połączenia (np. ESTABLISHED, SYN_SENT, TIME_WAIT).				- Connection state (for example: ESTABLISHED, SYN_SENT, TIME_WAIT).
- PID, nazwę procesu (znormalizowaną z rozszerzeniem .exe) oraz ścieżkę procesu.	- PID, process name (normalized with .exe suffix), and process path.

(4) Opcjonalnie odpytuje AbuseIPDB dla każdego unikalnego publicznego adresu IP i wyświetla:

- Informacje geolokalizacyjne IP (nazwa kraju oraz kod kraju, jeśli dostępne).
- Podsumowanie reputacji AbuseIPDB, w tym:

  * Ten adres IP został zgłoszony X razy. Poziom zaufania nadużyć wynosi Y%.						- This IP was reported X times. Confidence of Abuse is Y%.
  * Ten adres IP nie został znaleziony w bazie danych.									- This IP was not found in our database.
  * Błąd AbuseIPDB: 401 Unauthorized (brak lub nieprawidłowy klucz API).						- AbuseIPDB error: 401 Unauthorized (missing/invalid API key).
  * Osiągnięto limit zapytań (HTTP 429) - spróbuj ponownie później.							- Rate limit hit (HTTP 429) - try again later.
  * Błąd AbuseIPDB: <status HTTP> <komunikat błędu> dla innych nieoczekiwanych odpowiedzi API.				- AbuseIPDB error: <HTTP status> <error message> for other unexpected API responses.
  * Błąd sieci/TLS: <komunikat błędu> w przypadku problemów z łącznością lub TLS, gdy nie zwrócono statusu HTTP.	- Network/TLS error: <error message> for connectivity or TLS-related failures where no HTTP status was returned.

Interfejs użytkownika i prezentowane dane:

Aplikacja wyświetla tabelę aktualizowaną na bieżąco z następującymi kolumnami:

- Publiczny adres IP			- Public IP Address
- Lokalizacja IP			- IP Location
- Port źródłowy				- Source Port
- Port docelowy				- Destination Port
- Protokół				- Protocol
- Stan połączenia			- Connection State
- PID					- PID
- Nazwa procesu				- Process Name
- Ścieżka procesu			- Process Path
- Podsumowanie reputacji AbuseIPDB	- AbuseIPDB Reputation Summary

Przebieg pracy i elementy sterujące:

(1) Start New Scan (Rozpocznij nowe skanowanie):

- Enumeruje bieżące aktywne połączenia TCP i filtruje je do publicznych adresów IP.
- Dodaje wyłącznie nowo zaobserwowane wiersze połączeń do istniejącej tabeli, bez usuwania wcześniejszych wyników.
- Buduje kolejkę roboczą unikalnych publicznych adresów IP wymagających wzbogacenia przez AbuseIPDB.
- Wykonuje zapytania reputacyjne z kontrolowanym tempem.

(2) Cancel (Anuluj):

- Natychmiast wstrzymuje skanowanie poprzez anulowanie aktywnych zadań przy użyciu CancellationToken.
- Zachowuje wszystkie zebrane wiersze oraz ukończone wyniki reputacyjne.
- Umożliwia użycie opcji Continue oraz Skip, jeśli pozostały oczekujące zadania.

(3) Continue Scanning (Kontynuuj skanowanie):

- Wznawia zapytania AbuseIPDB dokładnie od miejsca, w którym skanowanie zostało wstrzymane.
- Kontynuuje przetwarzanie kolejki adresów IP według indeksu, bez ponownego uruchamiania zakończonych zapytań.

(4) Skip Active IP Checks (Pomiń aktywne adresy IP):

- Zatrzymuje oczekujące zapytania AbuseIPDB i przełącza moduł w tryb tylko nowych połączeń.
- Tworzy migawkę tożsamości połączeń aktualnie obecnych w tabeli.
- Przy kolejnym Start New Scan dodaje i ocenia wyłącznie nowo zaobserwowane aktywne połączenia.

(5) Save The Results (Zapisz wyniki):

- Eksportuje bieżącą tabelę do pliku CSV na żądanie.
- Używa nazwy pliku z sygnaturą czasową i domyślnie zapisuje go na Pulpicie.

Szczegóły integracji z AbuseIPDB:

(1) Wyświetla obowiązkowy komunikat dotyczący korzystania z API AbuseIPDB podczas uruchamiania aplikacji.
(2) Prosi o podanie klucza API AbuseIPDB, który jest przechowywany wyłącznie w pamięci na czas bieżącej sesji.
(3) Jeśli klucz API nie zostanie podany lub okno dialogowe zostanie pominięte, aplikacja kontynuuje zbieranie metadanych połączeń i procesów bez wykonywania zapytań reputacyjnych.
(4) Korzysta z punktu końcowego AbuseIPDB check z parametrem maxAgeInDays ustawionym na 365 oraz włączonym trybem verbose.
(5) Zapytania są ograniczane czasowo do około jednego zapytania co 1,1 sekundy w celu respektowania limitów API.
(6) Wyświetla szacowany pozostały czas skanowania podczas przetwarzania kolejki reputacyjnej.
(7) Buforuje odpowiedzi AbuseIPDB dla poszczególnych adresów IP w pamięci, aby uniknąć powtarzania zapytań w ramach tej samej sesji.

Typowy scenariusz użycia:

(1) Utworzenie linii bazowej publicznych adresów IP, z którymi punkt końcowy aktywnie się komunikuje, oraz procesów odpowiedzialnych za te połączenia.
(2) Uruchomienie lub obserwacja podejrzanej aktywności w systemie.
(3) Ponowne użycie funkcji Start New Scan w celu szybkiej identyfikacji nowo ustanowionych połączeń wychodzących do publicznych adresów IP.
(4) Wykorzystanie kontekstu reputacyjnego AbuseIPDB do priorytetyzacji analizy nieoczekiwanej lub potencjalnie złośliwej aktywności sieciowej.