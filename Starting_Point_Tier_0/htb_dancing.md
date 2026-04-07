# HackTheBox: Dancing — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 0)  
**Maszyna:** Dancing  
**System docelowy:** Windows  

## 1. Rekonesans i Enumeracja
Użyłem komendy skanującej `sudo nmap -sS -A -p- [IP]`, która wykazała otwarty port 445 działający na usłudze `microsoft-ds` (protokół SMB). 
Do enumeracji usługi użyłem narzędzia `smbclient`, wykonując polecenie `smbclient -L //[IP]/`, aby wylistować dostępne udziały sieciowe (SMB Shares) bez podawania hasła (Null Session).

## 2. Wektor Ataku (Exploitacja)
Skanowanie ujawniło kilka udziałów sieciowych. Okazało się, że jeden z nich (udział o nazwie `WorkShares`) był źle skonfigurowany i pozwalał na dostęp gościnny (Guest Access). 
Zalogowałem się do udziału z pustym hasłem komendą `smbclient //[IP]/WorkShares`, co pozwoliło mi na swobodne przeglądanie struktury katalogów, pobranie wrażliwych plików i odczytanie flagi.

## 3. Wnioski dla Blue Teamu
Zła konfiguracja uprawnień na udostępnionych folderach sieciowych to częsty i krytyczny błąd w środowiskach Windows/Active Directory. 

**Rekomendacje:**
1. **Zasada najmniejszych uprawnień (Least Privilege):** Należy całkowicie wyłączyć dostęp anonimowy (Null Sessions) oraz dostęp dla konta "Gość" (Guest) do jakichkolwiek zasobów sieciowych.
2. Każdy udział SMB powinien wymagać silnego uwierzytelnienia (uwierzytelnieni użytkownicy z domeny).
3. Należy regularnie audytować uprawnienia NTFS i uprawnienia współdzielenia (Share permissions) na serwerach plików.
