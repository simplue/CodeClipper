# bat 获取文件清单
```
@echo off
setlocal EnableDelayedExpansion

:input_dir_loop
set "dir_input_prompt=请输入要遍历的目录（按 Enter 键提交，默认为当前目录）："
set /p "dir_path=请输入要遍历的目录（按 Enter 键提交，默认为当前目录）："
if "%dir_path%"=="" (
  set "dir_path=%cd%"
  REM echo 输入不能为空，请重新输入。
  REM set /p "dir_path=%dir_input_prompt%"
  REM goto loop
)

set "share_prefix=\\"
if "%dir_path:~0,2%"=="%share_prefix%" (
  for /F "tokens=1 delims=\\" %%a in ("%dir_path%") do (
    set "host=\\%%a"
  )

  echo "%dir_path%" 是共享目录，主机名是 !host!。
  net use "!host!"
)

if not exist "%dir_path%" (
  echo 目录 "%dir_path%" 不存在，请重新输入。
  set "dir_path="
  goto input_dir_loop
)

set "prefix_input_prompt=请输入要输出文件前缀（按 Enter 键提交，默认为文件清单）："
set /p "csv_file_prefix=%prefix_input_prompt%"
if "%csv_file_prefix%" == "" set "csv_file_prefix=文件清单"

set "time_ymd=%date:~0,4%%date:~5,2%%date:~8,2%"
set "time_hms=%time:~0,2%%time:~3,2%%time:~6,2%"
set "time_stamp=%time_ymd%_%time_hms%"

set "csv_file=%cd%\%csv_file_prefix%_%time_stamp%.csv"
REM echo %csv_file%

echo "全路径","路径","全文件名","文件名","扩展名","大小","大小（字节）" > "%csv_file%"

(for /r "%dir_path%" %%f in (*) do (
  set "full_path=%%~f"
  set "file_path=%%~dpf"
  set "full_file_name=%%~nxf"
  set "file_name=%%~nf"
  set "ext_name=%%~xf"

  set "file_size_byte=%%~zf"
  set "file_size=%%~zf"
  set "unit=kb"
  set /a "file_size_kb=file_size/1024"

  echo !full_path!

  if !file_size_kb! GEQ 1024 (
    set /a "file_size_mb=file_size_kb/1024"
    set "unit=mb"
    if !file_size_mb! GEQ 1024 (
      set /a "file_size_gb=file_size_mb/1024"
      set "file_size=!file_size_gb!.!file_size_mb:~-1! GB"
    ) else (
      set "file_size=!file_size_mb!.!file_size_kb:~-1! MB"
    )
  ) else (
    set "file_size=!file_size_kb! KB"
  )
  
  echo !full_path!,!file_path!,!full_file_name!,!file_name!,!ext_name!,!file_size!,!file_size_byte! >> "%csv_file%"
))

IF DEFINED host (
  net use !host! /delete
)

echo "执行结果已输出到：%csv_file%"

pause
```