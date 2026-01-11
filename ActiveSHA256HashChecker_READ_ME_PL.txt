Nazwa narzędzia: ActiveSHA256HashChecker.exe

Wersja: 1.0
Suma kontrolna SHA256: 0EE07FBC2FE0E2DD9EB913900148F2980F7855A46FD74693E2FAB91C95FE38F1
Rozmiar pliku: 39,0 KB
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

Cel: Active SHA-256 Hash Checker to narzędzie Windows GUI zaprojektowane do szybkiego triage punktu końcowego w zakresie aktualnie uruchomionych procesów. Aplikacja enumeruje działające procesy, oblicza sumy kontrolne SHA-256 ich plików wykonywalnych na dysku oraz opcjonalnie wzbogaca te hashe o dane reputacyjne z VirusTotal w celu wsparcia tworzenia linii bazowej oraz dalszego threat huntingu.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - korzystanie z API VirusTotal:

(1) Narzędzie opcjonalnie integruje się z API VirusTotal w celu wzbogacenia hashy plików o informacje reputacyjne.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowy komunikat informacyjny dotyczący zasad korzystania z VirusTotal, w tym ograniczeń Public API oraz Premium API, a także warunków dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować ten komunikat, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikat, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, ograniczenia licencyjne oraz przyjmują pełną odpowiedzialność za sposób wykorzystania zintegrowanej usługi.
(5) Sprawdzenia reputacyjne są opcjonalne. Gdy klucz API nie zostanie podany, narzędzie pozostaje w pełni funkcjonalne w zakresie enumeracji procesów i obliczania hashy, a jedynie końcowa kolumna reputacyjna VirusTotal pozostaje niewypełniona lub wyświetla błąd autoryzacji.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług VirusTotal. Limity API oraz warunki licencyjne mogą ulegać zmianom w czasie.

Działanie aplikacji:

(1) Enumeruje wszystkie aktualnie uruchomione procesy, zbierając PID, nazwę procesu oraz ścieżkę do pliku wykonywalnego.
(2) Oblicza sumę kontrolną SHA-256 dla każdej unikalnej ścieżki pliku wykonywalnego poprzez odczyt pliku z dysku z użyciem współdzielenia do odczytu, co umożliwia hashowanie binariów będących aktualnie w użyciu.
(3) Opcjonalnie odpytuje VirusTotal dla każdej unikalnej sumy SHA-256 i wyświetla zwięzłe podsumowanie detekcji, w tym:

- Żaden dostawca zabezpieczeń nie oznaczył pliku jako złośliwego.						- No security vendors flagged this file as malicious.
- X/Y dostawców zabezpieczeń oznaczyło plik jako złośliwy.							- X/Y security vendors flagged this file as malicious.
- Błąd VirusTotal: 401 Unauthorized (brak lub nieprawidłowy klucz API).						- VirusTotal error: 401 Unauthorized (missing/invalid API key).
- Osiągnięto limit zapytań (HTTP 429) - spróbuj ponownie później.						- Rate limit hit (HTTP 429) - try again later.
- Obecnie nie posiadamy komentarzy pasujących do zapytania (np. hash nieznany lub brak użytecznych statystyk).	- We currently don't have any comments that fit your search (e.g., hash not found or no usable stats returned).
- Błąd VirusTotal: <status HTTP> <powód> dla innych nieoczekiwanych odpowiedzi.					- VirusTotal error: <HTTP status> <reason> for other unexpected responses.

(4) Wyświetla wszystkie wyniki w tabeli aktualizowanej na bieżąco.

Interfejs użytkownika i prezentowane dane:

Aplikacja wyświetla tabelę zawierającą następujące kolumny:

- PID									- PID
- Nazwa procesu (znormalizowana do postaci z rozszerzeniem .exe)	- Process Name (normalized to display with .exe suffix)
- Ścieżka procesu							- Process Path
- Hash SHA-256 procesu							- Process SHA-256 Hash
- Podsumowanie detekcji VirusTotal					- VirusTotal Detection Summary

Przebieg pracy i elementy sterujące:

(1) Start New Scan (Rozpocznij nowe skanowanie):

