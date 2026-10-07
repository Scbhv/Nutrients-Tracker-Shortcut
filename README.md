# Nutrition Tracker

*https://routinehub.co/shortcut/21541/*

Nutrition Tracker is a shortcut for Apple Shortcuts and Apple Health that helps you log food and nutrients more easily.

Instead of entering everything manually every time, you can:

- scan a barcode,
- type nutrition details yourself,
- use AI to estimate a meal,
- or reuse foods you saved before.

The shortcut turns all of these into the same internal format, calculates the nutrients for the amount you ate, and saves them to Apple Health.

This shortcut works best together with:

- https://routinehub.co/shortcut/24903/
- https://routinehub.co/shortcut/26329/


## What this shortcut does

Nutrition Tracker helps you:

- log foods faster,
- save favorite meals and ingredients,
- use different input methods for the same food,
- calculate nutrients based on portion size,
- send the result to Apple Health.

You can track your food without needing to build a separate workflow for every type of input.


## How it works

The shortcut follows one simple idea:

Every food source is converted into the same nutrition format before it is calculated and saved.

That means the same process is used whether the food came from:

- a barcode,
- manual input,
- AI analysis,
- or a saved JSON file.

From there, the shortcut does the same steps:

1. Add food
2. Normalize the data
3. Choose serving size
4. Calculate nutrients
5. Save to Apple Health


## Supported input methods

### 1) Barcode scan
Best for packaged foods.

- scan the barcode,
- look up product information,
- read the nutrition values,
- adjust as needed,
- continue with tracking.

This uses Open Food Facts to pull basic product data.

### 2) Manual input
Best when:

- the food has no barcode,
- the product is homemade,
- the nutrition label is known,
- or you want full control over the values.

You can type the nutrient values yourself and save them for later.

### 3) AI input
Best for meals that are harder to look up:

- restaurant meals,
- homemade dishes,
- mixed meals,
- food shown in a photo.

AI can estimate nutrition values, but results should be treated as estimates unless you have a verified label.

### 4) Saved JSON files
Once you have saved a food, you can reuse it later without doing the whole process again.

This makes frequently eaten foods much faster to log.


## Quick start

1. Install the shortcut from RoutineHub.
2. Open the shortcut in Apple Shortcuts.
3. Allow the required permissions.
4. Choose how you want to add food.
5. Enter or scan the food details.
6. Enter the amount you ate.
7. Review the nutrient values.
8. Save the result to Apple Health.


## Example workflow

A simple example:

- scan a yogurt cup,
- confirm the nutrition data,
- enter 200 g,
- calculate the nutrients for that amount,
- save the result to Apple Health.

If the same yogurt is eaten often, you can save it and reuse it later.


## Serving size calculation

Nutrition stored in the shortcut is based on 100 g.

Then the shortcut calculates the amount for the portion you actually ate.

Example:

- Protein: 14 g per 100 g
- Portion eaten: 250 g
- Calculation: 14 × 2.5 = 35 g

This makes it easy to log foods that are not sold in standard serving sizes.


## Why it is useful

Nutrition Tracker is helpful if you want a more flexible way to log food without relying on a single source.

It makes it easy to combine:

- product barcodes,
- hand-entered nutrition values,
- AI estimates,
- and your own saved foods.

This keeps the process consistent while still allowing different ways to add data.


## Privacy

Nutrition Tracker is designed to be privacy-conscious, but privacy depends on the method used.

Local data:

- saved food files can be stored in your Files app,
- the shortcut can calculate values locally,
- it does not need to read your private Health data to do the math.

Barcode lookup:

- the barcode may be sent to Open Food Facts for product lookup.

AI input:

- an image or text may be sent to the AI provider you choose.

Apple Health:

- the shortcut requires permission before writing nutrition data to Health.


## Offline usage

Saved foods can be reused without internet access.

This is useful when:

- you already saved the food before,
- you want to log a known meal quickly,
- or you are offline.

Barcode lookup and AI features usually need internet access depending on the service used.


## Tips for regular users

- Use barcode mode for packaged foods when possible.
- Use manual input for homemade meals or custom recipes.
- Save repeat meals so you can reuse them later.
- Be careful with AI estimates if accuracy matters.
- Check the serving size carefully before saving to Health.


## Installation

Requirements:

- Apple Shortcuts
- Apple Health
- A compatible iPhone or iPad
- Permission to access the required categories

Install the shortcut here:

https://routinehub.co/shortcut/21541/

Then:

1. Open the link.
2. Add the shortcut.
3. Run it.
4. Allow the required permissions.
5. Choose a food input method.
6. Add the food.
7. Enter the amount eaten.
8. Save the result to Apple Health.


## Permissions you may be asked for

Depending on the feature used, the shortcut may ask for:

- Internet access for barcode and update features
- Camera access for barcode scanning and photo-based AI
- Files access for saving and loading food JSON files
- Apple Health access for writing nutrition data

You stay in control of these permissions.


## Troubleshooting

### Barcode lookup does not find the food
Try manual entry or AI input instead. Some products may not be in the database or may have incomplete information.

### AI values look off
AI estimates are not always exact. Try to provide more detail, or prefer a verified nutrition label when available.

### Apple Health is not updating
Make sure the shortcut has Health permissions and that the nutrient type is supported by Apple Health.

### I eat the same food often
Save it as a local JSON food so you can reuse it next time instead of entering it again.


## Disclaimer

This shortcut is intended to support general nutrition tracking and awareness. It is not a medical tool and does not replace professional nutrition advice, medical guidance, or advice from a qualified health professional.


## Credits

- Open Food Facts for barcode product data
- Apple Shortcuts and Apple Health for the automation and health integration
- The Nutrition Tracker community for feedback and testing


## Summary

Nutrition Tracker is a practical way to log food in Apple Health using different sources, while keeping the process simple and consistent.

It is especially useful if you want a flexible system that works with:

- barcodes,
- manual entries,
- AI estimates,
- and saved foods.

If you want, I can also create a cleaner version specifically for the GitHub homepage, or a shorter version designed for a RoutineHub listing.
