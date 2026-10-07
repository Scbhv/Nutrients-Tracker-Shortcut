# Nutrition Tracker

https://routinehub.co/shortcut/21541/

Your nutrition tracker is built directly into Apple Shortcuts and Apple Health.

Nutrition Tracker is an Apple Shortcut designed to make detailed nutrition tracking in Apple Health faster, reusable, and independent of a single data source.

This shortcut works best with two of my other shortcuts:

- https://routinehub.co/shortcut/24903/
- https://routinehub.co/shortcut/26329/

Check them out. A README for them might drop soon too.

---

## Nutrient Tracker Shortcut

Food can currently be added using:

- 📷 Barcode Scan
- ✍️ Manual Input
- 🤖 AI
- 💾 Saved JSON files

Nutrition Tracker is built around one important principle:

Every input method produces the same nutrition JSON. Everything after that is shared.

Instead of creating a completely different Apple Health workflow for barcode scanning, AI, manual input, and saved foods, all sources are first converted into a standardized nutrition dictionary.

That dictionary is then used by the rest of the Shortcut.

The architecture can therefore be simplified to five major stages:

1. INPUT
   ↓
2. NORMALIZATION
   ↓
3. STORAGE
   ↓
4. PORTION CALCULATION
   ↓
5. APPLE HEALTH

...or more detailed like this:

```text
             ┌────────────────────┐
             │  Nutrition Tracker │
             └─────────┬──────────┘
                       │
                       ▼
              Choose Input Method
                       │
       ┌───────────────┼────────────────┐───────────────┐
    Barcode           Manual             AI         Saved JSON
       │                │                │               │
       │                │                │               │
       └────────–───────┴────────-───────┴───────────────┘
                       │
                       ▼
               Raw Nutrition Data
                       │
                       ▼
                    Formatting
                       │
                       ▼
             Standard Nutrition JSON
                       │
             ┌─────────┴──────────┐
             ▼                    ▼
        Save as JSON          Serving Size
                                  │
                                  ▼
                            Portion ÷ 100
                                  │
                                  ▼
                          Calculate Nutrients
                                  │
                                  ▼
                             Apple Health
```

---

## 1. Choosing an Input Method

When you start Nutrition Tracker, you choose how the food should be loaded.

The main options are:

| Input | Purpose |
| --- | --- |
| Barcode Scan | Retrieve packaged food from Open Food Facts |
| Manual Input | Enter nutrition information yourself |
| AI | Estimate or extract nutrition information using AI |
| Saved JSON | Load a food that was previously saved |

All of these methods eventually produce the same standardized nutrition dictionary.

---

## 2. Barcode Scan

Barcode scanning is intended primarily for packaged food.

Nutrition Tracker uses the device camera to scan the barcode.

The barcode is then sent to Open Food Facts to retrieve product information.

The Shortcut requests:

- `product_name`
- `nutriments`

The nutriments dictionary can contain values such as:

- Energy
- Fat
- Saturated fat
- Carbohydrates
- Sugars
- Fiber
- Protein
- Salt
- Sodium
- Potassium
- Calcium
- Magnesium
- Iron
- Zinc
- Vitamins
- and many other available nutrients

Not every Open Food Facts product contains every nutrient and not every food is available in the library.

Nutrition Tracker therefore processes whatever information is available.

<!-- ![Barcode Scanner](docs/images/02-barcode-scanner.png) -->

### Barcode Workflow

```text
Scan Barcode
      ↓
Read Barcode Number
      ↓
Open Food Facts API
(product_name + nutriments)
      ↓
.json adjustments
      ↓
Save / Continue
```

For example, the API data might eventually be converted into:

```json
{
  "food_name": "Example Food",
  "energy-kcal_100g": 361,
  "fat_100g": 6.7,
  "carbohydrates_100g": 56,
  "sugars_100g": 1.2,
  "fiber_100g": 11,
  "proteins_100g": 14
}
```

These values describe the food per 100 g.

The actual amount eaten is calculated later.

### A small thing happening in the background

Open Food Facts and the Nutrition Tracker do not always use exactly the same units.

