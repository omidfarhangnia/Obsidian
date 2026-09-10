## OpenTelemetry چیست؟

**OpenTelemetry** یک:
- **observability framework and toolkit** است که با هدف تسهیل موارد زیر طراحی شده است:
  - **Generation**
  - **Export**
  - **Collection**
این موارد مربوط به **telemetry data** مانند موارد زیر هستند:
- **traces**
- **metrics**
- **logs**

> [!tip]  OpenTelemetry is **not** an observability backend itself.

![[Pasted image 20260910144243.png]]![[Pasted image 20260910144518.png]]


OpenTelemetry نیاز به **observability** را برآورده می‌کند و در عین حال از دو اصل کلیدی پیروی می‌کند:
1. شما مالک داده‌ای هستید که تولید می‌کنید. بنابراین **vendor lock-in** وجود ندارد.
2. فقط لازم است یک مجموعه واحد از **APIs** و **conventions** را یاد بگیرید.
**Vendor lock-in** یعنی:
> **وابستگی شدید به یک Vendor (ارائه‌دهنده)** به‌گونه‌ای که خارج شدن از آن سرویس یا مهاجرت به یک Vendor دیگر، دشوار، پرهزینه یا زمان‌بر شود.
مثلاً فرض کن یک پروژه را کاملاً بر اساس سرویس‌های یک Cloud Provider خاص ساخته‌ای و از APIهای اختصاصی آن استفاده کرده‌ای. بعد از مدتی اگر بخواهی پروژه را به Provider دیگری منتقل کنی، متوجه می‌شوی که:
- باید بخش زیادی از کد را تغییر بدهی.
- داده‌ها به‌راحتی قابل انتقال نیستند.
- معماری پروژه به سرویس‌های اختصاصی آن Provider وابسته شده.
- مهاجرت هزینه و زمان زیادی می‌برد.
در این حالت می‌گوییم پروژه دچار **Vendor lock-in** شده است.


# Code-based Instrumentation

مراحل اصلی برای راه‌اندازی **code-based instrumentation** در OpenTelemetry:
## 1. Import کردن OpenTelemetry API و SDK
- اگر در حال توسعه یک **library** یا component هستید که توسط یک **runnable binary** استفاده می‌شود، فقط به **API** وابستگی داشته باشید.
- اگر artifact شما یک **standalone process/service** است، به هر دو **API** و **SDK** نیاز دارید.
## 2. Configure کردن OpenTelemetry API
برای ایجاد **traces** یا **metrics** ابتدا باید یک **tracer provider** و/یا **meter provider** ایجاد کنید.
- معمولاً توصیه می‌شود SDK یک **default provider** برای این objectها ارائه دهد.
- سپس از provider یک **tracer** یا **meter** دریافت کنید.
- برای آن یک **name** و **version** تعیین کنید.
- `name` باید مشخص کند چه چیزی **instrumented** شده است.
- برای یک library، معمولاً نام خود library استفاده می‌شود؛ مانند:
  `com.example.myLibrary`
- بهتر است `version` نیز مشخص شود، مثلاً:
  `semver:1.0.0`
## 3. Configure کردن OpenTelemetry SDK
اگر یک **service process** می‌سازید، باید SDK را برای **export** کردن **telemetry data** به یک **analysis backend** پیکربندی کنید.
- این configuration بهتر است به‌صورت programmatic و از طریق **configuration file** یا مکانیزم مشابه انجام شود.
- هر language ممکن است گزینه‌های tuning مخصوص خود را داشته باشد.
## 4. Create کردن Telemetry Data

پس از configure کردن API و SDK می‌توانید با استفاده از **tracer** و **meter**، موارد زیر را ایجاد کنید:
- **traces**
- **metric events**
برای dependencyهای خود نیز از **Instrumentation Libraries** استفاده کنید.
## 5. Export کردن Data
پس از ایجاد **telemetry data** باید آن را به یک **analysis backend** ارسال کنید.
OpenTelemetry دو روش اصلی برای export دارد:
1. **In-process export**
2. استفاده از **OpenTelemetry Collector**
### In-process Export
در این روش از **exporter**ها استفاده می‌شود.
**Exporter** یک library است که objectهای **span** و **metric** موجود در memory را به format مناسب برای ابزارهای analysis مانند **Jaeger** یا **Prometheus** تبدیل می‌کند.
### OTLP و OpenTelemetry Collector

OpenTelemetry از یک **wire protocol** به نام `OTLP` پشتیبانی می‌کند که توسط تمام OpenTelemetry SDKها پشتیبانی می‌شود.