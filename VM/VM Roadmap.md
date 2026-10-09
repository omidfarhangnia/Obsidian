
## Phase 0 — Host Baseline و بررسی امکان‌سنجی

این فاز را اضافه می‌کنم و حتی قبل از یادگیری VM قرار می‌گیرد.

اول باید بفهمیم **زمین زیر پایمان امن و مناسب هست یا نه**:

- [x] نسخه و Edition ویندوز
- [x] وضعیت Windows Update
- [x] CPU virtualization
- [x] SLAT
- [x] Secure Boot
- [x] TPM
- [x] VBS / HVCI
- [x] RAM / CPU / Storage
- [x] وضعیت BitLocker
- [x] وضعیت Windows Defender و Firewall

این موضوع برای هدف تو حیاتی است، چون **Host خودش بخشی از مرز امنیتی Sandbox است**. Microsoft نیز روی به‌روز بودن Host، Firmware و Driverها و سخت‌گیری روی خود Hyper-V Host تأکید می‌کند.

**خروجی فاز:** یک Host شناخته‌شده و مناسب برای شروع.

---

# Phase 1 — Mental Model: OS ← Kernel ← Hypervisor ← VM

اینجا هیچ چیز پیچیده‌ای نصب نمی‌کنیم.

باید واقعاً بفهمی:

```
Hardware
   ↓
Hypervisor
   ↓
Virtual Hardware
   ↓
Guest Kernel
   ↓
Guest Processes
```

و در طرف دیگر:

```
Host OS
   ↓
Host Processes
```

مفاهیمی که یاد می‌گیری:

- Kernel
- User Space
- Process
- Memory
- Privilege
- System Call
- Hardware Virtualization
- Hypervisor
- Host
- Guest
- Virtual CPU
- Virtual RAM
- Virtual Disk
- Virtual NIC

هدف این نیست که اصطلاحات را حفظ کنی؛ باید بفهمی **مرز Host و Guest کجاست و چرا وجود دارد.**

---

# Phase 2 — Linux Fundamentals با WSL2

اینجا WSL2 وارد می‌شود.

اما یک تصمیم معماری مهم داریم:

> **WSL2 = محیط یادگیری Linux، نه Sandbox امنیتی نهایی**

یاد می‌گیری:

- Bash
- Filesystem
- Users / Groups
- Permissions
- `sudo`
- Processes
- Services
- Package Managers
- SSH
- Environment Variables
- Networking پایه

این مرحله برای تو به‌عنوان Frontend Developer ارزش دوگانه دارد: هم Linux را یاد می‌گیری و هم بعداً Docker، SSH، CI/CD و deployment برایت طبیعی‌تر می‌شود.

---

# Phase 3 — Hypervisor Fundamentals

اینجا مفهوم Hypervisor را به شکل عملی یاد می‌گیری.

روی Windows، انتخاب اصلی مسیر ما **Hyper-V** خواهد بود؛ البته قبل از نصب، Edition و قابلیت‌های سیستم را در Phase 0 بررسی می‌کنیم، چون Hyper-V در Windows 10/11 Home قابل نصب نیست و به پردازنده 64-bit با SLAT، virtualization سخت‌افزاری و منابع کافی نیاز دارد.

اینجا یاد می‌گیری:

- Type 1 vs Type 2
- Hyper-V architecture
- VM lifecycle
- virtual hardware
- Hyper-V Manager
- PowerShell management

**VirtualBox و VMware را در همین فاز مقایسه می‌کنیم، ولی وارد یادگیری هر دو نمی‌شویم.**

این حذف مهمی از Roadmap قبلی است؛ نمی‌خواهم تو بین چند Hypervisor پخش شوی.

---

# Phase 4 — Build Your First Real VM

حالا اولین VM واقعی.

مثلاً:

```
Windows Host
   │
   └── Hyper-V
        │
        └── Ubuntu VM
```

یاد می‌گیری:

- ISO
- Generation 2
- UEFI
- virtual disk
- virtual CPU
- RAM
- boot order
- Secure Boot
- virtual NIC

برای VMهای مدرن، Microsoft استفاده از Generation 2 را برای بهره‌گیری از امکاناتی مثل Secure Boot توصیه می‌کند؛ Secure Boot در Gen 2 از Windows و Linux پشتیبانی می‌کند.

---

# Phase 5 — خراب کردن VM عمداً

این فاز را عمداً جدا می‌کنم.