For example, some minerals are returned by Open Food Facts in g/100 g while the internal JSON uses mg/100 g.

So the Shortcut converts those values before storing them.

For example:

```text
0.25 g / 100 g
       ↓
250 mg / 100 g
```

This means the rest of the Shortcut can work with one consistent format.

---

## 3. Manual Input

Nutrition information can also be entered manually.

This is useful when:

- a product is missing from Open Food Facts,
- the database entry is incorrect,
- the food has no barcode,
- or you're offline

It will ask for every single nutrient one after another.

They are grouped by measurement and after each group you will be asked if you want to skip the next one, so you can skip the mg and/or µg nutrients if you don't need them.

The final .json will look something like this:

```json
{
  "food_name": "Homemade Granola",
  "energy-kcal_100g": 430,
  "fat_100g": 15,
  "saturated-fat_100g": 3,
  "carbohydrates_100g": 55,
  "sugars_100g": 12,
  "fiber_100g": 8,
  "proteins_100g": 13
}
```

Missing nutrients do not prevent the food from being used.

Values that are unavailable are normally kept as 0 in the standardized dictionary.

---

## 4. AI Input

The AI mode is intended for food where reliable structured nutrition data is not immediately available.

Like:

- Restaurant meals
- Homemade meals
- Mixed meals
- Food from a picture
- Products where you don't want to enter everything manually

<!-- ![AI Nutrition Analysis](docs/images/04-ai-analysis.jpeg) -->

You will be asked to take a picture of the food and then you will hopefully get a structured JSON rather than normal conversational text.

For example:

```json
{
  "food_name": "Vegetable Pasta",
  "energy-kcal_100g": 142,
  "fat_100g": 4.2,
  "saturated-fat_100g": 1.1,
  "unsaturated-fat_100g": 3.1,
  "carbohydrates_100g": 20.5,
  "sugars_100g": 3.2,
  "fiber_100g": 2.8,
  "proteins_100g": 5.1
}
```

Because the response follows the same format used by the rest of Nutrition Tracker, it can immediately enter the normal processing workflow.

The AI does not need to know anything about Apple Health.

It only has to produce compatible nutrition JSON.

Nutrition Tracker handles everything after that.

```text
Take Photo
     ↓
AI analyzes food
     ↓
Estimate food + nutrients
     ↓
Generate JSON
     ↓
Nutrition Tracker
     ↓
Serving Size
     ↓
Apple Health
```

### Using another Shortcut or AI service

The AI JSON can also be passed into Nutrition Tracker through Shortcut Input.

This means you don't necessarily have to build the AI directly into this Shortcut.

You can have something like:

```text
Another Shortcut
      ↓
Take / select photo
      ↓
AI analyzes food
      ↓
AI generates Nutrition JSON
      ↓
Nutrition Tracker
      ↓
Serving Size
      ↓
Apple Health
```

This also means you can replace the AI service if you want.

ChatGPT is optional and is only one possible way of generating the JSON. You can use another AI service as long as it produces the expected structure.

When nutrition information is clearly visible on packaging, those visible values should be preferred over AI estimation whenever possible.

AI-generated values should always be treated as estimates when no verified data is available.

---

## 5. Saved JSON

Foods do not need to be downloaded, generated, or entered every time.

Once a food has been processed, its nutrition profile can be stored as a .json file.

For example:

```text
Nutrients Database/
│
├── Banana.json
├── Oatmeal.json
├── Greek Yogurt.json
├── Protein Powder.json
├── Milk.json
└── Pasta.json
```

A saved file for bananas could contain:

```json
{
  "food_name": "Banana",
  "energy-kcal_100g": 89,
  "fat_100g": 0.3,
  "carbohydrates_100g": 22.8,
  "sugars_100g": 12.2,
  "fiber_100g": 2.6,
  "proteins_100g": 1.1
}
```

When that food is selected later, Nutrition Tracker can skip:

- Barcode scanning
- Open Food Facts
- AI analysis
- Manual entry

and move directly to the serving-size calculation.

The backend (the folder) will look like this:

### Optional file naming tip

If you often eat certain foods as individual pieces, you can also put an approximate standard weight into the filename.

