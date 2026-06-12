# La Feyita — Restaurant Knowledge Base (for inbound voice AI)
*Working doc — built from Gabe's screenshots, 2026-06-11. More menu pages incoming.*

## Architecture decision: prompt vs knowledge base (Gabe + Claude, 2026-06-11)
Hybrid, split by function:
- **PROMPT gets the transactional spine** — a compact menu index (item — price — required choices, one line each, no descriptions), plus the pricing rules (+$1 to-go, +$3 cheese sauce, tax included) and the no-alcohol-to-go rule. Order-taking must be deterministic: no retrieval lookups mid-order (latency + chunk-miss risk = misquoted prices).
- **KNOWLEDGE BASE gets the rich layer** — full dish descriptions, hours, patio/parking, restaurant story, policies. RAG is fine for occasional "what's in it?" questions where a paraphrase is harmless.
- Fallback if the prompt ceiling can't hold the full index: categories + bestsellers in prompt, full menu in KB, bot confirms price aloud after each retrieval.
- This file is the master source; the prompt index and the KB doc are both generated from it.

## Restaurant info
- **Name:** Goes by **La Feyita** (Facebook page name; menu watermark logo reads "La Feita"). The building sign still says "La Bonita" — old name, history involves a divorce/family situation; do NOT have the bot reference La Bonita. ⚠️ Get exact spelling + pronunciation from the owner for the greeting (La Feyita / La Feita — "lah fey-EE-tah"?).
- **Address:** 108 Main St, Bunker, MO 63629
- **Phone:** (573) 689-1425
- **Setting:** Small-town Missouri restaurant; brick building with large outdoor patio seating (tables, umbrellas, string lights). Gravel parking lot on site.
- **Facebook:** "La Feyita" page (~388 followers), Messenger enabled
- **Hours (from website):**
  - Sunday 10:00 am – 6:00 pm
  - Monday — not listed → presumably **CLOSED** (⚠️ confirm)
  - Tuesday–Saturday 10:00 am – 10:00 pm
- **Website:** Has an online-ordering site (found 2026-06-11) with Pickup AND Delivery buttons — get the URL. Popular items per site: Chicken Chimichanga $15.65, Nachos Checo's $14.60, Arroz Special $15.65.
- **Pickup/delivery:** Online ordering offers both Pickup and Delivery (⚠️ confirm delivery radius/rules and whether PHONE orders can be delivery or pickup-only)
- **Order delivery to kitchen (SMS/email target):** ❓ not captured yet

## Menu (all prices include tax)

### Nachos
| Item | Price | Notes / choices |
|---|---|---|
| Nachos Checo's | ❓ (was $11 on old menu — confirm current) | bed of crispy tortilla chips topped with beans, chicken, beef, lettuce, tomatoes, cheese & sour cream |
| Checo's Dip | ❓ (was $8 on old menu — confirm current + still offered) | ground beef, pico de gallo & queso dip |
| Fajita Nachos | $14 | choice of **chicken or steak**, grilled vegetables, bed of crispy tortilla chips + cheese sauce |
| Fajita Nachos Bravas | $17 | fajita steak + chicken + shrimp, bell peppers, onions, topped with queso dip |
| Shrimp Fajita Nachos | $17 | shrimp fajitas, bell peppers, tomatoes, onion, crispy nachos + melted cheese |

### Salads
| Item | Price | Notes / choices |
|---|---|---|
| Taco Salad | small $6 / regular $12 | crispy flour tortilla shell; choice of **ground beef or shredded chicken**; cheese, lettuce, tomatoes, sour cream |
| Fajita Taco Salad | small $9 / regular $14 | crispy flour tortilla shell; choice of **chicken or steak** sautéed w/ onion, bell pepper, tomatoes; lettuce, cheese, sour cream |

