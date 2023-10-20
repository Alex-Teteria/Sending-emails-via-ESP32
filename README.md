# Sending-emails-via-ESP32
Using ESP32 with MicroPython to send email

Requires: ESP32 microcontroller board. You need a sender email and you’ll need to know your email SMTP server settings.

## Contents

1. [Overview](./README.md#1-overview)
2. [Obtaining a free SMTP service for sending email](./README.md#2-Obtaining-a-free-SMTP-service-for-sending-email)
3. [Install](./README.md#3-install)
4. [Quick start](./README.md#4-Quick-start)
5. [Some notes](./README.md#5-Some-notes-when-preparing-messages-for-sending)

## 1. Overview

Using an ESP32 board with MicroPython automated the process of sending emails using an SMTP server. As an example, sending notifications by e-mail when sensors are triggered is implemented. It's easy to use and you can receive realtime alterts in all your projects. To send e-mails using MicroPython, the [uMail](https://github.com/shawwwn/uMail) module is used. This module is not part of the MicroPython library, so it needs to be downloaded.

## 2. Obtaining a free SMTP service for sending email
I strongly recommend creating a new e-mail account from which the newsletter will be sent. If something goes wrong and you get banned, it won't be your main email address :blush:  
This newly created account must be used when registering on the SMTP mailing service.  
After creating the sender email, the next step is to get the SMTP email server settings. I haven't been able to get them through Gmail yet.
Therefore, after a short wandering, the choice settled on the following services, taking into account the availability of a free option:  
[Sendgrid](https://sendgrid.com/). Its free use allows you to send 100 letters per day.  
[Brevo](https://www.brevo.com/). Its free use allows you to send 300 letters per day.
All of them require verification during registration by sending an SMS message code to the phone number you specified. When registering, you should specify the new e-mail that was created for sending messages.

## 3. Install
Copy the code to your project.

## 4. Quick start
#### Typical Wi-Fi connection code for ESP board (Station mode)

```python
import network

def connect(ssid, passwd):
    wlan.disconnect()
    wlan.connect(ssid, passwd)
    while not wlan.isconnected():
        pass
    return wlan.ifconfig()

if __name__ == '__main__':
    wlan_id = 'your wi-fi ssid'
    wlan_pass = 'your password'
    
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    print(connect(wlan_id, wlan_pass))
```
#### Creating an object of the Mail_bot class
```python
class Mail_bot:
    """This is our Mail bot"""
    def __init__(self, smtp_server, smtp_login, smtp_key, from_email):
        self.smtp_server = smtp_server
        self.smtp_login = smtp_login
        self.smtp_key = smtp_key
        self.from_email = from_email
        
    def send_mail(self, to_email, subject, message):
        to_user = 'To: '+to_email.split('@')[0]+' <'+to_email+'>\n'
        smtp = umail.SMTP(self.smtp_server, 465, ssl=True)
        smtp.login(self.smtp_login, self.smtp_key)
        smtp.to(to_email)
        smtp.write(self.from_email)
        smtp.write(to_user)
        smtp.write('Subject: ' + subject +'\n\n')
        l = message.split('\n')
        for s in l:
            smtp.write(s + '\n')
        smtp.send()
        smtp.quit()
```
#### Creating an object of the Set_time class
```python
class Set_time:
    '''
    Підводить годинник реалного часу (rtc) до поточного часу сервера, який вказано
    в параметрі url. Для формування запиту використовує бібліотеку urequests
    '''
    def __init__(self, rtc, time_zone=2, url='http://time.in.ua/'):
        self.rtc = rtc # примірник об'єкту machine.RTC()
        self.time_zone = time_zone # UTC+2
        self.url = url
        self.d_month = {'Jan': 1, 'Feb':2, 'Mar': 3, 'Apr': 4, 'May': 5, 'Jun': 6,
                        'Jul': 7, 'Aug': 8, 'Sep': 9, 'Oct': 10, 'Nov': 11, 'Dec': 12}
        self.d_weekday = {'Mon,': 1, 'Tue,': 2, 'Wed,': 3, 'Thu,': 4, 'Fri,': 5, 'Sat,': 6, 'Sun,': 7}
                
    def set_rtc(self):
        try:
            r = urequests.head(self.url)
            weekday, day, month, year, current_time, mt = r.headers['Date'].split()
            hours, minutes, seconds = [int(x) for x in current_time.split(':')]
            print(int(year), self.d_month[month], int(day), self.d_weekday[weekday], int(hours), int(minutes), int(seconds))
# Заводимо годинник:   
            self.rtc.datetime((int(year), self.d_month[month], int(day), self.d_weekday[weekday], int(hours)+self.time_zone, int(minutes), int(seconds), 200))
        except:
            print('Error! Unable to create request instance')
```
#### Example sending e-mails according to the mailing list when the sensors are triggered
```python

'''