For example:

`Ei(m~50g).json`

The `~` is just meant to show that the weight is approximate.

This is only a naming convention and has no effect on the actual calculation.

The amount that gets added to Apple Health is still determined by the portion you enter when running the Shortcut.

---

## 6. Data Normalization

One of the most important parts of Nutrition Tracker is nutrient normalization.

Different databases, APIs and AI models do not always use the same property names or units.

For example, a source might provide a value under a slightly different name or in a different unit.

Nutrition Tracker therefore converts the incoming data into its own standardized dictionary before continuing with the rest of the Shortcut.

The important part is that the following stages don't have to care where the information originally came from.

It basically works like this:

```text
Barcode
Manual
AI
Saved JSON
   │
   ▼
Standardized Nutrition Dictionary
   │
   ▼
Same processing for everything
```

Normalization happens at different points depending on the input method.

For example, the Barcode input converts the Open Food Facts values into the internal format before the common processing starts.

The final normalization step then makes sure that the values used by the rest of the Shortcut have the expected structure and numeric types.

This makes Nutrition Tracker compatible with:

- Open Food Facts API
- AI-generated JSON
- Manually generated JSON
- Saved food JSON
- and potentially other sources in the future

---

## 7. Nutrition JSON Format

The JSON dictionary is the central interface of Nutrition Tracker.

An example empty food JSON may look like this:

```json
{
  "food_name": "",
  "energy-kcal_100g": 0,
  "fat_100g": 0,
  "saturated-fat_100g": 0,
  "unsaturated-fat_100g": 0,
  "carbohydrates_100g": 0,
  "sugars_100g": 0,
  "fiber_100g": 0,
  "proteins_100g": 0,
  "salt_100g": 0,
  "sodium_100g": 0,
  "potassium_100g": 0,
  "chloride_100g": 0,
  "calcium_100g": 0,
  "magnesium_100g": 0,
  "iron_100g": 0,
  "zinc_100g": 0,
  "copper_100g": 0,
  "manganese_100g": 0,
  "phosphorus_100g": 0,
  "iodine_100g": 0,
  "chromium_100g": 0,
  "selenium_100g": 0,
  "molybdenum_100g": 0,
  "thiamine_100g": 0,
  "riboflavin_100g": 0,
  "niacin_100g": 0,
  "pantothenic-acid_100g": 0,
  "vitamin-a_100g": 0,
  "vitamin-b6_100g": 0,
  "vitamin-b12_100g": 0,
  "vitamin-c_100g": 0,
  "vitamin-d_100g": 0,
  "vitamin-e_100g": 0,
  "vitamin-k_100g": 0,
  "folate_100g": 0,
  "biotin_100g": 0,
  "cholesterol_100g": 0,
  "caffeine_100g": 0,
  "water_100g": 0,
  "creatine_100g": 0
}
```

The actual schema can contain even more fields than shown here.

The idea is that the JSON can be broader than what Apple Health currently supports.

This leaves room for future features without having to redesign all existing food files.

For example, fields can also be used for:

- Omega-3
- Omega-6
- Choline
- Fluoride
- Retinol
- Beta-carotene
- Different vitamin forms
- Electrolyte-related values
- Creatine
- Amino acids
- Other supplement-related values

Most nutrient keys follow:

`nutrient_100g`

For example:

```json
{
  "fat_100g": 6.7,
  "fiber_100g": 11,
  "proteins_100g": 14
}
```

Energy uses:

```json
{
  "energy-kcal_100g": 361
}
```

The important thing is not that every possible nutrient has to be available.

A food can contain only the values that are actually known.

---

## 8. Local Food Database (backend)

Every saved food becomes part of your personal food database, which is stored in your own files.

The backend folder is:

`Nutrients Database`

The first time you add a food, you have to:

- scan it,
- manually enter it,
- or analyze it.

Afterwards, Nutrition Tracker can reuse the stored JSON.

### Example

```text
First time:
Barcode
   ↓
Open Food Facts
   ↓
Normalize
   ↓
Save Oatmeal.json
   ↓
Track
```

Later:

