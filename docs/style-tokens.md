# توکن‌های طراحی SafarYar

## پالت رنگ
| نام | مقدار Hex | کاربرد |
| --- | --- | --- |
| Primary Orange | `#F57C00` | CTA اصلی، دکمه رزرو |
| Deep Purple | `#6A1B9A` | پس‌زمینه گرادیان، کارت‌های برجسته |
| Calm Teal | `#00897B` | تگ‌های وضعیت، نوارهای پیشرفت |
| Sand Beige | `#F4E1B1` | پس‌زمینه کارت‌ها، سطح دوم |
| Charcoal | `#1F2933` | متن اصلی |
| Slate | `#475569` | متن ثانویه |
| Snow | `#FAFAFA` | پس‌زمینه کلی |
| Success Green | `#2E7D32` | اعلان موفقیت |
| Warning Amber | `#FFB300` | هشدار بودجه |
| Error Red | `#D32F2F` | خطا |

## تایپوگرافی
- **Primary Font:** IRANSans (وزن‌های ۳۰۰، ۴۰۰، ۷۰۰)
- **Secondary Font:** Vazir Variable (گستره وزن ۲۰۰ تا ۸۰۰)
- **Fallback لاتین:** "Inter", sans-serif
- **Heading Scale:**
  - Display: 40px / 700
  - H1: 32px / 700
  - H2: 28px / 600
  - H3: 24px / 600
  - Body: 16px / 400
  - Caption: 13px / 400

## شبکه و فاصله‌گذاری
| نام | مقدار |
| --- | --- |
| Base Unit | 8px |
| Spacing XS | 4px |
| Spacing SM | 8px |
| Spacing MD | 16px |
| Spacing LG | 24px |
| Spacing XL | 32px |
| Radius SM | 12px |
| Radius LG | 24px |
| Card Elevation | 12px blur, 0 12px 24px rgba(106,27,154,0.12) |

## آیکون‌ها
- کتابخانه Lucide Icons با بسته سفارشی راست‌به‌چپ.
- اندازه‌های استاندارد: 20px (متن)، 24px (اکشن)، 32px (نقشه).

## گرادیان‌ها
- **Sunset Hero:** `linear-gradient(135deg, #F57C00 0%, #6A1B9A 100%)`
- **Teal Mist:** `linear-gradient(135deg, rgba(0,137,123,0.85) 0%, rgba(244,225,177,0.8) 100%)`

## حالات تعاملی
| حالت | دستورالعمل |
| --- | --- |
| Hover | افزایش روشنایی 8% + سایه نرم |
| Active | جابه‌جایی 2px به پایین + سایه کمتر |
| Disabled | کاهش Opacity به 40%، بدون سایه |
| Focus | حلقه 2px با رنگ Teal و شفافیت 40% |

## Motion
- استفاده از Framer Motion برای ترنزیشن‌ها.
- مدت زمان پیش‌فرض: 250ms ease-out.
- Microinteraction مانند کشیدن کارت: spring (stiffness 200, damping 24).

## زبان طراحی
- استفاده از کارت‌های گرد با Shadow نرم.
- تصاویر الهام‌بخش از طبیعت و شهرهای ایران.
- تاکید بر خوانایی در حالت تاریک (Dark Mode) با پس‌زمینه‌ی #101826 و متن روشن (#E2E8F0).

> این توکن‌ها باید در سیستم طراحی Figma و پیاده‌سازی Tailwind config منعکس شوند.
