Nazwa narzędzia: ServiceHashChecker.exe

Wersja: 1.0
Suma kontrolna SHA256: 9CECF2C1E691D63FB071C1DF13A5CC467AD34197B18D37B7496FC7F960CF074A
Rozmiar pliku: 44,5 KB
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

Cel: Service Hash Checker to narzędzie Windows GUI zaprojektowane do triage punktu końcowego w oparciu o usługi systemowe. Inwentaryzuje usługi systemu Windows, wyodrębnia ścieżki plików wykonywalnych i linie poleceń, oblicza sumy kontrolne SHA-256 binariów usług na dysku oraz opcjonalnie wzbogaca te hashe o podsumowania detekcji VirusTotal, wspierając tworzenie linii bazowej oraz threat hunting ukierunkowany na mechanizmy persystencji systemowej.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - korzystanie z API VirusTotal:

(1) Narzędzie opcjonalnie integruje się z API VirusTotal w celu wzbogacenia hashy binariów usług o informacje reputacyjne.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowy komunikat informacyjny dotyczący zasad korzystania z VirusTotal, w tym ograniczeń Public API oraz Premium API, a także warunków dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować ten komunikat, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikat, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, ograniczenia licencyjne oraz przyjmują pełną odpowiedzialność za sposób wykorzystania zintegrowanej usługi.
(5) Sprawdzenia reputacyjne są opcjonalne. Gdy klucz API nie zostanie podany, enumeracja usług i haszowanie pozostają w pełni funkcjonalne, a końcowa kolumna reputacyjna wyświetla błąd autoryzacji lub pozostaje niewypełniona.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług VirusTotal. Limity API oraz warunki licencyjne mogą ulegać zmianom w czasie.

Działanie aplikacji:

(1) Enumeruje usługi systemu Windows przy użyciu wielu źródeł danych:

- WMI (Win32_Service) do pobrania PathName (ścieżka binarium wraz z argumentami), StartMode (typ uruchamiania) oraz State (stan).
- ServiceController do weryfikacji tożsamości usługi i jej bieżącego statusu.

(2) Normalizuje metadane usług w celu spójnego raportowania:

- Buduje czytelną nazwę usługi w formacie "DisplayName (ServiceName)", gdy jest dostępna.
- Normalizuje typy uruchamiania (np. Auto -> Automatic).
- Normalizuje stany usług (np. StartPending -> Start Pending).

(3) Niezawodnie wyodrębnia ścieżkę pliku wykonywalnego z linii poleceń usługi:

- Obsługuje ścieżki ujęte i nieujęte w cudzysłowy, w tym ścieżki zawierające spacje, poprzez obcięcie do .exe, .dll lub .sys, gdy jest to wymagane.
- Rozwija zmienne środowiskowe oraz rozwiązuje pliki wykonywalne względne przy użyciu katalogów System32, Windows oraz wyszukiwania w PATH.
- Wyświetla linie poleceń z nieujętym w cudzysłowy plikiem wykonywalnym dla czytelniejszej prezentacji, przy zachowaniu argumentów.

(4) Oblicza sumę kontrolną SHA-256 dla każdego rozwiązanego pliku wykonywalnego usługi:

- Odczytuje pliki przy użyciu FileShare.ReadWrite, co umożliwia haszowanie binariów aktualnie używanych.
- Generuje jednoznaczne wartości statusu zamiast hashy, gdy haszowanie nie jest możliwe, w tym:

  * ERROR_FILE_NOT_FOUND (WIN32: 2)       * Nie znaleziono pliku (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)             * Nie znaleziono ścieżki (WIN32: 3)
  * ACCESS_DENIED (WIN32: 5)              * Odmowa dostępu (WIN32: 5)
  * ERROR: <TypWyjątku>                   * Błąd: <TypWyjątku>

(5) Opcjonalnie odpytuje VirusTotal dla unikalnych sum SHA-256 i wyświetla zwięzłe werdykty:

- Żaden dostawca zabezpieczeń nie oznaczył pliku jako złośliwego.                                               - No security vendors flagged this file as malicious.
- X/Y dostawców zabezpieczeń oznaczyło plik jako złośliwy.                                                      - X/Y security vendors flagged this file as malicious.
- Błąd VirusTotal: 401 Unauthorized (brak lub nieprawidłowy klucz API).                                         - VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Osiągnięto limit zapytań (HTTP 429) - spróbuj ponownie później.                                               - Rate limit hit (HTTP 429) - try again later.
- Obecnie nie posiadamy komentarzy pasujących do zapytania (np. hash nieznany lub brak użytecznych statystyk).  - We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- Błąd VirusTotal: <status HTTP> <powód> dla innych nieoczekiwanych odpowiedzi.                                 - VirusTotal error: <HTTP status> <reason> for other unexpected responses.

