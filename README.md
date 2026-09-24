Nutrition Tracker

Your nutrition tracker built directly into Apple Shortcuts and Apple Health.

Nutrition Tracker is an Apple Shortcut designed to make detailed nutrition tracking in Apple Health faster, reusable, and independent of a single data source.

Food can currently be added using:

* 📷 Barcode Scan
* ✍️ Manual Input
* 🤖 AI
* 💾 Saved JSON files
* 🔗 JSON passed from another Shortcut

No matter where the nutritional information comes from, Nutrition Tracker converts it into the same internal JSON structure.

From that point onward, every input method uses the same workflow:

Normalize → Save → Select serving size → Calculate nutrients → Log to Apple Health

This standardized JSON format is the core of Nutrition Tracker.

⸻

Table of Contents

* How Nutrition Tracker Works
* Architecture
* 1. Choosing an Input Method
* 2. Barcode Scan
* 3. Manual Input
* 4. AI Input
* 5. Shortcut Input
* 6. Saved JSON
* 7. Data Normalization
* 8. Nutrition JSON Format
* 9. Local Food Database
* 10. Serving Size
* 11. Nutrient Calculation
* 12. Apple Health
* 13. Nutrients
* 14. Nutrients Without a Direct Apple Health Mapping
* 15. Why JSON?
* 16. Offline Usage
* 17. Privacy
* 18. Installation
* 19. Permissions
* 20. Screenshots
* 21. Example Workflow
* 22. Using Another AI Service
* 23. Extending Nutrition Tracker
* 24. Troubleshooting
* 25. Limitations
* 26. Feedback
* 27. Credits
* 28. Disclaimer

⸻

How Nutrition Tracker Works

Nutrition Tracker is built around one important principle:

Every input method produces the same nutrition JSON. Everything after that is shared.

Instead of creating a completely different Apple Health workflow for barcode scanning, AI, manual input, and saved foods, all sources are first converted into a standardized nutrition dictionary.

That dictionary is then used by the rest of the Shortcut.

flowchart TD
    A[Nutrition Tracker] --> B{Choose Input Method}
    B --> C[Barcode Scan]
    B --> D[Manual Input]
    B --> E[AI]
    B --> F[Saved JSON]
    B --> G[Shortcut Input]
    C --> H[Raw Nutrition Data]
    D --> H
    E --> H
    F --> H
    G --> H
    H --> I[Normalize Nutrient Names]
    I --> J[Standard Nutrition JSON]
    J --> K[Save Food]
    J --> L[Enter Serving Size]
    K --> L
    L --> M[Serving Size ÷ 100]
    M --> N[Calculate Every Nutrient]
    N --> O[Map Supported Nutrients]
    O --> P[Apple Health]

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

⸻

Architecture

Nutrition Tracker separates where the nutrition data comes from from what happens with the data afterwards.

             ┌─────────────────────┐
             │  Nutrition Tracker  │
             └──────────┬──────────┘
                        │
                        ▼
              Choose Input Method
                        │
       ┌────────────────┼────────────────┐
       │                │                │
       ▼                ▼                ▼
    Barcode           Manual             AI
       │                │                │
       │                │                │
       └────────┬───────┴────────┬───────┘
                │                │
                ▼                ▼
          Saved JSON       Shortcut Input
                │                │
                └────────┬───────┘
                         │
                         ▼
                Raw Nutrition Data
                         │
                         ▼
                  NORMALIZATION
                         │
                         ▼
              Standard Nutrition JSON
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
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

Because the later stages do not care where the data originally came from, additional input methods can be added without rebuilding the entire Shortcut.

⸻

1. Choosing an Input Method

When Nutrition Tracker starts, you choose how the food should be loaded.

The main options are:

Input	Purpose
Barcode Scan	Retrieve packaged food from Open Food Facts
Manual Input	Enter nutrition information yourself
AI	Estimate or extract nutrition information using AI
Saved JSON	Load a food that was previously saved
Shortcut Input	Receive compatible JSON from another Shortcut

All of these methods eventually produce the same standardized nutrition dictionary.

<!-- SCREENSHOT 01: Main input menu -->
<!-- ![Nutrition Tracker Main Menu](docs/images/01-main-menu.png) -->

