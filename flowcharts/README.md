# Airbnb Clone Backend — Flowcharts

## What We Did in This Task

For this task, we selected the **Property Booking** process—one of the core backend features—and designed a detailed flowchart to map its workflow and data flow. 

The flowchart visualizes each step involved when a guest books a property, from searching availability to payment and confirmation. It helps clarify the backend logic required to ensure reliable booking management, prevent double bookings, and trigger notifications.

**Key Steps in the Process:**
1. Guest searches for available properties.
2. Guest selects a property and enters booking details (dates, guests).
3. System validates availability for the requested period.
4. If available, booking is created with a "pending" status.
5. Guest proceeds to payment.
6. Payment is processed securely.
7. On payment success, booking status is updated to "confirmed" and notifications are sent.
8. On payment failure or if the dates are unavailable, the process ends with an error message.

The flowchart is provided as a PNG (`data-flow-diagram.png`).

---

## Files

- `data-flow-diagram.png`: Exported PNG image of the flowchart.


## Directory Structure

```
flowcharts/
  ├── README.md
  └── data-flow-diagram.png
```

---