Interfejs użytkownika i prezentowane dane:

Aplikacja wyświetla tabelę aktualizowaną na bieżąco z następującymi kolumnami:

- Nazwa usługi                          - Service Name
- Status                                - Status
- Typ uruchamiania                      - Startup Type
- Ścieżka pliku wykonywalnego           - Executable Path
- Nazwa pliku wykonywalnego             - Executable File Name
- Linia poleceń pliku wykonywalnego     - Executable Command Line
- Hash SHA-256 pliku wykonywalnego      - Executable File SHA-256 Hash
- Podsumowanie detekcji VirusTotal      - VirusTotal Detection Summary

Przebieg pracy i elementy sterujące:

(1) Start New Scan (Rozpocznij nowe skanowanie):

- Zbiera wszystkie bieżące usługi i dodaje wyłącznie nowo wykryte wpisy usług do istniejącej tabeli.
- Buduje wewnętrzne kolejki robocze dla haszowania oraz opcjonalnych zapytań do VirusTotal.
- Przetwarza wyniki z bieżącą aktualizacją tabeli.

(2) Cancel (Anuluj):

- Natychmiast wstrzymuje skanowanie poprzez anulowanie aktywnych zadań przy użyciu CancellationToken.
- Zachowuje wszystkie zebrane wiersze oraz ukończone wyniki hashy i reputacji.

(3) Continue Scanning (Kontynuuj skanowanie):

- Wznawia skanowanie dokładnie od miejsca, w którym zostało wstrzymane.
- Kontynuuje haszowanie oraz zapytania do VirusTotal z użyciem zapisanych indeksów kolejek.

(4) Skip Services (Pomiń usługi):

- Zatrzymuje przetwarzanie istniejących usług i przełącza moduł w tryb tylko nowych usług.
- Tworzy migawkę tożsamości usług obecnych w momencie pominięcia.
- Kolejne operacje Start New Scan dodają i oceniają wyłącznie nowo utworzone usługi.

(5) Save The Results (Zapisz wyniki):

- Eksportuje bieżącą tabelę do pliku CSV na żądanie.
- Używa nazwy pliku z sygnaturą czasową i domyślnie zapisuje go na Pulpicie.

Szczegóły integracji z VirusTotal:

(1) Obsługuje klucze VirusTotal Public API oraz Premium API.
(2) Klucz API jest odczytywany ze zmiennej środowiskowej VT_API_KEY lub może zostać podany w oknie dialogowym.
(3) Jeśli klucz API nie zostanie podany, haszowanie przebiega normalnie, a wyniki VirusTotal wyświetlają wskaźnik błędu autoryzacji.
(4) Zapytania do VirusTotal są ograniczane czasowo do około jednego zapytania co 15 sekund, zgodnie z limitem Public API wynoszącym 4 zapytania na minutę.
(5) Podczas etapu zapytań VirusTotal aplikacja wyświetla szacowany pozostały czas skanowania.

Funkcje wydajności:

(1) Deduplikacja:

- Haszowanie jest wykonywane jednokrotnie dla każdej unikalnej ścieżki pliku wykonywalnego i stosowane do wszystkich usług wskazujących na ten sam plik binarny.
- Zapytania do VirusTotal są wykonywane jednokrotnie dla każdej unikalnej sumy SHA-256 i stosowane do wszystkich pasujących wierszy.

(2) Buforowanie w pamięci:

- Bufor ścieżka pliku wykonywalnego -> SHA-256 zapobiega powtarzanemu haszowaniu.
- Bufor SHA-256 -> podsumowanie VirusTotal zapobiega powtarzanym zapytaniom reputacyjnym.

(3) Raportowanie statusu w czasie rzeczywistym:

- Linie statusu wskazują bieżącą fazę (zbieranie, haszowanie, zapytania VirusTotal lub oczekiwanie na limit).

Typowy scenariusz użycia:

(1) Utworzenie linii bazowej usług systemu Windows, w tym konfiguracji uruchamiania oraz lokalizacji binariów.
(2) Ponowne uruchomienie skanowania po podejrzeniu kompromitacji w celu szybkiej identyfikacji nowo dodanych usług.
(3) Weryfikacja binariów usług przy użyciu sum SHA-256 oraz opcjonalne wykorzystanie podsumowań VirusTotal do priorytetyzacji podejrzanych lub nieoczekiwanych mechanizmów persystencji systemowej.