⸻

2. Barcode Scan

Barcode scanning is intended primarily for packaged food.

Nutrition Tracker uses the device camera to scan the barcode.

The barcode is then sent to Open Food Facts to retrieve product information.

The Shortcut requests information such as:

product_name
nutriments

The nutriments dictionary can contain values such as:

* Energy
* Fat
* Saturated fat
* Carbohydrates
* Sugars
* Fiber
* Protein
* Salt
* Sodium
* Potassium
* Calcium
* Magnesium
* Iron
* Zinc
* Vitamins
* and many other available nutrients

Not every Open Food Facts product contains every nutrient.

Nutrition Tracker therefore processes whatever information is available.

<!-- SCREENSHOT 02: Barcode scanner -->
<!-- ![Barcode Scanner](docs/images/02-barcode-scanner.png) -->

⸻

Barcode Workflow

Scan Barcode
      ↓
Read Barcode Number
      ↓
Open Food Facts
      ↓
product_name + nutriments
      ↓
Normalize Nutrient Names
      ↓
Nutrition Tracker JSON
      ↓
Save / Continue

For example, the API data might eventually be converted into:

{
  "food_name": "Example Food",
  "energy-kcal_100g": 361,
  "fat_100g": 6.7,
  "carbohydrates_100g": 56,
  "sugars_100g": 1.2,
  "fiber_100g": 11,
  "proteins_100g": 14
}

These values describe the food per 100 g.

The actual amount eaten is calculated later.

⸻

3. Manual Input

Nutrition information can also be entered manually.

This is useful when:

* a product is missing from Open Food Facts,
* Open Food Facts contains incomplete information,
* the database entry is incorrect,
* the food has no barcode,
* you have a nutrition label available,
* you created the food yourself,
* or you simply want full control over the values.

<!-- SCREENSHOT 03: Manual Input -->
<!-- ![Manual Nutrition Input](docs/images/03-manual-input.png) -->

For example:

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

Missing nutrients do not prevent the food from being used.

Values that are unavailable can either be omitted during processing or represented as 0, depending on the specific part of the Shortcut.

⸻

4. AI Input

The AI mode is intended for food where reliable structured nutrition data is not immediately available.

Examples include:

* Restaurant meals
* Homemade meals
* Mixed meals
* Food shown in a photo
* Products without complete nutrition labels
* Unpackaged food

<!-- SCREENSHOT 04: AI Input -->
<!-- ![AI Nutrition Analysis](docs/images/04-ai-analysis.png) -->

The AI is instructed to return structured JSON rather than normal conversational text.

For example:

{
  "food_name": "Vegetable Pasta",
  "estimated_weight_in-gr": 450,
  "energy-kcal_100g": 142,
  "fat_100g": 4.2,
  "saturated-fat_100g": 1.1,
  "unsaturated-fat_100g": 3.1,
  "carbohydrates_100g": 20.5,
  "sugars_100g": 3.2,
  "fiber_100g": 2.8,
  "proteins_100g": 5.1
}

Because the response follows the same format used by the rest of Nutrition Tracker, it can immediately enter the normal processing workflow.

The AI does not need to know anything about Apple Health.

It only has to produce compatible nutrition JSON.

Nutrition Tracker handles everything after that.

⸻

AI Photo Analysis

A possible workflow is:

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

When nutrition information is clearly visible on packaging, those visible values should be preferred over AI estimation whenever possible.

AI-generated values should always be treated as estimates when no verified data is available.

⸻

5. Shortcut Input

Nutrition Tracker can also act as a central nutrition-processing Shortcut for other Shortcuts.

Instead of every Shortcut implementing its own:

* nutrition calculations,
* serving-size calculations,
* JSON handling,
* nutrient mapping,
* and Apple Health logging,

another Shortcut can generate Nutrition Tracker-compatible JSON and pass it to Nutrition Tracker.

For example:

Custom Food Shortcut
        ↓
Take Photo
        ↓
Run AI
        ↓
Create Nutrition JSON
        ↓
Run Nutrition Tracker
        ↓
Calculate Serving
        ↓
Apple Health

This allows Nutrition Tracker to act as a reusable backend.

Possible external workflows include:

