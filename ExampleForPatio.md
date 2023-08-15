set DEBUG=0
if %DEBUG% NEQ 1 @echo off
if %DEBUG% NEQ 1 CLS
Setlocal EnableDelayedExpansion
set verfile=current.txt
timeout 20 > nul
:take_localdir
if exist "C:\Program Files\Astron\PowerPOS\v5\CashDesk.exe" (
	set "localdir=C:\Program Files\Astron\PowerPOS\v5\"
) else (
	if exist "C:\Program Files (x86)\Astron\PowerPOS\v5\CashDesk.exe" (
		set "localdir=C:\Program Files (x86)\Astron\PowerPOS\v5\"
	) else (
		goto no_powerpos
	)
)
:take_ip
for /F "tokens=2 delims=:" %%a in (' ipconfig ^| findstr IPv4 ^| findstr IP) do (  
	for /F "tokens=3 delims=." %%i in ("%%a") do ( 
		set ipaddr=IP
	)
)
set "serverdir=\\%ipaddr%\pos\update\"
set counter=1

:check_server
if %DEBUG% NEQ 1 CLS
ping -n 1 -l 1 -w 1000 %ipaddr% > nul
if %errorlevel% NEQ 0 (
	set /a counter+=1
	if %counter% LSS 4 goto check_server
	goto check_password
)

:check_version
for /F "tokens=1 delims= " %%a in ('type "!localdir!!verfile!"') do set localver=%%a
type "%localdir%%verfile%"
if %errorlevel% NEQ 0 set localver=№
type "%serverdir%%verfile%"
if %errorlevel% NEQ 0 goto check_password
for /F "tokens=1 delims= " %%a in ('type "!serverdir!!verfile!"') do set currentver=%%a
if /i %localver% LSS %currentver% goto update_version
goto end

:update_version
echo  version 
echo €¤св ®Ў­®ў«Ґ­ЁҐ. ЌҐ ўлЄ«оз ©вҐ Є ббг
xcopy "%serverdir%HF%currentver%" "%localdir%" /E /C /R /Y /Z > nul
if %errorlevel%==1 goto check_server
xcopy "%serverdir%%verfile%" "%localdir%" /C /R /Y /Z > nul
if %errorlevel%==1 goto check_server
xcopy "%localdir%%verfile%" "%serverdir%%COMPUTERNAME%\" /C /R /Y /Z > nul
goto end

:check_password
if %DEBUG% NEQ 1 CLS
echo "pass=ЌҐ г¤ «®бм гбв ­®ўЁвм бўп§м б бҐаўҐа®¬, б®®ЎйЁвҐ бЁбвҐ¬­®¬г  ¤¬Ё­Ёбва в®аг. Љ бб  Ўг¤Ґв § ЇгйҐ­  зҐаҐ§ 30 бҐЄ"
timeout 30 > nul
goto end
:no_powerpos
echo PowerPOS ­Ґ ­ ©¤Ґ­, гЎҐ¤ЁвҐбм, зв® Є бб®ў®Ґ ЏЋ гбв ­®ў«Ґ­®
pause
goto exit
:end
start /d "%localdir%" CashDesk.exe
:exit
exit