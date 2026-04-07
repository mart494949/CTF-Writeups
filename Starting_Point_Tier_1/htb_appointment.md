# HackTheBox: Appointment — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 1)  
**Maszyna:** Appointment  
**Wektor:** Web / SQL Injection (SQLi)  

## 1. Rekonesans i Enumeracja
Użyłem komendy `sudo nmap -sS -A -p- [IP]` do przeskanowania wszystkich portów i zidentyfikowałem aplikacje webową działającą na porcie 80.

## 2. Wektor Ataku (Exploitacja)
Panel logowania był podatny na atak **SQL Injection**.
Zastosowałem atak SQL Injection wpisując login admin' #. Znak apostrofu (') pozwolił na zamknięcie ciągu znaków (stringu) i 'ucieczkę' z pola tekstowego w zapytaniu bazy, a znak # zakomentował resztę oryginalnego zapytania (w tym weryfikację hasła).
Dzięki temu uzyskałem nieautoryzowany dostęp do panelu administracyjnego (Authentication Bypass).

## 3. Wnioski dla Blue Teamu
Błąd ten wynika z bezpośredniego i niebezpiecznego łączenia (konkatenacji) danych wprowadzanych przez użytkownika z kodem zapytania SQL.

**Rekomendacje:**
1. **Prepared Statements (Zapytania parametryzowane):** To absolutny standard i jedyna w pełni skuteczna metoda obrony przed SQLi. Kod aplikacji powinien oddzielać logikę zapytania od danych dostarczanych przez klienta.
2. Należy zaimplementować zasadę "Nigdy nie ufaj danym od użytkownika" (Zero Trust dla inputu), używając dodatkowo frameworków typu ORM (Object-Relational Mapping), które automatycznie sanituzją wprowadzane dane.