Nutrition Label OCR
        ↓
Nutrition Tracker
Recipe Calculator
        ↓
Nutrition Tracker
Local AI Model
        ↓
Nutrition Tracker
Custom API
        ↓
Nutrition Tracker

As long as the data can be converted to the expected JSON structure, the remaining Nutrition Tracker workflow can stay the same.

⸻

6. Saved JSON

Foods do not need to be downloaded, generated, or entered every time.

Once a food has been processed, its nutrition profile can be stored as a .json file.

For example:

Nutrition Tracker/
│
├── Banana.json
├── Oatmeal.json
├── Greek Yogurt.json
├── Protein Powder.json
├── Milk.json
└── Pasta.json

A saved file might contain:

{
  "food_name": "Banana",
  "energy-kcal_100g": 89,
  "fat_100g": 0.3,
  "carbohydrates_100g": 22.8,
  "sugars_100g": 12.2,
  "fiber_100g": 2.6,
  "proteins_100g": 1.1
}

When that food is selected later, Nutrition Tracker can skip:

* Barcode scanning
* Open Food Facts
* AI analysis
* Manual entry

and move directly to the serving-size calculation.

<!-- SCREENSHOT 05: Saved foods -->
<!-- ![Saved Foods](docs/images/05-saved-foods.png) -->

⸻

7. Data Normalization

One of the most important parts of Nutrition Tracker is nutrient normalization.

Different databases, APIs and AI models do not always use the same property names.

For example, protein may appear as:

protein_100g

or:

proteins_100g

Fiber may appear as:

fiber_100g
fibre_100g
dietary-fiber_100g

Vitamin B1 might appear as:

vitamin-b1_100g
thiamine_100g

Instead of requiring every source to use exactly the same name, Nutrition Tracker can recognize multiple aliases and map them to the nutrient used internally.

Conceptually:

protein_100g ──────┐
                   ├──► Protein
proteins_100g ─────┘

and:

fiber_100g ─────────┐
fibre_100g ─────────┼──► Fiber
dietary-fiber_100g ─┘

This makes Nutrition Tracker more compatible with:

* Open Food Facts
* AI-generated JSON
* Manually generated JSON
* External Shortcuts
* Future APIs

⸻

8. Nutrition JSON Format

The JSON dictionary is the central interface of Nutrition Tracker.

A larger food object may look like this:

{
  "food_name": "",
  "estimated_weight_in-gr": 0,
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
  "creatine_100g": 0,
  "valine_100g": 0,
  "isoleucine_100g": 0,
  "leucine_100g": 0
}

The complete internal structure can contain additional fields and aliases beyond this example.

These can include values such as:

* Omega-3
* Omega-6
* Choline
* Fluoride
* Retinol
* Beta-carotene
* Different vitamin forms
* Electrolyte-related values
* Creatine
* Amino acids
* Additional supplement-related fields

The schema is intentionally broader than the values currently written to Apple Health.

⸻

Naming Convention

Most nutrient keys follow:

nutrient_100g

For example:

{
  "fat_100g": 6.7,
  "fiber_100g": 11,
  "proteins_100g": 14
}

Energy uses:

{
  "energy-kcal_100g": 361
}

Metadata does not necessarily use the _100g suffix:

{
  "food_name": "Oatmeal",
  "estimated_weight_in-gr": 250
}

⸻

9. Local Food Database

Every saved food becomes part of a personal food database.

The first time you add a food, you may need to:

* scan it,
* enter it,
* or analyze it.

Afterwards, Nutrition Tracker can reuse the stored JSON.

Example:

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

Later:

Saved JSON
   ↓
Oatmeal.json
   ↓
Serving Size
   ↓
Track

This reduces repeated API requests and makes frequently eaten foods significantly faster to log.

⸻

10. Serving Size

Nutrition profiles are stored relative to 100 g.

After the food has been loaded, Nutrition Tracker asks for the amount actually consumed.

Example:

How many grams did you eat?
250 g

Nutrition Tracker calculates a serving factor:

Serving Factor = Serving Size ÷ 100

For 250 g:

250 ÷ 100 = 2.5

That factor is then applied to the available nutrition values.

