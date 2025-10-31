# معماری سامانه SafarYar

این سند، معماری کلان اپلیکیشن سفر یار را برای دو بستر موبایل (React Native) و وب (Next.js 14) به همراه بک‌اند NestJS شرح می‌دهد. تمرکز بر مقیاس‌پذیری، تجربه کاربری لحظه‌ای و پشتیبانی از ویژگی‌های هوشمند است.

## 1. نمای کلی سیستم
```
[کاربر موبایل] --GraphQL/REST--> [Gateway API]
[کاربر وب] ----WebSocket/REST--> [Gateway API]
                                   |---> [Auth Service (NextAuth)]
                                   |---> [Recommendation Engine]
                                   |---> [Trip Planner Service]
                                   |---> [Booking Integrations]
                                   |---> [Notification Service]
                                   |---> [AI Voice Assistant]
                                   '---> [PostgreSQL + Redis]
```

## 2. لایه‌ها و سرویس‌ها
### 2.1 لایه ارائه (Client)
- **React Native App**: پیاده‌سازی با Expo Router، Tailwind NativeWind، State با Zustand، Query Layer با React Query.
- **Next.js 14 App**: استفاده از App Router، سرور کامپوننت‌ها، shadcn/ui، TailwindCSS و انیمیشن‌ها با Framer Motion.
- **Local Cache & Offline**: SQLite (expo-sqlite) + IndexedDB برای ذخیره‌ی داده‌های حیاتی (برنامه سفر، بودجه، رزروهای اخیر).

### 2.2 Gateway API
- NestJS با ماژول GraphQL (Apollo Federation) + REST.
- Rate Limiting با `@nestjs/throttler`، لاگینگ با Pino.
- نسخه‌بندی API (`/v1`, `/v2`).

### 2.3 سرویس احراز هویت
- NextAuth.js با Providerهای OTP، Google و Apple.
- ذخیره‌سازی نشست‌ها در PostgreSQL.
- استفاده از JWT کوتاه‌عمر برای مصرف موبایل.

### 2.4 موتور پیشنهاد هوشمند
- Rule Engine اولیه (persona-based + بودجه + آب‌وهوا).
- Model Serving: استفاده از OpenAI Assistants با prompt مهندسی شده و context شامل داده‌های مقصد.
- Pipeline داده: Cron برای بروزرسانی آب‌وهوا، رویدادها و امتیازات کاربر.

### 2.5 سرویس برنامه‌ریز سفر
- ساخت itinerary چندروزه با توجه به بودجه، زمان و Mood.
- ترکیب داده از Mapbox (POI)، رزروها و علایق ذخیره شده.
- الگوریتم: مسیریابی (Mapbox Optimization API) + محدودیت‌های زمانی.

### 2.6 کیف پول و دونگ
- تراکنش‌های گروهی در جدول جداگانه با وضعیت تسویه.
- همگام‌سازی لحظه‌ای با WebSocket (NestJS Gateway + Redis Pub/Sub).

### 2.7 ادغام‌های بیرونی
| ادغام | کاربرد | روش |
| --- | --- | --- |
| Mapbox | نقشه و مسیریابی | Tiles + Directions API |
| OpenWeatherMap | پیش‌بینی آب‌وهوا | REST با کش ۳۰ دقیقه‌ای |
| Booking Mock | نتایج اقامت | gRPC Mock Service |
| Restaurant Mock | رزرو رستوران | REST Mock + Webhook |
| Zarinpal | پرداخت | REST + Callback |

## 3. مدل داده‌ای کلیدی (PostgreSQL)
- `users`: اطلاعات کاربر، تنظیمات زبان، نقش
- `profiles`: شخصیت سفر، بودجه، علایق
- `trips`: سفرهای برنامه‌ریزی‌شده، وضعیت، درصد پیشرفت
- `itinerary_items`: فعالیت‌ها، زمان شروع/پایان، مکان
- `bookings`: رزروهای اقامت و حمل‌ونقل
- `group_wallets`: کیف پول مشترک، اعضا، مانده
- `transactions`: تراکنش‌های کیف پول + دونگ
- `notifications`: پیام‌های درون‌برنامه و پوش

Prisma Schema به صورت مونو ریپو به اشتراک گذاشته می‌شود و migrations با `prisma migrate` مدیریت خواهد شد.

## 4. جریان‌های داده
### 4.1 ثبت‌نام و شخصی‌سازی اولیه
1. کاربر با OTP وارد می‌شود.
2. پرسشنامه علایق/بودجه در کلاینت ذخیره و به `/profiles` ارسال می‌گردد.
3. موتور پیشنهاد، مقاصد اولیه را برمی‌گرداند.

### 4.2 برنامه‌ریزی سفر
1. کاربر مقصد را انتخاب می‌کند.
2. سرویس برنامه‌ریز، پیشنهادات اقامت و حمل‌ونقل را از سرویس‌های مربوطه می‌کشد.
3. بودجه و زمان‌بندی محاسبه و itinerary تولید می‌شود.
4. نتایج برای استفاده آفلاین در کلاینت کش می‌شوند.

### 4.3 کیف پول گروهی
1. ایجاد گروه → دعوت اعضا (Deep Link).
2. هر تراکنش در Redis Queue قرار می‌گیرد.
3. Service Worker در Next.js نوتیفیکیشن ارسال می‌کند.

## 5. هوش مصنوعی و شخصی‌سازی
- **Destination AI**: مدل rule-based + LLM برای تولید توضیحات شخصی‌سازی شده.
- **Smart Budget Planner**: الگوریتمی که قیمت‌ها را از Booking/Transport می‌گیرد و به ریال تبدیل می‌کند، سپس با توجه به سقف هزینه پیشنهاد می‌دهد.
- **Voice Assistant**: سرویس جدا با WebSocket؛ تشخیص گفتار فارسی (Speech-to-Text) → Intent Classification → پاسخ TTS.

## 6. DevOps و زیرساخت
- CI/CD: Github Actions (lint, test, build, deploy).
- محیط‌ها: Development (Docker Compose)، Staging (Railway)، Production (Kubernetes Light).
- Observability: Sentry برای خطا، Prometheus + Grafana برای مانیتورینگ، Logtail برای لاگ.

## 7. امنیت و انطباق
- ذخیره توکن‌ها در Vault.
- رمزنگاری داده‌های حساس (PGP برای اطلاعات پاسپورت).
- رعایت قوانین حریم خصوصی داده‌های کاربر در ایران.

## 8. برنامه تست و کیفیت
- Unit Test با Jest (NestJS/React).
- E2E با Detox (موبایل) و Playwright (وب).
- تست‌های کارایی با k6.

## 9. نقشه راه معماری
1. راه‌اندازی Monorepo (Turborepo) و CI اولیه
2. پیاده‌سازی Auth + Profile + Recommendation MVP
3. افزودن Booking Integrations و کیف پول
4. توسعه Voice Assistant و Offline Mode
5. سخت‌گیری بر Observability و Load Testing

> این سند به عنوان مبنای توسعه محسوب می‌شود و با پیشرفت پروژه به‌روزرسانی خواهد شد.