### Tacos
| Item | Price | Notes / choices |
|---|---|---|
| Street Tacos | $12 | three tacos w/ lettuce, tomatoes, sour cream; choice of **steak or chicken**; choice of **flour or corn** tortillas |
| Street Tacos Shrimp | $14 | three shrimp tacos w/ lettuce, tomatoes, sour cream |
| Tacos Especiales | $12 | three soft corn tortillas; steak, grilled onions, cilantro, avocado, cheese |
| Taco Plate | $10 | two tacos, **hard or soft**; choice of **beef or shredded chicken**; served w/ rice & beans |
| Taco Especiales Carnitas | $13 | four flour tortillas; pork tips, grilled onions, cilantro, avocado, shredded cheese |

### Burritos
| Item | Price | Notes / choices |
|---|---|---|
| Burrito Special | $10 | one beef burrito, red sauce, lettuce, cheese, sour cream, tomatoes |
| Burrito Gonzalez | $15 | choice of **sirloin strips or chicken**, onions, bell peppers, tomatoes; cheese sauce; w/ rice & beans |
| Burrito Grande | $16 | **chicken or steak** fajitas, grilled veg, rice & beans inside; ranchero + tomatillo + cheese sauces; sour cream, lettuce, pico |
| Burrito de Carne Asada | $15 | sirloin steak, onions, bell peppers; smothered in cheese + salsa verde; w/ rice & beans |
| Burrito Checo's | $12 | choice of **ground beef or chicken chunks**; lettuce, tomatoes, sour cream, cheese; w/ rice & beans |
| Burrito De Chile Colorado | $14 | steak in chile colorado sauce, beans, melted cheese sauce; w/ rice & pico |

### Chimichangas
*Flour tortilla, deep-fried, topped w/ cheese sauce; served w/ rice, beans, lettuce, tomatoes, sour cream*
| Item | Price |
|---|---|
| Beef or Chicken | $13 |
| Fajita chicken or steak (w/ grilled vegetables) | $15 |
| Shrimp (w/ grilled vegetables) | $17 |

### Quesadillas
| Item | Price | Notes / choices |
|---|---|---|
| Fajita Quesadilla | $15 | choice of **marinated sirloin strips or chicken**; onions, bell peppers, grilled tomatoes, cheese; lettuce/tomato/sour cream on side |
| Quesadilla Fajita Brava | $17 | chicken + steak + shrimp; onions, bell peppers, grilled tomatoes, cheese |
| Shrimp Quesadilla | $17 | marinated shrimp; onions, bell peppers, grilled tomatoes, cheese |

### Fajitas
| Item | Single | Double |
|---|---|---|
| Chicken or Steak Fajitas | $17 | $32 |
| Shrimp Fajitas | $20 | $32 |
| Fajitas Bravas (chicken, steak & shrimp) | $20 | $32 |
| Papa Fajita (baked potato topped w/ fajitas & cheese dip) | Chicken/Steak $18 · Bravas/Shrimp $21 | — |

