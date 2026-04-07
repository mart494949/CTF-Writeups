# HackTheBox: Sequel — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 1)  
**Maszyna:** Sequel  
**Wektor:** Database / Misconfiguration  

## 1. Rekonesans i Enumeracja
Użyłem komendy `nmap -sS -A -p- [IP]` do przeskanowania wszystkich portów i zidentyfikowałem otwarty port 3306, usługa to baza danych MariaDB.

## 2. Wektor Ataku (Exploitacja)
Skanowanie ujawniło, że usługa bazy danych jest wystawiona bezpośrednio na zewnątrz. Spróbowałem połączyć się z nią jako domyślny administrator (`root`) bez podawania hasła.
Początkowo klient odrzucił połączenie z powodu błędu wymuszania SSL przez mój system. 

**ominięcie:** Zmodyfikowałem komendę logowania dodając flagę omijającą szyfrowanie: `mysql -h [IP] -u root --skip-ssl`.
Zmodyfikowanie komendy zakończyło się sukcesem. Użyłem komendy SHOW DATABASES; do wyswietlenia baz danych. Zidentyfikowałem jedną niestandardową bazę danych, wpisałem komendę USE htb;. 
Po wejściu do bazy danych "htb" zastosowałem komendę SHOW TABLES do wyświetlenia tabel. Na końcu wyświetliłem całą tabelę config za pomocą komendy SELECT * from config i otrzymałem flagę.

## 3. Wnioski dla Blue Teamu
Wystawienie relacyjnej bazy danych bezpośrednio do publicznej sieci bez uwierzytelnienia to krytyczny błąd architektury i naruszenie podstawowych zasad bezpieczeństwa.

**Rekomendacje:**
1. **Network Binding i Segmentacja:** Baza danych (np. MySQL/MariaDB) powinna zawsze nasłuchiwać wyłącznie na interfejsie lokalnym (`bind-address = 127.0.0.1`) lub w wydzielonej, ściśle chronionej podsieci wewnętrznej (VLAN), do której dostęp ma tylko serwer aplikacji.
2. Należy bezwzględnie ustawić silne hasła dla konta `root` oraz usunąć wszelkie domyślne, anonimowe konta tworzone podczas instalacji bazy.
3. Wymusić szyfrowanie (SSL/TLS) dla wszystkich połączeń z bazą danych, aby zapobiec podsłuchiwaniu ruchu w sieci wewnętrznej.