<!-- SCREENSHOT 06: Serving size -->
<!-- ![Serving Size](docs/images/06-serving-size.png) -->

⸻

11. Nutrient Calculation

Suppose a food contains:

Protein = 14 g / 100 g

and the serving is:

250 g

Nutrition Tracker calculates:

14 × 2.5 = 35 g

The general formula is:

Actual Nutrient =
Nutrient per 100 g × Serving Size ÷ 100

For this example:

Energy:
361 × 2.5
= 902.5 kcal
Fat:
6.7 × 2.5
= 16.75 g
Carbohydrates:
56 × 2.5
= 140 g
Sugars:
1.2 × 2.5
= 3 g
Fiber:
11 × 2.5
= 27.5 g
Protein:
14 × 2.5
= 35 g

The same stored food can therefore be used for any serving size.

⸻

12. Apple Health

After the serving values have been calculated, compatible nutrients are passed to Apple Health.

Nutrition Tracker uses Apple Shortcuts’ Health logging functionality to create individual Health samples.

Each nutrient is handled separately.

For example:

Calculated Nutrition JSON
          │
          ├── Energy ──────► Apple Health
          ├── Protein ─────► Apple Health
          ├── Fiber ───────► Apple Health
          ├── Calcium ─────► Apple Health
          ├── Magnesium ───► Apple Health
          └── ...
<!-- SCREENSHOT 07: Apple Health result -->
<!-- ![Apple Health Nutrition Data](docs/images/07-apple-health.png) -->

This also makes the data available to compatible apps that read nutrition information from Apple Health, subject to the permissions the user has granted those apps.

⸻

13. Nutrients

Nutrition Tracker’s data structure can contain a large number of nutritional values.

Macronutrients

* Energy
* Fat
* Saturated fat
* Unsaturated fat
* Carbohydrates
* Sugars
* Fiber
* Protein

Fat-related values

* Saturated fat
* Unsaturated fat
* Omega-3
* Omega-6
* Cholesterol

Minerals and Electrolytes

* Salt
* Sodium
* Potassium
* Calcium
* Magnesium
* Iron
* Zinc
* Copper
* Manganese
* Phosphorus
* Iodine
* Chloride
* Chromium
* Selenium
* Molybdenum
* Fluoride
* Electrolytes

Vitamins

* Vitamin A
* Vitamin B1 / Thiamine
* Vitamin B2 / Riboflavin
* Vitamin B3 / Niacin
* Vitamin B5 / Pantothenic acid
* Vitamin B6
* Vitamin B7 / Biotin
* Vitamin B9 / Folate
* Vitamin B12
* Vitamin C
* Vitamin D
* Vitamin E
* Vitamin K

Additional vitamin-related values can include:

* Retinol
* Beta-carotene
* Folic acid
* Individual vitamin D forms
* Individual vitamin K forms

Other Nutritional Values

* Water
* Caffeine
* Choline

Amino Acids and Sports Nutrition

* Creatine
* Leucine
* Isoleucine
* Valine

Additional Stored Values

The format can also contain additional values used for custom tracking or future functionality, such as supplement-related values.

⸻

Apple Health Compatible Nutrition Types

Apple Health provides dedicated nutrition categories for many of the values used by Nutrition Tracker, including:

* Energy
* Total fat
* Saturated fat
* Monounsaturated fat
* Polyunsaturated fat
* Carbohydrates
* Sugar
* Fiber
* Protein
* Cholesterol
* Sodium
* Potassium
* Calcium
* Magnesium
* Iron
* Zinc
* Copper
* Manganese
* Phosphorus
* Iodine
* Chloride
* Chromium
* Selenium
* Molybdenum
* Vitamin A
* Thiamine
* Riboflavin
* Niacin
* Pantothenic acid
* Vitamin B6
* Biotin
* Vitamin B12
* Vitamin C
* Vitamin D
* Vitamin E
* Vitamin K
* Folate
* Water
* Caffeine

Which values Nutrition Tracker actually writes depends on the data available and the mappings implemented in the current Shortcut version.

⸻

14. Nutrients Without a Direct Apple Health Mapping

Nutrition Tracker intentionally stores more information than Apple Health can directly represent.

This prevents potentially useful data from being discarded.

⸻

