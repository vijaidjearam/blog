---
layout: post
date: 2026-03-24 11:29:00
title: Technitium DNS Weekly Report → ntfy / Email
category: DNS
tags: technitium DNS python
---


# Technitium DNS Weekly Report → ntfy / Email

```python
#!/usr/bin/env python3
"""
Technitium DNS Server - Weekly Device Report
Fetches all visited & blocked sites for a target device IP
for the last 7 days and sends via ntfy and/or email.

Schedule with cron:  0 8 * * 1  python3 /path/to/dns_report.py
"""

import requests
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from collections import Counter
from datetime import datetime, timedelta
import sys
import time

# ===================== CONFIGURATION =====================

# Technitium DNS Server
DNS_SERVER      = "http://192.168.1.x:5380"   # <-- Your DNS server IP
USERNAME        = "admin"
PASSWORD        = "your_password"              # <-- Your password

# Target Device
TARGET_DEVICE_IP = "192.168.1.100"             # <-- Device IP to report
DEVICE_NAME      = "Test's PC"                # <-- Friendly name

# Pagination (increase MAX_PAGES if you have heavy traffic)
ENTRIES_PER_PAGE = 1000
MAX_PAGES        = 100

# ---- NTFY Configuration ----
NTFY_ENABLED = True
NTFY_URL     = "https://ntfy.sh/test-home"
NTFY_TITLE   = "DNS Report"
NTFY_PRIORITY = "default"                      # min, low, default, high, urgent
NTFY_TAGS     = "bar_chart,globe_with_meridians"

# ---- Email Configuration (optional) ----
EMAIL_ENABLED  = False
SMTP_SERVER    = "smtp.gmail.com"
SMTP_PORT      = 587
SMTP_USE_TLS   = True
SMTP_USER      = "your_email@gmail.com"
SMTP_PASSWORD  = "your_app_password"
EMAIL_FROM     = "your_email@gmail.com"
EMAIL_TO       = "recipient@gmail.com"
EMAIL_SUBJECT  = "Weekly DNS Report"

# ==========================================================


def get_token():
    """Authenticate with Technitium and return API token."""
    url = f"{DNS_SERVER}/api/user/login"
    params = {"user": USERNAME, "pass": PASSWORD}
    try:
        resp = requests.get(url, params=params, timeout=15)
        resp.raise_for_status()
        data = resp.json()
        if data.get("status") == "ok":
            print("✅ Authenticated with Technitium DNS")
            return data["token"]
        else:
            raise Exception(f"Login failed: {data.get('errorMessage', data)}")
    except requests.exceptions.ConnectionError:
        print(f"❌ Cannot connect to Technitium at {DNS_SERVER}")
        sys.exit(1)


def fetch_logs_for_week(token, client_ip):
    """Fetch all query logs for the target IP for the last 7 days."""
    now = datetime.now()
    week_ago = now - timedelta(days=7)

    # Technitium expects: yyyy-MM-dd HH:mm:ss  (or ISO 8601 in newer versions)
    start_str = week_ago.strftime("%Y-%m-%d %H:%M:%S")
    end_str   = now.strftime("%Y-%m-%d %H:%M:%S")

    print(f"📅 Fetching logs from {start_str} to {end_str}")
    print(f"🎯 Target device: {client_ip} ({DEVICE_NAME})\n")

    all_entries = []

    for page in range(1, MAX_PAGES + 1):
        url = f"{DNS_SERVER}/api/queryLogs/list"
        params = {
            "token":            token,
            "pageNumber":       page,
            "entriesPerPage":   ENTRIES_PER_PAGE,
            "clientIpAddress":  client_ip,
            "start":            start_str,
            "end":              end_str,
        }

        try:
            resp = requests.get(url, params=params, timeout=30)
            resp.raise_for_status()
            data = resp.json()
        except Exception as e:
            print(f"⚠️  Error fetching page {page}: {e}")
            break

        if data.get("status") != "ok":
            print(f"⚠️  API error: {data.get('errorMessage', data)}")
            break

        entries = data.get("response", {}).get("entries", [])
        if not entries:
            break

        all_entries.extend(entries)
        print(f"   Page {page}: {len(entries)} entries (total: {len(all_entries)})")

        # If we got fewer entries than requested, we've reached the end
        if len(entries) < ENTRIES_PER_PAGE:
            break

        time.sleep(0.1)  # Be gentle on the server

    print(f"\n📊 Total entries fetched: {len(all_entries)}")
    return all_entries


def parse_logs(entries):
    """Parse log entries into allowed/blocked domain lists."""
    allowed_domains = []
    blocked_domains = []
    all_domains     = []

    for entry in entries:
        # Extract domain name (field name varies by Technitium version)
        domain = (
            entry.get("qName")
            or entry.get("questionName")
            or entry.get("qname")
            or "unknown"
        )
        domain = domain.rstrip(".")  # Remove trailing dot

        # Skip internal/infrastructure queries
        if domain in ("unknown", "", "localhost"):
            continue

        # Detect if the query was blocked
        response_type = str(entry.get("responseType", "")).lower()
        rcode         = str(entry.get("responseCode", entry.get("rcode", ""))).lower()

        blocked_keywords = [
            "blocked", "upstreamblocked", "cacheblocked",
            "customblocked", "blocklist", "adblocked"
        ]

        is_blocked = (
            any(kw in response_type for kw in blocked_keywords)
            or rcode in ("refused", "nxdomain")  # depends on your block config
            or entry.get("isBlocked", False)
        )

        all_domains.append(domain)

        if is_blocked:
            blocked_domains.append(domain)
        else:
            allowed_domains.append(domain)

    return allowed_domains, blocked_domains, all_domains


def build_report(allowed_domains, blocked_domains, all_domains):
    """Build a formatted text report."""
    now       = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    week_ago  = (datetime.now() - timedelta(days=7)).strftime("%Y-%m-%d")
    today     = datetime.now().strftime("%Y-%m-%d")

    allowed_counter = Counter(allowed_domains)
    blocked_counter = Counter(blocked_domains)
    all_counter     = Counter(all_domains)

    # ---- Build the report string ----
    lines = []
    lines.append("=" * 50)
    lines.append(f"📊 WEEKLY DNS REPORT")
    lines.append(f"📱 Device: {DEVICE_NAME} ({TARGET_DEVICE_IP})")
    lines.append(f"📅 Period: {week_ago} → {today}")
    lines.append(f"🕐 Generated: {now}")
    lines.append("=" * 50)

    # Summary
    lines.append("")
    lines.append("📈 SUMMARY")
    lines.append(f"  Total Queries    : {len(all_domains)}")
    lines.append(f"  Allowed Queries  : {len(allowed_domains)}")
    lines.append(f"  Blocked Queries  : {len(blocked_domains)}")
    if all_domains:
        block_pct = (len(blocked_domains) / len(all_domains)) * 100
        lines.append(f"  Block Rate       : {block_pct:.1f}%")
    lines.append(f"  Unique Sites     : {len(all_counter)}")
    lines.append(f"  Unique Allowed   : {len(allowed_counter)}")
    lines.append(f"  Unique Blocked   : {len(blocked_counter)}")

    # ---- ALL VISITED SITES (sorted by query count) ----
    lines.append("")
    lines.append("🌐 ALL VISITED SITES (by query count)")
    lines.append("-" * 50)

    for i, (domain, count) in enumerate(allowed_counter.most_common(), 1):
        lines.append(f"  {i:>4}. {domain}  ({count})")

    # ---- ALL BLOCKED SITES ----
    lines.append("")
    lines.append("🚫 ALL BLOCKED SITES (by query count)")
    lines.append("-" * 50)

    if blocked_counter:
        for i, (domain, count) in enumerate(blocked_counter.most_common(), 1):
            lines.append(f"  {i:>4}. {domain}  ({count})")
    else:
        lines.append("  (No blocked queries)")

    lines.append("")
    lines.append("=" * 50)

    return "\n".join(lines)


def build_short_summary(allowed_domains, blocked_domains, all_domains, top_n=15):
    """Build a shorter summary for ntfy (fits in message limit)."""
    allowed_counter = Counter(allowed_domains)
    blocked_counter = Counter(blocked_domains)
    week_ago = (datetime.now() - timedelta(days=7)).strftime("%Y-%m-%d")
    today    = datetime.now().strftime("%Y-%m-%d")

    lines = []
    lines.append(f"📱 {DEVICE_NAME} ({TARGET_DEVICE_IP})")
    lines.append(f"📅 {week_ago} → {today}")
    lines.append(f"📊 Queries: {len(all_domains)} | Blocked: {len(blocked_domains)}")
    if all_domains:
        lines.append(f"🛡️ Block rate: {(len(blocked_domains)/len(all_domains))*100:.1f}%")
    lines.append("")

    lines.append(f"🌐 Top {top_n} Visited:")
    for i, (domain, count) in enumerate(allowed_counter.most_common(top_n), 1):
        lines.append(f" {i}. {domain} ({count})")

    lines.append("")
    lines.append(f"🚫 Top {min(top_n, len(blocked_counter))} Blocked:")
    if blocked_counter:
        for i, (domain, count) in enumerate(blocked_counter.most_common(top_n), 1):
            lines.append(f" {i}. {domain} ({count})")
    else:
        lines.append(" (none)")

    return "\n".join(lines)


def send_ntfy(full_report, short_summary):
    """Send notification via ntfy.sh with full report as attachment."""
    if not NTFY_ENABLED:
        return

    print("\n📤 Sending ntfy notification...")

    # ---- Send the full report as a file attachment ----
    try:
        timestamp = datetime.now().strftime("%Y%m%d")
        filename = f"dns_report_{timestamp}.txt"

        resp = requests.put(
            NTFY_URL,
            data=full_report.encode("utf-8"),
            headers={
                "Title":    NTFY_TITLE,
                "Tags":     NTFY_TAGS,
                "Priority": NTFY_PRIORITY,
                "Filename": filename,
            },
            timeout=15,
        )
        resp.raise_for_status()
        print(f"   ✅ Full report sent as attachment: {filename}")
    except Exception as e:
        print(f"   ⚠️  Attachment send failed: {e}")

    # ---- Send a short summary as a readable notification ----
    try:
        resp = requests.post(
            NTFY_URL,
            data=short_summary.encode("utf-8"),
            headers={
                "Title":    f"{NTFY_TITLE} - {DEVICE_NAME}",
                "Tags":     NTFY_TAGS,
                "Priority": NTFY_PRIORITY,
            },
            timeout=15,
        )
        resp.raise_for_status()
        print("   ✅ Summary notification sent!")
    except Exception as e:
        print(f"   ❌ Notification failed: {e}")


def send_email(full_report):
    """Send the full report via email."""
    if not EMAIL_ENABLED:
        return

    print("\n📧 Sending email report...")

    try:
        msg = MIMEMultipart("alternative")
        msg["Subject"] = f"{EMAIL_SUBJECT} - {DEVICE_NAME}"
        msg["From"]    = EMAIL_FROM
        msg["To"]      = EMAIL_TO

        # Plain text version
        text_part = MIMEText(full_report, "plain", "utf-8")
        msg.attach(text_part)

        # HTML version (optional - makes it look nicer)
        html_report = "<pre style='font-family: monospace; font-size: 13px;'>\n"
        html_report += full_report.replace("\n", "<br>")
        html_report += "\n</pre>"
        html_part = MIMEText(html_report, "html", "utf-8")
        msg.attach(html_part)

        with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:
            if SMTP_USE_TLS:
                server.starttls()
            server.login(SMTP_USER, SMTP_PASSWORD)
            server.sendmail(EMAIL_FROM, EMAIL_TO, msg.as_string())

        print("   ✅ Email sent successfully!")
    except Exception as e:
        print(f"   ❌ Email failed: {e}")


def save_report_local(full_report):
    """Save the report to a local file as backup."""
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"dns_report_{TARGET_DEVICE_IP}_{timestamp}.txt"
    try:
        with open(filename, "w", encoding="utf-8") as f:
            f.write(full_report)
        print(f"\n💾 Report saved locally: {filename}")
    except Exception as e:
        print(f"\n⚠️  Could not save locally: {e}")


# ===================== MAIN =====================

def main():
    print("🚀 Technitium DNS Weekly Report Generator\n")

    # Step 1: Authenticate
    token = get_token()

    # Step 2: Fetch logs for the last 7 days
    entries = fetch_logs_for_week(token, TARGET_DEVICE_IP)

    if not entries:
        no_data_msg = f"No DNS queries found for {DEVICE_NAME} ({TARGET_DEVICE_IP}) in the last 7 days."
        print(f"\n⚠️  {no_data_msg}")

        # Still notify that there's no data
        if NTFY_ENABLED:
            requests.post(
                NTFY_URL,
                data=no_data_msg.encode("utf-8"),
                headers={"Title": NTFY_TITLE, "Tags": "warning"},
                timeout=15,
            )
        return

    # Step 3: Parse logs
    allowed, blocked, all_domains = parse_logs(entries)
    print(f"\n   Allowed: {len(allowed)} | Blocked: {len(blocked)} | Total: {len(all_domains)}")

    # Step 4: Build reports
    full_report   = build_report(allowed, blocked, all_domains)
    short_summary = build_short_summary(allowed, blocked, all_domains, top_n=15)

    # Print to console
    print("\n" + full_report)

    # Step 5: Send notifications
    send_ntfy(full_report, short_summary)
    send_email(full_report)
    save_report_local(full_report)

    print("\n🎉 Done!")


if __name__ == "__main__":
    main()
```

