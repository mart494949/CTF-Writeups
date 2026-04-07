# HackTheBox: Meow — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 0)  
**Maszyna:** Meow  
**System docelowy:** Linux  

## 1. Rekonesans i Enumeracja
Rozpocząłem analizę celu od skanowania portów za pomocą narzędzia Nmap (nmap -p- -sS -A [IP]). 
Wynik skanowania wykazał otwarty port 23, na którym działała przestarzała usługa Telnet.

## 2. Wektor Ataku (Exploitacja)
Telnet to protokół pozwalający na zdalne logowanie do systemu. Zainicjowałem połączenie komendą (telnet [IP]).
System poprosił o podanie nazwy użytkownika. Zdecydowałem się sprawdzić domyślne konto administratora (root).

**Krytyczna luka:** Okazało się, że konto `root` jest całkowicie niezabezpieczone — nie posiadało skonfigurowanego hasła (blank password). Podanie samego loginu wystarczyło do natychmiastowego uzyskania najwyższych uprawnień w systemie i odczytania flagi.

## 3. Wnioski dla Blue Teamu
Usługa Telnet nie powinna być nigdy wystawiana ani używana w nowoczesnych środowiskach sieciowych. 

**Dlaczego to jest groźne:** Telnet przesyła cały ruch sieciowy (w tym wpisywane loginy i hasła) w otwartym tekście (plaintext), bez żadnego szyfrowania. W przypadku ataku typu Man-in-the-Middle (MitM) w sieci lokalnej, atakujący może bez problemu przechwycić poświadczenia.

**Rekomendacja:** Należy bezwzględnie wyłączyć i odinstalować usługę Telnet. Jedynym standardem do zdalnego zarządzania maszynami Linux powinien być protokół SSH (Secure Shell), który domyślnie szyfruje całą komunikację. Należy również audytować konta systemowe pod kątem pustych lub domyślnych haseł.