Unsaturated Fat

Nutrition Tracker may receive:

{
  "unsaturated-fat_100g": 3.1
}

Apple Health does not use one generic Unsaturated Fat category.

Instead, it provides separate categories for:

Monounsaturated Fat
Polyunsaturated Fat

If a data source only provides total unsaturated fat, Nutrition Tracker should not simply guess how much belongs to each category.

The original value can therefore remain in the JSON without being incorrectly split.

⸻

Salt vs Sodium

Food labels commonly provide:

{
  "salt_100g": 1.2
}

while Apple Health provides a dedicated dietary Sodium category.

Nutrition Tracker can therefore keep:

salt_100g

for the original food information while using:

sodium_100g

when a suitable sodium value is available for Apple Health.

These values should not be treated as identical.

⸻

Creatine

{
  "creatine_100g": 0
}

can be stored in Nutrition Tracker even though Apple Health does not currently provide a normal dietary creatine category.

⸻

Amino Acids

Values such as:

{
  "leucine_100g": 0,
  "isoleucine_100g": 0,
  "valine_100g": 0
}

can remain available for future features even if they are not written to Apple Health.

⸻

Electrolytes

A combined value such as:

Electrolytes

does not correspond directly to a single Apple Health nutrition category.

Individual electrolytes can instead be represented separately where supported:

Sodium
Potassium
Chloride
Magnesium
Calcium

⸻

Additional Compounds

Other stored information may not have a direct Apple Health nutrition type.

This does not mean the data is useless.

It can later be used for:

* Custom reports
* Nutrition dashboards
* Supplement tracking
* Training nutrition
* AI analysis
* Daily summaries
* New Shortcut features
* Future Apple Health integrations

⸻

15. Why JSON?

JSON is used because it provides several important advantages.

One Common Interface

Every input method eventually creates the same type of object.

Barcode ─────┐
Manual ──────┤
AI ──────────┼──► JSON ───► Nutrition Tracker
Saved Food ──┤
Other App ───┘

⸻

Offline Usage

Saved JSON files do not require a new API or AI request.

⸻

Expandability

A new nutrient can be added without rebuilding the complete data model.

For example:

{
  "creatine_100g": 0
}

or:

{
  "leucine_100g": 0
}

⸻

Compatibility

JSON can easily be produced and processed by:

* Apple Shortcuts
* Cherri
* JavaScript
* Python
* APIs
* AI models
* Servers
* Other automation systems

⸻

Reusability

A food only needs to be defined once.

After that, the same file can be used for:

50 g
100 g
150 g
250 g
500 g

without changing the original nutritional profile.

⸻

16. Offline Usage

One of the main advantages of local food storage is that already saved foods can continue to work without an internet connection.

Internet available
        │
        ├── Barcode
        ├── AI
        └── Manual Input
                │
                ▼
            Save JSON
                │
                ▼
        Local Food Database
                │
                ▼
Internet unavailable
                │
                ▼
          Load Saved JSON
                │
                ▼
          Serving Size
                │
                ▼
          Apple Health

An internet connection is therefore only required for features that actually depend on an online service.

⸻

17. Privacy

Nutrition Tracker itself is built using Apple Shortcuts, but privacy depends on which input method is used.

Local Operations

Operations such as:

* Reading saved JSON
* Selecting food
* Calculating serving sizes
* Normalizing locally available data
* Calculating nutrient values

can be performed locally.

⸻

Barcode Lookup

Barcode mode sends the product barcode to Open Food Facts so that product information can be retrieved.

⸻

AI

Using AI may send:

* Food descriptions
* Nutrition text
* Images
* Other provided input

to the selected AI provider.

The privacy policy of that provider applies.

AI is optional.

⸻

Apple Health

Nutrition Tracker requires permission before writing Health data.

Health permissions remain under the user’s control.

⸻

18. Installation

Requirements

Nutrition Tracker requires:

* Apple Shortcuts
* Apple Health
* A compatible iPhone or iPad for the required actions
* Permission to access the required Health categories

Internet access is additionally needed for certain features such as:

* Open Food Facts
* Online AI services

Saved foods can be reused without those online requests.

⸻

Install

[ ADD SHORTCUT DOWNLOAD LINK HERE ]