```text
Saved JSON
   ↓
Oatmeal.json
   ↓
Serving Size
   ↓
Track
```

This reduces repeated API requests and makes frequently eaten foods significantly faster to log.

Pro tip: You can select multiple foods now and they get processed one after another.

---

## 9. Serving Size

Nutrition profiles are normally stored relative to 100 g.

After the food has been loaded, Nutrition Tracker asks for the amount actually consumed.

Example:

```text
How many grams did you eat?
250 g
```

Nutrition Tracker calculates a serving factor:

`Serving Factor = Serving Size ÷ 100`

For 250 g:

`250 ÷ 100 = 2.5`

That factor is then applied to the available nutrition values.

### Supplements and very small serving sizes

If you have supplements that are normally consumed in very small amounts, enter their normal serving size instead of scaling the nutritional values up to 100 g.

For example, if a supplement normally has a serving size of 5 g, enter the nutritional values for that 5 g serving as they are provided on the product.

However, when you want to track that supplement, enter 100 g as the portion size.

This makes the calculation use the saved serving values exactly once instead of scaling them down again.

So for a 5 g serving:

```text
Saved nutrition values
       ↓
5 g serving
       ↓
Track with 100 g
       ↓
100 ÷ 100 = 1
       ↓
Original values are added
```

This is mainly useful for supplements where using a 100 g reference would result in completely unrealistic numbers.

---

## 10. How to Calculate the Nutritions

The general formula is:

`Actual Nutrient = Nutrient per 100 g × (Serving Size ÷ 100)`

So suppose a food contains:

`Protein = 14 g / 100 g`

and the serving is:

`250 g`

The Nutrition Tracker then calculates:

`14 × (250 ÷ 100) = 35 g`

It then repeats that step for every given nutrient for one food.

After that, the Shortcut has to match every calculated nutrient with the corresponding Apple Health nutrient.

This is where the code gets a bit ugly.

The Shortcut performs a rather long if chain to match each value with the corresponding Apple Health sample type.

Unfortunately, this is not really avoidable here because the Health action does not allow the sample type to be supplied as a normal variable.

So instead of being able to do something like:

`logHealth(nutrientTypeVariable)`

the Shortcut has to explicitly specify:

- Dietary Energy
- Dietary Fat Total
- Dietary Protein
- Dietary Calcium
- ...

for every supported nutrient.

After running through all selected foods, the Shortcut will then ask if you want to repeat the process in case you forgot something.

---

## 11. Apple Health

After the serving values have been calculated, compatible nutrients are passed to Apple Health.

Nutrition Tracker uses Apple Shortcuts' Health logging functionality to create individual Health samples.

Each nutrient is handled separately.

For example:

```text
Calculated Nutrition JSON
          │
          ├── Energy ──────► Apple Health
          ├── Protein ─────► Apple Health
          ├── Fiber ───────► Apple Health
          ├── Calcium ─────► Apple Health
          ├── Magnesium ───► Apple Health
          └── ...
```

The Shortcut currently writes supported values using the appropriate Apple Health quantity unit:

- g
- mg
- kcal
- mL

The actual precision is then handled by Apple's quantity system, so values can be stored at the precision supported by Apple Health.

### Why there are so many individual Health actions

This is one of the more annoying limitations of the current Shortcuts/Health implementation.

The Health action needs a specific sample type such as:

- Dietary Protein
- Dietary Calcium

The sample type cannot simply be replaced by a normal variable.

That's why the Shortcut contains a separate block for each supported nutrient.

It looks repetitive, but it is intentional.

---

## 12. Nutrients

Nutrition Tracker's JSON data structure can contain a large number of nutritional values.

### Macronutrients

- Energy
- Fat
- Saturated fat
- Unsaturated fat
- Carbohydrates
- Sugars
- Fiber
- Protein

### Minerals and electrolytes

- Sodium
- Potassium
- Calcium
- Magnesium
- Iron
- Zinc
- Copper
- Manganese
- Phosphorus
- Iodine
- Chloride
- Chromium
- Selenium
- Molybdenum

### Vitamins

