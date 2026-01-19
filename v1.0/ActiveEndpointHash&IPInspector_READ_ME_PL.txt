Nazwa narzędzia: ActiveEndpointHash&IPInspector.exe

Wersja: 1.0
Suma kontrolna SHA256: A43FB41C5311AEF1C5E54962AB1930BE39BC330C03D8DE394832C329A94B3882
Rozmiar pliku: 162 KB
Napisane w języku C#, z przeznaczeniem dla platformy .NET Framework.
Skompilowane jako aplikacja Windows GUI w postaci pliku wykonywalnego .exe z nagłówkiem MZ.

Autor: Michał Sołtysik
Cybersecurity Analyst & Consultant | Forensics Examiner | SOC Trainer | Cyber Warfare Organizer
Analityk i Konsultant ds. Bezpieczeństwa Cybernetycznego | Ekspert ds. Informatyki Śledczej | Trener SOC (Centrum Operacji Bezpieczeństwa Cybernetycznego) | Organizator Działań w Cyberwojnie
Oficjalna strona: https://michalsoltysik.com/
LinkedIn: https://www.linkedin.com/in/michal-soltysik-ssh-soc/
Treści z zakresu cyberbezpieczeństwa: https://www.youtube.com/playlist?list=PL0RdRWQWldOAAKBqOVEutxKMP-a6CNoLY
Accredible: https://www.credential.net/profile/michalsoltysik/wallet
Credly: https://www.credly.com/users/michal-soltysik
Email: me@michalsoltysik.com

Cel: Active Endpoint Hash & IP Inspector to zestaw narzędzi Windows GUI, który łączy pięć modułów inspekcji punktu końcowego w jednej aplikacji z zakładkami. Został zaprojektowany do szybkiego tworzenia linii bazowej oraz dalszego threat huntingu poprzez połączenie lokalnej inspekcji punktu końcowego z opcjonalnym wzbogacaniem reputacyjnym przy użyciu VirusTotal oraz AbuseIPDB.
Licencja: Darmowa do użytku prywatnego i komercyjnego.

Ważna informacja - zewnętrzne API reputacyjne:

(1) Aplikacja opcjonalnie integruje się z zewnętrznymi serwisami reputacyjnymi (VirusTotal oraz AbuseIPDB) w celu wzbogacenia zebranych artefaktów o kontekstową inteligencję.
(2) Przy pierwszym uruchomieniu aplikacja wyświetla obowiązkowe okna informacyjne wyjaśniające limity użycia API, ograniczenia licencyjne oraz warunki dopuszczalnego użycia.
(3) Użytkownicy muszą jawnie zaakceptować te informacje, aby kontynuować. Odrzucenie akceptacji uniemożliwia dalsze korzystanie z aplikacji.
(4) Akceptując komunikaty, użytkownicy potwierdzają, że rozumieją obowiązujące limity API, są uprawnieni do korzystania z podanych kluczy API oraz przyjmują pełną odpowiedzialność za sposób użycia narzędzia i zintegrowanych usług.
(5) Sprawdzenia reputacyjne są opcjonalne. Gdy klucze API nie zostaną podane, wszystkie moduły inspekcji pozostają w pełni funkcjonalne, a jedynie końcowe kolumny związane z reputacją pozostają niewypełnione.

Uwaga: Użytkownicy są odpowiedzialni za zapewnienie zgodności z aktualnymi Warunkami Świadczenia Usług VirusTotal oraz AbuseIPDB. Limity API, warunki licencyjne oraz polityki mogą ulegać zmianom w czasie.

Struktura aplikacji:

ActiveEndpointHashIPInspector.exe jest główną aplikacją oraz ujednoliconym punktem wejściowym dla całego zestawu narzędzi.
Aplikacja hostuje pięć modułów inspekcji punktu końcowego w ramach pojedynczego interfejsu TabControl. Każdy moduł jest zaimplementowany jako dedykowany UserControl i osadzony jako osobna zakładka.
Sama główna aplikacja nie wykonuje logiki skanowania. Do jej obowiązków należy:

(1) hostowanie i orkiestracja modułów,
(2) prezentacja ujednoliconego interfejsu użytkownika,
(3) wymuszanie komunikatów startowych oraz akceptacji odpowiedzialności,
(4) obsługa opcjonalnego wprowadzania kluczy API dla bieżącej sesji,
(5) oraz zapewnienie spójnego modelu pracy we wszystkich modułach.