چیزی که می‌خواهی فقط با خواندن متوجه نمی‌شوی؛ باید خرابش کنی.

مثلاً:

```
Clean VM
   ↓
Change files
   ↓
Break services
   ↓
Destroy filesystem
   ↓
Guest unusable
```

بعد:

```
Restore
   ↓
Clean state
```

و اینجا تفاوت این مفاهیم را یاد می‌گیری:

- Checkpoint
- Snapshot
- Backup
- Export
- Clone
- Rebuild

یک نکته کلیدی که از اینجا به بعد همیشه رعایت می‌کنیم:

> **Checkpoint/ Snapshot را Backup فرض نمی‌کنیم.**

هدف این فاز این است که «خراب شدن Guest» برایت تبدیل به یک اتفاق عادی شود.

---

# Phase 6 — Threat Model و Trust Boundaries

این مرحله را از انتهای Roadmap به اینجا آوردم.

حالا می‌پرسیم:

> از چه چیزی داریم محافظت می‌کنیم؟

تهدیدها را دسته‌بندی می‌کنیم:

```
Guest
 ├── destructive command
 ├── malicious software
 ├── credential theft
 ├── network attacks
 ├── data exfiltration
 └── VM escape
```

و منابع Host:

```
Host
 ├── filesystem
 ├── credentials
 ├── browser sessions
 ├── SSH keys
 ├── API keys
 ├── LAN
 ├── USB
 └── other devices
```

اینجا مفهوم اصلی ما:

**Least Privilege + Minimal Attack Surface**

خواهد بود.

---

# Phase 7 — Host ↔ Guest Isolation

این مهم‌ترین فاز امنیتی VM است.

به‌جای اینکه فقط VM بسازیم، شروع می‌کنیم به حذف راه‌های ارتباط غیرضروری:

- Shared Folders
- Clipboard
- Drag & Drop
- Drive sharing
- USB passthrough
- Printer
- Audio
- Camera
- Microphone
- Host filesystem access
- unnecessary integration features

برای مثال، Hyper-V Enhanced Session Mode می‌تواند clipboard، file transfer و حتی drive و USB را در اختیار VM قرار دهد؛ بنابراین برای Sandbox سخت‌گیرانه باید این قابلیت‌ها را آگاهانه مدیریت کنیم، نه اینکه صرفاً برای راحتی روشنشان کنیم.

این دقیقاً همان جایی است که می‌فهمی:

> «VM داشتن» با «VM ایمن داشتن» دو چیز متفاوت‌اند.

---

# Phase 8 — Virtual Networking و Network Isolation

بعد از filesystem، بزرگ‌ترین سطح حمله برای Agent شبکه است.

اینجا عمیقاً یاد می‌گیری:

- NIC
- IP
- Subnet
- Gateway
- DNS
- NAT
- Internal Switch
- External Switch
- Private Network
- Inbound
- Outbound
- Egress

Hyper-V Virtual Switch امکان ایجاد شبکه‌های مختلف و policyهای ایزوله‌سازی را فراهم می‌کند؛ NAT هم می‌تواند اتصال Guest را از طریق یک Internal Switch برقرار کند.

بعد برای Sandbox چند profile خواهیم داشت:

```
OFFLINE
No network

RESTRICTED
Only required outbound connectivity

NORMAL
General Internet access
```

برای فایل مشکوک، `OFFLINE` می‌تواند یک profile باشد.

برای Hermes، احتمالاً `RESTRICTED` منطقی‌تر خواهد بود.

---

# Phase 9 — Disposable Sandbox Design

اینجا دیگر یک VM معمولی نداریم.

یک **Golden VM** ایجاد می‌کنیم:

```
Golden Image
     │
     ├── Hermes VM
     ├── Test VM
     └── Disposable VM
```

هدف:

```
Create
   ↓
Use
   ↓
Destroy
   ↓
Delete
   ↓
Recreate
```

یاد می‌گیری:

- Golden Image
- Clean baseline
- Clone
- Export
- Import
- Checkpoint strategy
- Disposable VM
- Persistent VM
- Rebuild automation

در این مرحله، خواسته اصلی تو:

> «فقط از Sandbox خارج شوم، آن را حذف کنم و یکی جدید بسازم»

واقعاً به یک workflow تبدیل می‌شود.

---

# Phase 10 — Docker Fundamentals

**اینجا Docker را وارد می‌کنیم، نه زودتر.**

