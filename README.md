### Arduino DS1302 second lag fix

I found the solution to rewind time by 20 seconds every day on my personel project.
<br>The timer has been running successfully for several months..

Codes :

~~~~
#include <virtuabotixRTC.h>

// RTC Port defines is here
#define RTC_ONE 2
#define RTC_TWO 3
#define RTC_THREE 4
int latest_day = 0;

virtuabotixRTC RTC(RTC_ONE, RTC_TWO, RTC_THREE);

void loop() {
  RTC.updateTime();
  //Get RTC times for print
  int rtc_hour = RTC.hours;
  int rtc_minute = RTC.minutes;
  int rtc_seconds = RTC.seconds;
  Serial.print(rtc_hour);
  Serial.print(":");
  Serial.print(rtc_minute);
  Serial.print(":");
  Serial.println(rtc_seconds);
  // rtc lag fix
  checkAndGoToBack();

}
/*
 * Second set for rtc module lag
 * The clock is taken back 20 seconds each day..
 */
void checkAndGoToBack() {
  if (latest_day == RTC.dayofmonth) {
    return;
  }
  latest_day = RTC.dayofmonth;
  // If the second is less than 20, it is waited for subtaction.
  while (RTC.seconds < 20) {
    RTC.updateTime();
    delay(1000);
  }
  int newSecond = RTC.seconds - 20;
  // Set all time object and - second - .
  RTC.setDS1302Time(newSecond, RTC.minutes, RTC.hours, RTC.dayofweek, RTC.dayofmonth, RTC.month, RTC.year);
  Serial.println("Second go to back for every day..");
}
~~~~
