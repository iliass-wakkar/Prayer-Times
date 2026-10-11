# Prayer-Times: Moroccan Islamic Prayer Times & Real-Time Countdown Engine

[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-3.4-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Axios](https://img.shields.io/badge/HTTP-Axios-5A29E4?style=for-the-badge&logo=axios&logoColor=white)](https://axios-http.com/)
[![API](https://img.shields.io/badge/API-AlAdhan_Astronomical-009688?style=for-the-badge)](https://aladhan.com/prayer-times-api)
[![Language](https://img.shields.io/badge/Interface-Arabic_RTL-107C41?style=for-the-badge)]()
[![License](https://img.shields.io/badge/License-ISC-blue?style=for-the-badge)]()

**Prayer-Times** (أوقات الصلاة بالمغرب) is a real-time Islamic prayer schedule and countdown web application engineered specifically for Moroccan cities and municipalities. It features automated browser geolocation, dual Hijri and Gregorian Arabic calendar synchronization, exact minute-by-minute countdown to the next prayer, and fuzzy search across Moroccan cities.

---

## Visual Showcase

![Prayer Times Dashboard](Screenshot%202025-01-15%20194823.png)
*Figure 1: Prayer-Times interface showing current city selection, dual Gregorian/Hijri date ribbons, real-time countdown to the next prayer, and daily prayer times grid.*

---

## Architectural Highlights

| Feature | Description |
| :--- | :--- |
| **Astronomical Calculation** | AlAdhan Timings engine configured with **Method 3** (Moroccan Ministry of Habous and Islamic Affairs standard). |
| **Geolocation Engine** | HTML5 `navigator.geolocation` coordinates resolved via **LocationIQ Reverse Geocoding** with automatic Arabic city normalization. |
| **Next Prayer Countdown** | Real-time temporal algorithm (`getNextPrayer`) calculating exact minute differentials with midnight day-rollover support. |
| **Dual Calendar Sync** | Automated Gregorian $\to$ Hijri conversion API (`gToH`) alongside full Arabic localized Gregorian date formatting. |
| **Moroccan Cities Database** | Bundled offline JSON dataset (`Cities.Json` & `TranslateCities.Json`) supporting live fuzzy search. |
| **UI Design System** | Tailwind CSS responsive typography with native right-to-left (RTL) Arabic layout. |

---

## Technical Architecture

```
                    ┌────────────────────────────┐
                    │      Browser Client        │
                    │  (HTML5 Geolocation API)   │
                    └─────────────┬──────────────┘
                                  │ (Lat, Lon Coordinates)
                                  ▼
                    ┌────────────────────────────┐
                    │  LocationIQ Reverse Geo    │
                    │   (Arabic City Resolver)   │
                    └─────────────┬──────────────┘
                                  │ (Resolved City Name)
                                  ▼
        ┌──────────────────────────────────────────────────┐
        │               Prayer Engine Core                 │
        │ ├── Cities.Json (Moroccan Directory)             │
        │ ├── TranslateCities.Json (Bilingual Map)        │
        │ └── AlAdhan REST API (Method 3: Habous)          │
        └─────────────────────────┬────────────────────────┘
                                  │
          ┌───────────────────────┴───────────────────────┐
          ▼                                               ▼
┌───────────────────────────┐                   ┌───────────────────────────┐
│     Dual Calendar Sync    │                   │   Next Prayer Countdown   │
│ • Hijri Date (gToH API)   │                   │ • Current vs Target Delta │
│ • Localized Gregorian     │                   │ • Midnight Rollover Guard │
└───────────────────────────┘                   └───────────────────────────┘
```

---

## Real-Time Countdown Algorithm

The countdown engine converts standard time strings (`HH:mm`) into cumulative minutes from midnight, determining the upcoming prayer and formatting human-readable Arabic intervals:

```javascript
function getNextPrayer(currentTimeStr, prayerTimes) {
    const convertToMinutes = (time) => {
        const [hours, minutes] = time.split(':').map(Number);
        return hours * 60 + minutes;
    };

    const currentMinutes = convertToMinutes(currentTimeStr);

    // Scan sequentially through Fajr, Sunrise, Dhuhr, Asr, Maghrib, Isha
    for (const prayer of prayerTimes) {
        const prayerMinutes = convertToMinutes(prayer.time);
        if (prayerMinutes > currentMinutes) {
            return { ...prayer, timeDiff: prayerMinutes - currentMinutes };
        }
    }

    // Rollover: If all prayers for today have passed, target Fajr of the following day
    const fajrTomorrow = prayerTimes[0];
    const totalMinutesInDay = 24 * 60;
    const fajrMinutes = convertToMinutes(fajrTomorrow.time);
    return {
        ...fajrTomorrow,
        timeDiff: totalMinutesInDay - currentMinutes + fajrMinutes,
    };
}
```

---

## Repository Structure

```
Prayer-Times/
├── index.html                   # Semantic HTML5 UI layout (RTL Arabic)
├── index.js                     # Core application logic, geolocation & API hooks
├── Cities.Json                  # Comprehensive list of Moroccan cities
├── TranslateCities.Json         # French/English to Arabic city dictionary
├── Screenshot 2025-01-15 194823.png # Application UI snapshot
├── package.json                 # Project dependencies & PostCSS build scripts
└── images/                      # Visual iconography & background assets
```

---

## Getting Started

### Prerequisites

- A modern web browser (Chrome, Firefox, Safari, Edge) with Geolocation permissions enabled.
- **Node.js** (Optional, only needed if modifying Tailwind CSS styles).

### 1. Clone the Repository

```bash
git clone https://github.com/iliass-wakkar/Prayer-Times.git
cd Prayer-Times
```

### 2. Run Directly

Because this is a zero-build client application, you can open `index.html` directly in any web browser:

```bash
# On Windows
start index.html

# Or serve via Python HTTP server
python -m http.server 8080
```

Visit `http://localhost:8080` in your browser.

### 3. Modifying Styles (Tailwind CSS)

If modifying stylesheet configurations:

```bash
npm install
npm run build
```

---

## Author & Engineering Credits

- **Iliass Wakkar** — Full-Stack & Systems Engineer • [Portfolio](https://www.wakkar.net) • [GitHub](https://github.com/iliass-wakkar) • [LinkedIn](https://www.linkedin.com/in/iliass-wakkar-940233335/)

---

## License

This project is licensed under the ISC License — see the [package.json](package.json) file for details.
