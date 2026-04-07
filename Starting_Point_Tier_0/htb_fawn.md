# HackTheBox: Fawn — Write-up
## Środowisko

**Platforma:** HackTheBox (Starting Point — Tier 0)  
**Maszyna:** Fawn  
**System docelowy:** Linux  

## 1. Rekonesans i Enumeracja

Uzyłem komendy nmap -sS -A -p- [IP] ktora pokazała otwarty port 21 (ftp) oraz wersje

## 2. Wektor Ataku (Exploitacja)

Aby sie dostać na serwer FTP nie używając konta korzysta się z flagi -a i loguje sie jako anonymous.

## 3. Wnioski dla Blue Teamu

FTP z założenia jest niebezpieczne ponieważ dane są przesyłane w sposób jawny(plaintext). Powinno zostać zastąpione przez SFTP oraz zablokować możliwość logowania anonimowego.