چون حالا دیگر می‌دانی VM چیست.

می‌فهمی که:

```
VM
→ Guest Kernel

Container
→ Shared Host/Guest Kernel
```

و دقیقاً متوجه می‌شوی چرا container همیشه معادل VM نیست.

بعد Docker را داخل Linux VM یاد می‌گیری.

یعنی:

```
Windows
   ↓
Hyper-V
   ↓
Linux VM
   ↓
Docker
   ↓
Container
```

این برای هدف نهایی تو خیلی جالب است، چون حالا اگر Docker container هم compromise شود، هنوز یک مرز بالاتر وجود دارد:

**Hyper-V VM**

بنابراین Docker برای Hermes می‌تواند به یک **لایه دفاعی دوم** تبدیل شود، نه اینکه تنها مرز اعتماد باشد.

---

# Phase 11 — Hermes Security Architecture

حالا Hermes را وارد معماری می‌کنیم.

و اینجا یک نکته بسیار مهم از مستندات فعلی Hermes داریم:

Hermes بین این دو تفاوت می‌گذارد:

```
Hermes on Host
   ↓
Only terminal inside Docker
```

و:

```
Entire Hermes process
   ↓
inside sandbox
```

دومی مرز قوی‌تری دارد، چون shell، code execution، MCP، plugins، hooks و skill loading را هم داخل همان trust boundary قرار می‌دهد.

بنابراین برای هدف تو، معماری نهایی ترجیحی من چیزی شبیه این خواهد بود:

```
Windows Host
      │
      ▼
   Hyper-V
      │
      ▼
 Linux VM
      │
      ▼
 Hermes
      │
      ▼
 Docker Sandbox
```

یعنی **دو لایه isolation**.

البته بعد از اینکه این معماری را ساختیم بررسی می‌کنیم آیا برای نسخه و قابلیت‌های Hermes که واقعاً روی سیستم تو استفاده می‌کنی، Docker backend کافی است یا whole-process wrapping مناسب‌تر است.

---

# Phase 12 — Secrets و Identity Isolation

این فاز را هم به Roadmap قبلی اضافه می‌کنم.

یکی از بزرگ‌ترین اشتباه‌ها این است که VM امن داشته باشی ولی بعد داخل آن:

```
~/.ssh/id_rsa
API_KEY
GITHUB_TOKEN
browser session
password
```

قرار بدهی.

پس یاد می‌گیری:

- Secret management
- environment variables
- credential injection
- short-lived credentials
- separate accounts
- separate SSH keys
- API token scope
- read-only credentials

در مستندات Hermes نیز به‌طور مشخص هشدار داده شده که variables یا credentialsای که عمداً به sandbox پاس داده می‌شوند، توسط کدی که داخل sandbox اجرا می‌شود قابل خواندن و استخراج هستند.

---

# Phase 13 — Windows Sandbox

حالا Windows Sandbox را یاد می‌گیریم.

در این مرحله دیگر می‌پرسی:

> «برای این تست واقعاً VM دائمی لازم دارم؟»

Windows Sandbox برای همین سناریوها بسیار مناسب است: یک محیط pristine و disposable که بعد از بسته شدن state آن دور ریخته می‌شود و بر پایه hardware virtualization اجرا می‌شود. Microsoft آن را برای اجرای برنامه‌ها و فایل‌های ناشناخته هم پیشنهاد می‌کند.

اما برای امنیت:

```
Networking = Disabled
Clipboard = Disabled
Mapped folders = None
```

یا دقیقاً بر اساس سناریو.

Microsoft نیز صراحتاً می‌گوید Networking به‌صورت پیش‌فرض فعال است و Mapped Folder می‌تواند ریسک ایجاد کند؛ بنابراین تنظیمات پیش‌فرض را برای Sandbox امنیتی قابل اعتماد نمی‌دانیم.

---

# Phase 14 — Security Validation

اینجا دیگر نمی‌گوییم:

> «فکر کنم امنه.»

بلکه تست می‌کنیم.

مثلاً:

```
Guest
 ├── access host files       ✗
 ├── modify host files      ✗
 ├── access host secrets    ✗
 ├── access LAN             ✗ / policy
 ├── access internet        policy
 ├── destroy itself         ✓
 ├── restore clean state    ✓
 └── rebuild from baseline  ✓
```

بعداً تست‌های جدی‌تر هم اضافه می‌کنیم.