Then:

1. Open the Nutrition Tracker link.
2. Add the Shortcut.
3. Run Nutrition Tracker.
4. Allow the required permissions.
5. Choose an input method.
6. Add or load a food.
7. Enter the amount consumed.
8. Allow Nutrition Tracker to save the supported values to Apple Health.

<!-- SCREENSHOT 08: Installation -->
<!-- ![Nutrition Tracker Installation](docs/images/08-installation.png) -->

⸻

19. Permissions

Depending on the features used, Nutrition Tracker may request access to:

Permission	Used For
Camera	Barcode scanning / food photos
Files	Saving and loading JSON foods
Apple Health	Logging nutrition information
Internet	APIs and online AI
AI service	AI nutrition analysis

You remain in control of which permissions are granted.

⸻

20. Screenshots

A recommended image folder is:

docs/
└── images/
    ├── 01-main-menu.png
    ├── 02-barcode-scanner.png
    ├── 03-manual-input.png
    ├── 04-ai-analysis.png
    ├── 05-saved-foods.png
    ├── 06-serving-size.png
    ├── 07-apple-health.png
    ├── 08-installation.png
    └── 09-example-food.png

The README already contains placeholders for these images.

Once an image has been added, remove the surrounding HTML comment.

For example:

<!-- ![Nutrition Tracker Main Menu](docs/images/01-main-menu.png) -->

becomes:

![Nutrition Tracker Main Menu](docs/images/01-main-menu.png)

This prevents broken images from being displayed before the screenshots are uploaded.

⸻

21. Example Workflow

Suppose you scan a package of oatmeal.

Scan Barcode
      ↓
Open Food Facts
      ↓
Product found:
Oatmeal

Nutrition Tracker obtains:

{
  "food_name": "Oatmeal",
  "energy-kcal_100g": 370,
  "fat_100g": 7,
  "carbohydrates_100g": 59,
  "fiber_100g": 10,
  "proteins_100g": 13
}

The data is normalized.

The food can then be saved as:

Nutrition Tracker/Oatmeal.json

You enter:

Serving Size: 150 g

Nutrition Tracker calculates:

Serving Factor:
150 ÷ 100 = 1.5

The resulting nutrition values are:

Energy:
370 × 1.5
= 555 kcal
Fat:
7 × 1.5
= 10.5 g
Carbohydrates:
59 × 1.5
= 88.5 g
Fiber:
10 × 1.5
= 15 g
Protein:
13 × 1.5
= 19.5 g

Supported values are then logged to Apple Health.

The next time you eat the same oatmeal:

Saved JSON
      ↓
Oatmeal.json
      ↓
Enter Serving Size
      ↓
Calculate
      ↓
Apple Health

No new barcode or internet request is required.

⸻

22. Using Another AI Service

ChatGPT is optional.

It is only used for AI-based nutrition analysis.

The rest of Nutrition Tracker does not depend on ChatGPT.

You can replace the AI section with another provider as long as the provider eventually returns compatible JSON.

Conceptually:

Nutrition Tracker
        ↓
AI Request
        ↓
Your AI Provider
        ↓
Nutrition JSON
        ↓
Normalization
        ↓
Nutrition Tracker

The important part is the output format, not which model generated it.

Possible AI sources include:

* ChatGPT
* Another online AI provider
* A local AI model
* A custom server
* A separate AI Shortcut
just replace the ChatGPT action inside of the shortcut and make sure all the previously connected variables are reconnected 
⸻

23. Extending Nutrition Tracker

Because JSON acts as the interface between input and processing, additional data sources can be added relatively easily.

Future input methods could include:

Nutrition Label OCR
        ↓
JSON
Recipe Calculator
        ↓
JSON
Restaurant Database
        ↓
JSON
Local AI
        ↓
JSON
Online AI
        ↓
JSON
Custom API
        ↓
JSON
NFC Food Tags
        
        ↓
JSON

Everything after the standardized JSON stage can remain shared.

⸻

Possible Future Features

The architecture could also support features such as:

