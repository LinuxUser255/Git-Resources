# How to translate UTC to ET

## Example timestamp and it's breakdown

`2025-12-10T16:06:40.889914+00:00`

It follows the international standard called ISO 8601. Once you know the
pattern, it’s actually the easiest and most unambiguous way to write date+time.

### Section-by-Section

| Part                  | Meaning                                      | What it is in the example           | Notes |
|-----------------------|----------------------------------------------|-------------------------------------|-------|
| `2025`                | Year (4 digits)                              | 2025                                |       |
| `-`                   | Separator                                    |                                     |       |
| `12`                  | Month (01–12)                                | December                            |       |
| `-`                   | Separator                                    |                                     |       |
| `10`                  | Day of the month (01–31)                     | 10th                                |       |
| `T`                   | Separator between date and time             | The capital T                       | Literally just says “now starts the time part” |
| `16`                  | Hour in 24-hour format (00–23)               | 16 = 4 PM                           |       |
| `:`                   | Separator                                    |                                     |       |
| `06`                  | Minutes (00–59)                              | 6 minutes                           |       |
| `:`                   | Separator                                    |                                     |       |
| `40`                  | Seconds (00–59)                              | 40 seconds                          |       |
| `.889914`             | Fractional seconds (milliseconds + microseconds) | .889914 = 889914 microseconds       | 3 digits = milliseconds, next 3 = microseconds |
| `+00:00`              | Time-zone offset from UTC                    | +00:00 = exactly UTC (Zulu time)    | You’ll also sometimes see just a “Z” instead of +00:00 |

### Quick Cheat Sheet to Read Any ISO Timestamp

```
YYYY-MM-DDThh:mm:ss.ssssss±hh:mm
│   │   │ │  │   │ │        │
│   │   │ │  │   │ │        └─ Time-zone offset (or Z)
│   │   │ │  │   │ └─ Fractional seconds (optional)
│   │   │ │  │   └─ Seconds
│   │   │ │  └─ Minutes
│   │   │ └─ Hour (24-hour clock)
│   │   └─ The “T” separator
│   └─ Day
└─ Month and Year
```

### Common Variations You’ll See

- `2025-12-10T16:06:40Z` → same as +00:00, the “Z” means “Zulu” = UTC
- `2025-12-10T11:06:40-05:00` → this would already be East Coast time (EST)
- Sometimes people drop the seconds or milliseconds if they’re zero

### Easy Way to Convert in Your Head (US East Coast)

1. Look at the offset at the end.
   - If it says `+00:00` or `Z` → it’s UTC.
   - If it says `-04:00` → it’s already EDT (summer).
   - If it says `-05:00` → it’s already EST (winter).
2. In December → we’re on Standard Time, so East Coast = UTC – 5 hours.
3. Just subtract 5 from the hour (and roll back the date if it goes negative).

This example:
UTC: 16:06 → subtract 5 → 11:06 AM EST same day.

That’s it! Once you memorize “T separates date/time” and “the number after the
last + or – is hours from UTC”, you can read these instantly.
