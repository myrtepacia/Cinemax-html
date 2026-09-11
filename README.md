# Cinemax — HTML and CSS

A standalone copy of every page on the Cinemax website. **HTML and CSS only —
no JavaScript anywhere.** One HTML file per page, all sharing a single
stylesheet.

Open any `.html` file straight in a browser — double-click it — and it works.
The links between pages work too. Nothing to install and nothing to run.

## The files

| File                  | Page                                               |
| --------------------- | -------------------------------------------------- |
| `cinemax.css`         | The stylesheet, in 17 numbered sections             |
| `index.html`          | Home — Now Showing and Upcoming Shows               |
| `signin.html`         | Sign in                                             |
| `signup.html`         | Sign up                                             |
| `book.html`           | Booking — seat map, snacks, running total           |
| `ticket.html`         | E-ticket with the QR code                           |
| `account.html`        | My Bookings                                         |
| `terms.html`          | Terms of Service                                    |
| `confirming.html`     | "Confirming your payment" (with the spinner)        |
| `cancelled.html`      | "Payment cancelled"                                 |
| `404.html`            | Page not found                                      |
| `500.html`            | Server error                                        |
| `admin.html`          | Staff dashboard — earnings and the snack queue      |
| `admin-movies.html`   | Staff — movies table and the add-movie form         |
| `admin-scanner.html`  | Staff — door scanner                                |
| `photo/`              | The six film posters                                |
| `qr-sample.svg`       | The QR code printed on the e-ticket                 |

## The colours

| Hex | Used for |
| --- | --- |
| `#fb2c36` | brand red — buttons, links, the active nav pill |
| `#e7000b` | the red button on hover |
| `#fef2f2` | tint behind anything selected |
| `#101828` `#4a5565` `#6a7282` `#99a1af` | text, darkest to lightest |
| `#ffffff` `#f9fafb` `#f3f4f6` `#e5e7eb` | card, page, hairline, border |
| `#00bc7d` `#dcfce7` `#036e46` `#008236` | green — showing, ready, paid |
| `#4f39f6` `#e0e7ff` | indigo — upcoming, free seats |
| `#a65f00` `#fef9c2` `#854d0e` | amber — warning, preparing |
| `#c10007` `#ffe2e2` | red — error, rejected |
| `#312e81` → `#6d28d9` → `#9333ea` | the e-ticket header gradient, `#ddd6ff` text on it |

## The one trick worth knowing

Selecting a seat on `book.html` uses **no JavaScript at all**. Each seat is a
hidden `<input type="checkbox">` inside a `<label>`, with a visible `<span>`
beside it:

```html
<label class="seat">
  <input type="checkbox" value="C5">
  <span class="seat-box">5</span>
</label>
```

The stylesheet hides the checkbox and restyles the span next to it:

```css
.booking input                  { display: none; }
.seat input:checked + .seat-box { background-color: #fb2c36; }
```

`+` means "the element immediately after". Clicking anywhere in the label
ticks the hidden box, and the span turns red. The same pattern drives the
snack list.

## What still works without JavaScript

**The booking page still cascades.** It opens with nothing chosen and the
seats hidden behind "Choose a date and a showtime". Pick both and the seats
and snacks appear — and switching showtime really does change which seats are
grey. That is pure CSS. `option:checked` matches whichever option a `<select>`
is currently showing, and `:has()` lets the block above react to it:

```css
#booking-layout:has(#date option:not([value=""]):checked)
               :has(#showtime option:not([value=""]):checked) #seats-and-snacks {
  display: block;
}

#booking-layout:has(#showtime option[value="102"]:checked)
               :is(.s-C1, .s-C2, .s-C3, .s-D6) .seat-box {
  background-color: #e5e7eb;
}
```

Each seat carries a class naming it (`.s-C1`), and each showtime lists its own
sold seats. A sold seat gets `pointer-events: none`, so the label never passes
the click to its hidden checkbox and the seat cannot be picked.

**The hamburger menu still opens.** It is a `<label>` tied to a hidden
checkbox sitting just before the nav, so `.menu-checkbox:checked ~ .bar-nav`
opens the drawer. At desk width `.bar-nav` is `display: contents`, so the nav
and buttons sit in the bar's own flex row as if the wrapper were not there.

## What JavaScript was doing, and is now gone

| Was | Now |
| --- | --- |
| **Scanner camera** | Cannot be done in CSS. The page shows the camera panel and all three verdict colours as a static mockup. |
| **Show / hide password** | Cannot be done in CSS — nothing can change an input's `type`. The button is gone. |
| **Running total** | CSS cannot add up prices. The summary shows its starting state and does not change as you tick seats. |
| **Scroll highlighting** | The nav link for "Now Showing" is marked current and stays that way. |
| **Staff order queue** | The dropdowns no longer move a row between Preparing / Ready / Sold. |

## Gotchas

These are load-bearing and will break quietly if changed. They used to be
comments in the code; they live here now instead.

1. **`.hidden` must stay last in `cinemax.css`.** It has to beat any component
   rule that sets its own `display`, and one plain class selector only beats
   another by coming later in the file. When it sat higher up, `.scan-actions`
   and `.ticket-actions` — both `display: flex` — quietly overrode it and
   their buttons showed when they were meant to be put away.

2. **`.bar-nav` is `display: contents` at desk width.** That makes the
   wrapper itself disappear from the layout so the nav and the buttons sit in
   the bar's own flex row. Give it any other display and the bar collapses
   into two items instead of four.

3. **`height: auto` on `.booking-poster`** — the only rule here that is not in
   the live stylesheet. The `<img>` carries `width="900" height="1200"` so the
   browser can reserve space, and those attributes apply as CSS. That gives
   the image an explicit height, and `aspect-ratio` only ever sizes a
   dimension that is `auto` — so without this the poster renders 1200px tall
   with `object-fit` slicing a strip out of the middle. **This is still a live
   bug on the real site.**

4. **`.snacks` and `.snacks h3` match nothing.** The markup has no `.snacks`
   wrapper, so `.seat-area h3` styles that heading instead. Add the wrapper to
   switch them on, or delete the rules. Same in the live stylesheet.

5. **The `.menu-checkbox` must stay immediately before `.bar-nav`.** The
   drawer opens through `~`, the sibling combinator, so putting anything
   between them or reordering the two silently stops the menu opening.

## How this differs from the live site

The live site builds a lot of its markup at runtime from the database. Those
parts are written out here as ordinary HTML, filled with the sample data the
project seeds: the movie cards, the booking page's dropdowns and seat map and
snack list, the e-ticket details, the My Bookings rows, the staff dashboard
figures and order rows, the movies table, and the scanner's verdict.

Hidden states are included in the markup carrying `class="hidden"` — loading
notes, error messages, empty states. Delete that class on any of them to see
what that state looks like.