### Specials
| Item | Price | Notes / choices |
|---|---|---|
| Arroz Special | $13 | grilled **chicken or steak** on bed of rice topped with queso dip |
| Arroz Checo's | $18 | chicken + steak + shrimp w/ onions, tomatoes, bell peppers on rice, topped w/ cheese dip |
| Arroz Camerones | $18 | grilled shrimp on bed of rice topped with queso dip |
| Pollo Bandido | $15 | chicken breast topped w/ cheese sauce; w/ rice, beans & flour tortillas |
| Pollo Chipotle | $15 | chicken breast in creamy chipotle sauce; w/ rice & refried beans |
| Pollo Checo's | $18 | marinated chicken breast, grilled shrimp & chorizo topped w/ cheese dip; sour cream & lettuce; w/ rice & beans |
| Arroz Con Pollo | $15 | chicken breast w/ sautéed vegetables & melted cheese; w/ rice, lettuce, sour cream |
| Pollo Parrilla | $14 | grilled chicken breast topped w/ onions, bell peppers, tomatoes, melted cheese; w/ rice & beans |
| Checo's Chicken Salad | $12 | grilled chicken on lettuce, tomatoes, onions, bell peppers, cheese & ranch |
| Papaitas con Queso Fajita | $12 | grilled **chicken or steak** on a plate of fries topped w/ queso dip |
| Molcajete Ranchero | $20 | steak + chicken marinated w/ pasilla peppers, sautéed onions, queso fresco, served in a molcajete |
| Checo's Melt | $14 | melted cheese topped w/ chorizo, steak & chicken on an iron skillet, w/ tortillas |
| Chile Colorado | $15 | steak chunks in ranchero sauce; w/ rice, beans & tortillas |
| Carnitas | $15 | pork tips w/ lettuce, tomatoes, onions; rice, beans & tortillas |
| Chile Verde | $16 | carnitas in green sauce; w/ rice, beans & tortillas |
| Milanesa Traditional | $17 | thin **chicken or steak**, lightly breaded; w/ rice, baked potato & tortillas |
| Camarones al Mojo de Ajo | $19 | jumbo shrimp in garlic butter sauce; w/ rice, lettuce, onion, tomatoes & avocado |
| Camarones a la Diabla | $19 | tiger shrimp in spicy ranchero sauce; w/ rice, lettuce, tomatoes, onions & avocado |

### Steaks
| Item | Price | Notes |
|---|---|---|
| Carne Asada | $16 | marinated sirloin, lettuce, lime, onions, sliced avocado; w/ rice, beans & flour tortillas |
| Steak Tampiqueno | $19 | 12 oz T-Bone; garden salad, rice, beans & baked potato |
| Steak Ranchero | $19 | 12 oz T-Bone w/ ranchero sauce; rice, beans & baked potato |
| Steak Mexicano | $19 | 12 oz T-Bone smothered w/ onions, tomatoes, bell peppers; rice, beans, baked potato & tortillas |

### Kids Menu
| Item | Price | Notes |
|---|---|---|
| Quesadilla | $6 | cheese quesadilla w/ rice & beans |
| Mini Quesadilla Fajita | $9 | **chicken or steak**; w/ rice & beans |
| Mini Nacho Fajitas | $9 | **chicken or steak** on a bed of chips |
| Mini Arroz Special | $9 | **chicken or steak** |
| Kids Taco | $7 | w/ rice & beans |
| Hamburger & Fries | $7 | |
| Chicken Fingers & Fries | $9 | |

### Sides
| Item | Price |
|---|---|
| Queso Dip | $5 |
| Queso Dip Large | $12 |
| Beans | $2 |
| Rice | $2 |
| Tortillas | $2 |
| Sour Cream | $1 |
| Pico de Gallo | $1 |
| Shrimp | $9 |
| Chorizo | $2 |

### Drinks
| Item | Price |
|---|---|
| Jarritos | $3 |
| Bottled Coke | $3 |
| Fountain Drinks | $3 |

### Margaritas (dine-in — NOT for phone orders)
| Size | Regular | Flavor |
|---|---|---|
| Jumbo | $10 | $12 |
| Monster | $16 | $18 |
| Pitcher | $21 | $23 |

### Beer (dine-in — NOT for phone orders)
- **Domestic:** Bud Light, Coors Light, Michelob Ultra, Busch Light
- **Imports:** Corona, Corona Light, Dos Equis Dark, Dos Equis Lager, Modelo Especial, Modelo Negra

## 🌐 WEBSITE / ONLINE-ORDERING MENU (current as of 2026-06-11 — newest price generation)
⚠️ **THE price question:** website prices run ~20%+ above the printed menu (Street Tacos $14.60 vs $12 printed; Queso Dip small $9.35 vs $5; Fajita Nachos $16.70 vs $14). Likely an online-ordering platform markup. **Which tier does a PHONE order get — in-store printed price or online price?** This single answer determines the bot's entire price table. (Gabe has more website screenshots coming; capture every section at online prices, decide tier later.)

