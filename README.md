# DropHunt 🛍️📱

DropHunt to nowoczesna aplikacja na system Android służąca do śledzenia cen i promocji w ulubionych sklepach internetowych.

## Funkcje
- **Przegląd kategorii i sklepów**: Dostęp do kategorii (Kosmetyki, Ubrania, Buty, Elektronika, Dom, Sport, Zabawki, Książki) oraz list oficjalnych sklepów z prawdziwymi adresami URL i linkami do promocji (zdefiniowanych w `shops.json`).
- **Dodawanie przez link**: Wklejanie linku do produktu ze schowka. Aplikacja pobiera stronę za pomocą biblioteki Jsoup (poza głównym wątkiem), parsuje dane (JSON-LD oraz meta tagi Open Graph) i zapisuje produkt w lokalnej bazie danych.
- **Historia cen i wykresy**: Śledzenie historii zmian cen każdego produktu, wyliczanie najniższej i najwyższej ceny oraz procentowego spadku względem historii. Wykres historii cen renderowany w czasie rzeczywistym za pomocą Compose Canvas.
- **Powiadomienia i WorkManager**: Automatyczne, cykliczne sprawdzanie cen ulubionych produktów co 6 godzin (z zachowaniem restrykcji sieciowych) oraz wysyłanie powiadomień systemowych w przypadku spadku ceny. Możliwość ręcznego wywołania sprawdzenia przyciskiem "Sprawdź ceny teraz".
- **Bez zmyślonych danych**: Wszystkie linki i sklepy są w pełni prawdziwe i zweryfikowane.

## Architektura i Stack Technologiczny
- **Język**: Kotlin
- **UI**: Jetpack Compose, Material 3
- **Architektura**: MVVM (ViewModel + StateFlow), Repository pattern
- **Baza danych**: Room (lokalna baza dla produktów, punktów cenowych oraz cache ofert)
- **Sieć i Serializacja**: Retrofit + Kotlinx Serialization, Coil (ładowanie obrazów), Custom Tabs (otwieranie ofert)
- **Zadania w tle**: WorkManager (cykliczne sprawdzanie cen)
- **Nawigacja**: Jetpack Navigation Compose

## Format JSON dla backendu (ProductFeedSource)
Endpoint: `GET /products?category=...&page=...`

Oczekiwana odpowiedź od serwera (JSON Array):
```json
[
  {
    "id": "prod_1",
    "name": "Przykładowy produkt",
    "shop": "Rossmann",
    "category": "Kosmetyki",
    "imageUrl": "https://example.com/image.jpg",
    "price": 49.99,
    "oldPrice": 69.99,
    "currency": "PLN",
    "offerUrl": "https://www.rossmann.pl/produkt/1"
  }
]
```

## Podłączenie własnego źródła danych (ProductFeedSource)
Aplikacja posiada interfejs [`ProductFeedSource`](app/src/main/java/com/example/drophunt/data/feed/ProductFeedSource.kt). Konfigurowalny adres bazowy (`baseUrl`) można ustawić w `BuildConfig.PRODUCT_FEED_BASE_URL` (np. w build.gradle.kts). 

Gdy `baseUrl` jest pusty lub serwer nie odpowiada, aplikacja automatycznie korzysta z lokalnego cache Room oraz wyświetla stan informujący o braku połączenia ze źródłem ofert wraz z przyciskiem dodawania produktu przez link.
