# Spanish Vocabulary Widget (Scriptable)

This project provides a **Spanish Vocabulary Widget** for iOS, built using the [Scriptable](https://scriptable.app/) app. It displays random A1/A2 level Spanish words on your home screen or lock screen, including verbs (with conjugations), nouns, and adjectives.

## Features
![Example Widget](Example.PNG)

*   **~1000 Word Vocabulary**: A curated list of common A1 and A2 verbs, nouns, and adjectives.
*   **Smart Conjugations**: Automatically displays random present or past tense conjugations for verbs (e.g., *yo serví*).
*   **Forms & Plurals**: Shows specific forms for adjectives (masculine/feminine/plural) and nouns.
*   **4-Line Display**:
    1.  **Word**: The base lemma (e.g., `servir, v.`).
    2.  **Form Info**: The grammatical context (e.g., `pas., yo` or `form, fem.`).
    3.  **Variation**: The actual conjugated/inflected word (e.g., `serví`), highlighted in Cyan.
    4.  **Meaning**: English translation.
*   **Abbreviations**: Uses standard abbreviations (e.g., `pres.`, `nos.`, `masc.`) to fit info specifically on small widgets.

## How to Use

1.  **Install Scriptable**: Download the [Scriptable app](https://apps.apple.com/us/app/scriptable/id1405459188) from the App Store.
2.  **Add the Script**:
    *   Open Scriptable and click **+** to create a new script.
    *   Copy the contents of `scriptable.md` (the JavaScript code, not the comments) into the editor.
    *   Name it something like "SpanishVocab".
3.  **Add the Widget**:
    *   Go to your iOS Home Screen.
    *   Long press -> `+` -> Select **Scriptable**.
    *   Choose the "Small" widget size.
    *   Tap the widget to configure it -> Select "SpanishVocab" under **Script**.
4.  **Enjoy**: The word will refresh periodically (default: every 2 hours).

## Data Source

The vocabulary is stored in `spanish_a2_full_vocab.json`.
*   You can expand the list using the `expand_vocab.py` script.
*   The script logic fetches the JSON directly from the raw GitHub URL, so you don't need to store the massive JSON file locally on your phone.

## Project Structure

*   `spanish_a2_full_vocab.json`: The main database of words.
*   `scriptable.md`: The JavaScript code for the Scriptable widget.
*   `expand_vocab.py`: Python utility used to generate and expand the vocabulary list programmatically.

## Recent Updates

*   Expanded vocabulary to ~1000 words.
*   Updated widget layout to a clean 4-line format.
*   Added abbreviations for better readability on small screens.
