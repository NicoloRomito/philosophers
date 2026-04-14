# philosophers

> An introduction to concurrent programming — threads, mutexes, and the dining philosophers problem.

`philosophers` is a 42 Common Core project based on Edsger Dijkstra's classic **Dining Philosophers Problem**. The goal is to simulate a table of philosophers eating, thinking, and sleeping — without any of them dying of starvation, and without data races or deadlocks.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Rules](#rules)
- [Technical Notes](#technical-notes)

---

## Overview

A set of philosophers sits at a round table. There is one fork between each pair of adjacent philosophers (i.e., the same number of forks as philosophers). To eat, a philosopher needs both the fork to their left and the fork to their right. After eating, they put both forks down and go to sleep, then think — repeating the cycle until the simulation ends.

The simulation must:
- Never let a philosopher die of starvation (if a solution is possible)
- Avoid data races entirely
- Print each state change with a precise timestamp

---

## Features

- POSIX threads (`pthread`) for each philosopher
- Mutex-protected forks to prevent race conditions
- Millisecond-precision timestamps using `gettimeofday`
- Optional stopping condition: simulation ends after each philosopher has eaten N times
- Death detection running in a monitoring thread
- Clean resource management — no memory leaks, no data races

---

## Project Structure

```
philosophers/
├── main.c              # Entry point — argument parsing and simulation launch
├── philo.h             # Header — structs, prototypes, includes
├── init.c              # Struct and mutex initialisation
├── threads.c           # Thread creation and lifecycle management
├── routine.c           # Philosopher routine (eat / sleep / think)
├── monitor.c           # Death monitoring loop
├── utils.c             # Time utilities, printing, argument validation
└── Makefile
```

> *Note: actual file names may vary — structure reflects the common project layout.*

---

## Requirements

- GCC or Clang with POSIX thread support
- GNU Make
- Unix-based OS (Linux or macOS)

---

## Installation

Clone the repository and build:

```bash
git clone https://github.com/NicoloRomito/philosophers.git
cd philosophers
make
```

Clean up:

```bash
make clean    # removes object files
make fclean   # removes binary and object files
make re       # full rebuild
```

---

## Usage

```bash
./philo <number_of_philosophers> <time_to_die> <time_to_eat> <time_to_sleep> [number_of_times_each_philosopher_must_eat]
```

| Argument | Description |
|---|---|
| `number_of_philosophers` | Number of philosophers (and forks) |
| `time_to_die` | Time in ms before a philosopher dies if they haven't started eating |
| `time_to_eat` | Time in ms it takes a philosopher to eat |
| `time_to_sleep` | Time in ms a philosopher sleeps after eating |
| `number_of_times_each_philosopher_must_eat` | (Optional) Simulation stops when all philosophers have eaten this many times |

**Examples:**

```bash
# 5 philosophers, die after 800ms, eat for 200ms, sleep for 200ms
./philo 5 800 200 200

# Same, but stop after each philosopher has eaten 7 times
./philo 5 800 200 200 7

# Should not die: 1 philosopher (no second fork — will die)
./philo 1 800 200 200
```

---

## Rules

- Philosophers do not communicate with each other
- They do not know if another philosopher is about to die
- Each philosopher is a thread; each fork is protected by a mutex
- A philosopher must not eat with the same fork twice simultaneously
- State changes are printed as: `timestamp_in_ms | philosopher_id | action`

---

## Technical Notes

- Timestamps are computed using `gettimeofday` for sub-millisecond accuracy
- A separate monitoring thread checks for deaths without blocking the eating cycle
- Even numbers of philosophers vs odd numbers require different fork-picking strategies to avoid deadlocks
- All mutexes are properly destroyed and threads joined on exit

---

*Project developed at 42 Firenze as part of the Common Core curriculum.*
