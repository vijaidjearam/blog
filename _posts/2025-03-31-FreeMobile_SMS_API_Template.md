---
layout: post
date: 2025-03-31 10:29:44
title: Freemobile SMS API template
category: notification
tags: sms notification freebox freemobile
---

```bash
# SMS API credentials
SMS_USER="USERNAME"
SMS_PASS="APIKEY"
SMS_MESSAGE="Pi-hole Gravity Update Completed Successfully."

# Send notification via SMS
curl -s "https://smsapi.free-mobile.fr/sendmsg" \
    --data-urlencode "user=${SMS_USER}" \
    --data-urlencode "pass=${SMS_PASS}" \
    --data-urlencode "msg=${SMS_MESSAGE}"
```