- Vitamin A
- Vitamin B6
- Vitamin B12
- Vitamin C
- Vitamin D
- Vitamin E
- Vitamin K
- Thiamine
- Riboflavin
- Niacin
- Pantothenic acid
- Folate
- Biotin

### Other values

- Salt
- Cholesterol
- Caffeine
- Water
- Creatine
- Amino acids
- Other values that can be added to the JSON format

Some of these values are currently only stored in the JSON data and are not sent to Apple Health.

That is intentional.

The JSON format is designed to be broader than the current Apple Health implementation so that new features can be added later without having to rebuild the food database.

---

## 13. Nutrients that Apple Health does not directly support

Not every nutrient in the JSON has a direct equivalent in Apple Health.

For example:

`"unsaturated-fat_100g": 5`

does not have a single Apple Health category called "Unsaturated Fat".

Apple Health separates different types of fat instead.

Because of that, Nutrition Tracker does not simply guess where the total unsaturated fat should go.

The same idea applies to salt.

The JSON can contain:

`"salt_100g": 1.2`

while Apple Health uses dietary sodium as its corresponding supported quantity.

So the Shortcut keeps both concepts separate:

```text
Salt
   ↓
stored in JSON
Sodium
   ↓
Apple Health Dietary Sodium
```

This prevents the Shortcut from pretending that two different measurements are the same thing.

Water is another special case.

The food JSON stores water as grams:

`water_100g`

When logging it to Apple Health, the Shortcut uses the corresponding volume quantity in mL because Apple Health's dietary water quantity is volume-based.

---

## 14. Why JSON?

JSON works particularly well for this Shortcut.

### Offline use

Once a food is saved, it can be loaded without another API request or AI request.

### Easy to extend

New nutrients can simply be added to the dictionary.

```json
{
  "creatine_100g": 0,
  "leucine_100g": 0
}
```

There is no need to redesign the entire food database.

### Compatibility

JSON can easily be read or generated by:

- Apple Shortcuts
- Cherri
- JavaScript
- Python
- APIs
- AI models
- other applications

### Reusability

A food only has to be entered once.

Afterwards, the same JSON file can be reused with different serving sizes.

---

## 15. Internal Workflow

The whole Shortcut can basically be reduced to these five parts:

1. INPUT
   ↓
2. NORMALIZATION
   ↓
3. STORAGE
   ↓
4. PORTION CALCULATION
   ↓
5. APPLE HEALTH

### Input

Loads the raw data through:

- Barcode
- Manual Input
- AI
- Saved JSON

### Normalization

Converts the source into the standardized Nutrition Tracker dictionary.

### Storage

New foods can be saved as .json files inside the `Nutrients Database` folder.

### Portion Calculation

Calculates:

`value_100g × serving_size / 100`

for every available nutrient.

### Apple Health

Writes the supported results as individual Health samples.

---

## 16. Complete Example

Let's say I scan a package of oatmeal.

### Step 1 — Scan Barcode

```text
Scan Barcode
     ↓
Open Food Facts
     ↓
Oatmeal
```

The returned information is converted into something like:

```json
{
  "food_name": "Oatmeal",
  "energy-kcal_100g": 370,
  "fat_100g": 7,
  "carbohydrates_100g": 59,
  "fiber_100g": 10,
  "proteins_100g": 13
}
```

### Step 2 — Save

The food can then be stored as:

`Nutrients Database/Oatmeal.json`

### Step 3 — Serving Size

I enter:

`150 g`

The Shortcut calculates:

`150 ÷ 100 = 1.5`

### Step 4 — Calculate

```text
Energy:
370 × 1.5 = 555 kcal
Fat:
7 × 1.5 = 10.5 g
Carbohydrates:
59 × 1.5 = 88.5 g
Fiber:
10 × 1.5 = 15 g
Protein:
13 × 1.5 = 19.5 g
```

### Step 5 — Apple Health

The supported values are then added to Apple Health.

The next time I eat oatmeal, I don't need to scan anything again:

```text
Saved JSON
     ↓
Oatmeal.json
     ↓
150 g
     ↓
Calculate
     ↓
Apple Health
```

No internet connection is required for the saved-food part.

---

## 17. Design Principles

