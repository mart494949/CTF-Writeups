# HackTheBox: Redeemer — Write-up

## Środowisko
**Platforma:** HackTheBox (Starting Point — Tier 0)  
**Maszyna:** Redeemer  
**System docelowy:** Linux  

## 1. Rekonesans i Enumeracja
Przeprowadziłem skanowanie wszystkich portów komendą `sudo nmap -sS -A -p- [IP]`. Skanowanie na pełnym zakresie (`-p-`) było kluczowe, ponieważ docelowa usługa działała na stosunkowo wysokim, niestandardowym porcie **6379**. 
Skan wykazał, że działa tam baza danych Redis (in-memory NoSQL database).

## 2. Wektor Ataku (Exploitacja)
Do połączenia się z celem wykorzystałem dedykowane narzędzie klienckie `redis-cli`. 
**Krytyczna luka:** Baza danych została wystawiona na świat bez żadnego uwierzytelnienia (brak wymogu podania hasła). 
Po nawiązaniu połączenia (`redis-cli -h [IP]`), użyłem komendy `keys *`, aby wyenumerować wszystkie klucze w bazie. Zidentyfikowałem klucz zawierający flagę i odczytałem jego zawartość za pomocą komendy `get [nazwa_klucza]`.

## 3. Wnioski dla Blue Teamu
Bazy danych typu in-memory są projektowane pod kątem maksymalnej wydajności, a nie wbudowanego bezpieczeństwa. Wystawienie ich bez ochrony na zewnątrz sieci to krytyczny błąd konfiguracji.

**Rekomendacje:**
1. **Network Binding:** Redis powinien nasłuchiwać wyłącznie na interfejsie lokalnym (`bind 127.0.0.1`), chyba że architektura bezwzględnie wymaga dostępu z zewnątrz.
2. **Uwierzytelnienie:** Należy włączyć wymóg uwierzytelniania w pliku konfiguracyjnym `redis.conf` (dyrektywa `requirepass`), stosując silne, złożone hasło.
3. Dostęp sieciowy do portu 6379 powinien być ściśle kontrolowany przez zaporę sieciową (Firewall/Security Groups) i ograniczony tylko do zaufanych adresów IP (np. serwerów aplikacji).