### Botanas (appetizers — NEW section, never on printed menus we've seen)
| Item | Online price | Notes |
|---|---|---|
| Queso Dip (Small) | $9.35 | |
| Queso Dip (Large) | $16.70 | |
| Checo's Dip | $11.45 | ground beef, pico de gallo & queso dip — CONFIRMED still offered |
| Queso Fundido | $15.65 | melted Chihuahua cheese, chorizo, bell peppers, tomatoes, onion; w/ tortillas |
| Guacamole Mexicano | $12.50 | guac exists! |
| Chicken Wings | $14.60 | eight wings, deep fried, tossed in special hot sauce |

### Nachos (online prices)
| Item | Online price |
|---|---|
| Nachos Checo's | $14.60 |
| Fajita Nachos | $16.70 |
| Fajita Nachos Bravas | $19.85 |
| Shrimp Fajita Nachos | $19.85 |

### Enchiladas (CONFIRMED current — online prices)
| Item | Online price | Notes |
|---|---|---|
| Enchiladas Suiza | $17.75 | four soft corn tortillas, **chicken or steak**, salsa tomatillo + cheese dip, lettuce, sour cream, tomatoes |
| Enchiladas Supremas | $14.60 | one beef, one chicken, one cheese, one bean; enchilada sauce + toppings |
| Enchiladas Mexicanas | $17.75 | **chicken or steak** strips, ranchera sauce, cheese sauce, salsa tomatillo + toppings |

### Tacos (online prices)
| Item | Online price | Notes |
|---|---|---|
| Street Tacos | $14.60 | three; steak or chicken; corn or flour |
| Street Tacos Shrimp | $16.70 | |
| Tacos Especiales | $14.60 | |
| Taco Plate | $12.50 | two tacos, hard or soft; beef or shredded chicken; rice & beans |
| Taco Especiales Carnitas | $15.65 | |
| Tacos Al Pastor | $15.65 | **NEW item** — four soft corn tortillas, slow-roasted pork in Chile Ancho salsa, cilantro, onions, grilled pineapple |

### Salads (online prices)
| Item | Online price | Notes |
|---|---|---|
| Taco Salad (Small) | $8.30 | ground beef or shredded chicken |
| Taco Salad (Regular) | $14.60 | ground beef or shredded chicken |
| Fajita Taco Salad (Small) | $11.45 | chicken or steak |
| Fajita Taco Salad (Large) | $16.70 | chicken or steak |

### Burritos (online prices)
| Item | Online price |
|---|---|
| Burrito Special | $12.50 |
| Burrito Gonzalez | $17.75 |
| Burrito Grande | $18.80 |
| Burrito de Carne Asada | $17.75 |
| Burrito Checo's | $14.60 |
| Burrito De Chile Colorado | $16.70 |

### Quesadillas (online prices)
| Item | Online price |
|---|---|
| Fajita Quesadilla | $17.75 |
| Quesadilla Fajita Brava | $19.85 |
| Shrimp Quesadilla | $19.85 |

### Fajitas (online prices — note: website lists each protein/size as its own item; ALL doubles are $35.60)
| Item | Single | Double |
|---|---|---|
| Chicken Fajita | $19.85 | $35.60 |
| Steak Fajita | $19.85 | $35.60 |
| Shrimp Fajitas | $23.00 | $35.60 |
| Fajitas Bravas (chicken, steak & shrimp) | $23.00 | $35.60 |
| Papa Fajita | $20.90 | — (⚠️ website shows ONE price; printed menu had chicken/steak $18 vs bravas/shrimp $21 — confirm whether protein choice changes price by phone) |

