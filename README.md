# DennisStaff

A Scrapy spider that crawls the Town of Dennis, MA departments page and extracts staff/department directory data into a structured format ready for import into a CivicPlus staff directory.

## What it does

The `staff` spider crawls `https://www.town.dennis.ma.us/departments`, walks each row of the departments table, and pulls out:

- Department/staff name
- Mailing and physical address (street, city, state, zip)
- Phone number, digits are stripped and re-formatted into `###-###-####`
- Sub-department info

Each row is yielded as a dictionary with the exact field names CivicPlus staff directory imports expect (`Name`, `Address 1`, `City`, `Phone`, `Hidden`, `Show Archive`, etc.), so the output can be fed straight into a bulk import rather than needing to be manually reformatted.

There's also a `people` spider stub (`spiders/people.py`) that's scaffolded but not implemented — it currently does nothing (`parse` just `pass`es).

## Project structure

```
dennisStaff/
├── spiders/
│   ├── staff.py      # Main spider — scrapes department directory rows
│   └── people.py      # Unimplemented stub for a future spider
├── items.py           # Empty Scrapy Item definition (not currently used)
├── pipelines.py        # Default pass-through pipeline (no custom processing)
├── settings.py         # Standard Scrapy project settings
scrapy.cfg
```

## Requirements

```
pip install scrapy
```

## Usage

From the project root (where `scrapy.cfg` lives):

```
scrapy crawl staff -o staff_directory.csv
```

This runs the `staff` spider and writes the results to a CSV (swap `.csv` for `.json` if you'd rather have JSON output).

## Notes

- `ROBOTSTXT_OBEY = True` is set in `settings.py`, so the spider respects the target site's robots.txt.
- Built specifically for the Town of Dennis website's markup (`allowed_domains = ["www.town.dennis.ma.us"]`) — the XPath selectors are tailored to that site's table structure and would need adjusting for a different municipal site.
- The `extractNumber` helper inside `staff.py` is defined but currently unused (it returns on the first character due to an early `return` inside the loop) — worth a look if address parsing ever seems off.
