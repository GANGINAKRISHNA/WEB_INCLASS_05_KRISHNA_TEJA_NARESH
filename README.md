# Activity 05 · CSS Crime Scene: Bug Diagnosis Report

**Team Name:** KOTHOJU NARESH_SRI KRISHNATEJA GANGINA

**Members / IDs:**
- KOTHOJU NARESH: 002923882
- SRI KRISHNATEJA GANGINA: 002923905

**File fixed:** `css_arcade_fixed.html` (original: `css_arcade_broken.html`)

---

## Summary

| Bug | Zone | Topic | Faulty line | Fix |
|-----|------|-------|-------------|-----|
| 1 | Zone 1 · Stacked Snacks | Flexbox axis | `flex-direction: column` | `flex-direction: row` |
| 2 | Zone 2 · Lopsided Board | Grid tracks | `grid-template-columns: 100px 100px` | `grid-template-columns: repeat(3, 100px)` |
| 3 | Zone 3 · Snap-Back Fraction | Animation fill mode | `animation: moveFraction 3s ease-in-out 1s` | add `both` |
| 4 | Zone 4 · The Buried Heart | Positioning and z-index | `.heart` has no `position` | add `position: absolute` |

---

## Bug 1: The Cookie Menu Won't Sit in a Row (Flexbox Axis)

**Symptom:** The three recipe cards stack on top of each other in a tall column, and `justify-content: space-between` seems to do nothing horizontally.

**Diagnosis:**
The `flex-direction` property sets the main axis. It was set to `column`, which makes the main axis vertical, so the three recipe cards stack on top of each other. Because `justify-content` works along the main axis, `space-between` was spreading the cards vertically (and there was no extra vertical space to distribute, so it looked like it did nothing). Changing `flex-direction` to `row` makes the main axis horizontal, so the cards sit side by side and `space-between` spaces them across the row.

**Fix:**
```css
.recipe-row {
  display: flex;
  flex-direction: row; /* 🐛 BUG #1 */
  justify-content: space-between;
  gap: 1rem;
}
```

**Short version:**
`flex-direction: column` made the main axis vertical, so the cards stacked and `justify-content: space-between` acted vertically instead of horizontally. Setting it to `row` puts the main axis horizontal, so the cards sit in a row and `space-between` spreads them across.

---

## Bug 2: Tic-Tac-Toe on a 2-Wide Board (Grid Tracks)

**Symptom:** The nine cells form a narrow board only two cells wide and five rows tall, with one lonely cell in the last row.

**Diagnosis:**
`grid-template-columns: 100px 100px` defines only two column tracks, so the grid has just two columns. The browser doesn't create a third column for the extra cells. It auto-places them into new implicit rows (sized by `grid-auto-rows: 100px`), two per row. Nine cells therefore make five rows: four full rows of two, plus one lonely cell in the last row. Changing it to `repeat(3, 100px)` defines three columns, so the cells fill a 3×3 board.

**Fix:**
```css
.board {
  display: grid;
  grid-template-columns: repeat(3, 100px); /* 🐛 BUG #2 */
  grid-auto-rows: 100px;
  gap: 8px;
  justify-content: center;
}
```

**Short version:**
Only two column tracks were defined, so the browser auto-placed the nine cells two per row into implicit rows, giving five rows with one cell left over. `repeat(3, 100px)` defines three columns, which makes a proper 3×3 board.

---

## Bug 3: The 3/4 Won't Stay Next to the = Sign (Animation Fill Mode)

**Symptom:** For the first second the answer already sits next to the = sign; then it jumps below the question in yellow, slides back, turns red, and the instant the animation ends it loses its red color.

**Diagnosis:**
The element should look like the 0% keyframe (yellow, sitting below the question) during the 1s delay, and like the 100% keyframe (red, next to the = sign) after the animation ends. That is controlled by `animation-fill-mode`. With no fill mode set, the default is `none`, so the keyframe styles only apply while the animation is actively running. During the delay the answer shows its normal styles (next to the = sign, no color), and the moment the animation finishes it snaps back to those same normal styles and loses the red. Adding `both` (`backwards` + `forwards`) applies the 0% keyframe during the delay and holds the 100% keyframe after the end.

**Fix:**
```css
#answer {
  position: absolute;
  left: 380px;
  top: 1rem;
  animation: moveFraction 3s ease-in-out 1s both; /* 🐛 BUG #3 */
}
```

**Short version:**
Without `animation-fill-mode`, the keyframe styles only apply while the animation is running, so the answer showed its default position during the 1s delay and snapped back (losing the red) when it ended. Adding `both` applies the 0% keyframe during the delay and keeps the 100% keyframe afterward.

---

## Bug 4: I ♥ NY, Where Did the Heart Go? (Stacking and z-index)

**Symptom:** "I" and "NY" sit perfectly on the tee, but the red heart ignores its `top` / `left` values, drifts to the top-left corner, and slips partly behind the shirt, even though it has `z-index: 10`.

**Diagnosis:**
`top`, `left`, and `z-index` only work on positioned elements, and `.heart` has no `position` value, so it defaults to `position: static`. Static elements ignore offset properties (`top`, `left`, `right`) and `z-index`, so the heart stays in normal flow at the top-left of `.shirt-wrap` instead of moving to `top: 110px`. Because it isn't positioned, it also can't be raised above the positioned `.shirt` (`z-index: 1`), so it slips partly behind the shirt. The other two words, `.word`, work because they have `position: absolute`. Adding `position: absolute` to `.heart` makes the offsets and `z-index: 10` take effect, so the heart is centered between "I" and "NY" and stacks above the shirt.

**Fix:**
```css
.heart {
  position: absolute; /* 🐛 BUG #4 */
  top: 110px;
  left: 0;
  right: 0;
  text-align: center;
  z-index: 10;
  color: var(--accent);
  font-size: 3.5rem;
}
```

**Short version:**
`top`, `left`, and `z-index` are ignored on `position: static` elements, and `.heart` had no `position` set, so it stayed in normal flow and got covered by the shirt. Adding `position: absolute` makes the offsets and `z-index: 10` apply, placing the heart between "I" and "NY" above the shirt.
