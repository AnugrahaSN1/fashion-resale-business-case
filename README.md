# Fashion resale business case

Should a fashion brand launch a take-back and resale programme?

The idea is simple: customers bring back clothes they don't wear any more, get a voucher, and the brand resells the clothes second-hand. Lots of brands are trying it (Patagonia Worn Wear, Levi's, The Iconic), and second-hand is growing fast. But whether it actually makes money for a brand is a lot less clear.

This project builds the business case in Python. Because most of the inputs are uncertain, it doesn't use one set of guesses. It uses a Monte Carlo simulation: every input is a range, the year is simulated 10,000 times, and the result is a probability of making money rather than a single number.

**Live report:** [View the business case](https://anugrahasn1.github.io/fashion-resale-circular-business-case/)
## What it compares

Two ways of running the programme:

- **In-house.** The brand inspects, cleans, photographs and sells the clothes itself. It keeps all the resale revenue but pays for a team, software and warehouse space.
- **Partner.** A resale specialist does the operations and keeps a share of the revenue plus a yearly fee.

## What goes into the model

For each simulated year:

1. Some customers trade in clothes (participation rate × items per trade-in)
2. Only some items are good enough to resell (acceptance rate)
3. Some of those sell within the year (sell-through), at a fraction of their original price
4. Each accepted item earns the customer a voucher
5. Vouchers cost the brand their face value, but bring customers back to buy new products. Only part of that is genuinely extra, because some of those customers would have bought anyway
6. Costs: processing, return shipping, recycling what can't be sold, fixed costs or partner fees
7. CO2 avoided: each resold item replaces part of a new purchase

## Results for the default brand

200,000 active customers, an average price of €90, 60% gross margin:

| | Chance of profit | Pessimistic (P10) | Median | Optimistic (P90) |
|---|---|---|---|---|
| Partner | 60% | -€54k | €15k | €116k |
| In-house | 3% | -€307k | -€200k | -€78k |

About 10,800 items resold and 19 tonnes of CO2e avoided per year (median).

What I take from this:

- **For a brand this size, in-house doesn't work.** The fixed costs are too high for the volume. With the default assumptions it doesn't break even even if 30% of customers took part.
- **The partner route is roughly break-even.** It's a reasonable pilot, but not something to launch for profit alone. The case rests on customer loyalty and sustainability as much as on margin.
- **Scale changes the answer.** With 1 million customers, the partner route makes money in 85% of simulated years and in-house becomes possible (41%).
- **What to test first:** the sensitivity chart shows that the resale price (as a % of the original price) and how much of the voucher spending is genuinely new are the two assumptions that move profit most. Those are what a pilot should measure.

## Where the numbers come from

Brand-run resale programmes don't publish their unit economics, so most inputs are my own assumptions, and the report labels each one. Two are based on published figures:

- **Resale price:** Patagonia estimates most Worn Wear trade-ins sell at about 20% of their original price ([Inside Retail](https://insideretail.com.au/sectors/fashion-accessories/should-fashion-brands-own-resale-202605)). I used that as the low end of the range.
- **CO2:** a report by Vaayu for Vinted found 1.8 kg CO2e avoided on average per second-hand item bought instead of new ([Vaayu, 2023](https://www.vaayu.tech/press-releases/shopping-second-hand-on-vinted-is-a-better-choice-for-the-climate-than-buying-new-according-to-a-report-by-vaayu)).

Market context: ThredUp's 2025 Resale Report expects the global second-hand apparel market to reach $367 billion by 2029 ([summary](https://transition-pathways.europa.eu/textiles/articles/resale-rise-key-insights-thredup-resale-report-2025)).

## Run it yourself

```bash
pip install -r requirements.txt
python circular_sim.py
```

Try a different brand:

```bash
python circular_sim.py --customers 1000000 --rrp 120
```

Or change every assumption:

```bash
python circular_sim.py --export-config my_brand.json   # writes the defaults to a file
# edit my_brand.json
python circular_sim.py --config my_brand.json
```

The report and the 10,000 simulated years (as a CSV) are saved in the `reports` folder.

## Limitations

- One year only. A real programme takes a while to ramp up, so a 3-year view with growing participation would be more realistic.
- It doesn't value new customers who discover the brand through resale. That could make the case look better.
- Ranges use a triangular distribution and are treated as independent. In reality some are linked (better quality items probably sell faster and for more).
