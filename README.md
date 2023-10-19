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
After creating the sender email, the next step is to get the SMTP email server settings. I haven't been able to get them using the Gmail service so far.
Therefore, after some wanderings, the choice fell on the service [sendgrid](https://sendgrid.com/)

## 3. Install
Copy the code to your project.

## 4. Quick start
#### Typical Wi-Fi connection code for ESP board (Station mode)

```python
import network
