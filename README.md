# 🗕️ Kalander – A Svelte Calendar System

> **Kalander** (pronounced *Kal-Lander*) is a modular, composable system for building calendar-driven UIs like weekly schedulers, planners, and appointment tools — all within the Svelte framework.

---

![Kalander Screenshot](https://github.com/DruPro/Kalander/blob/main/docs/Screenshot%202025-07-26%20173922.png?raw=true)


## 🔧 Purpose

Kalander is not a UI component — it's an **architecture**.

It gives structure to calendar logic, date manipulation, layout geometry, and time slot organization, so you can build your own scheduler UIs without reinventing core mechanics.

Rather than being a single component, Kalander is a **network of cooperating modules**, each responsible for one aspect of the calendar's logic.

---

## 🧹 Mental Model

Kalander is made up of **three primary modules**:

| Module             | Responsibility                                     |
| ------------------ | -------------------------------------------------- |
| `KalanderManager`  | Central controller — orchestrates all other pieces |
| `TimeSlotManager`  | Handles time slot logic like filtering & overlap   |
| `CalendarGeometry` | Calculates how time translates to pixels on screen |

These parts plug into your Svelte UI components, providing a unified logic layer that can be reused across views, modals, and toolbars.

---

## 🕹️ The Flow of Data

Here's how everything connects when you build with Kalander:

```
          +---------------------+
          |     Kalander UI     | ← e.g. your <Calander /> or toolbar
          +---------------------+
                   |
         passes props / reacts to changes
                   |
                   v
        +------------------------+
        |   KalanderManager()    |
        +------------------------+
         |           |           |
     context     geometry    timeSlots
     (selected   (height,    (filtering,
      date)       layout)     collision)

```

Each sub-module focuses on one thing:

* **TimeSlotManager** deals with *what exists*: appointments, blocks, data.
* **CalendarGeometry** deals with *how it looks*: converting `10:30` to pixel height.
* **KalanderManager** brings it together and reacts to the selected day or week.

---

## 🧠 How It Feels to Use

When working with Kalander:

* You **create a `KalanderManager`** in your parent layout.
* You **bind it** to components like `<Calander />` and `<Toolbar />`.
* You **feed it raw time slot data**, and it tells you which time slots belong to which day, what their screen height should be, and how they relate.
* You use its **context object** to track the currently selected day reactively.

---

## 🌍 Example Integration

```svelte
<script>
	import KalanderManager from './managers/KalanderManager.svelte';
	const kalander = KalanderManager();

	let timeSlots = $state.raw([...]); // your appointment data
</script>

<Calander kalanderManager={kalander} timeSlotData={timeSlots} />

<Toolbar bind:currentSelectedDate={kalander.context.currentSelectedCalanderDate} />
```

> Other components like `<RepeatAppointment />` or `<ScheduleTypeModal />` are optional extras, not core to Kalander — but Kalander integrates with them easily via its shared state.

---

## 🔄 What Kalander Does For You

* Keeps a **timezone-aware "now"** value (`currentDatePacific`) so your calendar is always accurate.
* Maintains a **central context** of the currently selected day.
* Provides a **clean separation** of:

  * Geometry (`hour → pixels`)
  * Logic (`which slots belong to which day`)
  * Data (`where is the user focused in the calendar`)
* Makes **UI composition** predictable and declarative.

---

## 📚 Why Use Kalander?

Kalander is perfect if you're building:

* A scheduling dashboard
* A weekly planner
* An appointment booking interface
* A time-blocking productivity tool

And you want:

* Clean separation of logic and layout
* A scalable foundation for calendar features
* To work with **Temporal**, not fragile `Date` objects

---

## 🧠 Summary

Kalander is:

* **Not** a monolithic calendar component
* **Not** opinionated about your UI
* **A modular brain** for calendar UIs — it lets you plug logic into whatever visual design you want

It helps your Svelte calendar stay accurate, maintainable, and extensible — without duplicating logic across your views.
