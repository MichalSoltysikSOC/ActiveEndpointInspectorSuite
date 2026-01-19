Nazwa narzędzia: ScheduledTaskHashChecker.exe

Wersja: 1.0
Suma kontrolna SHA256: B7E0E117D3B747E74918A369928434E8D83E8F5BB1F090956E065ABCAC3330AB
Rozmiar pliku: 53,5 KB
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

Cel: Scheduled Task Hash Checker to narzędzie Windows GUI zaprojektowane do analizy persystencji systemowej oraz threat huntingu ukierunkowanego na Zaplanowane zadania systemu Windows. Aplikacja enumeruje zaplanowane zadania, rozwija je do poziomu pojedynczych akcji, oblicza sumy kontrolne SHA-256 plików wykonywalnych akcji oraz opcjonalnie wzbogaca te hashe o podsumowania detekcji VirusTotal, wspierając tworzenie linii bazowej i wykrywanie nowo wprowadzonych mechanizmów persystencji systemowej.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - korzystanie z API VirusTotal:

(1) Narzędzie opcjonalnie integruje się z API VirusTotal w celu wzbogacenia binariów akcji zaplanowanych zadań o informacje reputacyjne.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowy komunikat informacyjny dotyczący zasad korzystania z VirusTotal, w tym ograniczeń Public API oraz Premium API, a także warunków dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować ten komunikat, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikat, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, ograniczenia licencyjne oraz przyjmują pełną odpowiedzialność za sposób wykorzystania zintegrowanej usługi.
(5) Sprawdzenia reputacyjne są opcjonalne. Gdy klucz API nie zostanie podany, enumeracja zadań i haszowanie pozostają w pełni funkcjonalne, a końcowa kolumna reputacyjna pozostaje niewypełniona lub wyświetla błąd autoryzacji.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług VirusTotal. Limity API oraz warunki licencyjne mogą ulegać zmianom w czasie.

Działanie aplikacji:

(1) Enumeruje wszystkie Zaplanowane zadania systemu Windows przy użyciu natywnego interfejsu COM Harmonogramu zadań, w tym zadania znajdujące się w zagnieżdżonych folderach.
(2) Przetwarza zadania na poziomie akcji:

- Każda akcja typu plik wykonywalny jest prezentowana jako osobny wiersz.
- Zadania zawierające wiele akcji są w pełni rozwijane.
- Zadania bez akcji typu plik wykonywalny są nadal rejestrowane w celu zapewnienia widoczności.

(3) Zbiera szczegółowy kontekst zadania:

- Pełna nazwa zaplanowanego zadania wraz ze ścieżką folderu.
- Typy wyzwalaczy i harmonogramy (np. logowanie, uruchomienie systemu, czasowe, zdarzeniowe, włączone lub wyłączone).
- Typ akcji zadania (Exec, ComHandler, SendEmail, ShowMessage lub Unknown).

(4) Normalizuje ścieżki plików wykonywalnych i linie poleceń:

- Wyodrębnia ścieżki plików wykonywalnych z definicji zadań.
- Rozwija zmienne środowiskowe, takie jak %windir% oraz %SystemRoot%.
- Rozwiązuje ścieżki względne przy użyciu katalogów System32, Windows oraz wyszukiwania w PATH.
- Wyświetla linie poleceń bez cudzysłowów wokół pliku wykonywalnego, przy zachowaniu argumentów.

(5) Oblicza sumy kontrolne SHA-256 dla rozwiązywanych plików wykonywalnych akcji:

- Wykorzystuje FileShare.ReadWrite w celu haszowania binariów, które mogą być aktualnie używane.
- Generuje jednoznaczne komunikaty statusu w przypadku braku możliwości haszowania, w tym:

  * ERROR_FILE_NOT_FOUND (WIN32: 2)       * Nie znaleziono pliku (WIN32: 2)
  * PATH_NOT_FOUND (WIN32: 3)             * Nie znaleziono ścieżki (WIN32: 3)
  * ACCESS_DENIED (WIN32: 5)              * Odmowa dostępu (WIN32: 5)
  * ERROR: <TypWyjątku>                   * Błąd: <TypWyjątku>

(6) Opcjonalnie odpytuje VirusTotal dla unikalnych sum SHA-256 i wyświetla zwięzłe werdykty:

