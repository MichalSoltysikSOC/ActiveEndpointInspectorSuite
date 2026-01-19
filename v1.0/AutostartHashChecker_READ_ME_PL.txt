Nazwa narzędzia: AutostartHashChecker.exe

Wersja: 1.0
Suma kontrolna SHA256: 091FDF8E18FFD52AE815DA1265DC8571CE3D0701E79EDBA525AEC4794830DBEB
Rozmiar pliku: 57,0 KB
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

Cel: Autostart Hash Checker to narzędzie Windows GUI zaprojektowane do triage mechanizmów persystencji systemowej oraz threat huntingu. Agreguje ono typowe mechanizmy autostartu systemu Windows, rozwiązuje pliki wykonywalne stojące za poszczególnymi wpisami, oblicza sumy kontrolne SHA-256 tych plików oraz opcjonalnie wzbogaca wyniki o podsumowania detekcji z VirusTotal, wspierając tworzenie linii bazowej i wykrywanie nowo wprowadzonych mechanizmów persystencji systemowej.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - korzystanie z API VirusTotal:

(1) Narzędzie opcjonalnie integruje się z API VirusTotal w celu wzbogacenia binariów powiązanych z autostartem o informacje reputacyjne.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowy komunikat informacyjny dotyczący zasad korzystania z VirusTotal, w tym ograniczeń Public API oraz Premium API, a także warunków dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować ten komunikat, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikat, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, ograniczenia licencyjne oraz przyjmują pełną odpowiedzialność za sposób wykorzystania zintegrowanej usługi.
(5) Sprawdzenia reputacyjne są opcjonalne. Gdy klucz API nie zostanie podany, enumeracja mechanizmów persystencji systemowej oraz haszowanie pozostają w pełni funkcjonalne, a końcowa kolumna reputacyjna pozostaje niewypełniona lub wyświetla błąd autoryzacji.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług VirusTotal. Limity API oraz warunki licencyjne mogą ulegać zmianom w czasie.

Zakres zbieranych danych:

Narzędzie agreguje wiele metod persystencji systemowej w jednej, ujednoliconej tabeli.

(1) Usługi systemu Windows (autostart):

- Enumeruje usługi przy użyciu WMI (Win32_Service).
- Filtruje usługi skonfigurowane z parametrem StartMode ustawionym na Automatic.
- Analizuje wartości rejestru DelayedAutoStart w celu rozróżnienia trybów:

  * Automatyczny.                                 * Automatic.
  * Automatyczny (opóźnione uruchamianie).        * Automatic (Delayed Start).

- Wyodrębnia pliki wykonywalne z wartości PathName, obsługując ścieżki ujęte w cudzysłowy oraz nieujęte w cudzysłowy ścieżki zawierające spacje.
- Rejestruje lokalizację źródła persystencji systemowej pod kluczem: HKLM\SYSTEM\CurrentControlSet\Services<NazwaUsługi>

(2) Zadania Harmonogramu zadań z wyzwalaczami uruchomienia lub logowania:

- Wykorzystuje PowerShell Get-ScheduledTask do identyfikacji zadań uruchamianych przy starcie systemu lub logowaniu użytkownika.
- Wyodrębnia pierwszą akcję typu plik wykonywalny (Execute wraz z Arguments).
- Normalizuje wyzwalacze do postaci:

  * Przy uruchomieniu systemu.                            * At startup.
  * Przy logowaniu.                                       * At log on.
  * Przy uruchomieniu systemu / Przy logowaniu.           * At startup / At log on.

- Rejestruje ścieżki źródłowe zadań pod lokalizacją: C:\Windows\System32\Tasks<ŚcieżkaZadania><NazwaZadania>
- Oznacza zakres jako Machine lub User w zależności od kontekstu zadania.

(3) Klucze rejestru Run oraz RunOnce:

- Enumeruje klasyczne klucze autostartu:

  * HKLM\Software\Microsoft\Windows\CurrentVersion\Run
  * HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
  * HKCU\Software\Microsoft\Windows\CurrentVersion\Run
  * HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

- Wyodrębnia pliki wykonywalne z linii poleceń przy zachowaniu oryginalnych szczegółów wpisu.

(4) Foldery autostartu:

- Enumeruje:

  * Folder Autostart dla bieżącego użytkownika.
  * Wspólny folder Autostart (wszyscy użytkownicy).

- Rozwiązuje skróty .lnk przy użyciu WScript.Shell w celu pozyskania ścieżki docelowej i argumentów.
- Traktuje pliki inne niż .lnk jako bezpośrednio wykonywane cele.

(5) Persystencja systemowa oparta o subskrypcje zdarzeń WMI:

- Odpytuje przestrzeń nazw root\subscription w zakresie:

  * __EventFilter (definicje zapytań i przestrzenie nazw).
  * CommandLineEventConsumer (pliki wykonywalne, linie poleceń, katalogi robocze).
  * ActiveScriptEventConsumer (silnik skryptowy oraz osadzony kod skryptu).

