# HackTheBox: Archetype — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 2)  
**Maszyna:** Archetype (Windows)  
**Wektor:** SMB Share / MSSQL xp_cmdshell RCE / Cleartext Passwords / PsExec Privilege Escalation

## 1. Rekonesans i Enumeracja
Skanowanie portów Nmap ujawniło otwarte porty 445 (SMB) oraz 1433 (MSSQL).
Korzystając z narzędzia `smbclient`, zalogowałem się na anonimowy udział sieciowy `backups`, z którego pobrałem plik konfiguracyjny `prod.dtsConfig`. W pliku tym znalazłem zapisane jawnym tekstem dane logowania (login i hasło) do konta serwisowego bazy danych `sql_svc`.

## 2. Foothold (Wektor Ataku)
Używając skryptu `impacket-mssqlclient` z flagą `-windows-auth`, zalogowałem się na konto użytkownika `ARCHETYPE\sql_svc`. Po uzyskaniu dostępu do bazy SQL, włączyłem zaawansowaną opcję `xp_cmdshell`, która pozwoliła mi na wykonywanie poleceń systemowych w wierszu poleceń Windowsa. 
Następnie postawiłem lokalny serwer HTTP w Pythonie i za pomocą komendy PowerShell zmusiłem serwer do pobrania i wykonania skryptu `Invoke-PowerShellTcp.ps1` (Nishang). To pozwoliło mi przechwycić Reverse Shell i zdobyć flagę użytkownika (User Flag).

## 3. Eskalacja Uprawnień (Privilege Escalation)
Mając dostęp jako użytkownik `sql_svc`, rozpocząłem poszukiwania śladów w systemie. Zlokalizowałem plik historii programu PowerShell (`ConsoleHost_history.txt`) w katalogu `AppData` użytkownika.
W historii poleceń znalazłem komendę mapowania dysku sieciowego, w której administrator użył swojego hasła wpisanego jawnym tekstem. Posiadając poświadczenia konta `Administrator`, użyłem pakietu Impacket (skrypty `psexec` / `smbexec`), aby połączyć się z serwerem i uzyskać najwyższe uprawnienia w systemie (`NT AUTHORITY\SYSTEM`), co pozwoliło na odczytanie flagi Root.

## 4. Wnioski dla Blue Teamu
Kompromitacja maszyny była możliwa z powodu nagromadzenia fatalnych praktyk zarządzania hasłami i uprawnieniami.

**Rekomendacje:**
1. **Ochrona poświadczeń:** Hasła nigdy nie powinny być przechowywane jawnym tekstem w plikach konfiguracyjnych na ogólnodostępnych udziałach sieciowych (SMB).
2. **Hardenizacja MSSQL:** Konto usługi SQL (Service Account) powinno mieć minimalne wymagane uprawnienia. Funkcja `xp_cmdshell` powinna pozostać wyłączona, a jeśli jest bezwzględnie wymagana, dostęp do niej musi być ściśle audytowany.
3. **Edukacja Administratorów:** Należy bezwzględnie unikać podawania haseł jawnym tekstem w parametrach linii komend (np. przy poleceniach `net use`), ponieważ współczesne powłoki logują tę aktywność na dysku.
