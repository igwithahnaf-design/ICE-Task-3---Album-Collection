# ICE Task 3 – Album Collection & Debugging

## 1. Introduction

The Album Collection app lets a user enter an album's title, artist, release year, genre and rating, validates the input, adds valid albums to a favourites list, displays the list in a `FlatList`, and lets the user delete albums. The supplied `App.tsx` contained several intentional errors. This project documents how they were found and fixed.

## 2. Development Environment

- Expo (`expo-template-blank-typescript`)
- React Native
- TypeScript
- Institutional VM
- BlueStacks 5 emulator
- Expo Go

## 3. Error Log

| # | Location | Problem | Error Type | Correction |
|---|----------|---------|------------|------------|
| 1 | `Album` type | `year` and `rating` were declared as `string`, but `handleSave` assigns `Number(year)` and `Number(rating)`. A number cannot be assigned to a string property, so TypeScript reports an error. | TypeScript | Changed `year: string` and `rating: string` to `year: number` and `rating: number`. |
| 2 | `validateForm` – title length check | Condition used `&&` (length < 2 AND length > 50). No value can satisfy both, so the check never fired. | Logic | Changed `&&` to `\|\|`. |
| 3 | `validateForm` – artist length check | Same impossible `&&` condition as the title check. | Logic | Changed `&&` to `\|\|`. |
| 4 | `validateForm` – year check | Only checked `year < MIN_YEAR`. Future years passed, although the message says the year must be between 1900 and the current year. | Logic / Validation | Added `\|\| numericYear > currentYear`. |
| 5 | `validateForm` – rating check | Only checked `rating < 1`. Ratings above 5 (e.g. 10) were accepted. | Logic / Validation | Added `\|\| numericRating > MAX_RATING`. |
| 6 | `handleSave` – `setAlbums` | `setAlbums([temporaryAlbum])` replaced the whole array, so each new album wiped the earlier ones. | State / Logic | Changed to `setAlbums((currentAlbums) => [...currentAlbums, temporaryAlbum])`. |
| 7 | `handleDelete` | `filter(album => album.id === id)` kept only the selected album and removed all the others. | Logic | Changed `===` to `!==`. |
| 8 | Genre `Picker` – `selectedValue` | Bound to `title` instead of `genre`, so the picker did not show the chosen genre. | Runtime / State | Changed to `selectedValue={genre}`. |
| 9 | Genre `Picker.Item` | Every item had `value={genre}` (the current state) instead of its own value, so every option selected the same value. | Runtime / Logic | Changed to `value={item}`. |
| 10 | `FlatList` – `keyExtractor` | Used `item.title`, which is not unique (two albums can share a title) and causes duplicate-key warnings and rendering problems. | Runtime | Changed to `item.id`. |

## 4. Testing

Tested in the BlueStacks 5 emulator with Expo Go:

- **Launch:** the app opens, the form is displayed and there are no errors.
- **Invalid input:** empty fields, a 1-character title/artist, a non-numeric or future year, no genre, and ratings of 0 and 6 each showed a validation alert and nothing was added.
- **Valid input:** a complete album was added and its title, artist, year, genre and rating displayed correctly.
- **Multiple albums:** several albums were added and all remained in the list with correct details.
- **Delete:** one album was deleted from a list of several; only that album was removed and the others stayed.
- **Repeat:** adding and deleting repeatedly kept the app stable.

## 5. Screenshot

The completed Album Collection app running successfully in the BlueStacks 5 emulator using Expo Go:

![Completed Album Collection app running in BlueStacks 5 using Expo Go](screenshot.png)

Additional screenshots from testing:

| Initial application (empty collection) | Valid album entered in the form |
|---|---|
| ![Initial application](screenshot-initial.png) | ![Form filled with a valid album](screenshot-form-filled.png) |

## 6. Conclusion

Debugging this app showed me that many bugs do not trigger compiler errors and only appear when you run and interact with the app. I learned to check TypeScript types against the values assigned, to write validation conditions carefully (`&&` versus `||`), to update React state without overwriting existing data by using the spread operator, and to use unique keys in a `FlatList`. Testing both valid and invalid input was essential to finding every problem.