* Recent foods
* Favorite foods
* Serving presets
* Recipes
* Multiple ingredients
* Meal templates
* Daily nutrition summaries
* Weekly nutrition reports
* Macro goals
* Micronutrient goals
* Electrolyte tracking
* Amino-acid tracking
* Creatine tracking
* Supplement tracking
* Custom nutrition dashboards
* Nutrition history exports
* Improved barcode databases
* Better AI analysis
* Nutrition-label OCR
* Custom AI providers
* Local AI
* Additional Health mappings

⸻

24. Troubleshooting

Food is not found by barcode

Try:

Manual Input

or:

AI

Afterwards, save the food as JSON so it does not need to be searched again.

⸻

Open Food Facts is missing nutrients

Open Food Facts entries depend on the information submitted for each product.

Some foods may have:

* only macros,
* incomplete micronutrients,
* outdated information,
* or incorrect information.

If you have the original product label, manual entry may be more accurate.

⸻

AI returns incorrect nutrition values based on text or photo input

AI nutrition values are estimates.

Provide more detail.

Instead of:

Pasta

use:

250 g cooked spaghetti
100 g tomato sauce
20 g Parmesan

Whenever possible, prefer verified nutrition labels or reliable food database information over AI estimates.

⸻

Apple Health values are missing

Check that Shortcuts has permission to write the required nutrition categories.

Also remember that:

* not every JSON value has a direct Apple Health category,
* missing source values cannot be logged,
* and some Nutrition Tracker fields are intentionally stored only for future or custom use.

⸻

Incorrect saved food

If a saved food contains incorrect data, update or replace the JSON instead of repeatedly tracking the incorrect values.

⸻

25. Limitations

AI Is an Estimate

AI cannot reliably know the exact:

* ingredients,
* recipe,
* preparation method,
* serving weight,
* product variation,
* or nutrient composition

of every meal.

⸻

Barcode Databases Are Not Perfect

Open Food Facts data can be incomplete or incorrect.

⸻

Serving Size Is Important

Correct source nutrition data with an incorrect serving size still produces incorrect tracking data.

⸻

Nutrition Tracker Stores More Than Apple Health

Some values are intentionally preserved even when Apple Health cannot currently store them directly.

⸻

Nutrition Tracker Is Not a Medical Device

It is intended for nutrition tracking and automation, not diagnosis or medical decision-making.

⸻

26. Feedback

If you find a bug or have an idea for a new feature, feel free to:

* Open a GitHub Issue
* Leave a comment
* Submit a feature request
* Contact me through one of my pinned networks

Useful information for bug reports includes:

Nutrition Tracker version:
Device:
iOS / iPadOS version:
Input method:
Barcode / Manual / AI / Saved JSON / Shortcut Input
Expected result:
Actual result:
Screenshot:

Please avoid publishing private Apple Health information in public bug reports.

⸻

27. Credits

Nutrition Tracker was created using:

* Apple Shortcuts
* Apple Health
* Open Food Facts
* JSON
* Optional AI integration

Creator

* GitHub: Scbhv
* RoutineHub: @simon0907
* Reddit: LongjumpingTomato946
* Buy Me a Coffee: simon0907

Older RoutineHub releases may also be associated with:

* Sionic

Add your preferred profile links here when publishing the repository.

⸻

28. Disclaimer

Nutrition Tracker is provided as a nutrition tracking and automation tool.

Nutrition information obtained from:

* AI,
* Open Food Facts,
* third-party databases,
* user-entered data,

may be incomplete or inaccurate.

For packaged foods, the manufacturer’s current nutrition label should generally be preferred when accurate nutritional information is important.

Nutrition Tracker is not a medical device and is not intended to diagnose, treat, prevent, or manage medical conditions.

⸻

Summary

The entire project can be reduced to one central architecture:

Data Source
    │
    ├── Barcode
    ├── Manual
    ├── AI
    ├── Saved JSON
           │
           ▼
    Normalize Data
           │
           ▼
 Standard Nutrition JSON
           │
         Save
           │
           ▼
    Serving Size     
           │
           |
           ▼
   Calculate Nutrients
           │
           ▼
      Apple Health

Every input method produces the same nutrition JSON. Everything after that is shared.

That makes Nutrition Tracker modular, reusable, offline-capable, and easy to extend with new data sources in the future.

⸻

Thanks for using Nutrition Tracker!