---

## What This Does

```
┌─────────────────────────────────────────────┐
│         Technitium DNS Server               │
│         (last 7 days of logs)               │
└──────────────┬──────────────────────────────┘
               │ API query filtered by IP
               ▼
┌─────────────────────────────────────────────┐
│         Python Script                       │
│  • Fetches all logs for TARGET_DEVICE_IP    │
│  • Separates allowed vs blocked             │
│  • Counts & ranks domains                   │
│  • Builds full report + short summary       │
└──────┬──────────────┬──────────────┬────────┘
       │              │              │
       ▼              ▼              ▼
   📱 ntfy         📧 Email      💾 Local
   (2 messages)    (full report)   (.txt file)
   • Summary msg
   • Full .txt
     attachment
```

---

## ntfy Notifications You'll Receive

**Notification 1 — Readable Summary:**
```
📱 Test's PC (192.168.1.100)
📅 2025-01-08 → 2025-01-15
📊 Queries: 12847 | Blocked: 2341
🛡️ Block rate: 18.2%

🌐 Top 15 Visited:
 1. www.google.com (834)
 2. youtube.com (612)
 3. reddit.com (445)
 ...

🚫 Top 15 Blocked:
 1. ads.doubleclick.net (312)
 2. tracking.facebook.com (198)
 ...
```

