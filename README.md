# energy-tracker-data

Content the GB Grid Watch app downloads so it can be updated without an app release:

- `priceCaps`: Ofgem price cap periods (used for the Agile vs cap comparison). From app 1.2.0 a cap can also carry optional fields for the "Price cap change" card on the Insight tab, which shows from `announced` until four weeks after the cap starts: `typicalBillGBP`, `elecBillChangePct`, `gasBillChangePct`, `why` and `worthKnowing` (lists of paragraphs using the text formatting below) and `source`. Add `typicalBillGBP` to the previous cap too, so the card can show the change in the typical bill.
- `billQuarters`: the quarterly unit-rate breakdown in "What's in your bill"
- `nuclear`: nuclear station details, plus the Hinkley Point C and Sizewell C sections

The app reads `app-content.json` from the `main` branch. It checks for a new copy at most every 6 hours, and GitHub's cache can add about 5 minutes. If the file can't be downloaded or doesn't pass the checks, the app keeps using its last good copy.

## Making a change

1. Edit `app-content.json`.
2. **Increase `revision` by 1** (and set `updated` to today's date). The app only takes a file whose revision is higher than the copy it already has.
3. Check it with the app's own validator, from the app repo's `app-src` folder:

   ```
   npm run content:check -- ../../energy-tracker-data/app-content.json
   ```

4. Commit and push to `main`.

Before each app release, run `npm run content:check -- --pull` in the app repo, so the app ships with the latest copy.

## Rules

- **This repo is the master copy.** Don't edit the app's bundled copy by hand; refresh it with `--pull` before a release.
- **Only add fields; never rename or remove one.** Older versions of the app ignore fields they don't know about, but they rely on the existing ones.
- **Don't add new values to fixed lists:** a station's `status` must be `active`, `decommissioning` or `closed`, and colours must be one of the five below. Older apps reject the whole section if they see a value they don't know.
- Keep `schema` at `1`. Changing it means older apps ignore this file entirely, so only change it together with an app release that reads the new format.
- Each section is checked separately. If one section is broken, the app ignores just that section and keeps its previous copy. A list that's empty, or shorter than expected, counts as broken.

## Text formatting

These nuclear fields support formatting: each station's `note`, `sources`, and each future station section's `body` paragraphs and `footnote`. Names, subtitles, badges and headings are plain text.

- `**bold**` for emphasis
- `{red:text}`, `{amber:text}`, `{green:text}`, `{blue:text}` or `{purple:text}` for a coloured highlight
- `\n` for a line break

Anything else shows as plain text. `accent`, `badgeAccent` and `box` take the same five colour names.