### Chimichangas (online prices — expanded to 6 variants vs printed menu's 3)
*Flour tortilla, deep-fried, topped w/ cheese sauce; served w/ rice, beans, lettuce, tomatoes & sour cream*
| Item | Online price | Notes |
|---|---|---|
| Beef Chimichanga | $15.65 | |
| Chicken Chimichanga | $15.65 | |
| Fajita Chicken Chimichanga | $17.75 | w/ grilled vegetables |
| Fajita Steak Chimichanga | $17.75 | w/ grilled vegetables |
| Fajita Bravas Chimichanga | $20.90 | chicken, steak & shrimp, w/ grilled vegetables — NEW (not on printed menu) |
| Shrimp Chimichangas | $19.85 | w/ grilled vegetables |

### Specials (online prices — website section count: 18, all captured)
| Item | Online price | Notes |
|---|---|---|
| Arroz Special | $15.65 | grilled **chicken or steak** |
| Arroz Checo's | $20.90 | chicken + steak + shrimp |
| Arroz Camarones | $20.90 | |
| Pollo Bandido | $17.75 | |
| Pollo Chipotle | $17.75 | |
| Pollo Checo's | $20.90 | |
| Arroz Con Pollo | $17.75 | |
| Pollo Parrilla | $16.70 | |
| Checo's Chicken Salad | $14.60 | |
| Papaitas con Queso Fajita | $14.60 | **chicken or steak** on fries w/ queso |
| Molcajete Ranchero | $23.00 | |
| Checo's Melt | $16.70 | |
| Chile Colorado | $17.75 | |
| Carnitas | $17.75 | |
| Chile Verde | $18.80 | |
| Milanesa Traditional | $19.85 | **chicken or steak** |
| Camarones al Mojo de Ajo | $21.95 | |
| Camarones a la Diabla | $21.95 | |

### Steaks (online prices — 5 items; website adds one new)
| Item | Online price | Notes |
|---|---|---|
| Carne Asada | $18.80 | |
| Steak Tampiqueno | $21.95 | 12 oz T-Bone |
| Steak Ranchero | $21.95 | 12 oz T-Bone |
| Steak Mexicano | $21.95 | 12 oz T-Bone |
| Steak and Shrimp | $27.20 | **NEW (website only)** — 12 oz T-Bone + jumbo grilled shrimp; rice, beans, baked potato |

### Kids Menu (online prices — 7 items)
| Item | Online price |
|---|---|
| Quesadilla (cheese) | $8.30 |
| Mini Quesadilla Fajita | $11.45 |
| Mini Nacho Fajitas | $11.45 |
| Mini Arroz Special | $11.45 |
| Kids Taco | $9.35 |
| Hamburger & Fries | $9.35 |
| Chicken Fingers & Fries | $11.45 |

### Sides (online prices — 8 items; small Queso Dip lives under Botanas online)
| Item | Online price |
|---|---|
| Queso Dip Large | $14.60 |
| Beans | $4.10 |
| Rice | $4.10 |
| Tortillas | $4.10 |
| Sour Cream | $3.05 |
| Pico de Gallo | $3.05 |
| Shrimp | $11.45 |
| Chorizo | $4.10 |

### Drinks (online prices — 3 items; no desserts section on website)
| Item | Online price |
|---|---|
| Jarritos | $5.15 |
| Bottled Coke | $5.15 |
| Fountain Drinks | $5.15 |

✅ **WEBSITE MENU CAPTURE COMPLETE (2026-06-11).** Sections: Botanas 6 · Nachos 4 · Enchiladas 3 · Tacos 6 · Salads 4 · Burritos 6 · Quesadillas 3 · Fajitas 9 · Chimichangas 6 · Specials 18 · Steaks 5 · Kids 7 · Sides 8 · Drinks 3 = **88 orderable line items.** No desserts, no margaritas/beer online (alcohol = dine-in only, as assumed).
**Note:** Fajita Plate and Fajita Brava Plate do NOT appear in the website's 18-item Specials section → likely discontinued (they were old-printed-menu items). The fajita craving is served by the FAJITAS section instead.
**Markup observation:** online markup is brutal on cheap items (sides $2 printed → $4.10 online, drinks $3 → $5.15, +70%+) vs ~17–25% on entrees — strongly suggests the online platform adds fees; phone orders at in-store prices would be a genuinely better deal for customers.