- Dodaje nowo wykryte procesy do istniejącej tabeli bez czyszczenia wcześniejszych wierszy.
- Odbudowuje wewnętrzne kolejki robocze dla obliczania hashy oraz opcjonalnych zapytań do VirusTotal.
- Po użyciu funkcji Skip Running Processes przetwarza wyłącznie procesy, które pojawiły się po wykonaniu akcji pominięcia.

(2) Cancel (Anuluj):

- Natychmiast wstrzymuje skanowanie poprzez anulowanie aktywnych zadań przy użyciu CancellationToken.
- Zachowuje wszystkie zebrane wiersze oraz ukończone wyniki hashy i reputacji.
- Pozwala użytkownikowi zdecydować, czy kontynuować skanowanie, czy pominąć pozostałe elementy.

(3) Continue Scanning (Kontynuuj skanowanie):

- Wznawia skanowanie dokładnie od miejsca, w którym zostało wstrzymane.
- Przeznaczone głównie do kontynuowania etapu zapytań do VirusTotal po przerwie.

(4) Skip Running Processes (Pomiń uruchomione procesy):

- Zatrzymuje przetwarzanie już zaobserwowanych procesów i przełącza skanowanie w tryb tylko nowych elementów.
- Tworzy migawkę aktualnie znanych PID-ów i ignoruje je przy kolejnych operacjach Start New Scan.
- Przydatne, gdy dotychczasowe zapytania VirusTotal nie są już istotne, a interesują wyłącznie nowo uruchomione procesy.

(5) Save The Results (Zapisz wyniki):

- Eksportuje aktualną zawartość tabeli do pliku CSV na żądanie.
- Domyślnie zapisuje plik na Pulpicie, używając nazwy z sygnaturą czasową.

Szczegóły integracji z VirusTotal:

(1) Obsługuje zarówno klucze VirusTotal Public API, jak i Premium API.
(2) Klucz API jest odczytywany ze zmiennej środowiskowej VT_API_KEY lub może zostać podany w oknie dialogowym.
(3) Jeśli klucz API nie zostanie podany lub okno dialogowe zostanie anulowane, obliczanie hashy przebiega normalnie, a zapytania do VirusTotal zwracają wskaźnik błędu autoryzacji.
(4) Zapytania do VirusTotal są ograniczane czasowo do około jednego zapytania co 15 sekund, zgodnie z limitem Public API wynoszącym 4 zapytania na minutę.
(5) Podczas etapu zapytań VirusTotal aplikacja wyświetla szacowany pozostały czas skanowania.

Funkcje efektywności i spójności:

(1) Deduplikacja:

- Haszowanie wykonywane jest jednokrotnie dla każdej unikalnej ścieżki pliku wykonywalnego i stosowane do wszystkich procesów współdzielących tę samą ścieżkę.
- Zapytania do VirusTotal wykonywane są jednokrotnie dla każdej unikalnej sumy SHA-256 i stosowane do wszystkich pasujących wierszy.

(2) Buforowanie:

- Bufor mapujący ścieżkę pliku wykonywalnego na sumę SHA-256 zapobiega ponownemu haszowaniu tego samego pliku.
- Bufor mapujący sumę SHA-256 na podsumowanie VirusTotal zapobiega powtarzaniu zapytań reputacyjnych.

(3) Raportowanie statusu w czasie rzeczywistym:

- Linie statusu wskazują bieżący etap (zbieranie danych, haszowanie, zapytania VirusTotal lub oczekiwanie na limit).
- Pozostały czas skanowania jest wyświetlany podczas etapu wzbogacania reputacyjnego.

Typowy scenariusz użycia:

(1) Utworzenie linii bazowej uruchomionych procesów oraz powiązanych z nimi hashy.
(2) Uruchomienie lub obserwacja nieznanej bądź podejrzanej aktywności na punkcie końcowym.
(3) Ponowne użycie funkcji Start New Scan w celu dodania i oceny nowo pojawiających się procesów.
(4) Wykorzystanie podsumowań VirusTotal do szybkiej identyfikacji nieoczekiwanych lub oznaczonych binariów, przy jednoczesnym zachowaniu możliwości wstrzymywania, wznawiania lub skupienia się wyłącznie na nowo wprowadzonych procesach.