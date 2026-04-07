# HackTheBox: Crocodile — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 1)  
**Maszyna:** Crocodile  
**Wektor:** Vulnerability Chaining (FTP Anonymous Login + Web Panel)  

## 1. Rekonesans i Enumeracja
W celu oszczędzenia czasu najpierw użyłem narzędzia nmap w postaci `nmap -p- --min-rate 1000 -T4 [IP]`. Po znalezieniu dwoch otwartych portów zastosowałem `nmap -p21,80 -sC [IP]`.

## 2. Wektor Ataku (Exploitacja)
Atak wymagał połączenia dwóch wektorów (Vulnerability Chaining):
1. Zalogowałem się na serwer FTP używając konta `anonymous`. Pobrałem stamtąd pliki zawierające nazwy użytkowników i hasła (allowed.userlist i allowed.userlist.passwd).
2. Znalazłem panel logowania za pomocą narzędzia gobuster. Użyłem poświadczeń wyciągniętych z FTP, aby zalogować się do panelu administracyjnego, co pozwoliło mi na przejęcie kontroli nad aplikacją i odczytanie flagi.

## 3. Wnioski dla Blue Teamu
Przechowywanie wrażliwych danych uwierzytelniających (loginów i haseł) w postaci jawnego tekstu (plaintext) na publicznie dostępnych udziałach to krytyczny błąd operacyjny.

**Rekomendacje:**
1. Całkowite wyłączenie dostępu anonimowego do serwera FTP.
2. Wdrożenie polityki zakazującej przechowywania haseł w plikach tekstowych. Należy używać dedykowanych menedżerów haseł (Secrets Management) typu HashiCorp Vault lub chmurowych odpowiedników.
3. Panele administracyjne nie powinny być wystawione publicznie do internetu – dostęp do nich powinien być możliwy wyłącznie z wewnętrznej sieci VPN i chroniony za pomocą Multi-Factor Authentication (MFA).