- Żaden dostawca zabezpieczeń nie oznaczył pliku jako złośliwego.                                               - No security vendors flagged this file as malicious.
- X/Y dostawców zabezpieczeń oznaczyło plik jako złośliwy.                                                      - X/Y security vendors flagged this file as malicious.
- Błąd VirusTotal: 401 Unauthorized (brak lub nieprawidłowy klucz API).                                         - VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Osiągnięto limit zapytań (HTTP 429) - spróbuj ponownie później.                                               - Rate limit hit (HTTP 429) - try again later.
- Obecnie nie posiadamy komentarzy pasujących do zapytania (np. hash nieznany lub brak użytecznych statystyk).  - We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- Błąd VirusTotal: <status HTTP> <powód> dla innych nieoczekiwanych odpowiedzi.                                 - VirusTotal error: <HTTP status> <reason> for other unexpected responses.

Interfejs użytkownika i prezentowane dane:

Aplikacja wyświetla tabelę aktualizowaną na bieżąco z następującymi kolumnami:

- Nazwa zaplanowanego zadania           - Scheduled Task Name
- Wyzwalacze zadania                    - Task Triggers
- Typ akcji zadania                     - Task Action Type
- Ścieżka pliku akcji                   - Action File Path
- Nazwa pliku akcji                     - Action File Name
- Linia poleceń akcji                   - Action Command Line
- Hash SHA-256 pliku akcji              - Action File SHA-256 Hash
- Podsumowanie detekcji VirusTotal      - VirusTotal Detection Summary

Przebieg pracy i elementy sterujące:

(1) Start New Scan (Rozpocznij nowe skanowanie):

- Enumeruje wszystkie bieżące zaplanowane zadania i rozwija je do wierszy akcji.
- Dodaje wyłącznie nowo wykryte akcje zadań do istniejącej tabeli, bez usuwania wcześniejszych wyników.

(2) Cancel (Anuluj):

- Natychmiast wstrzymuje skanowanie poprzez anulowanie aktywnych zadań.
- Zachowuje wszystkie zebrane wiersze oraz ukończone wyniki hashy i reputacji.

(3) Continue Scanning (Kontynuuj skanowanie):

- Wznawia haszowanie lub zapytania do VirusTotal dokładnie od miejsca, w którym skanowanie zostało wstrzymane.
- Kontynuuje przetwarzanie z użyciem zapisanych indeksów kolejek.

(4) Skip Scheduled Tasks (Pomiń zaplanowane zadania):

- Zamraża aktualnie znany zestaw zadań i przełącza moduł w tryb tylko nowych zadań.
- Kolejne operacje Start New Scan przetwarzają wyłącznie nowo utworzone zaplanowane zadania lub akcje.

(5) Save The Results (Zapisz wyniki):

- Eksportuje pełną tabelę do pliku CSV na żądanie.
- Używa nazwy pliku z sygnaturą czasową i domyślnie zapisuje go na Pulpicie.

Funkcje wydajności i analizy:

(1) Deduplikacja:

- Haszowanie jest wykonywane jednokrotnie dla każdej unikalnej ścieżki pliku wykonywalnego i stosowane do wszystkich akcji zadań używających tego samego pliku binarnego.
- Zapytania do VirusTotal są wykonywane jednokrotnie dla każdej unikalnej sumy SHA-256.

(2) Buforowanie w pamięci:

- Bufor ścieżka pliku wykonywalnego -> SHA-256 ogranicza powtarzane haszowanie.
- Bufor SHA-256 -> podsumowanie VirusTotal minimalizuje zbędne zapytania do API.

(3) Stabilna tożsamość zadania:

- Utrzymuje spójną identyfikację przy użyciu unikalnego klucza wyprowadzonego ze ścieżki zadania, nazwy zadania oraz indeksu akcji.

(4) Raportowanie statusu w czasie rzeczywistym:

- Wyświetla bieżący etap skanowania oraz szacowany pozostały czas podczas wzbogacania VirusTotal.

Typowy scenariusz użycia:

(1) Utworzenie linii bazowej mechanizmów persystencji systemowej opartych na zaplanowanych zadaniach na punkcie końcowym systemu Windows.
(2) Ponowne uruchomienie skanowania po podejrzeniu kompromitacji lub wykonaniu złośliwego oprogramowania w celu identyfikacji nowo dodanych lub zmodyfikowanych zaplanowanych zadań.
(3) Weryfikacja binariów akcji zadań przy użyciu sum SHA-256 oraz opcjonalne wykorzystanie podsumowań VirusTotal do priorytetyzacji podejrzanych mechanizmów persystencji systemowej.
(4) Wsparcie triage SOC, działań DFIR oraz proaktywnego threat huntingu ukierunkowanego na nadużycia zaplanowanych zadań.