# compute-cafe-data

The public data feed behind [compute.cafe](https://www.compute.cafe), a live panel of on-demand GPU cloud prices.

This repo is the panel's public data layer, published as plain JSON. If you want the numbers without the website, this is the source.

## What's in here

| Path | Contents |
| --- | --- |
| `data/providers.json` | Every tracked provider with its current listings: GPU model, price per hour, location, availability |
| `data/listings-full.json` | The same listings flattened, one row per offer |
| `data/listings-essential.json` | Trimmed listing view the panel loads first |
| `data/history-index.json` | Recent daily price points per listing series (rolling 14-day window) |
| `data/world-dots.json` | Datacenter locations behind the panel's map |
| `data/meta.json` | Last refresh time, per-provider source and status, and the FX rate used for INR-priced providers |
| `history/YYYY-MM-DD.json` | Daily snapshots, rolling 14-day window |

## Freshness

Providers with live APIs refresh every hour; the full catalog refreshes every 6 hours. `data/meta.json` carries the exact `observed_at` timestamp and source for each provider, so you never have to guess how old a number is.

## Rules the data follows

- First-party only. Every rate comes from the provider's own public API or pricing page. No estimates, no scraped roundups, no guessed figures.
- On-demand only. Spot, reserved, and commitment-priced offers are excluded so rows stay comparable.
- USD throughout. INR-native providers are converted with the FX rate stamped in `meta.json`.

## Using it

Everything here is plain JSON, no auth:

```
curl https://raw.githubusercontent.com/DeepanshuPal/compute-cafe-data/main/data/providers.json
```


Or use the bundled CLI (single file, Python 3 standard library only):

```
curl -O https://raw.githubusercontent.com/DeepanshuPal/compute-cafe-data/main/cafe
chmod +x cafe
./cafe providers                  # who is tracked, and how fresh the data is
./cafe cheapest --gpu h100        # cheapest current H100 offers
./cafe gpu "rtx 4090" --limit 20  # RTX 4090 offers, cheapest first
./cafe history h100               # recent daily prices (14-day public window)
```

This public feed carries the current market plus a rolling 14-day window of price history, so you can check freshness and build against the schema. The full historical archive and the price index built on it are part of [compute.cafe](https://www.compute.cafe) - if you need long-range history, write to deepanshupal.dce@gmail.com.

If you build on this data, a link back to [compute.cafe](https://www.compute.cafe) is appreciated. Prices are each provider's own published rates - verify with the provider before you buy.