The Nutrition Tracker is deliberately not just a barcode app.

The JSON format is the central interface between different data sources and Apple Health.

This means future input methods can be added without having to completely rebuild the rest of the Shortcut.

For example:

```text
Nutrition Label OCR
        ↓
       JSON
        ↓
Nutrition Tracker
```

or:

```text
Recipe Calculator
        ↓
       JSON
        ↓
Nutrition Tracker
```

or:

```text
Restaurant Database
        ↓
       JSON
        ↓
Nutrition Tracker
```

or:

```text
Local AI Model
        ↓
       JSON
        ↓
Nutrition Tracker
```

or:

```text
Custom API
        ↓
       JSON
        ↓
Nutrition Tracker
```

As long as the source can produce compatible Nutrition Tracker JSON, the rest of the workflow can stay the same.

That's the main reason I built it this way.

---

## Update System

Nutrition Tracker uses an update system based on UpdateKit by Mike Beasley.

The basic idea is that the Shortcut can check whether a newer version is available instead of requiring users to manually keep checking the RoutineHub page.

UpdateKit handles the version comparison and can provide information about the available update.

The current UpdateKit API is designed so that shortcut makers can either use a separate updater or integrate the update check directly into their own Shortcut. (Mike Beasley)

This is especially useful for a Shortcut like Nutrition Tracker because it is still actively being developed and new Apple Shortcuts / Health features can require changes to the Shortcut.

Apple still requires the user to confirm the installation of a Shortcut update. The update system can make finding the update easier, but it cannot silently replace the Shortcut. (Mike Beasley)

More information about UpdateKit:

https://www.mikebeas.com/updatekit-api

---

## Related Shortcuts

Nutrition Tracker is mainly meant to provide the nutrition data.

I also made a couple of other Shortcuts that work nicely with the data stored in Apple Health.

### Net Calorie Balance

https://routinehub.co/shortcut/24903/

This Shortcut can be used to calculate your net calorie balance based on the nutrition information tracked in Apple Health.

So instead of only tracking what you ate, it can be used to look at the relationship between:

```text
Calories consumed
        +
Calories burned
        ↓
Net calorie balance
```

If you're using Nutrition Tracker for regular nutrition tracking, this is probably the most useful companion Shortcut.

### Remaining Caffeine

https://routinehub.co/shortcut/26329/

The other Shortcut is a bit more specific.

It visualizes how much of your tracked caffeine is estimated to remain in your body over the following hours.

The result can be displayed visually using the Charty app, so instead of just having a list of caffeine entries you can see the estimated decline over time.

```text
Caffeine consumed
       ↓
Apple Health
       ↓
Caffeine tracking
       ↓
Estimated remaining caffeine
       ↓
Charty visualization
```

This is useful if you want to see how individual caffeine intake can affect the estimated amount remaining later in the day.

---

## Future Ideas

There are still quite a few things that could be added.

Some possible future input methods:

- 📷 Nutrition Label OCR
- 🍳 Recipe Calculator
- 🏪 Restaurant Database
- 🤖 Local AI
- 🌐 Other AI services
- 🔌 Custom APIs
- 🧬 More amino acids
- 💊 More supplement-specific nutrients
- 📊 More Apple Health integrations

The JSON-based structure should make these easier to add without changing the basic workflow.

---

## A few things to keep in mind

### Open Food Facts

Open Food Facts is useful, but its data is community-supplied and can be incomplete or incorrect.

If a product label clearly provides different values, check the label before relying on the database entry.

### AI

AI nutrition estimates are estimates.

They can be useful for meals where no structured data exists, but they should not be treated as laboratory measurements.

### Apple Health

Only nutrients with a corresponding Apple Health quantity are currently written to Apple Health.

The JSON database intentionally contains more information than Apple Health currently supports.

---

## Thanks

Thanks for using the shortcut and hopefully it makes nutrition tracking a little less annoying.

If you find a bug or have an idea for a new feature, feel free to leave a comment on the RoutineHub page or contact me through one of my pinned networks.

If you build something cool around the Nutrition Tracker JSON format, I'd also be interested in seeing it.
