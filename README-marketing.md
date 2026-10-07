# Nutrition Tracker

Track your food in Apple Health with a flexible, simple shortcut.

Nutrition Tracker is built for people who want a faster and more reusable way to log nutrition. Instead of creating separate workflows for every type of food, the shortcut turns diverse inputs into one clean process.

You can add food from several sources:

- Barcode scan
- Manual input
- AI-assisted food estimation
- Saved JSON files
- JSON passed from another Shortcut

Once the food is added, the shortcut normalizes the data, asks for the serving size, calculates the nutrients, and logs the results to Apple Health.

## Why it stands out

Most nutrition trackers are built around one exact source of data. Nutrition Tracker is different because it works as a central processing layer for multiple input methods.

That means you can:

- scan packaged foods,
- estimate homemade meals with AI,
- manually type nutrition values,
- reuse your previously saved foods,
- and keep the same workflow for every source.

## The core idea

Nutrition Tracker converts each food into a standard nutrition JSON structure. After that, everything uses the same logic:

- normalize values,
- save the food,
- select serving size,
- calculate the nutrients,
- log them to Apple Health.

This makes the shortcut easier to extend and much more flexible than a one-off tracker.

## Best for

- people tracking calories and macros,
- users who want AI-assisted nutrition logging,
- anyone who wants more control over food data,
- users building custom automation around nutrition entry,
- people using Apple Health as their main health dashboard.

## Quick workflow

1. Choose an input method
2. Add food data
3. Confirm the nutrition profile
4. Enter the portion you ate
5. Review the calculated nutrients
6. Save to Apple Health

## Privacy and data handling

Nutrition Tracker is designed to be useful without locking you into a single data source. It supports local data storage for saved foods and keeps the process straightforward.

Important notes:

- Barcode lookup may send the product barcode to Open Food Facts
- AI input may send food information or images to the selected AI provider
- Health permissions are required before writing nutrition data
- Local saved foods can be reused without internet access

## Example use cases

- Scan a protein bar and log it in seconds
- Manually enter a homemade meal with ingredients you know
- Use AI to estimate a restaurant dish
- Reuse a saved breakfast every morning
- Pass data from another shortcut into the same nutrition pipeline

## Summary

Nutrition Tracker is a flexible Apple Shortcuts workflow for people who want an easier and more adaptable way to log nutrition into Apple Health. It combines multiple data sources under one consistent process, making it faster, more reusable, and more powerful than a standard single-source tracker.