این فاز در واقع **آزمون نهایی معماری Sandbox** است.

---

# Phase 15 — Incident Response و Recovery

این فاز را نیز اضافه می‌کنم.

چون یک Sandbox امنیتی فقط درباره prevention نیست.

باید بدانی اگر یک روز واقعاً شک کردی Guest compromise شده:

```
1. Stop
2. Disconnect
3. Don't interact further
4. Preserve useful evidence
5. Destroy/rebuild according to scenario
6. Rotate exposed credentials
7. Verify Host integrity
```

و مهم‌تر:

**چه زمانی فقط VM را حذف می‌کنیم و چه زمانی باید احتمال compromise شدن Host را جدی بگیریم؟**

این تفاوت بسیار مهم است.

---

# Phase 16 — Automation

آخر مسیر، همه چیز دستی نمی‌ماند.

با PowerShell و ابزارهای VM management کاری می‌کنیم که:

```
Create Sandbox
      ↓
Configure
      ↓
Attach Network Policy
      ↓
Start
      ↓
Use
      ↓
Destroy
      ↓
Recreate
```

قابل تکرار باشد.

برای تو این فاز احتمالاً خیلی جذاب خواهد بود، چون عملاً وارد دنیای:

**Infrastructure Automation**

می‌شوی.

---

# نتیجه نهایی

بعد از این Roadmap، دیگر این چهار مورد برایت یک چیز نخواهند بود:

```
WSL
Docker
VM
Proxmox
```

بلکه جای هرکدام را می‌دانی:

|تکنولوژی|جایگاه|
|---|---|
|**WSL2**|Linux learning / development|
|**Hyper-V**|VM isolation و hypervisor اصلی|
|**Docker**|container isolation، مخصوصاً داخل VM|
|**Windows Sandbox**|disposable Windows testing|
|**VirtualBox / VMware**|مقایسه و شناخت اکوسیستم|
|**Proxmox**|فعلاً خارج از پروژه؛ مخصوصاً چون bare-metal/server می‌خواهد|

و مهم‌تر از آن، معماری ذهنی‌ات این می‌شود:

```
                HOST
                  │
                  │
              Hypervisor
                  │
          ┌───────┴───────┐
          │               │
       VM Layer       Disposable
          │             Sandbox
          │
       Linux VM
          │
       Docker
          │
       Hermes
```

## چیزی که عمداً حذف کردم

**Proxmox را کاملاً از مسیر عملی این پروژه حذف کردم.**

نه به این دلیل که ابزار بدی است؛ برعکس، برای یک virtualization lab روی سرور یا دستگاه اختصاصی بسیار مناسب است. اما در شرایط فعلی تو، یادگیری و نصب آن روی سخت‌افزار bare-metal چیزی به هدف فعلی اضافه نمی‌کند.

همچنین **VirtualBox را به‌عنوان ابزار اصلی حذف کردم**. نمی‌خواهم وقتت صرف یادگیری دو Hypervisor دسکتاپ شود. ابتدا یک Hypervisor را عمیق یاد می‌گیریم.

و **Docker را حذف نکردم، بلکه جایش را تغییر دادم**: بعد از اینکه VM و isolation را فهمیدی، Docker را به‌عنوان لایه دیگری از isolation یاد می‌گیریم.

---

## چیزی که در Roadmap جدید واقعاً تغییر کرده

مسیر اکنون این است:

**Host ← مفاهیم ← Linux ← Hypervisor ← VM ← Destroy/Recover ← Threat Model ← Host/Guest Isolation ← Network Isolation ← Disposable VM ← Docker ← Hermes ← Secrets ← Windows Sandbox ← Validation ← Incident Response ← Automation**

و این به نظرم برای هدفی که تعریف کردی، از نسخه قبلی **کامل‌تر و منطقی‌تر** است.

یک نکته آخر هم مهم است: Hyper-V روی Windows 10/11 Pro یا Enterprise در دسترس است و Windows Sandbox نیز در نسخه‌های Pro/Enterprise/Education ارائه می‌شود؛ بنابراین **Phase 0 را قبل از هر نصب و تغییر سیستمی انجام می‌دهیم** تا بر اساس Edition و وضعیت واقعی Host تصمیم بگیریم، نه بر اساس فرض.

از اینجا به بعد، **Roadmap را تمام‌شده در نظر می‌گیریم و وارد Phase 0 می‌شویم**.