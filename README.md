# Work
@echo off
setlocal enabledelayedexpansion

:: Get number of hours from user
set /p "hours=Enter number of hours to run (e.g., 2): "

:: Calculate total iterations needed (3 calls per hour since it's every 20 minutes)
set /a "total_iterations=hours * 3"
set "current_iteration=0"

:: Set the interval in seconds (20 minutes = 1200 seconds)
set "interval=1200"
set "countdown=%interval%"

echo.
echo Script will run for %hours% hours, making API calls every 20 minutes
echo Total API calls planned: %total_iterations%
echo Press Ctrl+C to stop the script at any time
echo.
timeout /t 3 > nul

:loop
:: Increment iteration counter
set /a "current_iteration+=1"

:: Make the API call using curl (replace URL with your actual API endpoint)
echo Making API call %current_iteration% of %total_iterations%...
curl -X GET "http://your-api-endpoint" -H "Content-Type: application/json"
echo API call completed at %time%
echo.

:: Check if we've reached the total number of iterations
if %current_iteration% GEQ %total_iterations% (
    echo.
    echo Script completed! Made %total_iterations% API calls over %hours% hours.
    pause
    exit /b 0
)

:: Countdown loop
:countdown_loop
:: Calculate remaining minutes and seconds for this interval
set /a "minutes=countdown/60"
set /a "seconds=countdown%%60"

:: Calculate overall remaining time
set /a "remaining_iterations=total_iterations-current_iteration"
set /a "remaining_hours=remaining_iterations/3"
set /a "remaining_minutes=(remaining_iterations%%3)*20"

:: Display countdown with proper formatting
if !seconds! LSS 10 (
    echo Next API call in: !minutes!:0!seconds!
) else (
    echo Next API call in: !minutes!:!seconds!
)
echo API calls remaining: %remaining_iterations% ^| Total time remaining: ~%remaining_hours% hours %remaining_minutes% minutes
echo Progress: %current_iteration%/%total_iterations% calls completed
echo.

:: Interruptible 1-second wait
timeout /t 1 /nobreak > nul

:: Decrease countdown
set /a "countdown-=1"

:: Check if we should make another API call
if !countdown! GTR 0 (
    cls
    goto countdown_loop
) else (
    set "countdown=%interval%"
    cls
    goto loop
)

exit /b 0