- Ujawnia bezplikowe lub zdarzeniowe mechanizmy persystencji systemowej, które nie występują w klasycznych lokalizacjach autostartu.

Rozwiązywanie plików wykonywalnych i haszowanie:

(1) Normalizacja ścieżek plików wykonywalnych:

- Rozwija zmienne środowiskowe, takie jak %windir% oraz %SystemRoot%.
- Podejmuje próby rozwiązania ścieżek względnych przy użyciu katalogów System32, Windows oraz wyszukiwania w PATH.

(2) Obliczanie sum kontrolnych SHA-256 dla rozwiązywanych plików:

- Wykorzystuje FileShare.ReadWrite w celu haszowania binariów, które mogą być aktualnie używane.
- Generuje jednoznaczne komunikaty statusu w przypadku braku możliwości haszowania, w tym:

  * ERROR_FILE_NOT_FOUND (WIN32: 2)       * Nie znaleziono pliku (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)             * Nie znaleziono ścieżki (WIN32: 3)
  * ACCESS_DENIED (WIN32: 5)              * Odmowa dostępu (WIN32: 5)
  * ERROR: <TypWyjątku>                   * Błąd: <TypWyjątku>

(3) Wzbogacanie VirusTotal (opcjonalne):

- Odpytuje VirusTotal v3 po sumie SHA-256, gdy dostępny jest klucz VT_API_KEY.
- Wyświetla zwięzłe podsumowania detekcji, takie jak:

- Żaden dostawca zabezpieczeń nie oznaczył pliku jako złośliwego.                                               - No security vendors flagged this file as malicious.
- X/Y dostawców zabezpieczeń oznaczyło plik jako złośliwy.                                                      - X/Y security vendors flagged this file as malicious.
- Błąd VirusTotal: 401 Unauthorized (brak lub nieprawidłowy klucz API).                                         - VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Osiągnięto limit zapytań (HTTP 429) - spróbuj ponownie później.                                               - Rate limit hit (HTTP 429) - try again later.
- Obecnie nie posiadamy komentarzy pasujących do zapytania (np. hash nieznany lub brak użytecznych statystyk).  - We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- Błąd VirusTotal: <status HTTP> <powód> dla innych nieoczekiwanych odpowiedzi.                                 - VirusTotal error: <HTTP status> <reason> for other unexpected responses.

Interfejs użytkownika i prezentowane dane:

Aplikacja wyświetla tabelę aktualizowaną na bieżąco z następującymi kolumnami:

- Metoda persystencji systemowej        - Persistence Method
- Lokalizacja źródła                    - Source Location
- Wyzwalacz uruchomienia                - Startup Trigger
- Zakres                                - Scope
- Ścieżka docelowa                      - Target Path
- Nazwa wpisu lub pliku                 - Entry or File Name
- Szczegóły (np. linia poleceń)         - Details (for example: command line)
- Hash SHA-256 celu                     - Target SHA-256 Hash
- Podsumowanie detekcji VirusTotal      - VirusTotal Detection Summary

Przebieg pracy i elementy sterujące:

(1) Start New Scan (Rozpocznij nowe skanowanie):

- Zbiera bieżące wpisy persystencji systemowej i dodaje wyłącznie nowo wykryte wiersze.
- Nie usuwa ani nie nadpisuje wcześniej zebranych wyników.

(2) Cancel (Anuluj):

- Natychmiast wstrzymuje skanowanie, zachowując wszystkie zebrane wiersze oraz ukończone wyniki.

(3) Continue Scanning (Kontynuuj skanowanie):

- Wznawia haszowanie lub zapytania do VirusTotal dokładnie od momentu wstrzymania skanowania.

(4) Skip Current Items (Pomiń bieżące elementy):

- Przełącza moduł w tryb przetwarzania wyłącznie nowych elementów.
- Kolejne operacje Start New Scan obejmują tylko nowo wprowadzone mechanizmy persystencji systemowej.

(5) Save The Results (Zapisz wyniki):

- Eksportuje pełną tabelę do pliku CSV na żądanie.
- Używa nazwy pliku z sygnaturą czasową i domyślnie zapisuje go na Pulpicie.

Typowy scenariusz użycia:

(1) Zbudowanie linii bazowej mechanizmów persystencji systemowej obecnych na punkcie końcowym systemu Windows.
(2) Ponowne skanowanie po uruchomieniu podejrzanego instalatora lub próbki w celu identyfikacji nowo dodanych wpisów autostartu.
(3) Powiązanie artefaktów persystencji systemowej z rzeczywistymi plikami binarnymi na dysku i ich weryfikacja przy użyciu sum SHA-256.
(4) Ujawnienie ukrytych technik persystencji systemowej, takich jak subskrypcje zdarzeń WMI, obok klasycznych mechanizmów autostartu.