## ⚠️ Menu versioning note
Multiple menu generations exist (FB photos show at least two older ones with prices $1–3 lower across the board). **The newest printed menu (Gabe's first two screenshots) is the price source of truth.** Items below appeared ONLY on older menus — confirm with the owner whether they're still offered and at what price before the bot can sell them:

| Old-menu-only item | Old price | Description |
|---|---|---|
| Checo's Dip | $8 | ground beef, pico de gallo & queso dip |
| Enchiladas Suiza | $12 | four soft corn tortillas, **chicken or steak**, salsa tomatillo + cheese sauce, lettuce, guacamole, sour cream, tomatoes |
| Enchiladas Supremas | $12 | four rolled corn tortillas — one beef, one chicken, one cheese, one bean; enchilada sauce, lettuce, sour cream, cheese, tomatoes |
| Enchiladas Mexicanas | $12 | four corn tortillas w/ **chicken or steak** strips; ranchera sauce, cheese sauce, salsa tomatillo; lettuce, guac, sour cream, tomatoes |
| Fajita Plate | $13 | grilled bell peppers/onions/tomatoes, **chicken or steak**; rice, beans, lettuce, pico, sour cream, guac & tortillas |
| Fajita Brava Plate | $16 | fajitas w/ chicken, steak & shrimp; rice, beans, lettuce, pico, sour cream, guac & tortillas |
| Fajita Chimichanga | $14 | fajita **chicken or steak** w/ peppers, onions & tomatoes |
| Guacamole (?) | — | old Fajita Plate/Papa Fajita descriptions mention guacamole; no guac side listed on any menu — ask if guac/side is orderable |

(Note: an entire ENCHILADAS section exists on the old menu but is absent from the new menu screenshots — possibly on a page not photographed. High-value question: enchiladas are a top-5 phone-order category for Mexican restaurants.)

## ⚠️ Pricing rules (critical for the order bot)
- **All prices include tax** — quoted total = menu math, no tax calculation.
- **+$1 per to-go order** (flat, per order) — applies to essentially every phone order. Bot must add it and mention it.
- **+$3 to add cheese sauce on top of anything** — the upsell modifier.
- Raw/undercooked food disclaimer on menu (steaks) — no bot action needed.
- ❓ Confirm: alcohol (margaritas/beer) should be refused for to-go orders — assume dine-in only unless Gabe says otherwise.

## Still needed (gaps)
- [ ] **#1 QUESTION: which price tier do PHONE orders get — printed in-store menu, or website/online-ordering prices (~20% higher)?**
- [ ] Website URL + which online-ordering platform it is (the bot should mention/offer it)
- [ ] Confirm Monday = closed (absent from website hours)
- [ ] Delivery: who delivers (in-house? platform?), radius, fees — and can the BOT take delivery orders or pickup-only by phone?
- [x] ~~Website menu sections~~ — COMPLETE, 88 items captured
- [ ] Does the +$1 to-go charge still apply, and does it apply to phone orders? (printed-menu rule; website may bake it in)
- [ ] Desserts / lunch specials — none found anywhere; confirm none exist
- [ ] Papa Fajita phone price: one flat price ($20.90-equivalent) or by protein like the printed menu ($18 vs $21)?
- [ ] Exact restaurant name spelling + pronunciation for the greeting (La Feyita / La Feita)
- [ ] Pickup only, or delivery too?
- [ ] Where orders go: whose cell for SMS, what email
- [ ] Spanish from day one?
- [ ] Alcohol on phone orders: refuse, or is there carry-out beer? (assuming refuse)
- [ ] Common caller questions (catering? reservations? party trays?)
- [ ] Fountain drink flavors + Jarritos flavors (callers will ask)
