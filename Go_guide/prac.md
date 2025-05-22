## GoLang CLI Ticket Booking App – Practice Project

This document outlines a CLI-based ticket booking application to practice core concepts in Go.

here's the problem statement try it out yourself and then see the reference repo if needed .

## Problem Statement:

You are building a simple command-line application for managing conference ticket bookings. The goal is to simulate the process of collecting user details, validating input, managing available tickets, and confirming bookings.

---

### Requirements

1. 1.The application should greet users and show total & remaining tickets.
2. 2.Prompt the user to input:
   - First Name
   - Last Name
   - Email
   - Number of tickets
3. 3.Validate the input:
   - First and last name must be at least 2 characters
   - Email must contain "@"
   - Number of tickets must be > 0 and ≤ remaining tickets
4. 4.If input is valid:
   - Store booking in memory using a struct
   - Decrease remaining tickets
   - Print booking confirmation
5. 5.Send ticket asynchronously with a delay (simulate with `time.Sleep`)
6. 6.Show all first names of booked users after each booking
7. 7.Program should stop when tickets are sold out
8. 8.Make necesaary assumptions and try to divide each works into different functions and packages.


---

### Constraints

- Use Go slices to store bookings
- Use structs for modeling user data
- Use goroutines and `sync.WaitGroup` to simulate sending tickets
- Input/output should happen via terminal (`fmt.Scan`, `fmt.Println`)

---
Reference implementation:  

##  https://gitlab.com/nanuchi/go-full-course-youtube

---

### Purpose

Build a working command-line app to understand how Go handles:

- Variables and constants
- Structs for data modeling
- Slices for dynamic collections
- Input handling via `fmt.Scan`
- Basic validation logic
- Modular function design
- Goroutines for async tasks
- sync.WaitGroup for concurrency control
- Formatted console output

---

### Project Overview

The application simulates a basic conference ticket booking system. It captures user details, processes ticket bookings, and simulates sending tickets via goroutines with artificial delay. All bookings are stored in-memory using a slice of custom struct type.

---
This project is a foundational step for deeper backend work in Go.
