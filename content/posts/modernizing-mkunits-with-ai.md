---
title: "Modernizing MKUnits With AI"
date: 2026-02-21T00:00:00+00:00
draft: false
toc: true
tags:
  - swift
  - ai
  - open-source
---

[MKUnits](https://github.com/michalkonturek/MKUnits) is a unit conversion library for Swift that I originally wrote back in 2014. It had a good run — 342 stars, used by people building apps that needed to convert between mass, length, area, volume, and more. Then life happened, and the library sat untouched from 2016 until earlier this year.

A decade is a long time in Swift. The language that existed in 2016 (Swift 3.0) is almost unrecognisable compared to Swift 6.0. I decided to bring MKUnits fully up to date, and I did it with AI assistance.

Here is what that looked like.

## Where Things Stood

The last commit before this modernisation effort was from November 2016. The library was:

- Written in Swift 3.0
- Distributed via CocoaPods and Carthage
- Tested with XCTest
- Structured as an Xcode-first project

None of these are wrong per se, but none of them are where the Swift ecosystem is in 2026 either.

## Swift 3 to Swift 6

The biggest jump. Swift 6 introduced strict concurrency checking — every type that crosses concurrency boundaries must conform to `Sendable`. The AI worked through the entire codebase, adding `Sendable` conformance where needed, replacing deprecated APIs, and updating Swift syntax that had evolved across ten major versions.

What makes this particularly tedious to do by hand is that you have to understand *why* each change is needed, not just *what* to change. The compiler error messages for Swift 6 concurrency are verbose. Having the AI reason through them systematically meant we got through the migration without getting lost in the noise.

The result was tagged as **v5.0.0**.

## SPM First

CocoaPods and Carthage were the standard in 2016. Swift Package Manager is the standard now. The project was restructured from the ground up as an SPM-first library — proper `Package.swift`, sensible target layout, no more `.xcodeproj` as the source of truth.

This also meant the library could now be used on Linux, not just macOS and iOS. A GitHub Actions workflow was added to run tests on both platforms on every push — something that simply was not possible with the old Xcode-only setup.

Tagged as **v6.0.0**.

## XCTest to Swift Testing

Apple introduced [Swift Testing](https://developer.apple.com/xcode/swift-testing/) as the modern replacement for XCTest. The entire test suite — hundreds of tests — was migrated to the new framework.

The difference is meaningful. Swift Testing uses macros (`#expect`, `#require`) instead of assertion functions, has better failure messages, and integrates more naturally with the rest of Swift. Migrating a large test suite by hand is exactly the kind of repetitive structural work that AI handles well. The tests went from this:

```swift
func testThatItConverts_1_Kilogram_to_Pound() {
    let sut = MKMass.kilogram(1)
    let result = sut.convert(to: MKMassUnit.pound)
    XCTAssertEqual(result, MKMass.pound(2.20462))
}
```

To this:

```swift
@Test func kilogram_to_pound() {
    let sut = MKMass.kilogram(1)
    #expect(sut.convert(to: .pound) == MKMass.pound(2.20462))
}
```

Cleaner, more expressive, and all without manually touching each of the hundreds of test cases.

## New Units

With the foundation modernised, adding new units became straightforward. Two new unit types were added:

**ByteUnit** — digital storage conversions: bit, byte, kilobyte, megabyte, gigabyte, terabyte, and the full IEC binary variants (kibibyte, mebibyte, gibibyte, tebibyte).

**TemperatureUnit** — this one required some thought. Most unit conversions are multiplicative (metres to feet is just a constant factor). Temperature is different — Celsius, Fahrenheit, and Kelvin all have different zero points, so conversion requires both scaling and offset. The AI worked through the maths, implemented offset-based conversion correctly, and added a full test suite to verify it.

## A Demo Target

A small command-line executable was added to the package — `MKUnitsDemo` — that exercises the library and serves as a living example of how to use it. Useful for anyone evaluating the library, and useful as a quick sanity check during development.

## Reflections

The thing that struck me most was the speed at which a decade of drift can be addressed. Not because AI writes perfect code — it does not — but because it removes the activation energy. Looking at a 10-year-old Swift 3 codebase and knowing you need to bring it to Swift 6 is daunting. Starting a conversation and just... doing it, step by step, is not.

The AI was particularly good at the mechanical parts: migrating syntax, applying consistent patterns across many files, translating XCTest to Swift Testing. It was less useful for decisions that required judgement — like how to structure the offset-based temperature conversion, or what the right API shape should be. Those conversations happened, but they needed me to push back and think carefully.

MKUnits is now a modern Swift 6 library. It took one session to cover what ten years had left behind.