Hostowanie i ładowanie modułów:

(1) Każdy moduł inspekcji jest zaimplementowany jako UserControl i ładowany dynamicznie w czasie uruchomienia przy użyciu refleksji.
(2) Aplikacja przeszukuje bieżące zestawienie w poszukiwaniu UserControl, którego nazwa klasy odpowiada oczekiwanemu typowi modułu (na przykład: ActiveSHA256HashCheckerControl).
(3) Jeśli wymagany moduł nie jest obecny w buildzie, odpowiadająca mu zakładka wyświetla czytelny komunikat zastępczy opisujący:

- oczekiwaną nazwę klasy,
- wymaganą widoczność (publiczny UserControl),
- oraz typowe problemy builda (błędne nazewnictwo lub pozostałe punkty wejściowe Main()).

(4) Moduły mogą opcjonalnie nadpisać widoczny tytuł zakładki poprzez wystawienie publicznej właściwości typu string o nazwie ModuleTitle.

Sekwencja startowa i obsługa kluczy API:

Przy pierwszym uruchomieniu aplikacja wykonuje następującą sekwencję:

(1) Okno informacyjne VirusTotal:

- wyjaśnia zasady korzystania z Public API oraz Premium API, a także ograniczenia licencyjne,
- odrzucenie akceptacji powoduje zamknięcie aplikacji.

(2) Okno informacyjne AbuseIPDB:

- wyjaśnia limity planów oraz warunki dopuszczalnego użycia,
- odrzucenie akceptacji powoduje zamknięcie aplikacji.

(3) Okno wprowadzania dwóch kluczy API (opcjonalne):

- prośba o podanie VT_API_KEY oraz ABUSEIPDB_API_KEY,
- jeśli zostanie pominięte, aplikacja kontynuuje działanie, ale usuwa obie zmienne środowiskowe, aby uniknąć niejednoznacznej częściowej konfiguracji,
- jeśli klucze zostaną podane, są one przechowywane wyłącznie jako zmienne środowiskowe na poziomie procesu dla bieżącej sesji i nie są zapisywane trwale.

Doświadczenie użytkownika:

(1) Wszystkie możliwości inspekcji są dostępne w jednym zintegrowanym interfejsie GUI, co pozwala analitykom przełączać się między perspektywami punktu końcowego bez uruchamiania wielu plików wykonywalnych.
(2) Każdy osadzony moduł stosuje spójny model operacyjny:

- skanowanie zorientowane na linie bazowe,
- możliwość pauzowania i wznawiania długotrwałych sprawdzeń reputacyjnych,
- tryby Skip ograniczone do wykrywania nowych artefaktów w ramach danej zakładki,
- oraz funkcje eksportu do plików CSV zaimplementowane w każdym module.

(3) Aplikacja zapewnia jednolity wygląd, zachowanie oraz układ kontrolek we wszystkich modułach, aby ograniczyć obciążenie poznawcze analityka podczas prowadzonych analiz.

Przeznaczenie:

(1) Laboratoria SOC oraz stanowiska pracy analityków.
(2) Operacje Blue Team w środowiskach testowych oraz produkcyjnych.
(3) Działania z zakresu threat huntingu oraz inżynierii detekcji.
(4) Analiza złośliwego oprogramowania oraz inspekcja po jego wykonaniu w kontrolowanych scenariuszach.
(5) Triage incydentów oraz poincydentalna inspekcja punktu końcowego.
(6) Produkcyjne punkty końcowe do tworzenia linii bazowych i prowadzenia ciągłej inspekcji, pod warunkiem że złośliwy kod nie jest celowo uruchamiany.

Podsumowanie:

ActiveEndpointHashIPInspector.exe jest warstwą nadrzędną oraz orkiestratorem pięciomodułowego zestawu narzędzi do inspekcji punktów końcowych systemu Windows. Zapewnia jeden ujednolicony interfejs użytkownika, wymusza odpowiedzialne korzystanie z API, zarządza opcjonalnymi kluczami sesyjnymi oraz osadza poszczególne moduły inspekcji jako zakładki, wspierając analizę punktów końcowych opartą na liniach bazowych oraz działania threat huntingowe.