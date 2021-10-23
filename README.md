# Profanity List

This repository contains highly consumable lists of words and/or phrases that may be considered profane or inappropriate. The profanity lists come in two forms: [JSON format](#json-format) that contains the words and phrases with associated meta information and a [plain text format](#plain-text-format) that contains one word or phrase per line.

Please note that most entries in the JSON lists have started their life with a [Severity Level](#severity-levels) of `3` (Strong) prior to any solid classification - this is not necessarily an accurate reflection of the common severity level of any of these words or phrases. [Contributions](#contributing) are more than welcome to adjust these levels to bring them more in line with what you may expect.

## Languages

| Name    | Code    | JSON                     | Plain Text             | Meta                                       |
| ------- | ------- | ------------------------ | ---------------------- | ------------------------------------------ |
| English | `en`    | [en.json](en.json)       | [en.txt](en.txt)       | `434` profanities, `809` matches, `6` tags |
| Emoji   | `emoji` | [emoji.json](emoji.json) | [emoji.txt](emoji.txt) | `7` profanities, `18` matches, `2` tags    |

## Available Tags

| Name    | Code    | Tags        |
| ------- | ------- | ----------- |
| English | `en`    | `general`   |
|         |         | `lgbtq`     |
|         |         | `racial`    |
|         |         | `religious` |
|         |         | `sexual`    |
|         |         | `shock`     |
| Emoji   | `emoji` | `general`   |
|         |         | `sexual`    |

## JSON Format

The JSON format has a top level array containing objects with the following structure:

| Property        | Required? | Description                                                                                                                                                                                                               |
| --------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`            | Y         | String in lowercase characters used as a unique ID for the profanity.                                                                                                                                                     |
| `match`         | Y         | String in lowercase characters. Asterisks (`*`) can be used to indicate the previous character can have one or more appearances. Pipes (`\|`) can be used as a separator if matching multiple terms under this profanity. |
| `severity`      | Y         | Integer from `1` to `4` corresponding to a supported [Severity Level](#severity-levels).                                                                                                                                  |
| `tags`          | N         | An array of lowercase strings to indicate how this profanity is tagged. These should be in English.                                                                                                                       |
| `allow_partial` | N         | Boolean value. Whether or not this profanity should be used with partial matching. Explicitly set to `false` when the match may otherwise have hundreds or thousands of exceptions.                                       |
|                 |           | Implementations should default to `true` if no attribute is present. (ie. the profanity can be used in partial matching)                                                                                                  |
| `exceptions`    | N         | An array of lowercase strings indicating exceptions to this profanity if using partial matching. An asterisk (`*`) is used as a placeholder for the matched word.                                                         |
|                 |           | Example: `sp*` would be a valid exception (`'sparse'`) for the profanity `arse` if relying on partial matching.\_                                                                                                         |

### Severity Levels

| Value | Severity |
| ----- | -------- |
| `1`   | Mild     |
| `2`   | Medium   |
| `3`   | Strong   |
| `4`   | Severe   |

### Example Data Structure

```json
[
  {
    "id": "plain-text",
    "match": "plain text",
    "severity": 1,
    "tags": ["insults", "anti-computer"],
    "exceptions": ["unusually *", "very *"]
  },
  {
    "id": "multiple-matches",
    "match": "multiple|multipal",
    "severity": 2,
    "tags": ["functionality"]
  },
  {
    "id": "elongated-words",
    "match": "lo*ng",
    "severity": 3,
    "tags": ["long-words"],
    "exceptions": ["*ing"]
  },
  {
    "id": "exact-match-only",
    "match": "en",
    "severity": 1,
    "tags": ["exact-words"],
    "partial_match": "false"
  }
]
```

### Example Matching

| Profanity ID       | Sentence                                      | Should Match? | Comment                                   |
| ------------------ | --------------------------------------------- | ------------- | ----------------------------------------- |
| `plain-text`       | I like **plain text**!                        | Y             | _Exact match_                             |
| `plain-text`       | I generally do **plain text**ing.             | Y             | _Partial match_                           |
| `plain-text`       | _Unusually plain text_ is weird...            | N             | _Part of an exception_                    |
| `plain-text`       | You have _very plain text_.                   | N             | _Part of an exception_                    |
| `plain-text`       | Plain old sentence with text                  | N             | _Not found at all_                        |
| `multiple-matches` | There are **multiple** ways to match.         | Y             | _Exact match on first match option_       |
| `multiple-matches` | I can spell **multipal** just fine, thx.      | Y             | _Exact match on second match option_      |
| `multiple-matches` | I'm using the word many instead...            | N             | _Not found at all_                        |
| `elongated-words`  | This is a long word.                          | Y             | _Exact match_                             |
| `elongated-words`  | Such a looooong wait!                         | Y             | _Match on repeating `'o'`_                |
| `elongated-words`  | I am _longing_ for some food                  | N             | _Part of an exception_                    |
| `elongated-words`  | Short words are the best!                     | N             | _Not found at all_                        |
| `exact-match-only` | The language of this is **en**                | Y             | _Exact match_                             |
| `exact-match-only` | _Ensure_ I _send_ a _pencil_ to the _agency_. | N             | _Partial matching disallowed/discouraged_ |

## Plain Text Format

The plain text format contains one word or phrase per line and does not include any meta information on the matching such as asterisks to indicate one or more characters matched.

### Example Data Structure

```text
plain text
long
multiple
multipal
```

## Node Modules

### List / Data Package

This package is available as an NPM package for use in JS projects.

NPM installation:

```bash
npm install @dsojevic/profanity-list
```

Yarn installation:

```bash
yarn add @dsojevic/profanity-list
```

## Contributing

Contributions to the profanity lists are welcome. As profanities are subjective by nature, please use your best judgement when it comes to adding to or updating these lists.

The source of truth for these lists is located in the JSON files in the `src` directory. The top level language files should be built from these source files using the `./bin/build.js` command.

### Adding Profanities

Adding new profanities to the lists and adding new lists for other languages are both highly encouraged. Please

### Removing Profanities

Removal of items is discouraged - if you feel strongly that an item isn't profane or doesn't belong, it is preferred that you instead adjust the severity level of the item and/or update the tags associated with it. This allows consumers of the JSON formats to make those decisions for themselves.

### Managing Tags

It is preferred that the number of tags used overall is kept relatively small in this data set so as to not overwhelm the choices required by a consumer. Please open an issue to propose any new tags so it can be made open for discussion.

Tag usage should be kept consistent from language to language. Some cases may warrant a language (or set of languages) having a small number of tags that are only applicable to them, though this should only be for exceptional circumstances.

### Severity Levels

As noted in the introduction, the default severity level for items in this list is `3` (Strong) as a starting point. Contributions that adjust these to levels that better reflect their severity are more than welcome. For example, if you believe an item is only mildly offensive to the common person, it can be downgraded to `1` (Mild) -- conversely, if you think it is even more offensive it can be upgraded to `4` (Severe).

---

Copyright (c) 2021 David Sojevic