**Notification 2 — Full report attached as `dns_report_20250115.txt`**

---

## Setup Steps

### 1. Edit the Configuration

```python
DNS_SERVER       = "http://192.168.1.5:5380"    # Your Technitium IP
PASSWORD         = "your_actual_password"
TARGET_DEVICE_IP = "192.168.1.100"              # Device to track
DEVICE_NAME      = "Test's PC"
```

### 2. Enable Query Logging in Technitium

> **Technitium Dashboard** → **Settings** → **Logging**
> - ✅ Enable Query Logging
> - ✅ Log to local folder
> - Set log retention ≥ 7 days

### 3. Test It

```bash
pip install requests
python3 dns_report.py
```

### 4. Schedule Weekly (Every Monday 8 AM)

**Linux (cron):**
```bash
crontab -e
# Add:
0 8 * * 1 /usr/bin/python3 /home/test/dns_report.py >> /home/test/dns_report.log 2>&1
```

**Windows (Task Scheduler):**
```
Action: Start a program
Program: python
Arguments: C:\scripts\dns_report.py
Trigger: Weekly, Monday, 8:00 AM
```

---

## Optional: Enable Email Too

Set `EMAIL_ENABLED = True` and fill in your SMTP details. For **Gmail**, use an [App Password](https://myaccount.google.com/apppasswords):

```python
EMAIL_ENABLED  = True
SMTP_SERVER    = "smtp.gmail.com"
SMTP_PORT      = 587
SMTP_USER      = "you@gmail.com"
SMTP_PASSWORD  = "xxxx xxxx xxxx xxxx"  # Gmail App Password
EMAIL_TO       = "you@gmail.com"
```

