# Sequence Diagram — Бронювання квитка в кінотеатрі

```mermaid
sequenceDiagram
  actor User as Користувач
  participant UI as Інтерфейс
  participant BookingService as Сервіс бронювання
  participant PaymentService as Сервіс оплати
  participant DB as База даних

  User->>UI: Вибирає фільм та сеанс
  UI->>BookingService: getAvailableSeats(sessionId)
  BookingService->>DB: Запит вільних місць
  DB-->>BookingService: Список місць
  BookingService-->>UI: Показати вільні місця

  User->>UI: Обирає місця та натискає "Забронювати"
  UI->>BookingService: createBooking(userId, sessionId, seats)
  BookingService->>DB: Зберегти бронювання (статус: очікує)
  DB-->>BookingService: OK
  BookingService-->>UI: Бронювання створено, перейти до оплати

  User->>UI: Вводить дані карти та підтверджує
  UI->>PaymentService: processPayment(bookingId, amount)

  alt Оплата успішна
    PaymentService-->>UI: Оплата підтверджена
    UI->>BookingService: confirmBooking(bookingId)
    BookingService->>DB: Оновити статус на "підтверджено"
    DB-->>BookingService: OK
    BookingService-->>UI: Квиток підтверджено
    UI-->>User: Показати квиток
  else Оплата відхилена
    PaymentService-->>UI: Помилка оплати
    UI->>BookingService: cancelBooking(bookingId)
    BookingService->>DB: Видалити бронювання
    DB-->>BookingService: OK
    UI-->>User: Повідомлення про помилку оплати
  end
```

## Опис

Діаграма показує процес бронювання квитка:

1. Користувач обирає фільм і переглядає вільні місця
2. Система створює попереднє бронювання
3. Користувач здійснює оплату
4. При успішній оплаті — квиток підтверджується
5. При невдалій оплаті — бронювання скасовується автоматично
